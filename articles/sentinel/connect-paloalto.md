---
title: Connetti i dati di Palo Alto Networks all'anteprima di Azure Sentinel | Microsoft Docs
description: Informazioni su come connettere i dati di Palo Alto Networks ai Sentinel di Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 8bdd5764bf2fc08890375adcdedbc5387b1a9534
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209594"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>Connettere il dispositivo di Palo Alto Networks

> [!IMPORTANT]
> Azure Sentinel è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile connettere Azure Sentinel per qualsiasi dispositivo di Palo Alto Networks salvando i file di log di CEF Syslog. L'integrazione con Azure Sentinel consente di eseguire facilmente analitica e le query tra i dati dei file di log di Palo Alto Networks. Per altre informazioni sul modo in cui Azure Sentinel CEF inserisce dati di tipo, vedere [Appliance connettersi CEF](connect-common-event-format.md).

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui in esecuzione Azure Sentinel.

## <a name="step-1-connect-your-palo-alto-networks-appliance-using-an-agent"></a>Passaggio 1: Connettere l'appliance di Palo Alto Networks usando un agente

Per connettersi all'appliance di Palo Alto Networks a Sentinel di Azure, è necessario distribuire un agente in un computer dedicato (macchina virtuale o in locale) per supportare la comunicazione tra il dispositivo e Sentinel di Azure. È possibile distribuire l'agente manualmente o automaticamente. La distribuzione automatica è disponibile solo se il computer dedicato è una nuova macchina virtuale che si sta creando in Azure. 

In alternativa, è possibile distribuire l'agente manualmente in una macchina virtuale di Azure esistente, in una macchina virtuale in un altro cloud o in un computer locale.

Per visualizzare un diagramma di rete di entrambe le opzioni, vedere [connettere origini dati](connect-data-sources.md#agent-options).


### <a name="deploy-the-agent-in-azure"></a>Distribuire l'agente di Azure

1. Nel portale di Azure Sentinel, fare clic su **connettori dati** e selezionare il tipo di dispositivo. 

1. Sotto **configurazione dell'agente Linux Syslog**:
   - Scegli **distribuzione automatica** se si desidera creare una nuova macchina è pre-installata con l'agente Azure Sentinel e include tutte le necessarie di configurazione, come descritto in precedenza. Selezionare **distribuzione automatica** e fare clic su **distribuzione automatica dell'agente**. Consente di andare alla pagina di acquisto per una macchina virtuale dedicata che viene connesso automaticamente all'area di lavoro, è. La macchina virtuale è una **v3 D2s standard (2 Vcpu, 8 GB di memoria)** e ha un indirizzo IP pubblico.
      1. Nel **distribuzione personalizzata** pagina, fornire i dettagli e scegliere un nome utente e una password e se si accettano i termini e condizioni, la macchina virtuale di acquisto.
      1. Configurare l'appliance per inviare i log usando le impostazioni riportate nella pagina di connessione. Per il connettore Generic Common Event Format, usare queste impostazioni:
         - Protocollo UDP =
         - Port = 514
         - Funzione Local-4 =
         - Formato = CEF
   - Scegli **distribuzione manuale** se si desidera usare una macchina virtuale esistente della macchina Linux dedicata in cui deve essere installato l'agente Azure Sentinel. 
      1. Sotto **scaricare e installare l'agente di Syslog**, selezionare **macchina virtuale Linux di Azure**. 
      1. Nel **macchine virtuali** schermata che si apre, selezionare la macchina si vuole usare e fare clic su **Connect**.
      1. Nella schermata di connettore, sotto **configurazione e l'inoltro Syslog**, impostare che il daemon Syslog sia **rsyslog.d** o **syslog-ng**. 
      1. Copiare i comandi seguenti ed eseguirli nell'appliance:
          - Se si seleziona rsyslog.d:
              
            1. Indicare il daemon Syslog per l'ascolto su struttura local_4 e per inviare i messaggi Syslog per l'agente Azure Sentinel tramite porta 25226 locale. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) che configura l'agente di Syslog per l'ascolto sulla porta 25226 locale. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` In cui {0} deve essere sostituito con il GUID dell'area di lavoro.
            
            1. Riavviare il daemon syslog `sudo service rsyslog restart`
             
          - Se è stato selezionato syslog-ng:

              1. Indicare il daemon Syslog per l'ascolto su struttura local_4 e per inviare i messaggi Syslog per l'agente Azure Sentinel tramite porta 25226 locale. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) che configura l'agente di Syslog per l'ascolto sulla porta 25226 locale. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` In cui {0} deve essere sostituito con il GUID dell'area di lavoro.

              3. Riavviare il daemon syslog `sudo service syslog-ng restart`
      2. Riavviare l'agente di Syslog usando questo comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Verificare che non siano presenti errori nel log dell'agente, eseguire questo comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Distribuire l'agente in un server Linux locale

Se non si usa Azure, è necessario distribuire manualmente l'agente Azure Sentinel per l'esecuzione in un server Linux dedicato.


1. Nel portale di Azure Sentinel, fare clic su **connettori dati** e selezionare il tipo di dispositivo.
1. Per creare una VM Linux dedicata, in **configurazione dell'agente Linux Syslog** sceglie **distribuzione manuale**.
   1. Sotto **scaricare e installare l'agente di Syslog**, selezionare **computer Non Azure Linux**. 
   1. Nel **degli agenti diretti** schermata che si apre selezionare **Agent per Linux** per scaricare l'agente o eseguire questo comando per scaricarlo nel computer Linux:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Nella schermata di connettore, sotto **configurazione e l'inoltro Syslog**, impostare che il daemon Syslog sia **rsyslog.d** o **syslog-ng**. 
      1. Copiare i comandi seguenti ed eseguirli nell'appliance:
         - Se si seleziona rsyslog:
           1. Indicare il daemon Syslog per l'ascolto su struttura local_4 e per inviare i messaggi Syslog per l'agente Azure Sentinel tramite porta 25226 locale. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) che configura l'agente di Syslog per l'ascolto sulla porta 25226 locale. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` In cui {0} deve essere sostituito con il GUID dell'area di lavoro.
           3. Riavviare il daemon syslog `sudo service rsyslog restart`
         - Se è stato selezionato syslog-ng:
            1. Indicare il daemon Syslog per l'ascolto su struttura local_4 e per inviare i messaggi Syslog per l'agente Azure Sentinel tramite porta 25226 locale. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Scaricare e installare il [file di configurazione security_events](https://aka.ms/asi-syslog-config-file-linux) che configura l'agente di Syslog per l'ascolto sulla porta 25226 locale. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` In cui {0} deve essere sostituito con il GUID dell'area di lavoro.
            3. Riavviare il daemon syslog `sudo service syslog-ng restart`
      1. Riavviare l'agente di Syslog usando questo comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Verificare che non siano presenti errori nel log dell'agente, eseguire questo comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Passaggio 2: Inoltrare i log di Palo Alto Networks all'agente di Syslog

Configurare Palo Alto Networks per inoltrare i messaggi Syslog in formato CEF per l'area di lavoro di Azure tramite l'agente di Syslog:
1.  Passare a [guide di configurazione Common Event Format (CEF)](https://docs.paloaltonetworks.com/resources/cef) e scaricare il file pdf per il tipo di dispositivo. Seguire tutte le istruzioni nella Guida per configurare il dispositivo di Palo Alto Networks per raccogliere gli eventi CEF. 

1.  Passare a [configurare Syslog monitoraggio](https://aka.ms/asi-syslog-paloalto-forwarding) e seguire i passaggi 2 e 3 per configurare l'inoltro di eventi CEF dal dispositivo di Palo Alto Networks a Sentinel di Azure.

    1. Assicurarsi di impostare il **formato server Syslog** al **BSD**.
    1. Assicurarsi di impostare il **numero di impianto** sullo stesso valore è impostato nell'agente di Syslog.

> [!NOTE]
> Le operazioni di copia/incolla da PDF potrebbero modificare il testo e inserire caratteri casuali. Per evitare questo problema, copiare il testo in un editor e rimuovere i caratteri che potrebbero interrompere il formato del log prima di averla poi incollata, come si può notare in questo esempio.
 
  ![Errore nella copia testo CEF](./media/connect-cef/paloalto-text-prob1.png)

## <a name="step-3-validate-connectivity"></a>Passaggio 3: Convalidare la connettività

Potrebbero occorrere fino a 20 minuti fino a quando i log di avvio venga visualizzato nel Log Analitica. 

1. Assicurarsi che i log vengano alla porta di destra nell'agente di Syslog. Eseguire questo comando computer agente Syslog: `tcpdump -A -ni any  port 514 -vv` Questo comando Visualizza i log che consente di trasmettere dal dispositivo al computer Syslog. Assicurarsi che i log vengono ricevuti da appliance di origine sulla porta di destra e struttura a destra.
2. Verificare che vi sia la comunicazione tra il daemon Syslog e l'agente. Eseguire questo comando computer agente Syslog: `tcpdump -A -ni any  port 25226 -vv` Questo comando Visualizza i log che consente di trasmettere dal dispositivo al computer Syslog. Assicurarsi che i log vengono anche ricevuti nell'agente.
3. Se entrambi i comandi forniti risultati corretti, controllare i Log Analitica per vedere se i log sono in arrivo. Tutti gli eventi trasferiti da questi dispositivi vengono visualizzati in formato non elaborato nel Log Analitica in `CommonSecurityLog` tipo.
1. Per verificare se sono presenti errori o se i log non sono in arrivo, Cerca in `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Assicurarsi che le dimensioni predefinite del messaggio Syslog sono limitata a 2048 byte (2KB). Se i log sono troppo lunghi, aggiornare il security_events usando questo comando: `message_length_limit 4096`
6. Per usare lo schema appropriato nel Log Analitica per gli eventi di Palo Alto Networks, cercare **CommonSecurityLog**.







## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato descritto come connettere i dispositivi di Palo Alto Networks a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere la visibilità di dati e le potenziali minacce](quickstart-get-visibility.md).
- Iniziare a usare [rilevando minacce con Azure Sentinel](tutorial-detect-threats.md).

