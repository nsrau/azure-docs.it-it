---
title: Log Analytics di diagnostica desktop virtuale di Windows-Azure
description: Come usare log Analytics con la funzionalità diagnostica desktop virtuali di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 99a9e68a2e0c39364cc5105f230b00ffb90d867d
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888805"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Usare Log Analytics per la funzionalità di diagnostica

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Spring 2020 con Azure Resource Manager oggetti desktop virtuali di Windows. Se si usa la versione 2019 del desktop virtuale di Windows senza Azure Resource Manager oggetti, vedere [questo articolo](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).
>
> L'aggiornamento di Spring 2020 per desktop virtuale di Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile usarla per carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Desktop virtuale di Windows USA monitoraggio di [Azure](../azure-monitor/overview.md) per il monitoraggio e gli avvisi come molti altri servizi di Azure. Ciò consente agli amministratori di identificare i problemi tramite un'unica interfaccia. Il servizio crea log attività per le azioni utente e amministrative. Ogni log attività rientra nelle categorie seguenti:  

- Attività di gestione:
    - Verificare se i tentativi di modificare gli oggetti desktop virtuali di Windows tramite API o PowerShell hanno esito positivo. Ad esempio, un utente può creare correttamente un pool di host usando PowerShell?
- Feed 
    - Gli utenti possono sottoscrivere correttamente le aree di lavoro? 
    - Gli utenti visualizzano tutte le risorse pubblicate nel client di Desktop remoto?
- Connessioni: 
    - Quando gli utenti avviano e completano le connessioni al servizio. 
- Registrazione host: 
    - L'host sessione è stato registrato correttamente con il servizio al momento della connessione?
- Errors: 
    - Gli utenti riscontrano problemi con attività specifiche? Questa funzionalità consente di generare una tabella che tiene traccia dei dati di attività a condizione che le informazioni vengano unite alle attività.
- Checkpoint  
    - Passaggi specifici del ciclo di vita di un'attività raggiunta. Ad esempio, durante una sessione, un utente è stato sottoposta a bilanciamento del carico in un particolare host, l'utente ha eseguito l'accesso durante una connessione e così via.

Le connessioni che non raggiungono il desktop virtuale di Windows non verranno visualizzate nei risultati della diagnostica perché il servizio ruolo di diagnostica fa parte del desktop virtuale di Windows. Problemi di connessione di desktop virtuali Windows possono verificarsi quando l'utente riscontra problemi di connettività di rete.

Monitoraggio di Azure consente di analizzare i dati di desktop virtuali Windows ed esaminare i contatori delle prestazioni delle macchine virtuali (VM), il tutto nello stesso strumento. In questo articolo vengono illustrate altre informazioni su come abilitare la diagnostica per l'ambiente di desktop virtuale di Windows. 

>[!NOTE] 
>Per informazioni su come monitorare le VM in Azure, vedere [monitoraggio delle macchine virtuali di Azure con monitoraggio di Azure](../azure-monitor/insights/monitor-vm-azure.md). Assicurarsi inoltre di [esaminare le soglie dei contatori delle prestazioni](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) per comprendere meglio l'esperienza utente nell'host sessione.

## <a name="before-you-get-started"></a>Prima di iniziare

Prima di poter usare Log Analytics, è necessario creare un'area di lavoro. A tale scopo, seguire le istruzioni riportate in uno dei due articoli seguenti:

- Se si preferisce usare portale di Azure, vedere [creare un'area di lavoro log Analytics in portale di Azure](../azure-monitor/learn/quick-create-workspace.md).
- Se si preferisce PowerShell, vedere [creare un'area di lavoro log Analytics con PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

Dopo aver creato l'area di lavoro, seguire le istruzioni in [connettere i computer Windows a monitoraggio di Azure](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) per ottenere le informazioni seguenti:

- ID dell'area di lavoro
- Chiave primaria dell'area di lavoro

Queste informazioni saranno necessarie in un secondo momento nel processo di installazione.

Assicurarsi di controllare la gestione delle autorizzazioni per monitoraggio di Azure per abilitare l'accesso ai dati per gli utenti che monitorano e gestiscono l'ambiente desktop virtuale Windows. Per altre informazioni, vedere [Introduzione a ruoli, autorizzazioni e sicurezza con monitoraggio di Azure](../azure-monitor/platform/roles-permissions-security.md). 

## <a name="push-diagnostics-data-to-your-workspace"></a>Eseguire il push dei dati di diagnostica nell'area di lavoro

È possibile eseguire il push dei dati di diagnostica dagli oggetti desktop virtuali di Windows nell'Log Analytics per l'area di lavoro. Questa funzionalità può essere configurata immediatamente quando si creano gli oggetti per la prima volta.

Per configurare Log Analytics per un nuovo oggetto:

1. Accedere al portale di Azure e passare a **desktop virtuale di Windows**. 

2. Passare all'oggetto, ad esempio un pool di host, un gruppo di app o un'area di lavoro, per cui si vogliono acquisire i log e gli eventi. 

3. Selezionare **impostazioni di diagnostica** nel menu sul lato sinistro della schermata. 

4. Selezionare **Aggiungi impostazioni di diagnostica** nel menu visualizzato sul lato destro dello schermo. 
   
    Le opzioni visualizzate nella pagina impostazioni di diagnostica variano a seconda del tipo di oggetto che si sta modificando.

    Ad esempio, quando si Abilita la diagnostica per un gruppo di app, verranno visualizzate le opzioni per configurare i checkpoint, gli errori e la gestione. Per le aree di lavoro, queste categorie configurano un feed per tenere traccia del momento in cui gli utenti sottoscrivono l'elenco di app. Per altre informazioni sulle impostazioni di diagnostica [, vedere creare un'impostazione di diagnostica per raccogliere i log e le metriche delle risorse in Azure](../azure-monitor/platform/diagnostic-settings.md). 

     >[!IMPORTANT] 
     >Ricordarsi di abilitare la diagnostica per ogni oggetto Azure Resource Manager che si desidera monitorare. I dati saranno disponibili per le attività dopo l'abilitazione della diagnostica. La prima configurazione potrebbe richiedere alcune ore.  

5. Immettere un nome per la configurazione delle impostazioni, quindi selezionare **Invia a log Analytics**. Il nome usato non deve avere spazi e deve essere conforme alle [convenzioni di denominazione di Azure](../azure-resource-manager/management/resource-name-rules.md). Come parte dei log, è possibile selezionare tutte le opzioni che si desidera aggiungere al Log Analytics, ad esempio Checkpoint, errore, gestione e così via.

6. Selezionare **Salva**.

>[!NOTE]
>Log Analytics offre la possibilità di trasmettere dati a [Hub eventi](../event-hubs/event-hubs-about.md) o di archiviarli in un account di archiviazione. Per altre informazioni su questa funzionalità, vedere [trasmettere i dati di monitoraggio di Azure a un hub eventi](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) e [archiviare i log delle risorse di Azure nell'account di archiviazione](../azure-monitor/platform/resource-logs-collect-storage.md). 

## <a name="how-to-access-log-analytics"></a>Come accedere a Log Analytics

È possibile accedere alle aree di lavoro Log Analytics nel portale di Azure o nel monitoraggio di Azure.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Accedere a Log Analytics in un'area di lavoro Log Analytics

1. Accedere al portale di Azure.

2. Cercare **log Analytics area di lavoro**. 

3. In servizi selezionare **log Analytics aree di lavoro**. 
   
4. Nell'elenco selezionare l'area di lavoro configurata per l'oggetto desktop virtuale di Windows.

5. Una volta nell'area di lavoro, selezionare **logs**. È possibile filtrare l'elenco dei menu con la funzione **Search** . 

### <a name="access-log-analytics-on-azure-monitor"></a>Accesso Log Analytics su monitoraggio di Azure

1. Accedere al portale di Azure

2. Cercare e selezionare **monitoraggio**. 

3. Selezionare **log**.

4. Seguire le istruzioni nella pagina registrazione per impostare l'ambito della query.  

5. Si è pronti per eseguire query di diagnostica. Tutte le tabelle di diagnostica hanno un prefisso "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Cadenza per l'invio di eventi di diagnostica

Gli eventi di diagnostica vengono inviati al Log Analytics al termine dell'operazione.

Log Analytics solo i report in questi stati intermedi per le attività di connessione:

- Started
- Connesso
- Completi

## <a name="example-queries"></a>Query di esempio

Le query di esempio seguenti mostrano come la funzionalità di diagnostica genera un report per le attività più frequenti nel sistema.

Per ottenere un elenco delle connessioni effettuate dagli utenti, eseguire questo cmdlet:

```kusto
WVDConnections 
| project-away TenantId,SourceSystem 
| summarize arg_max(TimeGenerated, *), StartTime =  min(iff(State== 'Started', TimeGenerated , datetime(null) )), ConnectTime = min(iff(State== 'Connected', TimeGenerated , datetime(null) ))   by CorrelationId 
| join kind=leftouter ( 
    WVDErrors 
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId 
    ) on CorrelationId     
| join kind=leftouter ( 
   WVDCheckpoints 
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId 
   | mv-apply Checkpoints on 
    ( 
        order by todatetime(Checkpoints['Time']) asc 
        | summarize Checkpoints=makelist(Checkpoints) 
    ) 
   ) on CorrelationId 
| project-away CorrelationId1, CorrelationId2 
| order by  TimeGenerated desc 
```

Per visualizzare l'attività del feed degli utenti:

```kusto
WVDFeeds  
| project-away TenantId,SourceSystem  
| join kind=leftouter (  
    WVDErrors  
    |summarize Errors=makelist(pack('Code', Code, 'CodeSymbolic', CodeSymbolic, 'Time', TimeGenerated, 'Message', Message ,'ServiceError', ServiceError, 'Source', Source)) by CorrelationId  
    ) on CorrelationId      
| join kind=leftouter (  
   WVDCheckpoints  
   | summarize Checkpoints=makelist(pack('Time', TimeGenerated, 'Name', Name, 'Parameters', Parameters, 'Source', Source)) by CorrelationId  
   | mv-apply Checkpoints on  
    (  
        order by todatetime(Checkpoints['Time']) asc  
        | summarize Checkpoints=makelist(Checkpoints)  
    )  
   ) on CorrelationId  
| project-away CorrelationId1, CorrelationId2  
| order by  TimeGenerated desc 
```

Per trovare tutte le connessioni per un singolo utente: 

```kusto
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
```
 

Per trovare il numero di volte in cui un utente si è connesso al giorno:

```kusto
WVDConnections 
|where UserName == "userupn" 
|take 100 
|sort by TimeGenerated asc, CorrelationId 
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d) 
```
 

Per trovare la durata della sessione in base all'utente:

```kusto
let Events = WVDConnections | where UserName == "userupn" ; 
Events 
| where State == "Connected" 
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated 
| join (Events 
| where State == "Completed" 
| project EndTime=TimeGenerated, CorrelationId) 
on CorrelationId 
| project Duration = EndTime - StartTime, ResourceAlias 
| sort by Duration asc 
```

Per individuare gli errori per un utente specifico:

```kusto
WVDErrors
| where UserName == "userupn" 
|take 100
```

Per determinare se si è verificato un errore specifico:

```kusto
WVDErrors 
| where CodeSymbolic =="ErrorSymbolicCode" 
| summarize count(UserName) by CodeSymbolic 
```

Per trovare l'occorrenza di un errore in tutti gli utenti:

```kusto
WVDErrors 
| where ServiceError =="false" 
| summarize usercount = count(UserName) by CodeSymbolic 
| sort by usercount desc
| render barchart 
```

>[!NOTE]
>La tabella più importante per la risoluzione dei problemi è WVDErrors. Usare questa query per comprendere quali problemi si verificano per le attività dell'utente, ad esempio le connessioni o i feed quando un utente sottoscrive l'elenco di app o desktop. Nella tabella vengono visualizzati gli errori di gestione e i problemi di registrazione dell'host.
>
>Durante l'anteprima pubblica, se è necessario assistenza per la risoluzione di un problema, assicurarsi di assegnare a CorrelationID l'errore nella richiesta della guida. Assicurarsi inoltre che il valore di errore del servizio indichi sempre ServiceError = "false". Un valore "false" indica che il problema può essere risolto da un'attività di amministrazione alla fine. Se ServiceError = "true", sarà necessario inoltrare il problema a Microsoft.

## <a name="next-steps"></a>Passaggi successivi 

Per esaminare gli scenari di errore comuni che possono essere identificati dalla funzionalità di diagnostica, vedere [identificare e diagnosticare i problemi](diagnostics-role-service.md#common-error-scenarios).
