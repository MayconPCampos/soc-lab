Ataque de Port Scan – Detecção com Suricata
1. Contexto

Este teste tem como objetivo simular um ataque de reconhecimento (port scan) realizado a partir de uma máquina externa (Kali Linux) contra um host interno protegido por um firewall pfSense, avaliando a capacidade de detecção do Suricata IDS no perímetro da rede.

O cenário faz parte de um laboratório SOC criado para estudo e prática de monitoramento, detecção e resposta a incidentes de segurança.

2. Topologia do Laboratório

O laboratório é composto por:

Kali Linux (atacante externo, fora da rede do pfSense)

pfSense (firewall e IDS com Suricata)

Ubuntu Server (Wazuh Agent – alvo do ataque)

Ubuntu Server (Wazuh Manager)

Windows 10 (acesso aos dashboards)

Todo o tráfego entre o atacante e o alvo passa obrigatoriamente pelo pfSense.

3. Simulação do Ataque

Comando executado no Kali Linux:

nmap -sS -T4 --top-ports 1000 <ip_do_alvo>

Descrição do comando:

-sS: SYN Scan (half-open scan)

-T4: timing agressivo

--top-ports 1000: scan das 1000 portas mais comuns

Objetivo do ataque:
Identificar serviços expostos no host interno e mapear a superfície de ataque.

Evidência:

Execução do comando e resultado no Kali Linux (imagem: images/kali-nmap.png)

4. Resultado do Scan

O Nmap identificou:

Porta 22/tcp (SSH) como aberta

Demais portas como closed ou filtered

Mensagens de retransmissão, indicando possível interferência do firewall ou do IDS

Esse comportamento é esperado em ambientes protegidos por firewall e IDS.

5. Detecção

Ferramenta utilizada:

Suricata IDS integrado ao pfSense

Interface:

WAN (ponto de entrada do tráfego externo)

Alertas observados:

Alertas do tipo ET SCAN

Tentativas sequenciais de conexão a portas associadas a serviços sensíveis, como:

MySQL (3306)

PostgreSQL (5432)

MSSQL (1433)

Oracle (1521)

Evidências:

Alertas no dashboard do Suricata (imagem: images/suricata-wan.png)

Registros no arquivo alerts.log (imagem: images/alerts-log.png)

Observação:
Não houve geração de alertas na interface LAN, comportamento esperado, pois o Suricata inspeciona o tráfego no ponto de entrada (WAN), antes do roteamento interno.

6. Análise Técnica

O ataque teve origem externa e destino interno

O padrão de acesso sequencial a múltiplas portas caracteriza um port scan

A detecção no perímetro confirma o correto posicionamento do IDS

Não foram identificadas tentativas de autenticação ou exploração ativa

O evento foi classificado como fase de reconhecimento, sem impacto direto ao ambiente.

7. Mapeamento MITRE ATT&CK

TA0043 – Reconhecimento

T1046 – Network Service Scanning

8. Conclusão

Este teste demonstra a detecção eficaz de atividades de reconhecimento externo em um ambiente protegido por firewall e IDS. A análise contextual evita ações de bloqueio desnecessárias e prepara o ambiente para possíveis ataques subsequentes.

O incidente foi tratado como atividade suspeita, sem comprometimento do ativo.