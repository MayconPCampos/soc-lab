1. Contexto:

O objetivo deste teste foi simular atividades maliciosas realizadas após um possível comprometimento inicial do sistema, representando ações que poderiam ocorrer após o sucesso de um ataque de força bruta contra o serviço SSH.
A partir de uma conexão SSH estabelecida entre a máquina atacante (Kali Linux) e o servidor alvo (Ubuntu Server), foram realizadas alterações nas permissões de arquivos críticos do sistema (/etc/passwd e /etc/shadow).
Essas alterações simulam técnicas utilizadas por atacantes para facilitar escalonamento de privilégios ou manipulação de contas de usuário.

2. Topologia:

O laboratório é composto por:
• Kali Linux (atacante na rede externa)
• pfSense (firewall com Suricata em modo IDS)
• Ubuntu Server (alvo na rede interna com Wazuh Agent)
• Ubuntu Server (SIEM com Wazuh Manager na rede interna)
• Windows 10 (acesso aos dashboards do pfSense e Wazuh)
Diagrama da topologia:
Arquitetura/Topologia do Laboratório.png

3. Execução do Ataque:

Inicialmente foi estabelecida uma conexão SSH entre a máquina atacante e o servidor alvo.
Comando executado no Kali Linux:

ssh serveradmin@192.168.1.102

Após autenticação bem-sucedida, foram verificadas as permissões dos arquivos críticos do sistema com os seguintes comandos:

ls -l /etc/passwd
ls -l /etc/shadow

Resultado:

/etc/passwd  -rw-r--r--
/etc/shadow  -rw-r-----

Em seguida foram alteradas as permissões desses arquivos utilizando privilégios administrativos com os comandos executados:

sudo chmod 777 /etc/passwd
sudo chmod 666 /etc/shadow

Resultado após modificações:

/etc/passwd  -rwxrwxrwx
/etc/shadow  -rw-rw-rw-

Evidências:
Imagens.png

4. Resultado:

As permissões dos arquivos críticos foram alteradas com sucesso durante a sessão SSH autenticada. Essas alterações simulam um cenário em que um atacante, após obter acesso ao sistema, modifica arquivos sensíveis para:
•facilitar persistência
•manipular contas de usuários
•permitir acesso não autorizado ao conteúdo de hashes de senha

Após a execução do teste, as permissões foram restauradas para evitar impactos permanentes no ambiente de laboratório.

Evidências:
Imagens/alteracao-passwd-shadow.png

5. Detecção:

Durante a execução do teste foram registrados eventos no Wazuh Dashboard relacionados à autenticação SSH, uso de privilégios sudo e alteração de integridade de arquivos.

Principais eventos identificados:

Autenticação SSH bem-sucedida
Rule ID 5715: sshd: authentication success

Uso de privilégios administrativos
Rule ID 5402: Successful sudo to ROOT executed

Sessões PAM
Rule ID 5501:
PAM: Login session opened
Rule ID 5502:
PAM: Login session closed

Alteração de integridade de arquivos
Rule ID 550:
Integrity checksum changed

6. Análise Técnica:

Após a autenticação SSH bem-sucedida, o usuário com privilégios administrativos executou comandos sudo para alterar permissões de arquivos críticos do sistema.
Os arquivos /etc/passwd e /etc/shadow são essenciais para o gerenciamento de usuários e credenciais no Linux. Alterações nas permissões desses arquivos podem permitir que usuários não autorizados visualizem ou modifiquem informações sensíveis.
O Wazuh detectou as alterações através do mecanismo de File Integrity Monitoring, que monitora mudanças em arquivos configurados como críticos.
A sequência de eventos observada foi:
•Autenticação SSH bem-sucedida
•Elevação de privilégios via sudo
•Modificação de arquivos sensíveis
•Detecção de alteração de integridade

Esse encadeamento de eventos demonstra um possível cenário de comprometimento seguido por manipulação do sistema.

9. Conclusão:

O teste demonstrou a eficácia do Wazuh na detecção de modificações em arquivos críticos do sistema através do mecanismo de monitoramento de integridade de arquivos.
A sequência de eventos registrada no SIEM permitiu identificar:

•autenticação SSH bem-sucedida
•execução de comandos com privilégios administrativos
•alteração de arquivos sensíveis do sistema

O monitoramento de integridade de arquivos mostrou-se eficaz para detectar modificações potencialmente maliciosas em componentes críticos do sistema.