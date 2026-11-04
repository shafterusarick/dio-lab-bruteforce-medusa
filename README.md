# dio-lab-bruteforce-medusa
Desafio Do Bootcamp Santander - Cibersegurança do Zero a prática, aonde abordamos os conceitos apresentados de Kali Linux, Medusa no Metasploitable 2 e DVWA para simular cenários de ataque de força bruta e exercitar medidas de prevenção.

## Objetivo

Simular ataques a credenciais em um ambiente virtual controlado, utilizando Kali Linux, Medusa, Metasploitable 2 e DVWA (*Damn Vulnerable Web Application*).

O laboratório tem como objetivos:

- Identificar serviços de rede e suas versões.
- Compreender técnicas de força bruta e password spraying.
- Realizar testes de autenticação em FTP, SMB e formulários web.
- Analisar os resultados obtidos.
- Propor medidas para prevenir ataques a credenciais.

## Ambiente de laboratório

- **Sistema de testes:** Kali Linux
- **Máquina vulnerável:** Metasploitable 2
- **Aplicação web vulnerável:** DVWA
- **Ferramentas utilizadas:** Nmap, Medusa, enum4linux e smbclient
- **Rede:** ambiente virtual de laboratório

Todos os testes foram realizados em máquinas destinadas ao aprendizado, sem envolver sistemas de terceiros.

### Simulação de força bruta em formulário web — DVWA

O DVWA (*Damn Vulnerable Web Application*) foi acessado pelo navegador do Kali Linux, utilizando a máquina Metasploitable 2 como servidor.

Foi utilizado o Medusa para realizar uma simulação de tentativas de autenticação com combinações de usuários e senhas no ambiente controlado.

**Resultado:** o Medusa identificou credenciais válidas durante a simulação de força bruta no formulário web do DVWA. A combinação encontrada foi utilizada para autenticar com sucesso na conta de administrador da aplicação.

O experimento demonstrou como credenciais descobertas por tentativas automatizadas podem permitir acesso a uma conta, dependendo das proteções de autenticação existentes.

**Evidência:** acesso confirmado durante o laboratório, sem captura de tela registrada.



## Testes realizados

### 1. Reconhecimento e identificação de serviços com Nmap

Inicialmente, utilizei o Nmap para identificar serviços disponíveis na máquina Metasploitable 2, localizada no endereço `192.168.251.31`, dentro da rede virtual do laboratório.

**Comando utilizado:**

```bash
nmap -sV -p 21,22,80,445,139 192.168.251.31
```

**Explicação dos parâmetros:**

- `-sV`: tenta identificar os serviços e suas respectivas versões.
- `-p`: especifica as portas que serão verificadas.
- `192.168.251.31`: endereço IP da máquina Metasploitable 2.

**Resultados obtidos:**

| Porta | Serviço | Software identificado |
|---|---|---|
| 21 | FTP | vsftpd 2.3.4 |
| 22 | SSH | OpenSSH 4.7p1 |
| 80 | HTTP | Apache httpd 2.2.8 |
| 139 | SMB | Samba smbd 3.X–4.X |
| 445 | SMB | Samba smbd 3.X–4.X |

**Análise:**

O escaneamento identificou serviços acessíveis que poderiam ser investigados nas etapas seguintes.

A identificação dos serviços FTP e SMB foi especialmente importante para o laboratório, pois esses protocolos foram utilizados nos testes posteriores de autenticação e enumeração.

As versões identificadas também permitem pesquisar possíveis vulnerabilidades conhecidas, embora a presença de uma versão específica não comprove, isoladamente, que o sistema seja vulnerável.

**Resultado:** identificação dos principais serviços de rede utilizados durante o laboratório.


### 2. Teste de autenticação no serviço FTP

Após identificar a porta 21 aberta durante o escaneamento com Nmap, realizei um teste de autenticação manual no serviço FTP do Metasploitable 2.

O objetivo foi verificar a possibilidade de estabelecer uma conexão e autenticar um usuário no servidor.

**Comando utilizado:**

```bash
ftp 192.168.251.31
```

**Procedimento:**

1. Estabeleci uma conexão com o serviço FTP pela porta 21.
2. Informei o usuário `msfadmin`.
3. Digitei a senha de treinamento configurada no Metasploitable 2.
4. O servidor confirmou a autenticação.

**Resultado observado:**

```text
220 (vsFTPd 2.3.4)
230 Login successful.
Remote system type is UNIX.
```

A mensagem `230 Login successful` confirmou que as credenciais fornecidas foram aceitas pelo servidor FTP.

**Análise de segurança:**

O FTP tradicional não criptografa as credenciais transmitidas durante a autenticação, representando um risco em redes não confiáveis.

Em ambientes profissionais, alternativas como SFTP ou FTPS devem ser consideradas, juntamente com controles de acesso e políticas de senhas adequadas.

**Evidência:** autenticação realizada com sucesso no ambiente de laboratório. <img width="513" height="158" alt="image" src="https://github.com/user-attachments/assets/cc524ea1-2d7c-456f-a320-36290048b051" />

