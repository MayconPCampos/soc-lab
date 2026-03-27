1. Contexto
O objetivo deste teste foi simular um ataque de força bruta contra o serviço SSH de um servidor interno (Ubuntu Server) localizado atrás do firewall pfSense.

O ataque foi executado a partir de uma máquina Kali Linux localizada na rede externa, utilizando a ferramenta Hydra para realizar múltiplas tentativas de autenticação com diferentes combinações de usuários e senhas.

Durante o teste foram analisados os mecanismos de detecção do ambiente, incluindo:
-Suricata (IDS de rede)
-Logs do sistema operacional (auth.log)
-Wazuh SIEM

2. Topologia

O laboratório é composto por:
• Kali Linux (atacante na rede externa)
• pfSense (firewall com Suricata em modo IDS)
• Ubuntu Server (alvo na rede interna com Wazuh Agent)
• Ubuntu Server (SIEM com Wazuh Manager na rede interna)
• Windows 10 (acesso aos dashboards do pfSense e Wazuh)

Diagrama da topologia:
Arquitetura/Topologia do Laboratório.png

3. Execução do Ataque

Comando executado no Kali Linux:

hydra -L users.txt -P passwords.txt ssh://192.168.1.102 -t 4

Descrição dos parâmetros:
-L users.txt → lista de usuários utilizados no ataque
-P passwords.txt → lista de senhas testadas
ssh://192.168.1.102 → serviço alvo
-t 4 → número de conexões paralelas

Objetivo:
Realizar múltiplas tentativas de autenticação SSH utilizando combinações de usuários e senhas para tentar obter acesso ao servidor.

4. Resultado

O Hydra realizou tentativas de autenticação
E obteve o seguinte resultado final:

0 valid password found

.
Apesar de nenhuma credencial válida ter sido encontrada durante o ataque, as múltiplas tentativas de autenticação foram registradas no sistema alvo.

Evidências:
Imagens/hydra-bruteforce.png

5. Detecção:

•Detecção no Suricata (IDS)
Durante o ataque foram gerados alertas no arquivo alerts.log da interface WAN.

Alertas identificados:

ET SCAN Potential SSH Scan
Classification: Attempted Information Leak
Priority: 2

ET SCAN LibSSH Based Frequent SSH Connections Likely BruteForce Attack
Classification: Attempted Administrator Privilege Gain
Priority: 1

Esse segundo alerta indica comportamento consistente com ataque de brute force SSH, caracterizado por múltiplas conexões SSH em curto intervalo de tempo.

•Detecção no Sistema Operacional

No auth.log do Ubuntu Server foram registradas diversas falhas de autenticação SSH, incluindo:

-authentication failed
-PAM: User login failed
-Attempt to login using a non-existent user
-Multiple failed logins in a small period of time

Esses eventos são característicos de ataques de força bruta.

•Detecção no Wazuh SIEM

O Wazuh correlacionou os eventos do sistema e gerou múltiplos alertas no dashboard.
Principais regras acionadas:

Rule ID / Descrição /Level
5710 / Attempt to login using a non-existent user / 5

5503/PAM: User login failed/5

5551/Multiple failed logins in a short period/10

2502/User missed the password more than one time/10

5712/SSH brute force attempt/10

A regra 5712 classificou explicitamente o evento como brute force attack.

6. Análise Técnica:

O ataque utilizou a ferramenta Hydra para realizar múltiplas tentativas de autenticação SSH utilizando listas de usuários e senhas, durante o ataque foram observadas três camadas de detecção:

1️⃣ IDS de rede (Suricata)
Detectou padrões de conexões frequentes SSH e classificou como possível brute force.

2️⃣ Logs do sistema operacional
Registraram todas as falhas de autenticação no serviço SSH.

3️⃣ SIEM (Wazuh)
Correlacionou os eventos e gerou alertas de severidade mais alta ao identificar múltiplas falhas de login em curto intervalo.

7. Mapeamento MITRE ATT&CK:

• TA0006 – Credential Access
Técnicas relacionadas:
• T1110 – Brute Force
• T1110.001 – Password Guessing

9. Conclusão
O teste demonstrou a eficácia do ambiente de monitoramento na detecção de ataques de força bruta contra o serviço SSH.
O Suricata identificou padrões de múltiplas conexões SSH suspeitas, enquanto o sistema operacional registrou as falhas de autenticação.
O Wazuh correlacionou os eventos e classificou o comportamento como possível ataque de brute force, gerando alertas de maior severidade.
Apesar do grande número de tentativas, o ataque não resultou em comprometimento do sistema.