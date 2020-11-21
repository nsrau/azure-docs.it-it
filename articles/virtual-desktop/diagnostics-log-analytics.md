---
title: Log Analytics di diagnostica desktop virtuale di Windows-Azure
description: Come usare log Analytics con la funzionalità diagnostica desktop virtuali di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8b696b175c4ef841eef1a51f1d357d1781cba7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018291"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Usare Log Analytics per la funzionalità di diagnostica

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).

Desktop virtuale di Windows USA monitoraggio di [Azure](../azure-monitor/overview.md) per il monitoraggio e gli avvisi come molti altri servizi di Azure. Questo permette agli amministratori di identificare i problemi tramite un'unica interfaccia. Il servizio crea log attività per le azioni utente e amministrative. Ogni log attività rientra nelle categorie seguenti:

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

Le connessioni che non raggiungono Desktop virtuale Windows non vengono visualizzate nei risultati della diagnostica perché il servizio dei ruoli di diagnostica fa parte del servizio Desktop virtuale Windows. Problemi di connessione di desktop virtuali Windows possono verificarsi quando l'utente riscontra problemi di connettività di rete.

Monitoraggio di Azure consente di analizzare i dati di desktop virtuali Windows ed esaminare i contatori delle prestazioni delle macchine virtuali (VM), il tutto nello stesso strumento. In questo articolo vengono illustrate altre informazioni su come abilitare la diagnostica per l'ambiente di desktop virtuale di Windows.

>[!NOTE]
>Per informazioni su come monitorare le VM in Azure, vedere [monitoraggio delle macchine virtuali di Azure con monitoraggio di Azure](../azure-monitor/insights/monitor-vm-azure.md). Assicurarsi inoltre di [esaminare le soglie dei contatori delle prestazioni](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) per comprendere meglio l'esperienza utente nell'host sessione.

## <a name="before-you-get-started"></a>Prima di iniziare

Prima di poter usare Log Analytics, è necessario creare un'area di lavoro. A tale scopo, seguire le istruzioni riportate in uno dei due articoli seguenti:

- Se si preferisce usare portale di Azure, vedere [creare un'area di lavoro log Analytics in portale di Azure](../azure-monitor/learn/quick-create-workspace.md).
- Se si preferisce PowerShell, vedere [creare un'area di lavoro log Analytics con PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

Dopo aver creato l'area di lavoro, seguire le istruzioni in [connettere i computer Windows a monitoraggio di Azure](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) per ottenere le informazioni seguenti:

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
>Log Analytics offre la possibilità di trasmettere dati a [Hub eventi](../event-hubs/event-hubs-about.md) o di archiviarli in un account di archiviazione. Per altre informazioni su questa funzionalità, vedere [trasmettere i dati di monitoraggio di Azure a un hub eventi](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) e [archiviare i log delle risorse di Azure nell'account di archiviazione](../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

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

2. Cercare e selezionare **Monitoraggio**.

3. Selezionare **Log**.

4. Seguire le istruzioni nella pagina registrazione per impostare l'ambito della query.

5. Si è pronti per eseguire query di diagnostica. Tutte le tabelle di diagnostica hanno un prefisso "WVD".

>[!NOTE]
>Per informazioni più dettagliate sulle tabelle archiviate nei log di monitoraggio di Azure, vedere la pagina relativa al [recinto dei dati di monitoraggio di Azure](/azure/azure-monitor/reference/). Tutte le tabelle relative al desktop virtuale Windows sono contrassegnate con l'etichetta "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Cadenza per l'invio di eventi di diagnostica

Gli eventi di diagnostica vengono inviati al Log Analytics al termine dell'operazione.

Log Analytics solo i report in questi stati intermedi per le attività di connessione:

- Avviato: quando un utente seleziona e si connette a un'app o a un desktop nel client di Desktop remoto.
- Connesso: quando l'utente si connette correttamente alla macchina virtuale in cui è ospitata l'app o il desktop.
- Completato: quando l'utente o il server disconnette la sessione in cui ha avuto luogo l'attività.

## <a name="example-queries"></a>Query di esempio

Accedere a query di esempio tramite il monitoraggio di Azure Log Analytics interfaccia utente:
1. Passare all'area di lavoro Log Analytics, quindi selezionare **log**. L'interfaccia utente di query di esempio viene mostrata automaticamente.
1. Modificare il filtro in **Category**.
1. Selezionare **desktop virtuale Windows** per esaminare le query disponibili.
1. Selezionare **Esegui** per eseguire la query selezionata.

Per altre informazioni sull'interfaccia di query di esempio, vedere [query salvate in monitoraggio di Azure log Analytics](../azure-monitor/log-query/example-queries.md).

L'elenco di query seguente consente di esaminare le informazioni di connessione o i problemi per un singolo utente. È possibile eseguire queste query nell' [editor di query log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query). Per ogni query, sostituire `userupn` con l'UPN dell'utente che si desidera cercare.


Per trovare tutte le connessioni per un singolo utente:

```kusto
WVDConnections
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

Per determinare se si è verificato un errore specifico per altri utenti:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- Quando un utente apre desktop completo, l'utilizzo dell'app nella sessione non viene rilevata come Checkpoint nella tabella WVDCheckpoints.
>- La colonna ResourcesAlias nella tabella WVDConnections indica se un utente si è connesso a un desktop completo o a un'app pubblicata. La colonna Mostra solo la prima app aperta durante la connessione. Tutte le app pubblicate aperte dall'utente vengono rilevate in WVDCheckpoints.
>- La tabella WVDErrors Mostra gli errori di gestione, i problemi di registrazione dell'host e altri problemi che si verificano quando l'utente sottoscrive un elenco di app o desktop.
>- WVDErrors consente di identificare i problemi che possono essere risolti dalle attività amministrative. Il valore in ServiceError indica sempre "false" per questi tipi di problemi. Se ServiceError = "true", sarà necessario inoltrare il problema a Microsoft. Assicurarsi di fornire il CorrelationID per gli errori escalation.

## <a name="next-steps"></a>Passaggi successivi

Per esaminare gli scenari di errore comuni che possono essere identificati dalla funzionalità di diagnostica, vedere [identificare e diagnosticare i problemi](diagnostics-role-service.md#common-error-scenarios).