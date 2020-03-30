---
title: Configurare l'analisi delle dipendenze basata su agente in Azure Migrate Server AssessmentSet up agent-based dependency analysis in Azure Migrate Server Assessment
description: Questo articolo descrive come configurare l'analisi delle dipendenze basata su agente in Valutazione del server di migrazione di Azure.This article describes how to set up agent-based dependency analysis in Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: e61b7b4e6c3e566aa67d2bd585d2049ae885083b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453616"
---
# <a name="set-up-dependency-visualization"></a>Impostare la visualizzazione delle dipendenze

Questo articolo descrive come configurare l'analisi delle dipendenze basata su agente in Azure Migrate:Server Assessment.This article describes how to set up agent-based dependency analysis in Azure Migrate:Server Assessment. [L'analisi delle dipendenze](concepts-dependency-visualization.md) consente di identificare e comprendere le dipendenze tra i computer che si desidera valutare ed eseguire la migrazione in Azure.Dependency analysis helps you to identify and understand dependencies across machines you want to assess and migrate to Azure.

## <a name="before-you-start"></a>Prima di iniziare

- [Informazioni sull'analisi](concepts-dependency-visualization.md#agent-based-analysis) delle dipendenze basata su agente.
- Esaminare i prerequisiti e i requisiti di supporto per l'impostazione della visualizzazione delle dipendenze basata su agente per [le macchine virtuali VMware,](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)i [server fisici](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)e le macchine [virtuali Hyper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Assicurarsi di aver creato un progetto di Azure Migrate.Make sure you've [created](how-to-add-tool-first-time.md) an Azure Migrate project.
- Se è già stato creato un progetto, assicurarsi di aver aggiunto lo strumento Azure Migrate:Server Assessment.If you've already created a project, make sure you've [added](how-to-assess.md) the Azure Migrate:Server Assessment tool.
- Assicurarsi di aver configurato [un'appliance Di Azure Migrate](migrate-appliance.md) per individuare le macchine locali. Informazioni su come configurare un accessorio per [VMware,](how-to-set-up-appliance-vmware.md) [Hyper-V](how-to-set-up-appliance-hyper-v.md)o [server fisici.](how-to-set-up-appliance-physical.md) L'appliance individua i computer locali e invia metadati e dati sulle prestazioni a Azure Migrate:Server Assessment.The appliance discovers on-premises machines, and sends metadata, performance data to Azure Migrate:Server Assessment.
- Per usare la visualizzazione delle dipendenze, associare [un'area di lavoro di Log Analytics](../azure-monitor/platform/manage-access.md) a un progetto di Azure Migrate:To use dependency visualization, you associate a Log Analytics workspace with an Azure Migrate project:
    - È possibile collegare un'area di lavoro solo dopo aver impostato l'appliance Azure Migrate e aver individuato le macchine nel progetto Azure Migrate.You can attach a workspace only after setting up the Azure Migrate appliance, and discovering machines in the Azure Migrate project.
    - Assicurarsi di avere un'area di lavoro nella sottoscrizione che contiene il progetto Azure Migrate.Make sure you have a workspace in the subscription that contains the Azure Migrate project.
    - L'area di lavoro deve risiedere nelle aree degli Stati Uniti orientali, del sud-est asiatico o dell'Europa occidentale. Le aree di lavoro in altre aree non possono essere associate a un progetto.
    - L'area di lavoro deve trovarsi in un'area in cui la mappa del servizio [è supportata.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)
    - È possibile associare un'area di lavoro log Analytics nuova o esistente a un progetto di Azure Migrate.You can associate a new or existing Log Analytics workspace with an Azure Migrate project.
    - L'area di lavoro viene collegata la prima volta che si imposta la visualizzazione delle dipendenze per un computer. L'area di lavoro per un progetto di Azure Migrate non può essere modificata dopo l'aggiunta.
    - In Log Analytics, l'area di lavoro associata ad Azure Migrate è contrassegnata con la chiave del progetto di migrazione e il nome del progetto.

## <a name="associate-a-workspace"></a>Associare un'area di lavoro

1. Dopo aver individuato i computer per la valutazione, in **Server** > **di migrazione del server Azure Migrate: Valutazione server**fare clic su **Panoramica**.  
2. In **Azure Migrate: Server Assessment**fare clic su **Essentials**.
3. In **Area di lavoro OMS**fare clic su Richiede **configurazione**.

     ![Configurare l'area di lavoro di Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. In **Configura area di lavoro Di Ms**, specificare se si desidera creare una nuova area di lavoro o utilizzarne una esistente.
    - È possibile selezionare un'area di lavoro esistente da tutte le aree di lavoro nella sottoscrizione di progetto di migrazione.
    - È necessario disporre dell'accesso in lettura all'area di lavoro per associarla.
5. Se si crea una nuova area di lavoro, selezionare una posizione.

    ![Aggiungere una nuova area di lavoro](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Scaricare e installare gli agenti di macchine virtuali

Installare gli agenti in ogni computer che si desidera analizzare.

> [!NOTE]
> Per i computer monitorati da System Center Operations Manager 2012 R2 o versione successiva, non è necessario installare l'agente MMA. La mappa del servizio si integra con Operations Manager. [Seguire le](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) linee guida per l'integrazione.

1. In **Azure Migrate: Server Assessment**fare clic su Server **individuati**.
2. Per ogni computer che si desidera analizzare con la visualizzazione delle **dipendenze,** nella colonna Dipendenze fare clic su **Richiede installazione agente**.
3. Nella pagina Dipendenze scaricare l'agente di gestione delle immagini di base e delle dipendenze per Windows o Linux.In the **Dependencies** page, download the MMA and Dependency agent for Windows or Linux.
4. In **Configura agente MMA**copiare l'ID e la chiave dell'area di lavoro. Sono necessari quando si installa l'agente MMA.

    ![Installare gli agenti](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Installare MMA

Installare l'MMA su ogni computer Windows o Linux che si desidera analizzare.

### <a name="install-mma-on-a-windows-machine"></a>Installare MMA in un computer Windows

Per installare l'agente in un computer Windows:

1. Fare doppio clic sull'agente scaricato.
2. Nella pagina introduttiva**** fare clic su **Avanti**. Nella pagina **Condizioni di licenza** fare clic su **Accetto** per accettare la licenza.
3. In **Cartella di destinazione** mantenere o modificare la cartella di installazione predefinita e quindi fare clic su **Avanti**.
4. In **Opzioni di installazione dell'agente** selezionare **Azure Log Analytics** > **Avanti**.
5. Fare clic su **Aggiungi** per aggiungere una nuova area di lavoro Log Analytics. Incollare l'ID e la chiave dell'area di lavoro copiati dal portale. Fare clic su **Avanti**.

È possibile installare l'agente dalla riga di comando o utilizzando un metodo automatizzato, ad esempio Configuration Manager o [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196).
- [Altre informazioni](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) sull'uso di questi metodi per installare l'agente MMA.
- L'agente MMA può essere installato anche usando questo [script](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Ulteriori informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) sui sistemi operativi Windows supportati da MMA.

### <a name="install-mma-on-a-linux-machine"></a>Installare MMA in un computer LinuxInstall MMA on a Linux machine

Per installare la MMA su un computer Linux:

1. Trasferire il bundle appropriato (x86 o x64) nel computer Linux mediante scp/sftp.
2. Installare il bundle usando l'argomento --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) sull'elenco dei sistemi operativi Linux supportati da MMA. 

## <a name="install-the-dependency-agent"></a>Installare Dependency Agent

1. Per installare Dependency Agent in un computer Windows, fare doppio clic sul file di installazione e seguire la procedura guidata.
2. Per installare Dependency Agent in un computer Linux, procedere all'installazione come utente ROOT usando il comando seguente:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) sul modo in cui è possibile usare gli script per installare l'agente di dipendenza.
- [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) sui sistemi operativi supportati dall'agente di dipendenza.


## <a name="create-a-group-using-dependency-visualization"></a>Creare un gruppo utilizzando la visualizzazione delle dipendenze

Creare ora un gruppo per la valutazione. 


> [!NOTE]
> I gruppi per i quali si vuole visualizzare le dipendenze non devono includere più di 10 computer. Se si dispone di più di 10 macchine, dividerle in gruppi più piccoli.

1. In **Azure Migrate: Server Assessment**fare clic su Server **individuati**.
2. Nella colonna **Dipendenze** fare clic su **Visualizza dipendenze** per ogni computer che si desidera esaminare.
3. Nella mappa delle dipendenze è possibile visualizzare quanto segue:
    - Connessioni TCP in ingresso (client) e in uscita (server) da e verso il computer.
    - I computer dipendenti in cui non sono installati gli agenti di dipendenza sono raggruppati in base ai numeri di porta.
    - I computer dipendenti con agenti di dipendenza installati vengono visualizzati come caselle separate.
    - Processi in esecuzione all'interno della macchina. Espandere ogni casella macchina per visualizzare i processi.
    - Proprietà del computer (inclusi FQDN, sistema operativo, indirizzo MAC). Fare clic su ogni casella della macchina per visualizzare i dettagli.

4. È possibile esaminare le dipendenze per intervalli di tempo diversi facendo clic sulla durata nell'etichetta dell'intervallo di tempo.
    - Per impostazione predefinita, l'intervallo è un'ora. 
    - È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
    - L'intervallo di tempo può essere fino a un'ora. Se è necessario un intervallo più lungo, usare Monitoraggio di Azure per eseguire query sui dati dipendenti per un periodo più lungo.

5. Dopo aver identificato i computer dipendenti che si desidera raggruppare, utilizzare CTRL , fare clic per selezionare più computer sulla mappa e fare clic su **Raggruppa macchine**.
6. Specificare un nome di gruppo.
7. Verificare che i computer dipendenti siano stati individuati da Azure Migrate.

    - Se un computer dipendente non viene individuato da Azure Migrate: Server Assessment, non è possibile aggiungerlo al gruppo.
    - Per aggiungere un computer, eseguire nuovamente l'individuazione e verificare che il computer sia individuato.

8. Se si vuole creare una valutazione per questo gruppo, selezionare la casella di controllo per creare una nuova valutazione per il gruppo.
8. Fare clic su **OK** per salvare il gruppo.

Dopo aver creato il gruppo, è consigliabile installare gli agenti in tutti i computer del gruppo e quindi visualizzare le dipendenze per l'intero gruppo.

## <a name="query-dependency-data-in-azure-monitor"></a>Query dependency data in Azure Monitor

È possibile eseguire query sui dati di dipendenza acquisiti dalla mappa del servizio nell'area di lavoro di Log Analytics associata al progetto Azure Migrate.You can query dependency data captured by Service Map in the Log Analytics workspace associated with the Azure Migrate project. Log Analytics viene usato per scrivere ed eseguire query di log di Monitoraggio di Azure.Log Analytics is used to write and run Azure Monitor log queries.

- [Informazioni su come](../azure-monitor/insights/service-map.md#log-analytics-records) cercare i dati della mappa del servizio in Log Analytics.Learn how to search for Service Map data in Log Analytics.
- [Panoramica](../azure-monitor/log-query/get-started-queries.md) della scrittura di query di log in [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Eseguire una query per i dati di dipendenza come segue:Run a query for dependency data as follows:

1. Dopo aver installato gli agenti, accedere al portale e fare clic su **Panoramica**.
2. In **Azure Migrate: Server Assessment**fare clic su **Panoramica**. Fare clic sulla freccia giù per espandere **Essentials.**
3. In **Area di lavoro OMS**fare clic sul nome dell'area di lavoro.
3. Nella pagina Area di lavoro di Log Analytics > **Generale**fare clic su **Registri**.
4. Scrivere la query e fare clic su **Esegui**.

### <a name="sample-queries"></a>Query di esempio

Di seguito sono riportate alcune query di esempio che è possibile usare per estrarre i dati di dipendenza.

- È possibile modificare le query per estrarre i punti dati preferiti.
- [Esaminare](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) un elenco completo dei record di dati di dipendenza.
- [Esaminare](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) le query di esempio aggiuntive.

#### <a name="sample-review-inbound-connections"></a>Esempio: esaminare le connessioni in ingresso

Esaminare le connessioni in ingresso per un set di macchine virtuali.

- I record nella tabella per le metriche di connessione (VMConnection) non rappresentano singole connessioni di rete fisica.
- Più connessioni di rete fisiche vengono raggruppate in una connessione logica.
- [Altre informazioni](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) su come vengono aggregati i dati della connessione di rete fisica in VMConnection.Learn more about how physical network connection data is aggregated in VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Esempio: riepilogare i dati inviati e ricevuti

In questo esempio viene riepilogato il volume di dati inviati e ricevuti nelle connessioni in ingresso tra un set di computer.

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

[Creare](how-to-create-assessment.md) una valutazione per un gruppo.


