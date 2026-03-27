1. Contexto:

O objetivo deste teste foi simular a criação de um mecanismo de persistência em sistema Linux, representando ações realizadas por um atacante após obter acesso inicial ao ambiente.

Foi criado um script malicioso no servidor alvo e configurado um agendamento automático via cron, garantindo a execução contínua do código mesmo após reinicializações do sistema.

Durante o teste, foi avaliada a capacidade de detecção do ambiente utilizando o Wazuh, com foco em:
•Monitoramento de integridade de arquivos 
•Execução de comandos com privilégio elevado
•Criação de novos arquivos no sistema

2. Topologia:

O laboratório é composto por:
• Kali Linux (atacante na rede externa)
• pfSense (firewall com Suricata em modo IDS)
• Ubuntu Server (alvo na rede interna com Wazuh Agent)
• Ubuntu Server (SIEM com Wazuh Manager)
• Windows 10 (acesso aos dashboards)
Diagrama da topologia:

Arquitetura/Topologia do Laboratório.png

3. Execução do Ataque:

Inicialmente foi estabelecida conexão SSH com o servidor alvo:

ssh usuario@192.168.X.X

Criação do script malicioso:

sudo nano /opt/backdoor.sh

Conteúdo do script:

#!/bin/bash
echo "Persistencia ativa em $(date)" >> /tmp/persistencia.log

Permissão de execução:

sudo chmod +x /opt/backdoor.sh

Criação da persistência via cron:

sudo nano /etc/cron.d/backdoor

Conteúdo do cron:

* * * * * root /opt/backdoor.sh

Esse agendamento executa o script a cada minuto com privilégios de root.

4. Resultado:

Após a criação da persistência, foi possível verificar a execução contínua do script através do arquivo de log:

cat /tmp/persistencia.log

Resultado:

Persistencia ativa Thu Feb 12 14:12:01
Persistencia ativa Thu Feb 12 14:13:01
Persistencia ativa Thu Feb 12 14:14:01
Persistencia ativa Thu Feb 12 14:15:01
Persistencia ativa Thu Feb 12 14:16:01

Isso confirma que o código malicioso estava sendo executado automaticamente.
Após a coleta das evidências, a persistência foi removida:

sudo rm /etc/cron.d/backdoor
sudo rm /opt/backdoor.sh
sudo rm /tmp/persistencia.log

Verificação:

ls /etc/cron.d

Resultado:

e2scrub_all sysstat

5. Detecção:

O Wazuh registrou diversos eventos durante a execução do teste.

Criação de arquivo no sistema:
Rule ID : 554

File added to the system

Indica criação do script malicioso ou arquivo de persistência.

Alteração de integridade de arquivos 
Rule ID: 550

Integrity checksum changed

Detectou modificações em arquivos monitorados, incluindo:
• /opt/backdoor.sh
• /etc/cron.d/backdoor

Uso de privilégios administrativos
Rule ID: 5402

Successful sudo to ROOT executed

Sessões de autenticação
Rule IDs:
• 5501 → Login session opened
• 5502 → Login session closed

Autenticação SSH bem-sucedida
Rule ID: 5715

sshd: authentication success

6. Análise Técnica:

O ataque simulou uma técnica clássica de persistência em ambientes Linux através do uso de cron jobs maliciosos.

O atacante:
1.Obteve acesso via SSH (credenciais válidas)
2.Criou um script malicioso
3.Concedeu permissão de execução
4.Configurou execução automática via cron
5.Garantiu execução contínua com privilégios de root

O Wazuh detectou:
• criação de arquivos
• alterações de integridade
• uso de sudo
• sessões de login

Esse tipo de persistência é comum porque:
• é simples de implementar
• executa automaticamente
• pode passar despercebido sem monitoramento adequado

7. Mapeamento MITRE ATT&CK

Táticas:
• TA0003 – Persistence
• TA0005 – Defense Evasion
• TA0002 – Execution

Técnicas:
• T1053.003 – Scheduled Task/Job: Cron
• T1059 – Command and Scripting Interpreter
• T1078 – Valid Accounts

9. Conclusão:

O teste demonstrou a criação de um mecanismo de persistência funcional em ambiente Linux utilizando cron jobs.

O Wazuh foi capaz de detectar:
• criação de arquivos suspeitos
• alterações de integridade
• execução de comandos com privilégio elevado

A execução contínua do script confirma a eficácia da técnica de persistência.
O teste reforça a importância do monitoramento de integridade de arquivos e da análise de eventos de sistema para identificação de atividades maliciosas após comprometimento.