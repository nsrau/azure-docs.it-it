---
title: Connettere i dati CEF ad Anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati CEF a Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: e74dd54403ed599aa95e8fc8a94c2bd7a3ca41d8
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719117"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Connettere la soluzione esterna usando il formato di evento comune

È possibile connettere Azure Sentinel con una soluzione esterna che consente di salvare i file di log in syslog. Se il dispositivo consente di salvare i log come registro CEF (Common Event Format) syslog, l'integrazione con Sentinel di Azure consente di eseguire facilmente analisi e query tra i dati.

> [!NOTE] 
> I dati vengono archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="how-it-works"></a>Come funziona

La connessione tra Sentinel di Azure e l'appliance CEF si verifica in tre passaggi:

1. Nel dispositivo è necessario impostare questi valori in modo che l'appliance invii i log necessari nel formato necessario all'agente syslog di Azure Sentinel, in base al Microsoft Monitoring Agent. È possibile modificare questi parametri nel dispositivo, purché vengano modificati anche nel daemon syslog nell'agente Sentinel di Azure.
    - Protocollo = UDP
    - Porta = 514
    - Funzione = Local4
    - Formato = CEF
2. L'agente syslog raccoglie i dati e li invia in modo sicuro a Log Analytics, in cui vengono analizzati e arricchiti.
3. L'agente archivia i dati in un'area di lavoro di Log Analytics in modo che sia possibile eseguire query in base alle esigenze, usando le analisi, le regole di correlazione e i dashboard.

> [!NOTE]
> L'agente può raccogliere log da più origini, ma deve essere installato nel computer dedicato.


 ![CEF in Azure](./media/connect-cef/cef-syslog-azure.png)

In alternativa, è possibile distribuire l'agente manualmente in una macchina virtuale di Azure esistente, in una macchina virtuale in un altro cloud o in un computer locale. 

 ![CEF in locale](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Assicurarsi di configurare la sicurezza del computer in base ai criteri di sicurezza dell'organizzazione. Ad esempio, è possibile configurare la rete in modo che venga allineata ai criteri di sicurezza della rete aziendale e modificare le porte e i protocolli nel daemon per allinearli ai propri requisiti. È possibile usare le istruzioni seguenti per migliorare la configurazione della sicurezza del computer:  [Secure VM in Azure](../virtual-machines/linux/security-policy.md), procedure consigliate [per la sicurezza di rete](../security/fundamentals/network-best-practices.md).

Per usare la comunicazione TLS tra la soluzione di sicurezza e il computer syslog, è necessario configurare il daemon syslog (rsyslog o syslog-ng) per la comunicazione in TLS: [Crittografia del traffico syslog con TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [crittografia dei messaggi di log con TLS-syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).


## <a name="step-1-configure-your-syslog-vm"></a>Passaggio 1: Configurare la macchina virtuale syslog

È necessario distribuire un agente in un computer Linux dedicato (VM o locale) per supportare la comunicazione tra il dispositivo e la sentinella di Azure. 

> [!NOTE]
> Assicurarsi di configurare la sicurezza del computer in base ai criteri di sicurezza dell'organizzazione. Ad esempio, è possibile configurare la rete in modo che venga allineata ai criteri di sicurezza della rete aziendale e modificare le porte e i protocolli nel daemon per allinearli ai propri requisiti. 


1. Nel portale di Azure Sentinel fare clic su **connettori dati** e selezionare **formato evento comune (CEF)** , quindi **aprire la pagina del connettore**. 

1. In **scaricare e installare l'agente syslog**selezionare il tipo di computer, ovvero Azure o in locale. 
1. Nella schermata **macchine virtuali** visualizzata selezionare il computer che si vuole usare e fare clic su **Connetti**.
1. Se si sceglie **scaricare e installare l'agente per le macchine virtuali Linux di Azure**, selezionare il computer e fare clic su **Connetti**. Se si è scelto di **scaricare e installare l'agente per le macchine virtuali Linux non di Azure**, nella schermata **Direct Agent** eseguire lo script in **download and onboarding Agent for Linux**.
1. Nella schermata del connettore CEF, in **Configura e in poi syslog**, impostare se il daemon syslog è **rsyslog. d** o **syslog-ng**. 
1. Copiare questi comandi ed eseguirli nel dispositivo:
    - Se è stato selezionato rsyslog. d:
              
       1. Indicare al daemon syslog di restare in ascolto sulla funzionalità local_4 e inviare i messaggi syslog all'agente Sentinel di Azure usando la porta 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) per configurare l'agente syslog in modo che sia in ascolto sulla porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Dove {0} deve essere sostituito con il GUID dell'area di lavoro.
            
       1. Riavviare il daemon syslog`sudo service rsyslog restart`<br> Per ulteriori informazioni, vedere la [documentazione di rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
    - Se è stato selezionato syslog-ng:
       1. Indicare al daemon syslog di restare in ascolto sulla funzionalità local_4 e inviare i messaggi syslog all'agente Sentinel di Azure usando la porta 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) per configurare l'agente syslog in modo che sia in ascolto sulla porta 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Dove {0} deve essere sostituito con il GUID dell'area di lavoro.

        3. Riavviare il daemon syslog`sudo service syslog-ng restart` <br>Per ulteriori informazioni, vedere la [documentazione di syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
1. Riavviare l'agente syslog utilizzando questo comando:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Verificare che non siano presenti errori nel log dell'agente eseguendo questo comando:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

Per utilizzare lo schema pertinente in Log Analytics per gli eventi CEF, cercare `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Passaggio 2: Inoltri i log CEF (Common Event Format) all'agente syslog

Impostare la soluzione di sicurezza per l'invio di messaggi syslog in formato CEF all'agente syslog. Assicurarsi di usare gli stessi parametri visualizzati nella configurazione dell'agente. Si tratta in genere di:

- Porta 514
- Local4 di struttura

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

8. Assicurarsi che le dimensioni predefinite del messaggio syslog siano limitate a 2048 byte (2 KB). Se i log sono troppo lunghi, aggiornare security_events. conf utilizzando questo comando: `message_length_limit 4096`


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance CEF ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Scopri come [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare le minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

