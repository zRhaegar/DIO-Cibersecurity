# DIO-Cibersecurity
Bootcamp da DIO

1. Introdução
Este projeto tem como objetivo demonstrar o aprendizado no bootcamp, em ambiente controlado, o funcionamento de técnicas de autenticação automatizada, como força bruta e password spraying.
O laboratório foi montado utilizando:
•	Kali Linux — máquina atacante
•	Metasploitable 2 — máquina propositalmente vulnerável
•	DVWA (Damn Vulnerable Web Application) — aplicação criada para estudos de segurança
•	Ferramenta Medusa — utilitário de automação de tentativas de login
Todas as atividades foram realizadas apenas em ambiente local, com fins educacionais, seguindo práticas de segurança e ética.
________________________________________
2. Arquitetura do Ambiente
Foram criadas duas máquinas virtuais no VirtualBox, conectadas por Host-Only Adapter para isolamento total.
Máquina	Sistema Operacional	IP	Função
VM 1	Kali Linux	192.168.56.10	Atacante / Testes
VM 2	Metasploitable 2	192.168.56.101	Alvo FTP / SMB
Testes de Conectividade
Do Kali para o Metasploitable:
ping 192.168.56.101
Respostas positivas confirmaram conectividade adequada.
________________________________________
3. Ferramenta Utilizada: Medusa
O Medusa é um login brute-force tool modular, capaz de realizar tentativas automatizadas de autenticação contra diversos serviços, como FTP, SSH, SMB, HTTP etc.
Características relevantes:
•	Execução paralela
•	Múltiplos módulos
•	Suporte a lista de usuários e listas de senhas
•	Adequado para testes educacionais em laboratório
medusa -h 192.168.56.101 -u  users.txt -P password.txt  -M ftp

4. Wordlists utilizadas
Assim como a professora em suas aulas, utilizei wordlists simples, criadas manualmente.
4.1. Wordlist de senhas (passwords.txt)
admin
password
12345
test
msfadmin
4.2. Lista de usuários (users.txt)
admin
user
msfadmin
service
As listas foram criadas manualmente e servem apenas para demonstrar o comportamento dos serviços vulneráveis.
________________________________________
5. Cenário 1 – Teste de Força Bruta em FTP (Metasploitable 2)
5.1. Objetivo
Simular tentativas repetidas de autenticação no serviço FTP da máquina Metasploitable 2.
5.2. Identificação do serviço
Foi realizada uma varredura de portas (sem explorar nada), apenas para confirmar quais portas estavam abertas.
A porta FTP (21/TCP) estava ativa, o que motivou o teste.
5.3. Execução do teste
No Kali Linux, foi utilizado o módulo FTP do Medusa, apontando para o IP do Metasploitable.
Configuração do alvo FTP:
•	IP: 192.168.56.101
•	Usuário demonstrativo: msfadmin
Comando utilizado (nível conceitual/seguro):
medusa -h 192.168.56.101 -u msfadmin -P passwords.txt -M ftp
5.4. Resultado observado
•	O serviço FTP não impôs bloqueio por tentativas consecutivas.
•	A autenticação foi possível utilizando credenciais fracas fornecidas dentro da wordlist simples.
•	Sinal claro de falta de políticas de segurança.
6. Cenário 2 – Automação de Login Web via DVWA (Nível Low)
6.1. Objetivo
Compreender como um formulário de login vulnerável responde a tentativas automatizadas.
6.2. Configuração
•	DVWA rodando no IP 192.168.56.102
•	Nível de segurança ajustado para Low
•	Sem rate limiting
•	Sem proteção por CAPTCHA
6.3. Passos Realizados
1.	Acessado o formulário de login pelo navegador.
2.	Analisado o envio da requisição POST.
3.	Criado um script básico para automatizar tentativas de login (sem divulgar técnicas ofensivas).
4.	Utilizada wordlist simples para fins demonstrativos.
Resultado
•	DVWA aceitou múltiplas tentativas sem restrição.
•	A ausência de mecanismos de segurança facilita automação não autorizada.
•	O experimento demonstrou como sistemas mal configurados podem ser explorados.
________________________________________
7. Cenário 3 – Password Spraying em SMB
7.1. Objetivo
Demonstrar password spraying:
→ testar uma senha contra vários usuários.
7.2. Configuração
Serviço SMB ativo no Metasploitable 2.
•	IP alvo: 192.168.56.101
•	Senha testada: "password"
•	Lista de usuários: users.txt
7.3. Execução
Utilizado o módulo SMB do Medusa (alto nível):
medusa -h 192.168.56.101 -U users.txt -p password -M smbnt
7.4. Resultados
•	Um dos usuários retornou acesso com senha extremamente fraca.
•	O serviço SMB não aplicou bloqueios após tentativas falhas.
•	Falhas de segurança típicas de ambientes inseguros.

8. Tabelas Resumo dos Testes
8.1. Resumo Geral
Serviço	Usuário(s)	Senhas	Resultado		       Observações
FTP	msfadmin	Wordlist simples	  Sucesso		Senha fraca / Sem lockout
DVWA	admin	Wordlist simples	   Sucesso		Falta de CAPTCHA e rate limit
SMB	lista de usuários	Senha única	  1 sucesso		 Password spraying facilitado
________________________________________
9. Medidas de Mitigação
9.1. Para FTP
•	Substituir FTP por SFTP (canal criptografado)
•	Implementar política de bloqueio após X tentativas
•	Utilizar fail2ban
•	Aplicar política de senhas fortes
9.2. Para Aplicações Web
•	Implementar CAPTCHA em formulários
•	Aplicar rate limiting em tentativas
•	Exigir senha forte
•	Adicionar autenticação multifator

9.3. Para SMB
•	Desabilitar SMB
•	Restringir acesso via firewall
•	Logar e monitorar tentativas de login
•	Usar política de expiração e bloqueio de contas

10. Considerações Finais / Reflexão
A realização desse projeto permitiu compreender como ferramentas de automação, como o Medusa, exploram falhas de autenticação em serviços desprotegidos. A principal lição é que senhas fracas, combinadas com ausência de mecanismos de defesa, tornam sistemas extremamente vulneráveis.
Importante reforçar que:
•	Os testes foram controlados e éticos
•	O aprendizado deve ser aplicado para fortalecer ambientes reais
•	Segurança começa com configuração adequada, monitoramento e políticas robustas

