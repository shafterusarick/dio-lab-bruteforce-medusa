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
