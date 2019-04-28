---
title: Raggruppare i computer usando le dipendenze dei computer con Azure Migrate | Microsoft Docs
description: Descrive come creare una valutazione usando le dipendenze dei computer con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: af47678b19209936aed86c132a8a3f400c3a7e8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596775"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Raggruppare i computer usando il mapping delle dipendenze dei computer

Questo articolo descrive come creare un gruppo di computer per eseguire la valutazione con [Azure Migrate](migrate-overview.md) visualizzando le dipendenze dei computer. Questo metodo viene in genere usato quando si vogliono valutare gruppi di macchine virtuali con livelli di attendibilità più elevati controllando in modo incrociato le dipendenze dei computer prima di eseguire una valutazione. La visualizzazione delle dipendenze è utile per pianificare in modo efficace la migrazione a Azure. Garantisce inoltre una migrazione completa e senza interruzioni a sorpresa. È possibile individuare tutti i sistemi interdipendenti di cui è necessario eseguire contemporaneamente la migrazione e determinare se un sistema in esecuzione è ancora utile o se è un candidato alla rimozione anziché alla migrazione.

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="prepare-for-dependency-visualization"></a>Prepararsi per la visualizzazione delle dipendenze
Azure Migrate Usa mapping dei servizi nei log di monitoraggio di Azure per abilitare la visualizzazione delle dipendenze delle macchine.

### <a name="associate-a-log-analytics-workspace"></a>Associare un'area di lavoro Log Analytics
Per sfruttare i vantaggi della visualizzazione delle dipendenze è possibile associare un'area di lavoro Log Analytics, nuova o esistente, a un progetto di Azure Migrate. È possibile creare o collegare solo un'area di lavoro nella stessa sottoscrizione in cui viene creato il progetto di migrazione.

- Per collegare un'area di lavoro Log Analytics a un progetto, in **Panoramica** passare alla sezione **Essentials** del progetto e fare clic su **Configurazione richiesta**

    ![Associare un'area di lavoro Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Durante l'associazione di un'area di lavoro si avrà la possibilità di creare una nuova area di lavoro o di collegarne una esistente:
  - Quando si crea una nuova area di lavoro è necessario specificare un nome per essa. L'area di lavoro viene quindi creata in una regione della stessa [area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) come progetto di migrazione.
  - Quando si collega un'area di lavoro esistente, è possibile sceglierla tra tutte le aree di lavoro disponibili nella stessa sottoscrizione del progetto di migrazione. Sono elencate solo le aree di lavoro create in un'area geografica in cui [è supportato il mapping dei servizi](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Per poter collegare un'area di lavoro è necessario avere l'accesso in lettura all'area di lavoro.

> [!NOTE]
> Non è possibile modificare l'area di lavoro associata a un progetto di migrazione.

### <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali
Dopo aver configurato un'area di lavoro, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole valutare. Se si hanno computer senza accesso a Internet, è necessario scaricare e installare il [Gateway Log Analytics](../azure-monitor/platform/gateway.md).

1. In **Panoramica** fare clic su **Gestisci** > **Computer** e selezionare il computer necessario.
2. Nella colonna **Dipendenze** fare clic su **Installa agenti**.
3. Nella pagina **Dipendenze** scaricare e installare Microsoft Monitoring Agent (MMA) e Dependency Agent in ogni macchina virtuale che si vuole valutare.
4. Copiare l'ID e la chiave dell'area di lavoro. Questi dati sono necessari quando si installa MMA nel computer locale.

> [!NOTE]
> Per automatizzare l'installazione degli agenti è possibile usare qualsiasi strumento di distribuzione come System Center Configuration Manager o [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), uno strumento di partner che dispone di una soluzione di distribuzione dell'agente per Azure Migrate.

### <a name="install-the-mma"></a>Installare MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Installare l'agente in un computer Windows

Per installare l'agente in un computer Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina di **benvenuto** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics** > **Avanti**.
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro Log Analytics. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.

È possibile installare l'agente dalla riga di comando o usando un metodo automatizzato, ad esempio System Center Configuration Manager. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sull'uso di questi metodi per installare l'agente MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Installare l'agente in un computer Linux

Per installare l'agente in un computer Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Altre informazioni](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) sull'elenco dei sistemi operativi Linux supportati da MMA.

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>Installare l'agente in un computer monitorato da SCOM

Per i computer monitorati da System Center Operations Manager 2012 R2 o versioni successive, non è necessario installare l'agente MMA. Mapping dei servizi dispone di un'integrazione con SCOM che sfrutta l'MMA SCOM per raccogliere i dati sulle dipendenze necessari. È possibile abilitare l'integrazione usando il materiale sussidiario disponibile [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). Si noti tuttavia che sarà necessario installare l'agente di dipendenza su tali computer.


### <a name="install-the-dependency-agent"></a>Installare Dependency Agent
1. Per installare Dependency Agent in un computer Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un computer Linux, procedere all'installazione come utente ROOT usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

Altre informazioni sul supporto di Dependency Agent per sistemi operativi [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) e [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).

[Altre informazioni](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sul modo in cui è possibile usare gli script per installare l'agente di dipendenza.


## <a name="create-a-group"></a>Creare un gruppo

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Gestisci** > **Computer**.
2. Cercare il computer in cui sono stati installati gli agenti.
3. Nella colonna **Dipendenze** relativa al computer viene ora visualizzata la dicitura **Visualizza dipendenze**. Fare clic sulla colonna per visualizzare le dipendenze del computer.
4. La mappa delle dipendenze del computer mostra i dettagli seguenti:
    - Le connessioni TCP in ingresso (client) e in uscita (server) verso o dal computer
        - I computer dipendenti in cui non sono installati l'agente MMA e l'agente Dependency Agent sono raggruppati in base ai numeri di porta
        - I computer dipendenti in cui sono installati l'agente MMA e l'agente Dependency Agent sono visualizzati in caselle separate
    - I processi in esecuzione sul computer: è possibile espandere ogni casella di computer per visualizzare i processi
    - Le proprietà di ogni computer, come il nome di dominio completo, il sistema operativo, l'indirizzo MAC e così via: fare clic su ogni casella di computer per visualizzare questi dettagli

      ![Visualizzare le dipendenze dei computer](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. È possibile esaminare le dipendenze per intervalli di tempo diversi facendo clic sulla durata nell'etichetta dell'intervallo di tempo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.

   > [!NOTE]
   >    Attualmente, l'interfaccia utente di visualizzazione delle dipendenze non supporta la selezione di un intervallo di tempo più lungo di un'ora. Usare monitoraggio di Azure registra [eseguire query sui dati delle dipendenze](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) su periodi di tempo.

5. Dopo aver identificato i computer dipendenti da raggruppare, fare clic tenendo premuto CTRL per selezionare più computer nella mappa e quindi fare clic su **Raggruppa macchine virtuali**.
6. Specificare un nome di gruppo. Verificare che i computer dipendenti siano stati individuati da Azure Migrate.

    > [!NOTE]
    > Se un computer dipendente non viene individuato da Azure Migrate, non è possibile aggiungerlo al gruppo. Per aggiungere tali computer al gruppo, è necessario eseguire nuovamente il processo di individuazione con l'ambito corretto nel server vCenter e verificare che il computer venga individuato da Azure Migrate.  

7. Se si vuole creare una valutazione per questo gruppo, selezionare la casella di controllo per creare una nuova valutazione per il gruppo.
8. Fare clic su **OK** per salvare il gruppo.

Una volta creato il gruppo, è consigliabile installare gli agenti in tutti i computer del gruppo e perfezionare il gruppo visualizzandone tutte le dipendenze.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Eseguire query sui dati delle dipendenze dai log di monitoraggio di Azure

I dati sulle dipendenze acquisiti da mapping dei servizi sono disponibili per l'esecuzione di query nell'area di lavoro di Log Analitica associato al progetto Azure Migrate. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sulle tabelle di dati di mapping dei servizi per eseguire una query in Monitoraggio di Azure Registra. 

Per eseguire la query Kusto:

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Panoramica**.
2. In **Panoramica** , andare alla sezione **Elementi fondamentali** del progetto e fare clic sul nome dell'area di lavoro accanto a **Spazio di lavoro OMS** .
3. Nella pagina dell'area di lavoro Log Analytics, fare clic su **Generale** > **Log**.
4. Scrivere la query per raccogliere dati sulle dipendenze mediante i log di monitoraggio di Azure. Trovare le query di esempio nella sezione successiva.
5. Eseguire la query facendo clic su Esegui. 

[Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sulla scrittura di query Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Monitoraggio di Azure di esempio registra le query

Di seguito sono esempi di query è possibile usare per estrarre i dati sulle dipendenze. È possibile modificare le query per estrarre i punti dati preferito. È disponibile un elenco completo dei campi nel record di dati delle dipendenze [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Trovare altri esempi di query [qui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Riepilogare le connessioni in ingresso in un set di computer

Si noti che i record nella tabella di metriche relative alla connessione VMConnection, non rappresentano le connessioni di rete fisica. Più connessioni di rete fisiche sono raggruppate in una connessione logica. [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sulla connessione di rete fisica come i dati vengono aggregati in un singolo record logico in VMConnection. 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Riepilogare volume dei dati inviati e ricevuti nelle connessioni in ingresso tra un set di computer

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sulle domande frequenti sulla visualizzazione delle dipendenze.
- [Informazioni](how-to-create-group-dependencies.md) su come perfezionare il gruppo visualizzandone le dipendenze.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
