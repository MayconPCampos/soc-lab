## 1. Contexto
O objetivo deste teste foi simular uma atividade de reconhecimento mais aprofundado contra um servidor interno (Ubuntu Server) localizado atrás do firewall pfSense, realizando identificação de versão de serviço e enumeração de configurações do serviço SSH.
A atividade foi originada a partir da VM Kali Linux na rede externa, com o Suricata operando em modo IDS na interface WAN.

## 2. Topologia

O laboratório é composto por:<br>

- Kali Linux (atacante na rede externa)<br>
- pfSense (firewall com Suricata em modo IDS)<br>
- Ubuntu Server (alvo na rede interna com Wazuh Agent)<br>
- Ubuntu Server (SIEM com Wazuh Manager na rede interna)<br>
- Windows 10 (acesso aos dashboards do pfSense e Wazuh)<br>

Diagrama da topologia:
[Topologia](https://github.com/MayconPCampos/soc-lab/blob/main/arquitetura%20da%20rede/Topologia%20da%20rede%20-%20laborat%C3%B3rio%20SOC.png)

## 3. Execução do ataque:

#### Primeiro comando executado no Kali linux:<br>
``nmap -sV -p 22 --version-intensity 5 192.168.1.102``

Descrição:<br>
``-sV`` → Detecção de versão do serviço<br>
``-p 22`` → Scan direcionado apenas à porta SSH<br>
``--version-intensity 5`` → Intensidade média de fingerprinting de versão<br>

Objetivo: Identificar versão exata do serviço SSH exposto.

#### Segundo comando executado no Kali linux:<br>

``nmap -p 22 --script ssh-hostkey,ssh2-enum-algos 192.168.1.102``

Descrição:<br>
``ssh-hostkey`` → Coleta das chaves públicas do servidor<br>
``ssh2-enum-algos`` → Enumeração dos algoritmos suportados pelo SSH (KEX, MAC, cifragem, etc.)<br>

Objetivo: Enumerar detalhes criptográficos e configurações do serviço SSH.

## 4. Resultado:

Como resultado do primeiro comando o Nmap identificou:

- Porta 22/tcp aberta
- Serviço: SSH
- Versão: OpenSSH 9.6p1 Ubuntu 3ubuntu13.14
- Sistema operacional identificado como Linux

Isso confirma a exposição do serviço SSH e revela versão específica do software.

No resultado do segundo comando foi possível enumerar:

- Algoritmos de troca de chave (KEX)
- Algoritmos de cifragem
- Algoritmos MAC
- Métodos de compressão
- Tipos de chaves do servidor

Essa informação permite ao atacante avaliar o uso de algoritmos fracos, possibilidade de downgrade criptográfico e vetores para exploração futura.

## 5. Detecção:

#### Primeiro comando:
Não foram identificados alertas no Suricata relacionados ao version scan.<br>

Possível explicação:<br>
O tráfego gerado foi compatível com handshake legítimo SSH, não houve padrão de varredura em múltiplas portas ou
a regra ET SCAN não foi acionada por baixo volume ou perfil comportamental.

#### Segundo comando:
Foi identificado o seguinte alerta no Suricata (interface WAN):<br>

- ``ET SCAN Potential SSH Scan``
- ``Classification: Attempted Information Leak``
- ``Priority: 2``
- ``192.168.240.132 -> 192.168.1.102:22``

Esse alerta indica tentativa de coleta de informações detalhadas do serviço SSH.

## 6. Análise Técnica:

O primeiro comando caracterizou-se como detecção ativa de versão (service fingerprinting), que não gerou alerta no IDS possivelmente por se assemelhar a tráfego legítimo de negociação SSH.

O segundo comando executou scripts NSE voltados à enumeração detalhada do serviço, gerando padrões de requisição adicionais que acionaram regra da categoria ET SCAN.

A atividade caracteriza:
- Reconhecimento direcionado
- Enumeração ativa de serviço
- Coleta de informações técnicas para potencial exploração futura

Não houve tentativa de autenticação ou exploração identificada.

## 7. Mapeamento MITRE ATT&CK
- TA0043 – Reconhecimento
- T1046 – Network Service Scanning
- T1595 – Active Scanning
- T1082 – System Information Discovery (parcial, via fingerprint de serviço)

## 8. Conclusão:

O teste demonstrou que atividades de enumeração de serviço podem ou não ser detectadas pelo IDS dependendo do padrão de tráfego gerado.
O version scan isolado não gerou alerta, enquanto a execução de scripts NSE específicos para enumeração SSH acionou regra da categoria ET SCAN classificada como “Attempted Information Leak”.
