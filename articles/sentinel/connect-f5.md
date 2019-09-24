---
title: Connettere i dati F5 ad Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati F5 a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 64ea16b6e5a2821db4f053928e4b95ba80d177dd
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240019"
---
# <a name="connect-your-f5-appliance"></a>Connettere il dispositivo F5



È possibile connettere Azure Sentinel a qualsiasi Appliance F5 salvando i file di log come syslog CEF. L'integrazione con Sentinel di Azure consente di eseguire facilmente analisi e query nei dati dei file di log da F5. Per altre informazioni sul modo in cui Azure Sentinel inserisce i dati CEF, vedere [connettere le appliance CEF](connect-common-event-format.md).

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="step-1-connect-your-f5-appliance-using-an-agent"></a>Passaggio 1: Connettere l'appliance F5 usando un agente

Per connettere il dispositivo F5 a Sentinel di Azure, è necessario distribuire un agente in un computer dedicato (VM o locale) per supportare la comunicazione tra il dispositivo e la sentinella di Azure.

In alternativa, è possibile distribuire l'agente manualmente in una macchina virtuale di Azure esistente, in una macchina virtuale in un altro cloud o in un computer locale.

> [!NOTE]
> Assicurarsi di configurare la sicurezza del computer in base ai criteri di sicurezza dell'organizzazione. Ad esempio, è possibile configurare la rete in modo che venga allineata ai criteri di sicurezza della rete aziendale e modificare le porte e i protocolli nel daemon per allinearli ai propri requisiti. 

Per visualizzare un diagramma di rete di entrambe le opzioni, vedere [Connect Data Sources](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent"></a>Distribuire l'agente 

1. Nel portale di Azure Sentinel fare clic su **connettori dati** , quindi premere **F5** e quindi aprire la **pagina del connettore**. 

1. In **scaricare e installare l'agente syslog**selezionare il tipo di computer, ovvero Azure o in locale. 
1. Nella schermata **macchine virtuali** visualizzata selezionare il computer che si vuole usare e fare clic su **Connetti**.
1. Se si sceglie **scaricare e installare l'agente per le macchine virtuali Linux di Azure**, selezionare il computer e fare clic su **Connetti**. Se si è scelto di **scaricare e installare l'agente per le macchine virtuali Linux non di Azure**, nella schermata **Direct Agent** eseguire lo script in **download and onboarding Agent for Linux**.
1. Nella schermata connettore, in **Configura e in poi syslog**, impostare se il daemon syslog è **rsyslog. d** o **syslog-ng**. 
1. Copiare questi comandi ed eseguirli nel dispositivo:
    - Se è stato selezionato rsyslog. d:
              
      1. Indicare al daemon syslog di restare in ascolto sulla funzionalità local_4 e inviare i messaggi syslog all'agente Sentinel di Azure usando la porta 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
      2. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) per configurare l'agente syslog in modo che sia in ascolto sulla porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Dove {0} deve essere sostituito con il GUID dell'area di lavoro.
            
      1. Riavviare il daemon syslog`sudo service rsyslog restart`
             
    - Se è stato selezionato syslog-ng:

      1. Indicare al daemon syslog di restare in ascolto sulla funzionalità local_4 e inviare i messaggi syslog all'agente Sentinel di Azure usando la porta 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      2. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) per configurare l'agente syslog in modo che sia in ascolto sulla porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Dove {0} deve essere sostituito con il GUID dell'area di lavoro.

      3. Riavviare il daemon syslog`sudo service syslog-ng restart`
 1. Riavviare l'agente syslog utilizzando questo comando:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
 1. Verificare che non siano presenti errori nel log dell'agente eseguendo questo comando:`tail /var/opt/microsoft/omsagent/log/omsagent.log`


## <a name="step-2-forward-f5-logs-to-the-syslog-agent"></a>Passaggio 2: Inviare i log F5 all'agente syslog

Configurare F5 per l'invio dei messaggi syslog in formato CEF all'area di lavoro di Azure tramite l'agente syslog:

Passare a F5 [configurazione della registrazione degli eventi di sicurezza dell'applicazione](https://aka.ms/asi-syslog-f5-forwarding)e seguire le istruzioni per configurare la registrazione remota, usando le linee guida seguenti:
  - Impostare il **tipo di archiviazione remota** su **CEF**.
  - Impostare il **protocollo** su **UDP**.
  - Impostare l' **indirizzo IP** sull'indirizzo IP del server syslog.
  - Impostare il **numero di porta** su **514**o la porta impostata per l'uso da parte dell'agente.
  - Impostare la **funzionalità** su quella impostata nell'agente syslog (per impostazione predefinita, l'agente lo imposta su **local4**).
  - È possibile impostare le **dimensioni massime della stringa di query** sulle dimensioni impostate nell'agente.

## <a name="step-3-validate-connectivity"></a>Passaggio 3: Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 

1. Assicurarsi di usare la struttura corretta. La funzionalità deve essere identica nel dispositivo e in Sentinel di Azure. È possibile controllare quale file di struttura si sta usando in Sentinel di Azure e modificarlo nel `security-config-omsagent.conf`file. 

2. Assicurarsi che i log vengano visualizzati sulla porta corretta nell'agente syslog. Eseguire questo comando nel computer agente syslog: `tcpdump -A -ni any  port 514 -vv`Questo comando Mostra i log che inviano flussi dal dispositivo al computer syslog. Assicurarsi che i log vengano ricevuti dall'appliance di origine sulla porta destra e sulla struttura a destra.

3. Assicurarsi che i log inviati siano conformi allo [standard RFC 3164](https://tools.ietf.org/html/rfc3164).

4. Nel computer che esegue l'agente syslog verificare che le porte 514, 25226 siano aperte e in ascolto, usando il comando `netstat -a -n:`. Per altre informazioni sull'uso di questo comando, vedere [netstat (8)-Linux man page](https://linux.die.net/man/8/netstat). Se è in ascolto corretto, si noterà quanto segue:

   ![Porte di Azure Sentinel](./media/connect-cef/ports.png) 

5. Verificare che il daemon sia impostato per l'ascolto sulla porta 514, in cui si stanno inviando i log.
    - Per rsyslog:<br>Verificare che il file `/etc/rsyslog.conf` includa questa configurazione:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Per ulteriori informazioni, vedere [imudp: Modulo](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) di input syslog UDP [e imtcp: Modulo di input syslog TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Per syslog-ng:<br>Verificare che il file `/etc/syslog-ng/syslog-ng.conf` includa questa configurazione:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Per ulteriori informazioni, vedere la [Guida all'amministrazione di syslog-ng Open Source 3,16-](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Verificare la comunicazione tra il daemon syslog e l'agente. Eseguire questo comando nel computer agente syslog: `tcpdump -A -ni any  port 25226 -vv`Questo comando Mostra i log che inviano flussi dal dispositivo al computer syslog. Assicurarsi che i log vengano ricevuti anche sull'agente.

6. Se entrambi i comandi forniscono risultati riusciti, controllare Log Analytics per verificare se i log sono in arrivo. Tutti gli eventi trasmessi da queste appliance vengono visualizzati in forma non elaborata `CommonSecurityLog` in log Analytics di tipo.

7. Per verificare se sono presenti errori o se i log non sono in arrivo, `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`cercare in. Se si verificano errori di mancata corrispondenza del formato del log `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` , passare al file `security_events.conf`e verificare che i log corrispondano al formato Regex visualizzato in questo file.

8. Assicurarsi che le dimensioni predefinite del messaggio syslog siano limitate a 2048 byte (2 KB). Se i log sono troppo lunghi, aggiornare security_events. conf utilizzando questo comando:`message_length_limit 4096`



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere le appliance F5 ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

