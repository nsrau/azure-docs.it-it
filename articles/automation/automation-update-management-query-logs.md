---
title: Eseguire query sui log di gestione degli aggiornamenti di AzureQuery Azure Update Management logs
description: In questo articolo viene descritto come eseguire una query sui log per la gestione degli aggiornamenti nell'area di lavoro di Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 81e12e775306cc8637dedd534f50e8a14bc09a26
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743880"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Query update records for Update Management in Azure Monitor Logs

Oltre ai dettagli forniti nella soluzione di gestione degli aggiornamenti, è possibile eseguire ricerche nei log archiviati nell'area di lavoro di Log Analytics. Nel riquadro sinistro della pagina della soluzione selezionare **Registri**. Viene visualizzata la pagina **Ricerca log.**

Per informazioni su come personalizzare le query o utilizzarle da client diversi e altro ancora, visitare: [Documentazione dell'API](https://dev.loganalytics.io/)di ricerca di Log Analytics .

## <a name="update-records"></a>Record di aggiornamento

Record raccolti da Gestione aggiornamenti per macchine virtuali Windows e Linux e dai tipi di dati visualizzati nei risultati della ricerca nei log. Nelle sezioni seguenti vengono descritti tali record.

### <a name="required-updates"></a>Aggiornamenti necessari

Viene creato un `RequiredUpdate` record con un tipo di che rappresenta gli aggiornamenti richiesti da un computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| Computer | Nome di dominio completo del computer di report. |
| KBID | ID articolo della Knowledge Base per l'aggiornamento di Windows. |
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager o dell'area di lavoro di Log Analytics. | 
| Prodotto | Prodotti per i quali è applicabile l'aggiornamento. | 
| PublishDate | La data in cui l'aggiornamento è pronto per essere scaricato e installato da Windows Update. |
| Server | | 
| SourceHealthServiceId | Identificatore univoco che rappresenta l'ID agente Windows di Log Analytics. |
| SourceSystem | *OperationsManager* | 
| TenantId | Identificatore univoco che rappresenta l'istanza dell'organizzazione di Azure Active Directory. | 
| TimeGenerated | Data e ora di creazione del record. | 
| Type | *Aggiornamento* | 
| UpdateClassification | Indica il tipo di aggiornamenti che è possibile applicare. Per Windows:<br> *Aggiornamenti critici*<br> *Aggiornamenti della sicurezza*<br> *Aggiornamenti cumulativi*<br> *Feature Pack*<br> *Service Pack*<br> *Aggiornamenti della definizione*<br> *Strumenti*<br> *Aggiornamenti*. Per Linux:<br> *Aggiornamenti critici e della sicurezza*<br> *Altro* |
| Aggiornamento Gravità | Valutazione della gravità per la vulnerabilità. I valori possibili sono:<br> *Critico*<br> *Importante*<br> *Moderata*<br> *Basso* |
| UpdateTitle | Titolo dell'aggiornamento.|

### <a name="update"></a>Aggiornamento

Viene creato un `Update` record con un tipo di che rappresenta gli aggiornamenti disponibili e il relativo stato di installazione per un computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| OrigineApprovazione | Si applica solo al sistema operativo Windows. Il valore è *Microsoft Update*. |
| Approved | *Vero* o *falso* |
| Classificazione | *Aggiornamenti* |
| Computer | Nome di dominio completo del computer di report. |
| Ambiente computer | *Azure* o *Non Azure*. |
| MSRCBulletinID | Numero ID bollettino di sicurezza | 
| MSRCSeverity | Valutazione della gravità per la vulnerabilità. I valori possibili sono:<br> *Critico*<br> *Importante*<br> *Moderata*<br> *Basso* |  
| KBID | ID articolo della Knowledge Base per l'aggiornamento di Windows. |
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager o dell'area di lavoro di Log Analytics. |
| UpdateID | Identificatore univoco dell'aggiornamento software. |
| RevisionNumber | Numero di revisione di una revisione specifica di un aggiornamento. |
| Facoltativo | *Vero* o *falso* | 
| RebootBehavior | Il comportamento di riavvio dopo l'installazione/disinstallazione di un aggiornamento. |
| _ResourceId | Identificatore univoco della risorsa a cui è associato il record. |
| Type | *Aggiornamento* |
| VMUUID | Identificatore univoco della macchina virtuale. |
| MG | Identificatore univoco del gruppo di gestione o dell'area di lavoro di Log Analytics.Unique identifier for the management group or Log Analytics workspace. | 
| TenantId | Identificatore univoco che rappresenta l'istanza dell'organizzazione di Azure Active Directory. | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | Data e ora di creazione del record. | 
| SourceComputerId | Identificatore univoco che rappresenta il computer di origine. | 
| Titolo | Titolo dell'aggiornamento. |
| PublishedDate (UTC) | La data in cui l'aggiornamento è pronto per essere scaricato e installato da Windows Update.  |
| UpdateState | Stato corrente dell'aggiornamento. | 
| Prodotto | Prodotti per i quali è applicabile l'aggiornamento. |
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. | 
| ResourceGroup | Nome del gruppo di risorse di cui è membro la risorsa. | 
| ResourceProvider | Specifica il provider di risorse. | 
| Risorsa | Nome della risorsa. | 
| ResourceType | Nome del tipo di risorsa. | 

### <a name="update-agent"></a>Aggiorna agente

Viene creato un `UpdateAgent` record con un tipo di che fornisce i dettagli dell'agente di aggiornamento nel computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer | Nome di dominio completo del computer di report. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager o dell'area di lavoro di Log Analytics. |
| OSVersion | Versione del sistema operativo. |
| Server | |
| SourceHealthServiceId | Identificatore univoco che rappresenta l'ID agente Windows di Log Analytics. |
| SourceSystem | *OperationsManager* | 
| TenantId | Identificatore univoco che rappresenta l'istanza dell'organizzazione di Azure Active Directory. |
| TimeGenerated | Data e ora di creazione del record. |
| Type | *Aggiornamento* | 
| WindowsUpdateAgentVersion | Versione dell'agente di Windows Update. |
| WSUSServer | Mostra gli errori se l'agente di Windows Update presenta un problema per facilitare la risoluzione dei problemi. |

### <a name="update-deployment-status"></a>Aggiorna stato di distribuzione 

Viene creato un `UpdateRunProgress` record con un tipo di che fornisce lo stato di distribuzione degli aggiornamenti di una distribuzione pianificata per computer. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| Computer | Nome di dominio completo del computer di report. |
| Ambiente computer | *Azure* o *Non Azure*. | 
| CorrelationId | Identificatore univoco del processo del runbook eseguito per l'aggiornamento. |
| EndTime | Ora di fine del processo di sincronizzazione. | 
| Risultato errore | Codice di errore di Windows Update generato se l'installazione di un aggiornamento non riesce. | 
| InstallationStatus (Stato installazione) | I possibili stati di installazione di un aggiornamento sul computer client,<br> *NotStarted* - processo non ancora attivato.<br> *FailedToStart:* impossibile avviare il processo nel computer.<br> *Non riuscito:* processo avviato ma non riuscito con un'eccezione.<br> *InProgress* - processo in corso.<br> *MaintenanceWindowExceeded* - se l'esecuzione era rimanente ma è stato raggiunto l'intervallo della finestra di manutenzione.<br> *Operazione riuscita:* processo riuscito.<br> *InstallFailed* - aggiornamento non è stato installato correttamente.<br> *Non incluso*<br> *Esclusione* |
| KBID | ID articolo della Knowledge Base per l'aggiornamento di Windows. | 
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager o dell'area di lavoro di Log Analytics. |
| OSType | Specifica il tipo di sistema operativo, *Windows* o *Linux*. | 
| Prodotto | Prodotti per i quali è applicabile l'aggiornamento. |
| Risorsa | Nome della risorsa. | 
| ResourceId | Identificatore univoco della risorsa a cui è associato il record. |
| ResourceProvider | Specifica il provider di risorse. | 
| ResourceType | Nome del tipo di risorsa. | 
| SourceComputerId | Identificatore univoco che rappresenta il computer di origine. | 
| SourceSystem | *OperationsManager* |
| StartTime | Ora in cui è pianificata l'installazione dell'aggiornamento. |
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. | 
| Ripetizione dei tentativi | Indica quando l'esecuzione dell'aggiornamento non è riuscita al primo tentativo e l'operazione corrente è un tentativo. |
| TimeGenerated | Data e ora di creazione del record. |
| Titolo | Titolo dell'aggiornamento. |
| Type | *UpdateRunProgress* |
| UpdateId (UpdateId) | Identificatore univoco dell'aggiornamento software. |
| VMUUID | Identificatore univoco della macchina virtuale. |
| _ResourceId | Identificatore univoco della risorsa a cui è associato il record. |

### <a name="update-summary"></a>Aggiornare il riepilogo 

Viene creato un `UpdateSummary` record con un tipo di che fornisce il riepilogo degli aggiornamenti per macchina. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione | 
|----------|-------------|
| Computer | Nome di dominio completo del computer di report. |
| Ambiente computer | *Azure* o *Non Azure*. | 
| CriticalUpdatesMissing | Numero di aggiornamenti critici mancanti applicabili. | 
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager o dell'area di lavoro di Log Analytics. |
| NETRuntimeVersion | Versione di .NET Framework installata nel computer Windows. |
| OldestMissingSecurityUpdateBucket | I valori possibili sono:<br> *Recente* se il valore è inferiore a 30 giorni<br> *30 giorni fa*<br> *60 giorni fa*<br> *90 giorni fa*<br> *120 giorni fa*<br> *150 giorni fa*<br> *180 giorni fa*<br> *Precedente* quando il valore è maggiore di 180 giorni | 
| OldestMissingSecurityUpdateInDays | Numero totale di giorni per l'aggiornamento meno recente rilevato come applicabile che non è stato installato. |
| OsVersion | Versione del sistema operativo. |
| OtherUpdatesMissing | Numero di aggiornamenti rilevati mancanti. |
| Risorsa |  Nome della risorsa. | 
| ResourceGroup | Nome del gruppo di risorse di cui è membro la risorsa. |
| ResourceId | Identificatore univoco della risorsa a cui è associato il record. |
| ResourceProvider | Specifica il provider di risorse. |
| ResourceType | Nome del tipo di risorsa. |
| RestartPending | *Vero* o *Falso*. |
| SecurityUpdatesMissing | Numero di aggiornamenti di sicurezza mancanti applicabili.| 
| SourceComputerId | Identificatore univoco della macchina virtuale. |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Identificatore univoco per la sottoscrizione di Azure. |
| TimeGenerated | Data e ora di creazione del record. |
| TotalUpdatesMissing | Numero totale di aggiornamenti mancanti applicabili. | 
| Type | *UpdateSummary* |
| VMUUID | Identificatore univoco della macchina virtuale. |
| WindowsUpdateAgentVersion | Versione dell'agente di Windows Update. |
| WindowsUpdateSetting | Mostra lo stato dell'agente di Windows Update. I valori possibili sono:<br> *Installazione pianificata*<br> *Notifica prima dell'installazione*<br> Errore restituito dall'agente WUA non integro. | 
| WSUSServer | Mostra gli errori se l'agente di Windows Update presenta un problema per facilitare la risoluzione dei problemi. |
| _ResourceId | Identificatore univoco della risorsa a cui è associato il record. |

## <a name="sample-queries"></a>Query di esempio

Nelle sezioni seguenti vengono fornite query di log di esempio per i record di aggiornamento raccolti per la gestione degli aggiornamenti.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Verificare l'onboarding di computer non di Azure

Per verificare che i computer connessi direttamente comunichino con i log di Monitoraggio di Azure, eseguire una delle ricerche di log seguenti.

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

In un computer Windows è possibile esaminare le informazioni seguenti per verificare la connettività dell'agente con i log di Monitoraggio di Azure:On a Windows computer, you can review the following information to verify agent connectivity with Azure Monitor logs:

1. Aprire **Microsoft Monitoring Agent** nel Pannello di controllo. Nella scheda **Analisi dei log di Azure** l'agente visualizza un messaggio per indicare che **Microsoft Monitoring Agent ha eseguito la connessione a Log Analytics**.
2. Aprire il registro eventi di Windows. Passare a **Registri applicazioni e servizi\Operations Manager** e cercare gli ID evento 3000 e 5002 del **connettore del servizio** di origine. Questi eventi indicano che il computer ha eseguito la registrazione all'area di lavoro Log Analytics e sta ricevendo la configurazione.

Se l'agente non è in grado di comunicare con i log di Monitoraggio di Azure e l'agente è configurato per comunicare con Internet tramite un firewall o un server proxy, verificare che il firewall o il server proxy sia configurato correttamente. Per sapere come verificare se il firewall o il server proxy è configurato correttamente, vedere [Connettere computer Windows al servizio Log Analytics in Azure](../azure-monitor/platform/agent-windows.md) oppure [Raccogliere dati dal computer Linux ospitato nell'ambiente in uso](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se i sistemi Linux sono configurati per la comunicazione con un proxy o il gateway di Log Analytics e si sta eseguendo l'onboarding di questa soluzione, aggiornare le autorizzazioni *proxy.conf* per concedere al gruppo omiuser le necessarie autorizzazioni di lettura per il file usando i comandi seguenti:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Gli agenti Linux appena aggiunti visualizzano lo stato **Aggiornato** dopo l'esecuzione di una valutazione. Il processo può richiedere fino a 6 ore.

Per verificare che un gruppo di gestione di Operations Manager stia comunicando con i log di Monitoraggio di Azure, vedere [Convalidare l'integrazione](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)di Operations Manager con i log di Monitoraggio di Azure.

### <a name="single-azure-vm-assessment-queries-windows"></a>Singole query di valutazione delle macchine virtuali di Azure (Windows)

Sostituire il valore VMUUID con il GUID VM della macchina virtuale per cui si esegue la query. È possibile trovare il VMUUID che deve essere usato eseguendo la query seguente nei log di Monitoraggio di Azure:You can find the VMUUID that should be used by running the following query in Azure Monitor logs:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

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

Per alcune distribuzioni Linux, esiste una mancata corrispondenza di endianness con il valore VMUUID proveniente da Azure Resource Manager e da ciò che viene archiviato nei log di Monitoraggio di Azure.For some Linux distros, there is a [endianness](https://en.wikipedia.org/wiki/Endianness) mismatch with the VMUUID value that comes from Azure Resource Manager and what is stored in Azure Monitor logs. La query seguente verifica la presenza di una corrispondenza in uno degli ordini di byte. Sostituire i valori VMUUID con il formato big-endian e little-endian del GUID in modo che i risultati vengano restituiti correttamente. È possibile trovare il VMUUID che deve essere usato eseguendo la query seguente nei log di Monitoraggio di Azure:You can find the VMUUID that should be used by running the following query in Azure Monitor logs:`Update | where Computer == "<machine name>"
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

* Usare le ricerche di log nei log di Monitoraggio di [Azure](../log-analytics/log-analytics-log-searches.md) per visualizzare i dati di aggiornamento dettagliati.
* [Creare avvisi](automation-tutorial-update-management.md#configure-alerts) per lo stato di distribuzione degli aggiornamenti.
