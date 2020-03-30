---
title: Scenari di fan-out/fan-in Funzioni permanenti - Azure
description: Informazioni su come implementare uno scenario di fan-out/fan-it nell'estensione Funzioni permanenti per Funzioni di Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562191"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Scenario di fan-out/fan-it in Funzioni permanenti - Esempio di backup cloud

*Fan-out/fan-in* fa riferimento al modello di esecuzione di più funzioni contemporaneamente e quindi di aggregazione dei risultati. Questo articolo illustra un esempio che usa [Funzioni permanenti](durable-functions-overview.md) per implementare uno scenario di fan-in/fan-out. L'esempio è una funzione permanente che esegue il backup di tutto o di una parte del contenuto del sito di un'app in Archiviazione di Azure.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Panoramica dello scenario

In questo esempio le funzioni caricano tutti i file in modo ricorsivo in una directory specificata nell'archiviazione BLOB e contano anche il numero totale di byte caricati.

È possibile scrivere una singola funzione che esegua tutte le operazioni. Il problema principale da affrontare è costituito dalla **scalabilità**. L'esecuzione di una singola funzione può essere eseguita solo in una singola macchina virtuale, pertanto la velocità effettiva sarà limitata dalla velocità effettiva di quella singola macchina virtuale. Un altro problema da affrontare è l'**affidabilità**. Se si verifica un errore a metà o se l'intero processo richiede più di 5 minuti, il backup potrebbe non riuscire in uno stato parzialmente completato. con la necessità di essere riavviato.

Un approccio più efficace consiste nello scrivere due funzioni regolari, una per enumerare i file e aggiungere i nomi di file a una coda e un'altra per leggere dalla coda e caricare i file nell'archiviazione BLOB. Questo approccio è migliore in termini di velocità effettiva e affidabilità, ma richiede il provisioning e la gestione di una coda. Aspetto ancora più importante, in questo caso viene introdotta una complessità significativa in termini di **gestione dello stato** e di **coordinamento** se si desidera eseguire altre operazioni, ad esempio indicare il numero totale di byte caricati.

Un approccio tramite Funzioni permanenti è caratterizzato da tutti i vantaggi citati con un overhead molto basso.

## <a name="the-functions"></a>Funzioni

Questo articolo descrive le funzioni seguenti nell'app di esempio:

* `E2_BackupSiteContent`: [funzione dell'agente](durable-functions-bindings.md#orchestration-trigger) `E2_GetFileList` di orchestrazione che chiama per ottenere `E2_CopyFileToBlob` un elenco di file di cui eseguire il backup, quindi chiama per eseguire il backup di ogni file.
* `E2_GetFileList`: [funzione](durable-functions-bindings.md#activity-trigger) di attività che restituisce un elenco di file in una directory.
* `E2_CopyFileToBlob`: funzione di attività che esegue il backup di un singolo file in Archiviazione BLOB di Azure.: An activity function that backs up a single file to Azure Blob Storage.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent funzione dell'agente di orchestrazione

Le operazioni di questa funzione dell'agente di orchestrazione sono le seguenti:

1. Acquisizione di un valore `rootDirectory` come parametro di input.
2. Chiamata di una funzione per ottenere un elenco ricorsivo di file in `rootDirectory`.
3. Esecuzione di più chiamate di funzioni in parallelo per caricare ogni file in Archiviazione BLOB di Azure.
4. Attesa del completamento di tutti i caricamenti.
5. Restituzione dei byte totali caricati in Archiviazione BLOB di Azure.

# <a name="c"></a>[C #](#tab/csharp)

Il codice che implementa la funzione dell'agente di orchestrazione è il seguente:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Si noti la riga `await Task.WhenAll(tasks);`. Tutte le singole `E2_CopyFileToBlob` chiamate alla funzione *non* erano attese, che consente loro di funzionare in parallelo. Quando si passa questa matrice di attività a `Task.WhenAll`, viene restituita un'attività che non verrà completata *fino al completamento di tutte le operazioni di copia*. Se si ha familiarità con Task Parallel Library (TPL) in .NET, questo scenario non è una novità. La differenza è che queste attività potrebbero essere eseguite contemporaneamente su più macchine virtuali e l'estensione Funzioni durevoli garantisce che l'esecuzione end-to-end sia resiliente al riciclo dei processi.

Dopo l'attesa da `Task.WhenAll`, tutte le chiamate di funzione sono state completate e hanno restituito valori. Ogni chiamata a `E2_CopyFileToBlob` restituisce il numero di byte caricato e di conseguenza per calcolare il numero di byte totale è sufficiente sommare tutti i valori restituiti.

# <a name="javascript"></a>[Javascript](#tab/javascript)

La funzione utilizza lo standard *function.json* per le funzioni dell'agente di orchestrazione.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Il codice che implementa la funzione dell'agente di orchestrazione è il seguente:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Si noti la riga `yield context.df.Task.all(tasks);`. Tutte le singole `E2_CopyFileToBlob` chiamate alla funzione *non* sono state restituite, il che consente loro di essere eseguite in parallelo. Quando si passa questa matrice di attività a `context.df.Task.all`, viene restituita un'attività che non verrà completata *fino al completamento di tutte le operazioni di copia*. Se hai familiarità [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) con JavaScript, allora questo non è nuovo per te. La differenza è che queste attività potrebbero essere eseguite contemporaneamente su più macchine virtuali e l'estensione Funzioni durevoli garantisce che l'esecuzione end-to-end sia resiliente al riciclo dei processi.

> [!NOTE]
> Anche se le attività sono concettualmente simili alle promesse JavaScript, le funzioni di orchestrazione dovrebbero usare `context.df.Task.all` e `context.df.Task.any` invece di `Promise.all` e `Promise.race` per gestire la parallelizzazione delle attività.

Dopo aver `context.df.Task.all`ceduto da , sappiamo che tutte le chiamate di funzione sono state completate e ci hanno restituito i valori. Ogni chiamata a `E2_CopyFileToBlob` restituisce il numero di byte caricato e di conseguenza per calcolare il numero di byte totale è sufficiente sommare tutti i valori restituiti.

---

### <a name="helper-activity-functions"></a>Funzioni di attività helper

Le funzioni di attività helper, in modo analogo agli altri esempi, sono normali funzioni che usano l'associazione di trigger `activityTrigger`.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList di attività

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Il file *function.json* per `E2_GetFileList` è simile al seguente:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

Di seguito ne viene riportata l'implementazione:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

La funzione `readdirp` utilizza il modulo (versione 2.x) per leggere in modo ricorsivo la struttura di directory.

---

> [!NOTE]
> È lecito chiedersi perché non è sufficiente inserire questo codice direttamente nella funzione dell'agente di orchestrazione. Sebbene possibile, questa operazione violerebbe una delle regole fondamentali delle funzioni dell'agente di orchestrazione, ovvero quella in base alla quale non è consigliabile che tali funzioni eseguano operazioni di I/O, incluso l'accesso al file system locale. Per ulteriori informazioni, vedere Vincoli di [codice della funzione Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>Funzione di attività di E2_CopyFileToBlob

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> È necessario installare `Microsoft.Azure.WebJobs.Extensions.Storage` il pacchetto NuGet per eseguire il codice di esempio.

La funzione usa alcune funzionalità avanzate delle associazioni di Funzioni di Azure, ovvero l'uso del [ `Binder` parametro](../functions-dotnet-class-library.md#binding-at-runtime), ma non è necessario preoccuparsi di tali dettagli ai fini di questa procedura dettagliata.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Il file *function.json* per `E2_CopyFileToBlob` è analogamente semplice:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

L'implementazione JavaScript usa [L'SDK di archiviazione](https://github.com/Azure/azure-storage-node) di Azure per Node per caricare i file in Archiviazione BLOB di Azure.The JavaScript implementation uses the Azure Storage SDK for Node to upload the files to Azure Blob Storage.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

L'implementazione carica il file dal disco e trasmette il contenuto in modo asincrono in un BLOB con lo stesso nome nel contenitore dei backup. Il valore restituito è il numero di byte copiati nell'archiviazione, che viene quindi usato dalla funzione dell'agente di orchestrazione per calcolare la somma di aggregazione.

> [!NOTE]
> Questo è un esempio perfetto dello spostamento delle operazioni di I/O in una funzione `activityTrigger`. Non solo il lavoro può essere distribuito tra molte macchine diverse, ma si ottengono anche i vantaggi di checkpointare lo stato di avanzamento. Se il processo host viene interrotto per qualsiasi motivo, è possibile conoscere quali caricamenti sono già stati completati.

## <a name="run-the-sample"></a>Eseguire l'esempio

È possibile avviare l'orchestrazione inviando la richiesta HTTP POST seguente.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> La funzione `HttpStart` richiamata funziona solo con contenuto in formato JSON. Per questo motivo, l'intestazione `Content-Type: application/json` è necessaria e il percorso della directory viene codificato come una stringa JSON. Il frammento di codice HTTP precedente presuppone inoltre che sia presente una voce nel file `host.json` che consente di rimuovere il prefisso predefinito `api/` da tutti gli URL di funzioni di trigger HTTP. È possibile trovare il markup per la configurazione nel file `host.json` negli esempi.

Questa richiesta HTTP attiva l'agente di orchestrazione `E2_BackupSiteContent` e la stringa `D:\home\LogFiles` viene passata come parametro. La risposta restituisce un collegamento per ottenere lo stato dell'operazione di backup:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

A seconda del numero di file di log presenti nell'app per le funzioni, questa operazione potrebbe richiedere alcuni minuti. È possibile ottenere lo stato più recente eseguendo una query sull'URL nell'intestazione `Location` della risposta HTTP 202 precedente.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

In questo caso la funzione è ancora in esecuzione. È ora possibile visualizzare l'input che è stato salvato nello stato dell'agente di orchestrazione e l'ora dell'ultimo aggiornamento. È possibile continuare a usare i valori dell'intestazione `Location` per eseguire il polling per il completamento. Quando lo stato è completato, viene visualizzato un valore di risposta HTTP simile al seguente:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Ora è possibile visualizzare che l'orchestrazione è stata completata e approssimativamente il tempo necessario per il completamento. Viene inoltre visualizzato un valore per il campo `output`, che indica che sono stati caricati circa 450 KB di log.

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stato illustrato come implementare il criterio di fan-out/fan-in. L'esempio successivo illustra come implementare il modello di monitoraggio usando i [timer permanenti](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Eseguire l'esempio di monitoraggio](durable-functions-monitor.md)