## 1. Contexto
O objetivo deste teste foi simular um ataque de reconhecimento originado em uma rede externa (VM Kali Linux) contra um servidor interno (VM Ubuntu Server) localizado atrás do firewall pfSense, com o Suricata operando em modo IDS na interface WAN.

## 2. Topologia

O laboratório é composto por:

- Kali Linux (atacante na rede externa)<br>
- pfSense (firewall com Suricata em modo IDS)<br>
- Ubuntu Server (alvo na rede interna com Wazuh Agent)<br>
- Ubuntu Server (SIEM com Wazuh Manager na rede interna)<br>
- Windows 10 (acesso aos dashboards do pfSense e Wazuh)<br>

Diagrama da topologia:
Arquitetura/Topologia do Laboratório.png

## 3. Execução do ataque:

Comando executado no Kali Linux:<br>
``nmap -sS -T4 --top-ports 1000 192.168.1.102``

Descrição:

``-sS -> SYN Scan (half-open scan)`` -> realiza varredura sem completar o handshake TCP, reduzindo registros no host alvo

``-T4`` -> Timing agressivo, usado para facilitar a detecção no Suricata para gerar as evidências do teste.

``--top-ports`` -> varredura das 1000 portas TCP mais comuns 

#### Objetivo: 
Identificar os serviços expostos e mapear a superfície de ataque.

Evidências:
Imagens/Kali-nmap.png

## 4. Resultado:

O Nmap identificou a porta 22/tcp (ssh) como aberta e as outras 999 portas como filtered, o que indica que o firewall bloqueou ou descartou silenciosamente.

Evidências:
Imagens/kali-nmap-resultado.png

## 5. Detecção:

O Suricata com IDS habilitado monitorando a interface WAN (Ponto de entrada do tráfego externo) identificou registro de múltiplos alertas:

``ET SCAN Suspicious inbound to MySQL port 3306``<br>
``ET SCAN Suspicious inbound to MSSQL port 1433``<br>
``ET SCAN Suspicious inbound to PostgreSQL port 5432``<br>
``ET SCAN Suspicious inbound to Oracle SQL port 1521``<br>
``ET SCAN Potential VNC Scan 5800–5820``<br>

Estes alertas indicam tentativas sequenciais e com padrão de mapeamento em portas associadas a serviços sensíveis,
Como o scan foi realizado utilizando técnica half-open (SYN Scan), não houve conclusão do handshake TCP nas portas não abertas, portanto não foram gerados logs no sistema operacional do Ubuntu.

Evidências:
Imagens/suricata-alert-logs.png.

## 6. Análise técnica:
O ataque teve origem em uma rede externa e seguiu um padrão que o caracteriza como port scan, o Suricata detectou o comportamento na interface WAN antes do roteamento interno. O único serviço identificado como aberto foi o SSH (22/tcp), não houve tentativa de autenticação.

## 7. Mapeamento MITRE ATT&CK
- TA0043 – Reconhecimento<br>
- T1046 – Network Service Scanning<br>

## 8. Conclusão:
O teste se mostrou eficaz em detectar a atividade na interface WAN, o Suricata classificou os eventos como atividade de varredura com base nas regras da categoria ET SCAN, o incidente foi tratado como atividade de reconhecimento sem comprometimento do alvo interno.
