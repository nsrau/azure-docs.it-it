---
title: Eseguire query nei log di Azure Gestione aggiornamenti
description: Questo articolo descrive come eseguire una query sui log per Gestione aggiornamenti nell'area di lavoro Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 09eacb42eff6ecf3a3fca2d7fb401f52195f5f2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617420"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Eseguire query sui record di aggiornamento per Gestione aggiornamenti nei log di monitoraggio di Azure

Oltre ai dettagli forniti nella soluzione Gestione aggiornamenti, è possibile eseguire ricerche nei log archiviati nell'area di lavoro Log Analytics. Nel riquadro sinistro della pagina soluzione selezionare **log**. Verrà visualizzata la pagina Ricerca log.

È inoltre possibile apprendere come personalizzare le query o usarle da client diversi. Vedere la [documentazione dell'API di ricerca log Analytics](https://dev.loganalytics.io/).

## <a name="update-records"></a>Record di aggiornamento

Gestione aggiornamenti raccoglie i record per le macchine virtuali Windows e Linux e i tipi di dati visualizzati nei risultati della ricerca nei log. Le sezioni seguenti descrivono i record.

### <a name="required-updates"></a>Aggiornamenti necessari

Viene creato un record con un `RequiredUpdate` tipo di che rappresenta gli aggiornamenti richiesti da un computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| Computer | Nome di dominio completo del computer per la creazione di report. |
| KBID | ID dell'articolo della Knowledge base per Windows Update. |
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. | 
| Prodotto | Prodotti per i quali l'aggiornamento è applicabile. | 
| PublishDate | Data in cui l'aggiornamento è pronto per il download e l'installazione dal Windows Update. |
| Server | | 
| SourceHealthServiceId | Identificatore univoco che rappresenta l'ID dell'agente di Log Analytics Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Identificatore univoco che rappresenta l'istanza di organizzazioni di Azure Active Directory. | 
| TimeGenerated | Data e ora di creazione del record. | 
| Tipo | *Aggiornamento* | 
| UpdateClassification | Indica il tipo di aggiornamenti che è possibile applicare. Per Windows:<br> *Aggiornamenti critici*<br> *Aggiornamenti della sicurezza*<br> *Aggiornamenti cumulativi*<br> *Feature Pack*<br> *Service Pack*<br> *Aggiornamenti della definizione*<br> *Strumenti*<br> *Aggiornamenti*. Per Linux:<br> *Aggiornamenti critici e della sicurezza*<br> *Altro* |
| UpdateSeverity | Classificazione di gravità per la vulnerabilità. I valori possibili sono:<br> *Critico*<br> *Importante*<br> *Moderata*<br> *Basso* |
| UpdateTitle | Titolo dell'aggiornamento.|

### <a name="update"></a>Aggiornamento

Viene creato un record con un `Update` tipo di che rappresenta gli aggiornamenti disponibili e il relativo stato di installazione per un computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| ApprovalSource | Si applica solo al sistema operativo Windows. Origine dell'approvazione per il record. Il valore è Microsoft Update. |
| Approved | True se il record è approvato o false in caso contrario. |
| Classificazione | Classificazione dell'approvazione. Il valore è Updates. |
| Computer | Nome di dominio completo del computer per la creazione di report. |
| ComputerEnvironment | Ambiente. I valori possibili sono Azure o non Azure. |
| MSRCBulletinID | Numero ID bollettino di sicurezza. | 
| MSRCSeverity | Classificazione di gravità per la vulnerabilità. I valori possibili sono:<br> Critico<br> Importante<br> Moderata<br> Basso |  
| KBID | ID dell'articolo della Knowledge base per Windows Update. |
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. |
| UpdateID | Identificatore univoco dell'aggiornamento software. |
| RevisionNumber | Numero di revisione di una revisione specifica di un aggiornamento. |
| Facoltativo | True se il record è facoltativo o false in caso contrario. | 
| RebootBehavior | Comportamento del riavvio dopo l'installazione o la disinstallazione di un aggiornamento. |
| _ResourceId | Identificatore univoco per la risorsa associata al record. |
| Tipo | Tipo di record. Il valore è Update. |
| VMUUID | Identificatore univoco per la macchina virtuale. |
| MG | Identificatore univoco per il gruppo di gestione o l'area di lavoro Log Analytics. | 
| TenantId | Identificatore univoco che rappresenta l'istanza di Azure Active Directory dell'organizzazione. | 
| SourceSystem | Sistema di origine per il record. Il valore è `OperationsManager`. | 
| TimeGenerated | Data e ora di creazione del record. | 
| SourceComputerId | Identificatore univoco che rappresenta il computer di origine. | 
| Titolo | Titolo dell'aggiornamento. |
| PublishedDate (UTC) | Data in cui l'aggiornamento è pronto per il download e l'installazione dal Windows Update.  |
| UpdateState | Stato corrente dell'aggiornamento. | 
| Prodotto | Prodotti per i quali l'aggiornamento è applicabile. |
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. | 
| ResourceGroup | Nome del gruppo di risorse a cui appartiene la risorsa. | 
| ResourceProvider | Provider di risorse. | 
| Risorsa | Nome della risorsa. | 
| ResourceType | Tipo di risorsa. | 

### <a name="update-agent"></a>Aggiorna agente

Viene creato un record con un `UpdateAgent` tipo di che fornisce i dettagli dell'agente di aggiornamento nel computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer | Nome di dominio completo del computer per la creazione di report. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. |
| OSVersion | Versione del sistema operativo. |
| Server | |
| SourceHealthServiceId | Identificatore univoco che rappresenta l'ID dell'agente di Log Analytics Windows. |
| SourceSystem | Sistema di origine per il record. Il valore è `OperationsManager`. | 
| TenantId | Identificatore univoco che rappresenta l'istanza di Azure Active Directory dell'organizzazione. |
| TimeGenerated | Data e ora di creazione del record. |
| Tipo | Tipo di record. Il valore è Update. | 
| WindowsUpdateAgentVersion | Versione dell'agente di Windows Update. |
| WSUSServer | Errori se si verifica un problema nell'agente di Windows Update, per facilitare la risoluzione dei problemi. |

### <a name="update-deployment-status"></a>Aggiorna stato di distribuzione 

Viene creato un record con un `UpdateRunProgress` tipo di che fornisce lo stato di distribuzione dell'aggiornamento di una distribuzione pianificata per computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| Computer | Nome di dominio completo del computer per la creazione di report. |
| ComputerEnvironment | Ambiente. I valori sono Azure o non Azure. | 
| CorrelationId | Identificatore univoco dell'esecuzione del processo Runbook per l'aggiornamento. |
| EndTime | Data e ora di fine del processo di sincronizzazione. | 
| ErrorResult | Windows Update codice di errore generato se l'installazione di un aggiornamento non riesce. | 
| InstallationStatus | Possibili stati di installazione di un aggiornamento nel computer client,<br> `NotStarted`-il processo non è stato ancora attivato.<br> `FailedToStart`-Impossibile avviare il processo nel computer.<br> `Failed`-il processo è stato avviato ma non è riuscito con un'eccezione.<br> `InProgress`-processo in corso.<br> `MaintenanceWindowExceeded`-Se l'esecuzione è rimasta, ma è stato raggiunto l'intervallo di manutenzione.<br> `Succeeded`-processo completato.<br> `InstallFailed`-l'installazione dell'aggiornamento non è riuscita.<br> `NotIncluded`<br> `Excluded` |
| KBID | ID dell'articolo della Knowledge base per Windows Update. | 
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. |
| OSType | Tipo di sistema operativo. I valori sono Windows o Linux. | 
| Prodotto | Prodotti per i quali l'aggiornamento è applicabile. |
| Risorsa | Nome della risorsa. | 
| ResourceId | Identificatore univoco per la risorsa associata al record. |
| ResourceProvider | Provider di risorse. | 
| ResourceType | Tipo di risorsa. | 
| SourceComputerId | Identificatore univoco che rappresenta il computer di origine. | 
| SourceSystem | Sistema di origine per il record. Il valore è `OperationsManager`. |
| StartTime | Ora pianificata per l'installazione dell'aggiornamento. |
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. | 
| SucceededOnRetry | Valore che indica se l'esecuzione dell'aggiornamento non è riuscita al primo tentativo e l'operazione corrente è un nuovo tentativo. |
| TimeGenerated | Data e ora di creazione del record. |
| Titolo | Titolo dell'aggiornamento. |
| Tipo | Tipo di aggiornamento. Il valore è `UpdateRunProgress`. |
| Codice UpdateID | Identificatore univoco dell'aggiornamento software. |
| VMUUID | Identificatore univoco per la macchina virtuale. |
| ResourceId | Identificatore univoco per la risorsa associata al record. |

### <a name="update-summary"></a>Aggiornare il riepilogo 

Viene creato un record con un `UpdateSummary` tipo di che fornisce il riepilogo degli aggiornamenti per computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| Computer | Nome di dominio completo del computer per la creazione di report. |
| ComputerEnvironment | Ambiente. I valori sono Azure o non Azure. | 
| CriticalUpdatesMissing | Numero di aggiornamenti critici applicabili mancanti. | 
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. |
| NETRuntimeVersion | Versione di .NET Framework installata nel computer Windows. |
| OldestMissingSecurityUpdateBucket | Identificatore del bucket di sicurezza mancante meno recente. I valori possibili sono:<br> Recente se il valore è inferiore a 30 giorni<br> 30 giorni fa<br> 60 giorni fa<br> 90 giorni fa<br> 120 giorni fa<br> 150 giorni fa<br> 180 giorni fa<br> Più vecchio quando il valore è maggiore di 180 giorni. | 
| OldestMissingSecurityUpdateInDays | Numero totale di giorni per l'aggiornamento meno recente rilevato come applicabile che non è stato installato. |
| OsVersion | Versione del sistema operativo. |
| OtherUpdatesMissing | Conteggio degli aggiornamenti rilevati mancanti. |
| Risorsa | Nome della risorsa per il record. | 
| ResourceGroup | Nome del gruppo di risorse che contiene la risorsa. |
| ResourceId | Identificatore univoco per la risorsa associata al record. |
| ResourceProvider | Provider di risorse. |
| ResourceType | Tipo di risorsa. |
| RestartPending | True se un riavvio è in sospeso; in caso contrario, false. |
| SecurityUpdatesMissing | Numero di aggiornamenti della sicurezza mancanti applicabili.| 
| SourceComputerId | Identificatore univoco per la macchina virtuale. |
| SourceSystem | Sistema di origine per il record. Il valore è `OpsManager`. | 
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. |
| TimeGenerated | Data e ora di creazione del record. |
| TotalUpdatesMissing | Numero totale di aggiornamenti mancanti applicabili. | 
| Tipo | Tipo di record. Il valore è `UpdateSummary`. |
| VMUUID | Identificatore univoco per la macchina virtuale. |
| WindowsUpdateAgentVersion | Versione dell'agente di Windows Update. |
| WindowsUpdateSetting | Stato dell'agente di Windows Update. I valori possibili sono:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` | 
| WSUSServer | Errori se si verifica un problema nell'agente di Windows Update, per facilitare la risoluzione dei problemi. |
| _ResourceId | Identificatore univoco per la risorsa associata al record. |

## <a name="sample-queries"></a>Query di esempio

Le sezioni seguenti forniscono query di log di esempio per i record di aggiornamento raccolti per Gestione aggiornamenti.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Verificare l'onboarding di computer non di Azure

Per verificare che i computer connessi direttamente comunicano con i log di monitoraggio di Azure, eseguire una delle ricerche log seguenti.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

In un computer Windows, è possibile esaminare le informazioni seguenti per verificare la connettività degli agenti con i log di monitoraggio di Azure:

1. Aprire **Microsoft Monitoring Agent** nel Pannello di controllo. Nella scheda **Analisi dei log di Azure** l'agente visualizza un messaggio per indicare che **Microsoft Monitoring Agent ha eseguito la connessione a Log Analytics**.
2. Aprire il registro eventi di Windows. Passare a **Registri applicazioni e servizi\Operations Manager** e cercare gli ID evento 3000 e 5002 del **connettore del servizio** di origine. Questi eventi indicano che il computer ha eseguito la registrazione all'area di lavoro Log Analytics e sta ricevendo la configurazione.

Se l'agente non è in grado di comunicare con i log di monitoraggio di Azure e l'agente è configurato per la comunicazione con Internet tramite un firewall o un server proxy, verificare che il firewall o il server proxy sia configurato correttamente. Per sapere come verificare se il firewall o il server proxy è configurato correttamente, vedere [Connettere computer Windows al servizio Log Analytics in Azure](../azure-monitor/platform/agent-windows.md) oppure [Raccogliere dati dal computer Linux ospitato nell'ambiente in uso](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se i sistemi Linux sono configurati per la comunicazione con un proxy o il gateway di Log Analytics e si sta eseguendo l'onboarding di questa soluzione, aggiornare le autorizzazioni *proxy.conf* per concedere al gruppo omiuser le necessarie autorizzazioni di lettura per il file usando i comandi seguenti:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Gli agenti Linux appena aggiunti visualizzano lo stato **Aggiornato** dopo l'esecuzione di una valutazione. Il processo può richiedere fino a 6 ore.

Per verificare che un gruppo di gestione di Operations Manager comunichi con i log di monitoraggio di Azure, vedere [Convalidare Operations Manager integrazione con i log di monitoraggio di Azure](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Singole query di valutazione delle macchine virtuali di Azure (Windows)

Sostituire il valore VMUUID con il GUID VM della macchina virtuale per cui si esegue la query. È possibile trovare il VMUUID che deve essere usato eseguendo la query seguente nei log di monitoraggio di Azure:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Riepilogo degli aggiornamenti mancanti

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Elenco degli aggiornamenti mancanti

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Singole query di valutazione delle macchine virtuali di Azure (Linux)

Per alcune distribuzioni di Linux, si verifica [una](https://en.wikipedia.org/wiki/Endianness) mancata corrispondenza di tipo di errore con il valore VMUUID che deriva da Azure Resource Manager e ciò che viene archiviato nei log di monitoraggio di Azure. La query seguente verifica la presenza di una corrispondenza in uno degli ordini di byte. Sostituire i valori VMUUID con il formato big-endian e little-endian del GUID in modo che i risultati vengano restituiti correttamente. È possibile trovare il VMUUID che deve essere usato eseguendo la query seguente nei log di monitoraggio di Azure:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Riepilogo degli aggiornamenti mancanti

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Elenco degli aggiornamenti mancanti

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Query di valutazione per più macchine virtuali

#### <a name="computers-summary"></a>Riepilogo dei computer

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Riepilogo degli aggiornamenti mancanti

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Elenco dei computer

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Elenco degli aggiornamenti mancanti

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Passaggi successivi

* Usare le ricerche log nei [log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md) per visualizzare i dati dettagliati sugli aggiornamenti.
* [Creare avvisi](automation-tutorial-update-management.md#configure-alerts) per lo stato di distribuzione degli aggiornamenti.
