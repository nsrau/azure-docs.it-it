---
title: Eseguire query nei log di Azure Gestione aggiornamenti
description: Questo articolo descrive come eseguire una query sui log per Gestione aggiornamenti nell'area di lavoro Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 01/10/2020
ms.topic: conceptual
ms.openlocfilehash: 5a1979b0e714f35694999c04e1f890b710d54ac9
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867058"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Eseguire query sui record di aggiornamento per Gestione aggiornamenti nei log di monitoraggio di Azure

Oltre ai dettagli forniti nella soluzione Gestione aggiornamenti, è possibile eseguire ricerche nei log archiviati nell'area di lavoro Log Analytics. Nella pagina soluzione, nel riquadro a sinistra, selezionare **log**. Verrà visualizzata la pagina **Ricerca log** .

È anche possibile scoprire come personalizzare le query o usarle da client diversi, visitando la documentazione dell' [API di ricerca log Analytics](https://dev.loganalytics.io/).

## <a name="update-records"></a>Record di aggiornamento

Record raccolti da Gestione aggiornamenti per le macchine virtuali Windows e Linux e i tipi di dati visualizzati nei risultati della ricerca nei log. Le sezioni seguenti descrivono i record.

### <a name="required-updates"></a>Aggiornamenti necessari

Viene creato un record con un tipo di `RequiredUpdate` che rappresenta gli aggiornamenti richiesti da un computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Description | 
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
| UpdateClassification | Indica il tipo di aggiornamenti che è possibile applicare. Per Windows:<br> *Aggiornamenti critici*<br> *Aggiornamenti della sicurezza*<br> *Aggiornamenti cumulativi*<br> *Feature Pack*<br> *Service Pack*<br> *Aggiornamenti delle definizioni*<br> *Strumenti*<br> *Aggiornamenti*. Per Linux:<br> *Aggiornamenti critici e della sicurezza*<br> *Altri* |
| UpdateSeverity | Classificazione di gravità per la vulnerabilità. I valori possibili sono:<br> *Critico*<br> *Importante*<br> *Moderato*<br> *Bassa* |
| UpdateTitle | Titolo dell'aggiornamento.|

### <a name="update"></a>Aggiornamento

Viene creato un record con un tipo di `Update` che rappresenta gli aggiornamenti disponibili e il relativo stato di installazione per un computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Description | 
|----------|-------------|
| ApprovalSource | Si applica solo al sistema operativo Windows. Il valore è *Microsoft Update*. |
| Approved | *True* o *false* |
| Classificazione | *Aggiornamenti* |
| Computer | Nome di dominio completo del computer per la creazione di report. |
| ComputerEnvironment | *Azure* o *non Azure*. |
| MSRCBulletinID | Numero ID bollettino di sicurezza | 
| MSRCSeverity | Classificazione di gravità per la vulnerabilità. I valori possibili sono:<br> *Critico*<br> *Importante*<br> *Moderato*<br> *Bassa* |  
| KBID | ID dell'articolo della Knowledge base per Windows Update. |
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. |
| UpdateID | Identificatore univoco dell'aggiornamento software. |
| RevisionNumber | Numero di revisione di una revisione specifica di un aggiornamento. |
| Facoltativo | *True* o *false* | 
| RebootBehavior | Comportamento del riavvio dopo l'installazione o la disinstallazione di un aggiornamento. |
| _ResourceId | Identificatore univoco per la risorsa a cui è associato il record. |
| Tipo | *Aggiornamento* |
| VMUUID | Identificatore univoco per la macchina virtuale. |
| MG | Identificatore univoco per il gruppo di gestione o l'area di lavoro Log Analytics. | 
| TenantId | Identificatore univoco che rappresenta l'istanza di organizzazioni di Azure Active Directory. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Data e ora di creazione del record. | 
| SourceComputerId | Identificatore univoco che rappresenta il computer di origine. | 
| Titolo | Titolo dell'aggiornamento. |
| PublishedDate (UTC) | Data in cui l'aggiornamento è pronto per il download e l'installazione dal Windows Update.  |
| UpdateState | Stato corrente dell'aggiornamento. | 
| Prodotto | Prodotti per i quali l'aggiornamento è applicabile. |
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. | 
| ResourceGroup | Nome del gruppo di risorse di cui la risorsa è membro. | 
| ResourceProvider | Specifica il provider di risorse. | 
| Gruppi | Nome della risorsa. | 
| ResourceType | Nome del tipo di risorsa. | 

### <a name="update-agent"></a>Aggiorna agente

Viene creato un record con un tipo di `UpdateAgent` che fornisce i dettagli dell'agente di aggiornamento nel computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Description | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer | Nome di dominio completo del computer per la creazione di report. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. |
| OSVersion | Versione del sistema operativo. |
| Server | |
| SourceHealthServiceId | Identificatore univoco che rappresenta l'ID dell'agente di Log Analytics Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Identificatore univoco che rappresenta l'istanza di organizzazioni di Azure Active Directory. |
| TimeGenerated | Data e ora di creazione del record. |
| Tipo | *Aggiornamento* | 
| WindowsUpdateAgentVersion | Versione dell'agente di Windows Update. |
| WSUSServer | Mostra gli errori se l'agente di Windows Update presenta un problema per facilitare la risoluzione dei problemi. |

### <a name="update-deployment-status"></a>Aggiornare lo stato della distribuzione 

Viene creato un record con un tipo di `UpdateRunProgress` che fornisce lo stato di distribuzione dell'aggiornamento di una distribuzione pianificata per computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Description | 
|----------|-------------|
| Computer | Nome di dominio completo del computer per la creazione di report. |
| ComputerEnvironment | *Azure* o *non Azure*. | 
| CorrelationId | Identificatore univoco dell'esecuzione del processo Runbook per l'aggiornamento. |
| EndTime | Data e ora di fine del processo di sincronizzazione. | 
| ErrorResult | Windows Update codice di errore generato se l'installazione di un aggiornamento non riesce. | 
| InstallationStatus | I possibili stati di installazione di un aggiornamento nel computer client, *in corso*, *riuscito*, *parzialmente non riusciti*. |
| KBID | ID dell'articolo della Knowledge base per Windows Update. | 
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. |
| OSType | Specifica il tipo di sistema operativo, *Windows* o *Linux*. | 
| Prodotto | Prodotti per i quali l'aggiornamento è applicabile. |
| Gruppi | Nome della risorsa. | 
| ResourceId | Identificatore univoco per la risorsa a cui è associato il record. |
| ResourceProvider | Specifica il provider di risorse. | 
| ResourceType | Nome del tipo di risorsa. | 
| SourceComputerId | Identificatore univoco che rappresenta il computer di origine. | 
| SourceSystem | *OperationsManager* |
| StartTime | Tempo di installazione pianificato per l'aggiornamento. |
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. | 
| SucceededOnRetry | Indica quando l'esecuzione dell'aggiornamento non è riuscita al primo tentativo e l'operazione corrente è un nuovo tentativo. |
| TimeGenerated | Data e ora di creazione del record. |
| Titolo | Titolo dell'aggiornamento. |
| Tipo | *UpdateRunProgress* |
| Codice UpdateID | Identificatore univoco dell'aggiornamento software. |
| VMUUID | Identificatore univoco per la macchina virtuale. |
| _ResourceId | Identificatore univoco per la risorsa a cui è associato il record. |

### <a name="update-summary"></a>Aggiornare il riepilogo 

Viene creato un record con un tipo di `UpdateSummary` che fornisce il riepilogo degli aggiornamenti per computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Description | 
|----------|-------------|
| Computer | Nome di dominio completo del computer per la creazione di report. |
| ComputerEnvironment | *Azure* o *non Azure*. | 
| CriticalUpdatesMissing | Numero di aggiornamenti critici mancanti applicabili. | 
| ManagementGroupName | Nome del gruppo di gestione Operations Manager o dell'area di lavoro Log Analytics. |
| NETRuntimeVersion | Versione di .NET Framework installata nel computer Windows. |
| OldestMissingSecurityUpdateBucket | | 
| OldestMissingSecurityUpdateInDays | |
| OsVersion | Versione del sistema operativo. |
| OtherUpdatesMissing | Conteggio degli aggiornamenti rilevati mancanti. |
| Gruppi |  Nome della risorsa. | 
| ResourceGroup | Nome del gruppo di risorse di cui la risorsa è membro. |
| ResourceId | Identificatore univoco per la risorsa a cui è associato il record. |
| ResourceProvider | Specifica il provider di risorse. |
| ResourceType | Nome del tipo di risorsa. |
| RestartPending | *True* o *False*. |
| SecurityUpdatesMissing | Numero di aggiornamenti della sicurezza mancanti applicabili.| 
| SourceComputerId | Identificatore univoco per la macchina virtuale. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. |
| TimeGenerated | Data e ora di creazione del record. |
| TotalUpdatesMissing | Numero totale di aggiornamenti mancanti applicabili. | 
| Tipo | *UpdateSummary* |
| VMUUID | Identificatore univoco per la macchina virtuale. |
| WindowsUpdateAgentVersion | Versione dell'agente di Windows Update. |
| WindowsUpdateSetting | Mostra lo stato dell'agente di Windows Update. I valori possibili sono:<br> *Installazione pianificata*<br> *Notifica prima dell'installazione*<br> Errore restituito dall'agente WUA non integro. | 
| WSUSServer | Mostra gli errori se l'agente di Windows Update presenta un problema per facilitare la risoluzione dei problemi. |
| _ResourceId | Identificatore univoco per la risorsa a cui è associato il record. |

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

Sostituire il valore VMUUID con il GUID VM della macchina virtuale per cui si esegue la query. È possibile trovare il VMUUID che deve essere usato eseguendo la query seguente nei log di monitoraggio di Azure: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

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

Per alcune distribuzioni di Linux, si verifica una [mancata corrispondenza](https://en.wikipedia.org/wiki/Endianness) tra l'elemento e il valore di VMUUID che deriva da Azure Resource Manager e ciò che viene archiviato nei log di monitoraggio di Azure. La query seguente verifica la presenza di una corrispondenza in uno degli ordini di byte. Sostituire i valori VMUUID con il formato big-endian e little-endian del GUID in modo che i risultati vengano restituiti correttamente. È possibile trovare il VMUUID che deve essere usato eseguendo la query seguente nei log di monitoraggio di Azure: `Update | where Computer == "<machine name>"
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
