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



### 3. Enumeração SMB com enum4linux e smbclient

Após identificar as portas 139 e 445 abertas no Metasploitable 2, realizei uma enumeração do serviço SMB para coletar informações sobre o ambiente.

O SMB (*Server Message Block*) é um protocolo utilizado para compartilhamento de arquivos, impressoras e outros recursos de rede.

#### 3.1. Enumeração com enum4linux

**Comando utilizado:**

```bash
enum4linux -a 192.168.251.31 | tee enum4_output.txt
```

**Explicação:**

- `enum4linux`: ferramenta utilizada para enumerar informações de serviços SMB.
- `-a`: executa várias verificações de enumeração disponíveis na ferramenta.
- `192.168.251.31`: IP do Metasploitable 2.
- `| tee enum4_output.txt`: mostra a saída no terminal e também a salva em um arquivo de texto.

**Resultados observados:**

A ferramenta identificou informações do ambiente, incluindo:

- Nome da máquina: `METASPLOITABLE`.
- Grupo de trabalho: `WORKGROUP`.
- Informações relacionadas ao serviço SMB.

A saída completa foi registrada no arquivo `enum4_output.txt`.

#### 3.2. Enumeração com smbclient

**Comando utilizado:**

```bash
smbclient -L //192.168.251.31 -U msfadmin
```

O parâmetro `-L` solicita a listagem dos compartilhamentos disponíveis no servidor.

O parâmetro `-U` especifica o usuário utilizado na autenticação.

**Resultados observados:**

Durante o teste, foram identificados os seguintes compartilhamentos:

| Compartilhamento | Descrição |
|---|---|
| `print$` | Drivers de impressora |
| `tmp` | Compartilhamento temporário |
| `opt` | Compartilhamento denominado opt |
| `msfadmin` | Diretório pessoal do usuário de treinamento |
| `IPC$` | Recurso de comunicação entre processos |
| `ADMIN$` | Compartilhamento administrativo anunciado pelo servidor |

A listagem demonstrou que o servidor disponibilizava informações sobre seus recursos SMB.

#### 3.3. Análise de segurança

A enumeração SMB permite identificar informações sobre máquinas, grupos de trabalho, usuários e compartilhamentos, dependendo das configurações e permissões do servidor.

Em ambientes corporativos, configurações inadequadas podem expor informações desnecessárias ou permitir acessos indevidos.

**Medidas de prevenção recomendadas:**

- Restringir o acesso ao SMB somente aos dispositivos necessários.
- Configurar corretamente as permissões dos compartilhamentos.
- Desabilitar compartilhamentos desnecessários.
- Manter o Samba e o sistema operacional atualizados.
- Monitorar tentativas de autenticação suspeitas.

**Resultado:** enumeração realizada com sucesso, permitindo identificar informações do grupo de trabalho e compartilhamentos disponíveis no Metasploitable 2.

### 4. Simulação de ataques a credenciais SMB com Medusa

Após realizar a enumeração SMB, utilizei o Medusa para simular tentativas automatizadas de autenticação no Metasploitable 2.

O objetivo foi compreender como combinações de usuários e senhas podem ser testadas em serviços de rede e identificar os riscos associados a credenciais fracas.

#### 4.1. Preparação das wordlists

Foram utilizadas listas de usuários e senhas de treinamento, armazenadas nos arquivos:

- `wordlists/smb_users.txt`
- `wordlists/senhas_spray.txt`
- `wordlists/senhas_spray2.txt`

As listas continham combinações fictícias utilizadas exclusivamente no laboratório.

#### 4.2. Execução do teste

**Comando utilizado:**

```bash
medusa -h 192.168.251.31 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -f
```

**Explicação dos parâmetros:**

- `-h`: endereço da máquina de destino.
- `-U`: arquivo contendo os nomes de usuários.
- `-P`: arquivo contendo as senhas utilizadas no teste.
- `-M smbnt`: módulo do Medusa responsável pela autenticação SMB.
- `-t 2`: limita o número de tarefas simultâneas.
- `-f`: encerra os testes contra o host após encontrar uma combinação válida.

#### 4.3. Resultado obtido

Durante a execução, o Medusa realizou tentativas de autenticação com os usuários e senhas definidos nas wordlists.

A ferramenta retornou a mensagem `ACCOUNT FOUND`, indicando que uma das combinações foi aceita pelo serviço SMB.

A credencial encontrada pertencia ao usuário de treinamento `msfadmin`, utilizado no ambiente Metasploitable 2.

**Resultado:** identificação de uma combinação válida de credenciais durante a simulação de ataque automatizado ao serviço SMB.

#### 4.4. Análise de segurança

O laboratório demonstrou como credenciais previsíveis podem ser identificadas por ferramentas que automatizam tentativas de autenticação.

A utilização de múltiplos usuários e senhas permite simular diferentes estratégias de ataque. Entretanto, o teste executado utilizou uma lista de senhas e não representa necessariamente um password spraying estrito.

O password spraying consiste, geralmente, em testar uma ou poucas senhas comuns contra vários usuários, procurando evitar um número elevado de tentativas consecutivas por conta.

**Medidas de prevenção recomendadas:**

- Utilizar senhas fortes e exclusivas.
- Implementar autenticação multifator quando suportada.
- Restringir o acesso ao serviço SMB.
- Monitorar falhas e tentativas repetidas de autenticação.
- Configurar políticas adequadas de bloqueio ou limitação de tentativas.
- Desabilitar contas e serviços desnecessários.

**Conclusão:** a simulação evidenciou os riscos associados a credenciais fracas e à exposição de serviços de autenticação sem controles adequados.

### 5. Simulação de força bruta em formulário web — DVWA
