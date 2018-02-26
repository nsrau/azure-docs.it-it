---
title: Panoramica di Funzioni permanenti - Azure (anteprima)
description: Introduzione all'estensione Funzioni permanenti per Funzioni di Azure.
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: f1def2a43edee58bc8b5a33880e206130a1b4687
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="durable-functions-overview-preview"></a>Panoramica di Funzioni permanenti (anteprima)

*Funzioni permanenti* è un'estensione di [Funzioni di Azure](functions-overview.md) e [Processi Web di Azure](../app-service/web-sites-create-web-jobs.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii.

L'estensione consente di definire flussi di lavoro con stato in un nuovo tipo di funzione denominata *funzione di orchestrazione*. Ecco alcuni vantaggi delle funzioni di orchestrazione:

* Definiscono i flussi di lavoro nel codice. Non sono necessari schemi JSON o finestre di progettazione.
* Possono chiamare altre funzioni in modo sincrono e asincrono. L'output delle funzioni chiamate può essere salvato in variabili locali.
* Impostano automaticamente checkpoint dello stato di avanzamento ogni volta che la funzione è in attesa. Lo stato locale non va mai perso se il processo viene riciclato o la macchina virtuale viene riavviata.

> [!NOTE]
> Funzioni permanenti è disponibile in anteprima ed è un'estensione avanzata per Funzioni di Azure che non è adatta per tutte le applicazioni. La parte restante di questo articolo presuppone una conoscenza avanzata dei concetti di [Funzioni di Azure](functions-overview.md) e delle problematiche relative allo sviluppo di applicazioni senza server.

Il caso d'uso principale per Funzioni permanenti è la semplificazione di problemi complessi di coordinamento con stato nelle applicazioni senza server. Le sezioni seguenti descrivono alcuni modelli di applicazione tipici che possono trarre vantaggio da Funzioni permanenti.

## <a name="pattern-1-function-chaining"></a>Modello 1: Concatenamento di funzioni

Il *concatenamento di funzioni* fa riferimento al modello di esecuzione di una sequenza di funzioni in un ordine particolare. Spesso l'output di una funzione deve essere applicato all'input di un'altra funzione.

![Diagramma di concatenamento funzioni](media/durable-functions-overview/function-chaining.png)

Funzioni permanenti consente di implementare questo modello nel codice in modo conciso.

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

I valori "F1", "F2", "F3" e "F4" sono i nomi delle altre funzioni nell'app per le funzioni. Il flusso di controllo viene implementato usando normali costrutti di codice imperativo. Questo vuol dire che il codice viene eseguito dall'alto verso il basso e può implicare semantica esistente del flusso di controllo, ad esempio istruzioni condizionali e cicli.  La logica di gestione degli errori può essere inclusa in blocchi try/catch/finally.

Il parametro `ctx` ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) fornisce metodi per richiamare altre funzioni tramite il nome, il passaggio di parametri e la restituzione dell'output della funzione. Ogni volta che il codice chiama `await`, Durable Functions framework *imposta checkpoint* sullo stato di avanzamento dell'istanza della funzione corrente. Se il processo o la macchina virtuale viene riciclato durante l'esecuzione, l'istanza della funzione riprende dalla chiamata `await` precedente. Altre informazioni su questo comportamento di riavvio sono disponibili più avanti.

## <a name="pattern-2-fan-outfan-in"></a>Modello 2: Fan-out/fan-in

*Fan-out/fan-in* fa riferimento al modello di eseguire più funzioni in parallelo e quindi restare in attesa del completamento di tutte.  Spesso sui risultati restituiti dalle funzioni vengono eseguite alcune operazioni di aggregazione.

![Diagramma di fan-out/fan-in](media/durable-functions-overview/fan-out-fan-in.png)

Nelle funzioni normali, il fan-out può essere effettuato facendo in modo che la funzione invii più messaggi a una coda. Effettuare di nuovo il fan-in, tuttavia, è molto più complesso. È necessario scrivere codice per rilevare il completamento delle funzioni attivate dalla coda e archiviare l'output delle funzioni. L'estensione Funzioni permanenti gestisce questo modello con codice relativamente semplice.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

Le operazioni di fan-out vengono distribuite in più istanze della funzione `F2` e vengono monitorate tramite un elenco dinamico di attività. Viene chiamata l'API .NET `Task.WhenAll` per attendere il completamento di tutte le funzioni chiamate. Quindi, gli output della funzione `F2` vengono aggregati dall'elenco di attività dinamiche e passati alla funzione `F3`.

L'impostazione automatica di checkpoint che avviene alla chiamata di `await` su `Task.WhenAll` assicura che qualsiasi riavvio o arresto anomalo del sistema durante l'esecuzione non richieda il riavvio di attività già completate.

## <a name="pattern-3-async-http-apis"></a>Modello 3: API HTTP asincrone

Il terzo modello riguarda il problema di coordinare lo stato di operazioni a esecuzione prolungata con client esterni. Un modo comune per implementare questo modello consiste nel fare in modo che l'azione a esecuzione prolungata sia attivata da una chiamata HTTP, reindirizzando quindi il client a un endpoint di stato su cui possa eseguire il polling per avere informazioni sul completamento dell'operazione.

![Diagramma API HTTP](media/durable-functions-overview/async-http-api.png)

Funzioni permanenti integra API incorporate che semplificano il codice da scrivere per l'interazione con l'esecuzione di funzioni a esecuzione prolungata. Gli [esempi](durable-functions-install.md) mostrano un semplice comando REST che può essere usato per avviare nuove istanze della funzione di orchestrazione. Dopo l'avvio di un'istanza, l'estensione espone API HTTP webhook che eseguono query sullo stato della funzione di orchestrazione. L'esempio seguente mostra i comandi REST per avviare un agente di orchestrazione e per eseguire una query sul relativo stato. Per maggiore chiarezza, alcuni dettagli sono omessi dall'esempio.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Poiché lo stato è gestito dal runtime di Funzioni permanenti, non è necessario implementare un meccanismo personalizzato di monitoraggio dello stato.

Anche se l'estensione Funzioni permanenti ha webhook incorporati per la gestione delle orchestrazioni a esecuzione prolungata, è possibile implementare questo modello usando trigger di funzione personalizzati ad esempio HTTP, coda o Hub eventi, e il binding `orchestrationClient`. Ad esempio, è possibile usare un messaggio di coda per attivare la terminazione.  In alternativa, è possibile usare un trigger HTTP protetto da un criterio di autenticazione di Azure Active Directory anziché webhook incorporati che per l'autenticazione usano una chiave generata. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

Il parametro `starter` di [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) è un valore proveniente dal binding di output `orchestrationClient`, incluso nell'estensione Funzioni permanenti. Fornisce metodi per avviare istanze di una funzione di orchestrazione, inviarle eventi, terminarla o eseguire query su istanze della funzione di orchestrazione nuove o esistenti. Nell'esempio precedente, una funzione attivata tramite HTTP accetta un valore `functionName` dall'URL in ingresso e lo passa a [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Questa API di binding restituisce quindi una risposta che contiene un'intestazione `Location` e ulteriori informazioni sull'istanza, che in seguito possono essere usate per cercare lo stato dell'istanza avviata o terminarla.

## <a name="pattern-4-stateful-singletons"></a>Modello 4: Singleton con stato

La maggior parte delle funzioni ha un inizio e una fine espliciti e non interagisce direttamente con origini evento esterne. Tuttavia, le orchestrazioni supportano un modello [singleton con stato](durable-functions-singletons.md) che consente loro di fungere da [attori](https://en.wikipedia.org/wiki/Actor_model) affidabili nell'elaborazione distribuita.

Il diagramma seguente illustra una funzione che viene eseguita in un ciclo infinito durante l'elaborazione degli eventi ricevuti da origini esterne.

![Diagramma Singleton con stato](media/durable-functions-overview/stateful-singleton.png)

Anche se Funzioni permanenti non è un'implementazione del modello basato su attori, le funzioni di orchestrazione presentano molte delle stesse caratteristiche di runtime. Sono ad esempio a esecuzione prolungata (potenzialmente a ciclo infinito), con stato, affidabili, a thread singolo, con trasparenza di posizione e indirizzabili a livello globale. Questo rende le funzioni di orchestrazione utili per gli scenari di tipo "attore".

Le funzioni ordinarie sono senza stato e pertanto non adatte per implementare un modello singleton con stato. L'estensione Funzioni permanenti, tuttavia, rende il modello singleton con stato relativamente semplice da implementare. Il codice seguente è una funzione di orchestrazione semplice che implementa un contatore.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int counterState = ctx.GetInput<int>();

    string operation = await ctx.WaitForExternalEvent<string>("operation");
    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }

    ctx.ContinueAsNew(counterState);
}
```

Questo codice rappresenta una "orchestrazione eterna" &mdash; ovvero che inizia e non termina mai. Esegue i passaggi seguenti:

* Inizia con un valore di input in `counterState`.
* Attende per un periodo illimitato un messaggio denominato `operation`.
* Esegue logica per aggiornare il proprio stato locale.
* Riavvia se stessa chiamando `ctx.ContinueAsNew`.
* Di nuovo attende per un periodo illimitato l'operazione successiva.

## <a name="pattern-5-human-interaction"></a>Modello 5: Interazione umana

Molti processi comportano un'interazione umana di qualche tipo. Il problema quando un processo automatizzato coinvolge delle persone è che queste ultime non sempre sono disponibili e reattive quanto i servizi cloud. I processi automatizzati devono tenerne conto e spesso lo fanno usando timeout e logica di compensazione.

Un esempio di processo di business con interazione umana è un processo di approvazione. Ad esempio, per una nota spese che supera un determinato importo può essere necessaria l'approvazione da parte di un manager. Se il manager non approva entro 72 ore (ad esempio se è in ferie), viene avviato un processo di escalation per ottenere l'approvazione da parte di un altro utente (ad esempio il manager del manager).

![Diagramma di interazione umana](media/durable-functions-overview/approval.png)

Questo modello può essere implementato usando una funzione di orchestrazione. L'agente di orchestrazione userà un [timer permanente](durable-functions-timers.md) per richiedere l'approvazione ed eseguire l'escalation in caso di timeout. Attenderà un [evento esterno](durable-functions-external-events.md), che potrebbe essere la notifica generata da un'interazione umana.

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

Il timer permanente viene creato chiamando `ctx.CreateTimer`. La notifica viene ricevuta da `ctx.WaitForExternalEvent`. Viene chiamato `Task.WhenAny` per decidere se eseguire l'escalation (si verifica prima il timeout) o elaborare l'approvazione (l'approvazione viene ricevuta prima del timeout).

## <a name="the-technology"></a>La tecnologia

L'estensione Funzioni permanenti è basata su [Durable Task Framework](https://github.com/Azure/durabletask), una libreria open source su GitHub per la creazione di orchestrazioni di attività permanenti. Così come Funzioni di Azure è l'evoluzione senza server di Processi Web di Azure, Funzioni permanenti è l'evoluzione senza server di Durable Task Framework. Durable Task Framework è molto usato in Microsoft e all'esterno anche per automatizzare i processi cruciali. È una scelta ideale per l'ambiente senza server di Funzioni di Azure.

### <a name="event-sourcing-checkpointing-and-replay"></a>Origine eventi, impostazione di checkpoint e riesecuzione

Le funzioni di orchestrazione mantengono in modo affidabile il proprio stato di esecuzione usando un modello di progettazione cloud noto come [Origine eventi](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Anziché archiviare direttamente lo stato *corrente* di un'orchestrazione, l'estensione Funzioni permanenti usa un archivio di solo accodamento per registrare la *serie completa di azioni* eseguita dall'orchestrazione di funzioni. Questo offre numerosi vantaggi, tra cui il miglioramento delle prestazioni, della scalabilità e della velocità di risposta rispetto al dump completo dello stato di runtime. Inoltre, consente di garantire coerenza finale ai dati transazionali e mantenere dati di cronologia e audit trail completi, che consentono di eseguire azioni di compensazione affidabili.

L'uso dell'origine eventi da parte di questa estensione è trasparente. Dietro le quinte, l'operatore `await` in una funzione di orchestrazione restituisce il controllo del thread di orchestrazione al dispatcher di Durable Task Framework. Il dispatcher quindi esegue il commit di eventuali nuove azioni pianificate dalla funzione di orchestrazione (ad esempio, la chiamata di una o più funzioni figlio o la pianificazione di un timer permanente) in un archivio. Questa azione di commit trasparente viene accodata alla *cronologia di esecuzione* dell'istanza di orchestrazione. La cronologia viene archiviata in una tabella di archiviazione. L'azione di commit aggiunge quindi messaggi a una coda per pianificare le operazioni effettive. A questo punto, la funzione di orchestrazione può essere scaricata dalla memoria. Se si usa il piano a consumo di Funzioni di Azure, la fatturazione per la funzione si interrompe.  Quando ci sono altre operazioni da eseguire, la funzione viene riavviata e il relativo stato viene ricostruito.

Quando a una funzione di orchestrazione vengono assegnate altre operazioni da eseguire (ad esempio, viene ricevuto un messaggio di risposta o un timer permanente scade), l'agente di orchestrazione si riattiva ed esegue nuovamente l'intera funzione dall'inizio per ricompilare lo stato locale. Se durante la riesecuzione il codice tenta di chiamare una funzione o di eseguire altro lavoro asincrono, Durable Task Framework esamina la *cronologia di esecuzione* dell'orchestrazione corrente. Se rileva che la funzione di attività è già stata eseguita e ha restituito un risultato, riesegue il risultato della funzione e l'esecuzione del codice dell'agente di orchestrazione continua. Il processo continua finché il codice della funzione non giunge a un punto in cui termina o viene pianificato nuovo lavoro asincrono.

### <a name="orchestrator-code-constraints"></a>Vincoli del codice dell'agente di orchestrazione

Questo comportamento di riesecuzione crea vincoli rispetto al tipo di codice che è possibile scrivere in una funzione di orchestrazione. Ad esempio, il codice dell'agente di orchestrazione deve essere deterministico, perché verrà rieseguito più volte e deve produrre lo stesso risultato ogni volta. Per l'elenco completo dei vincoli, vedere la sezione relativa ai [vincoli del codice dell'agente di orchestrazione](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) nell'articolo su **impostazione di checkpoint e riavvio**.

## <a name="language-support"></a>Supporto per le lingue

Attualmente C# è l'unico linguaggio supportato per Funzioni permanenti. Questo include le funzioni di orchestrazione e le funzioni di attività. In futuro verrà aggiunto il supporto per tutti i linguaggi supportati da Funzioni di Azure. Per informazioni sullo stato attuale del supporto di linguaggi aggiuntivi, vedere l'[elenco di problemi del repository GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) di Funzioni di Azure.

## <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica

L'estensione Funzioni permanenti invia automaticamente dati di rilevamento strutturati ad [Application Insights](functions-monitoring.md) quando l'app per le funzioni è configurata con una chiave di strumentazione di Application Insights. Questi dati di rilevamento possono essere usati per monitorare il comportamento e lo stato di avanzamento delle orchestrazioni.

Ecco un esempio di come si presentano gli eventi di rilevamento nel portale Application Insights usando [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![Risultati della query App Insights](media/durable-functions-overview/app-insights-1.png)

Il campo `customDimensions` di ogni voce di log contiene numerosi dati strutturati utili. Ecco un esempio di una voce di questo tipo completamente espansa.

![Campo customDimensions nella query App Insights](media/durable-functions-overview/app-insights-2.png)

A causa del comportamento di riproduzione del dispatcher di Durable Task Framework, probabilmente saranno presenti voci di log ridondanti per le azioni rieseguite. Questo può essere utile per comprendere il comportamento di riproduzione del modulo principale. L'articolo sulla [diagnostica](durable-functions-diagnostics.md) contiene query di esempio che escludono i log di riesecuzione in modo da visualizzare solo i log "in tempo reale".

## <a name="storage-and-scalability"></a>Archiviazione e scalabilità

L'estensione Funzioni permanenti usa code, tabelle e BLOB di Archiviazione di Azure per salvare in modo permanente lo stato della cronologia di esecuzione e l'esecuzione di funzioni trigger. Si può usare l'account di archiviazione predefinito per l'app per le funzioni oppure configurare un account di archiviazione separato. L'uso di un account separato può essere necessario a causa dei limiti di velocità effettiva della risorsa di archiviazione. Non è necessario che il codice dell'agente di orchestrazione scritto interagisca con le entità in questi account di archiviazione e non dovrebbe farlo. Come dettaglio di implementazione, le entità vengono gestite direttamente da Durable Task Framework.

Le funzioni di orchestrazione pianificano le funzioni attività e ricevono le relative risposte tramite messaggi di coda interni. Quando si esegue un'app per le funzioni nel piano a consumo di Funzioni di Azure, le code vengono monitorate dal [controller di scalabilità di Funzioni di Azure](functions-scale.md#how-the-consumption-plan-works) e nuove istanze di calcolo vengono aggiunte in base alle esigenze. Quando viene scalata orizzontalmente a più macchine virtuali, una funzione di orchestrazione può essere eseguita in una sola macchina virtuale mentre le funzioni attività da essa chiamate vengono eseguite su più macchine virtuali diverse. Per altre informazioni sul comportamento di scalabilità di Funzioni permanenti, vedere [Scalabilità e prestazioni](durable-functions-perf-and-scale.md).

Per archiviare la cronologia di esecuzione per gli account dell'agente di orchestrazione viene usata l'archiviazione tabelle. Ogni volta che un'istanza viene riattivata in una determinata macchina virtuale, recupera la cronologia di esecuzione dall'archiviazione tabelle in modo da poter ricompilare il suo stato locale. Uno degli aspetti utili della disponibilità della cronologia nell'archiviazione tabelle è che consente di esaminare la cronologia delle orchestrazioni tramite strumenti come [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Screenshot di Azure Storage Explorer](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> Sebbene sia comodo e utile esaminare la cronologia di esecuzione nell'archiviazione tabelle, evitare di creare qualsiasi dipendenza in questa tabella. Possono cambiare con l'evoluzione dell'estensione Funzioni permanenti.

## <a name="known-issues-and-faq"></a>Problemi noti e domande frequenti

In generale, tutti i problemi noti dovrebbero essere segnalati nell'elenco di [problemi GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Se si verifica un problema e questo non è presente in GitHub, aprire un nuovo problema e includere una descrizione dettagliata. Anche se si vuole semplicemente porre una domanda, è possibile aprire un problema GitHub e contrassegnarlo come domanda.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Continuare a leggere la documentazione su Funzioni permanenti](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Installare l'estensione Funzioni permanenti e i relativi esempi](durable-functions-install.md)

