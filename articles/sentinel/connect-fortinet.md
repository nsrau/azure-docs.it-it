---
title: Connetti i dati Fortinet all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere dati Fortinet Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: f3ab4861e874074e7de059c7c50064d53749748c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611291"
---
# <a name="connect-your-fortinet-appliance"></a>Connettersi all'appliance Fortinet

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio. Non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [condizioni d'uso aggiuntive per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile connettere Azure Sentinel per qualsiasi dispositivo Fortinet salvando i file di log come Syslog Common Event Format (CEF). Grazie all'integrazione con Azure Sentinel, è possibile eseguire facilmente analitica e le query tra i dati dei file di log da Fortinet. Per altre informazioni sul modo in cui Azure Sentinel CEF inserisce dati di tipo, vedere [Appliance connettersi CEF](connect-common-event-format.md).

> [!NOTE]
> I dati vengono archiviati nella posizione geografica dell'area di lavoro in cui si esegue Azure Sentinel.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Passaggio 1: Connettersi all'appliance Fortinet tramite un agente

Per connettersi all'appliance Fortinet a Sentinel di Azure, distribuire un agente in una macchina virtuale dedicata o nella macchina locale per supportare la comunicazione tra il dispositivo e Sentinel di Azure. È possibile distribuire l'agente manualmente o automaticamente. Distribuzione automatica è disponibile solo se la macchina dedicata è una nuova macchina virtuale create in Azure.

È anche possibile distribuire l'agente manualmente in una VM di Azure esistente, in una macchina virtuale in un altro cloud o in un computer locale.

Per visualizzare un diagramma di rete di entrambe le opzioni, vedere [connettere origini dati](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Distribuire l'agente di Azure

1. Nel portale di Azure Sentinel, selezionare **connettori dati** e selezionare il tipo di dispositivo.

1. Sotto **configurazione dell'agente Linux Syslog**:
   - Scegli **distribuzione automatica** se si desidera creare una nuova macchina in cui è preinstallata con l'agente Azure Sentinel nonché tutta la configurazione necessaria, come descritta in precedenza. Selezionare **distribuzione automatica** > **distribuzione automatica dell'agente**. Viene visualizzata la pagina di acquisto per una macchina virtuale dedicata che viene connesso automaticamente all'area di lavoro. La macchina virtuale è una **v3 D2s standard (2 Vcpu, 8 GB di memoria)** e ha un indirizzo IP pubblico.
      1. Nel **distribuzione personalizzata** pagina, fornire i dettagli, immettere un nome utente e una password e se si accettano i termini e condizioni, la macchina virtuale di acquisto.
      1. Configurare l'appliance per inviare i log usando le impostazioni elencate nella pagina della connessione. Per il connettore Generic Common Event Format, usare queste impostazioni:
         - Protocollo UDP =
         - Port = 514
         - Funzione Local-4 =
         - Formato = CEF
   - Scegli **distribuzione manuale** se si desidera usare una macchina virtuale esistente della macchina Linux dedicata in cui è installato l'agente Azure Sentinel. 
      1. Sotto **scaricare e installare l'agente di Syslog**, selezionare **macchina virtuale Linux di Azure**. 
      1. Nel **macchine virtuali** schermata, selezionare la macchina si vuole usare e quindi selezionare **Connect**.
      1. Nella schermata di connettore sotto **Configura e l'inoltro Syslog**, impostare che il daemon Syslog sia **rsyslog.d** o **syslog-ng**. 
      1. Copiare i comandi seguenti ed eseguirli nell'appliance:
          - Se si seleziona rsyslog.d:
              
            1. Indicare il daemon Syslog per l'ascolto su struttura local_4 e per inviare i messaggi Syslog per l'agente Azure Sentinel tramite la porta 25226 locale. Usare questo comando: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) che configura l'agente di Syslog per l'ascolto sulla porta 25226 locale. Usare questo comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` in cui {0} deve essere sostituito con il GUID dell'area di lavoro.
            1. Riavviare il daemon syslog tramite questo comando: `sudo service rsyslog restart`
             
          - Se è stato selezionato syslog-ng:

              1. Indicare il daemon Syslog per l'ascolto su struttura local_4 e per inviare i messaggi Syslog per l'agente Azure Sentinel tramite la porta 25226 locale. Usare questo comando: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) che configura l'agente di Syslog per l'ascolto sulla porta 25226 locale. Usare questo comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` in cui {0} deve essere sostituito con il GUID dell'area di lavoro.
              1. Riavviare il daemon syslog tramite questo comando: `sudo service syslog-ng restart`
      1. Riavviare l'agente di Syslog tramite questo comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Verificare che non siano presenti errori nel log dell'agente, eseguire questo comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Distribuire l'agente in un server Linux locali

Se non si usa Azure, è necessario distribuire manualmente l'agente Azure Sentinel per l'esecuzione in un server Linux dedicato.

1. Nel portale di Azure Sentinel, selezionare **connettori dati** e selezionare il tipo di dispositivo.
1. Per creare una VM Linux dedicata, in **configurazione dell'agente Linux Syslog** selezionate **distribuzione manuale**.

    1. Sotto **scaricare e installare l'agente di Syslog**, selezionare **computer Non Azure Linux**.
    1. Nel **degli agenti diretti** schermata che si apre selezionare **Agent per Linux** per scaricare l'agente o eseguire questo comando per scaricarlo nel computer Linux: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. Nella schermata di connettore, sotto **configurazione e l'inoltro Syslog**, impostare che il daemon Syslog sia **rsyslog.d** o **syslog-ng**.
       1. Copiare i comandi seguenti ed eseguirli nell'appliance:

          - Se si seleziona rsyslog:

            1. Indicare il daemon Syslog per l'ascolto su struttura local_4 e per inviare i messaggi Syslog per l'agente Azure Sentinel tramite porta 25226 locale. Usare questo comando: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) che configura l'agente di Syslog per l'ascolto sulla porta 25226 locale. Usare questo comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` in cui {0} deve essere sostituito con il GUID dell'area di lavoro.
            1. Riavviare il daemon syslog tramite questo comando: `sudo service rsyslog restart`

          - Se è stato selezionato syslog-ng:

            1. Indicare il daemon Syslog per l'ascolto su struttura local_4 e per inviare i messaggi Syslog per l'agente Azure Sentinel tramite la porta 25226 locale. Usare questo comando: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) che configura l'agente di Syslog per l'ascolto sulla porta 25226 locale. Usare questo comando: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` in cui {0} deve essere sostituito con il GUID dell'area di lavoro.
            1. Riavviare il daemon syslog tramite questo comando: `sudo service syslog-ng restart`

      1. Riavviare l'agente di Syslog tramite questo comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Verificare che non siano presenti errori nel log dell'agente, eseguire questo comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Passaggio 2: Inoltrare Fortinet log all'agente di Syslog

Configurare Fortinet per inoltrare i messaggi Syslog in formato CEF per l'area di lavoro di Azure tramite l'agente di Syslog.

1. Aprire l'interfaccia della riga di comando nell'appliance Fortinet ed eseguire i comandi seguenti:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Sostituire il server **indirizzo ip** con l'indirizzo IP dell'agente.
    - Impostare **nome_funzionalità** usare la funzionalità è configurata nell'agente. Per impostazione predefinita, l'agente si imposta su local4.
    - Impostare il **porta syslog** al **514** o la porta impostata sull'agente.
    - Per abilitare il formato CEF nelle prime versioni FortiOS, potrebbe essere necessario eseguire il set di comandi **csv disabilitare**.
 
   > [!NOTE] 
   > Per altre informazioni, vedere la [raccolta di documenti Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selezionare la versione e usare la **Handbook** e **riferimento ai messaggi di Log**.

 Per usare lo schema appropriato in Azure Monitor Log Analitica per gli eventi Fortinet, cercare `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Passaggio 3: Convalidare la connettività

Potrebbe richiedere fino a 20 minuti fino a quando i log di avvio venga visualizzato nel Log Analitica. 

1. Assicurarsi di che usare la funzione a destra. La funzionalità deve essere lo stesso nell'appliance e in Azure Sentinel. È possibile controllare quali file di funzionalità è in uso in Azure Sentinel e modificarlo nel file `security-config-omsagent.conf`. 

2. Assicurarsi che i log vengano alla porta di destra nell'agente di Syslog. Eseguire questo comando nel computer agente Syslog: `tcpdump -A -ni any  port 514 -vv`. Questo comando Visualizza i log di flusso dal dispositivo al computer Syslog. Assicurarsi che i log vengono ricevuti da appliance di origine nella struttura a destra e la porta di destra.

3. Assicurarsi che i log si inviano rispettino [RFC 5424](https://tools.ietf.org/html/rfc542).

4. Nel computer che esegue l'agente di Syslog, assicurarsi che la porta 514 e 25226 locale sia aperte ed è in ascolto usando il comando `netstat -a -n:`. Per altre informazioni sull'uso di questo comando, vedere [netstat(8) - pagina di manuale di Linux](https://linux.die.net/man/8/netstat). Se è in ascolto correttamente, viene visualizzato:

   ![Azure Sentinel porte](./media/connect-cef/ports.png) 

5. Assicurarsi che il daemon è impostato per l'ascolto sulla porta 514, in cui si sta inviando i log.
    - Per rsyslog:<br>Assicurarsi che il file `/etc/rsyslog.conf` include questa configurazione:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Per altre informazioni, vedere [imudp: Il modulo di input UDP Syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) e [imtcp: Il modulo di input TCP Syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Per syslog-ng:<br>Assicurarsi che il file `/etc/syslog-ng/syslog-ng.conf` include questa configurazione:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Per altre informazioni, vedere [imudp: Il modulo di input UDP Syslog](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) e [edizione Open source di syslog-ng 3.16 - Guida all'amministrazione](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Verificare che vi sia la comunicazione tra il daemon Syslog e l'agente. Eseguire questo comando nel computer agente Syslog: `tcpdump -A -ni any  port 25226 -vv`. Questo comando Visualizza i log di flusso dal dispositivo al computer Syslog. Assicurarsi che i log vengono anche ricevuti nell'agente.

6. Se entrambi i comandi forniti risultati corretti, controllare i Log Analitica per vedere se i log sono in arrivo. Tutti gli eventi trasferiti da questi dispositivi vengono visualizzati in formato non elaborato nel Log Analitica in `CommonSecurityLog` tipo.

7. Per verificare se sono presenti errori o se i log non sono in arrivo, Cerca in `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Se lo stato sono presenti errori di mancata corrispondenza tra formato di log, passare a `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ed esaminare il file `security_events.conf`. Assicurarsi che i log corrispondano al formato di espressione regolare che vedere in questo file.

8. Assicurarsi che le dimensioni predefinite del messaggio Syslog sono limitata a 2048 byte (2 KB). Se i log sono troppo lunghi, aggiornare security_events tramite questo comando: `message_length_limit 4096`

10. Se non vengono ricevuti i log Fortinet dall'agente, eseguire questo comando, a seconda del tipo del daemon Syslog si usa, per impostare la funzionalità e i log per cercare la parola Fortinet nei log:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Riavviare il daemon Syslog tramite questo comando: `sudo service rsyslog restart`
       - syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Riavviare il daemon Syslog tramite questo comando: `sudo service syslog-ng restart`

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si appreso come connettere dispositivi Fortinet a Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

