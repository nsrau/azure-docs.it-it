---
title: Scenari di fan-out/fan-in Funzioni permanenti - Azure
description: Informazioni su come implementare uno scenario di fan-out/fan-it nell'estensione Funzioni permanenti per Funzioni di Azure.
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
ms.openlocfilehash: a5d539172f03246e3c658f2485d29d3ae389ae52
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Scenario di fan-out/fan-it in Funzioni permanenti - Esempio di backup cloud

*Fan-out/fan-in* fa riferimento al modello di esecuzione di più funzioni contemporaneamente e quindi di aggregazione dei risultati. Questo articolo illustra un esempio che usa [Funzioni permanenti](durable-functions-overview.md) per implementare uno scenario di fan-in/fan-out. L'esempio è una funzione permanente che esegue il backup di tutto o di una parte del contenuto del sito di un'app in Archiviazione di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Seguire le istruzioni in [Install Durable Functions](durable-functions-install.md) (Installare Funzioni permanenti) per impostare l'esempio.
* Questo articolo presuppone che l'utente abbia già eseguito la procedura dettagliata di esempio descritta in [Hello Sequence](durable-functions-sequence.md) (Sequenza di Hello).

## <a name="scenario-overview"></a>Panoramica dello scenario

In questo esempio le funzioni caricano tutti i file in modo ricorsivo in una directory specificata nell'archiviazione BLOB e contano anche il numero totale di byte caricati.

È possibile scrivere una singola funzione che esegua tutte le operazioni. Il problema principale da affrontare è costituito dalla **scalabilità**. Una singola funzione può essere eseguita solo in un'unica macchina virtuale, pertanto la velocità effettiva sarà limitata a quella di tale macchina. Un altro problema da affrontare è l'**affidabilità**. Se durante l'esecuzione si verifica un errore o se l'intero processo richiede più di 5 minuti, il backup potrebbe non riuscire e terminare in uno stato parzialmente completato, con la necessità di essere riavviato.

Un approccio più efficace consiste nello scrivere due funzioni regolari, una per enumerare i file e aggiungere i nomi di file a una coda e un'altra per leggere dalla coda e caricare i file nell'archiviazione BLOB. Questo approccio è migliore in termini di velocità effettiva e affidabilità, ma richiede di eseguire il provisioning e di gestire una coda. Aspetto ancora più importante, in questo caso viene introdotta una complessità significativa in termini di **gestione dello stato** e di **coordinamento** se si desidera eseguire altre operazioni, ad esempio indicare il numero totale di byte caricati.

Un approccio tramite Funzioni permanenti è caratterizzato da tutti i vantaggi citati con un overhead molto basso.

## <a name="the-functions"></a>Funzioni

Questo articolo descrive le funzioni seguenti nell'app di esempio:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

Le sezioni seguenti illustrano la configurazione e il codice usati per lo sviluppo nel portale di Azure. Il codice per lo sviluppo in Visual Studio viene visualizzato alla fine dell'articolo.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>L'orchestrazione di backup del cloud (Visual Studio Code e codice di esempio del portale di Azure)

La funzione `E2_BackupSiteContent` usa il codice *function.json* standard per le funzioni dell'agente di orchestrazione.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Il codice che implementa la funzione dell'agente di orchestrazione è il seguente:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

Le operazioni di questa funzione dell'agente di orchestrazione sono le seguenti:

1. Acquisizione di un valore `rootDirectory` come parametro di input.
2. Chiamata di una funzione per ottenere un elenco ricorsivo di file in `rootDirectory`.
3. Esecuzione di più chiamate di funzioni in parallelo per caricare ogni file in Archiviazione BLOB di Azure.
4. Attesa del completamento di tutti i caricamenti.
5. Restituzione dei byte totali caricati in Archiviazione BLOB di Azure.

Si noti la riga `await Task.WhenAll(tasks);`. Tutte le chiamate alla funzione `E2_CopyFileToBlob` *non* erano in attesa. Questa condizione è intenzionale per consentire l'esecuzione in parallelo. Quando si passa questa matrice di attività a `Task.WhenAll`, viene restituita un'attività che non verrà completata *fino al completamento di tutte le operazioni di copia*. Se si ha familiarità con Task Parallel Library (TPL) in .NET, questo scenario non è una novità. La differenza è che queste attività potrebbero essere in esecuzione in più macchine virtuali contemporaneamente e l'estensione di Funzioni permanenti assicura che l'esecuzione end-to-end sia resiliente al riciclo dei processi.

Dopo l'attesa da `Task.WhenAll`, tutte le chiamate di funzione sono state completate e hanno restituito valori. Ogni chiamata a `E2_CopyFileToBlob` restituisce il numero di byte caricato e di conseguenza per calcolare il numero di byte totale è sufficiente sommare tutti i valori restituiti.

## <a name="helper-activity-functions"></a>Funzioni di attività helper

Le funzioni di attività helper, in modo analogo agli altri esempi, sono normali funzioni che usano l'associazione di trigger `activityTrigger`. Il file *function.json* per `E2_GetFileList` è ad esempio simile al seguente:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Di seguito ne viene riportata l'implementazione:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

> [!NOTE]
> È lecito chiedersi perché non è sufficiente inserire questo codice direttamente nella funzione dell'agente di orchestrazione. Sebbene possibile, questa operazione violerebbe una delle regole fondamentali delle funzioni dell'agente di orchestrazione, ovvero quella in base alla quale non è consigliabile che tali funzioni eseguano operazioni di I/O, incluso l'accesso al file system locale.

Il file *function.json* per `E2_CopyFileToBlob` è analogamente semplice:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

Anche l'implementazione è semplice perché usa alcune funzionalità avanzate delle associazioni di Funzioni di Azure (ovvero l'uso del parametro `Binder`), senza che sia necessario preoccuparsi di tali informazioni per gli scopi di questa procedura dettagliata.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

L'implementazione carica il file dal disco e trasmette il contenuto in modo asincrono in un BLOB con lo stesso nome nel contenitore dei backup. Il valore restituito è il numero di byte copiati nell'archiviazione, che viene quindi usato dalla funzione dell'agente di orchestrazione per calcolare la somma di aggregazione.

> [!NOTE]
> Questo è un esempio perfetto dello spostamento delle operazioni di I/O in una funzione `activityTrigger`. In questo modo non solo il lavoro può essere distribuito tra molte macchine virtuali diverse, ma è anche possibile ottenere i vantaggi determinati dall'uso di checkpoint per lo stato di avanzamento. Se il processo host viene interrotto per qualsiasi motivo, è possibile conoscere quali caricamenti sono già stati completati.

## <a name="run-the-sample"></a>Eseguire l'esempio

È possibile avviare l'orchestrazione inviando la richiesta HTTP POST seguente.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> La funzione `HttpStart` richiamata funziona solo con contenuto in formato JSON. Per questo motivo, l'intestazione `Content-Type: application/json` è necessaria e il percorso della directory viene codificato come una stringa JSON.

Questa richiesta HTTP attiva l'agente di orchestrazione `E2_BackupSiteContent` e la stringa `D:\home\LogFiles` viene passata come parametro. La risposta restituisce un collegamento per ottenere lo stato dell'operazione di backup:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

A seconda del numero di file di log presenti nell'app per le funzioni, questa operazione potrebbe richiedere alcuni minuti. È possibile ottenere lo stato più recente eseguendo una query sull'URL nell'intestazione `Location` della risposta HTTP 202 precedente.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

In questo caso la funzione è ancora in esecuzione. È ora possibile visualizzare l'input che è stato salvato nello stato dell'agente di orchestrazione e l'ora dell'ultimo aggiornamento. È possibile continuare a usare i valori dell'intestazione `Location` per eseguire il polling per il completamento. Quando lo stato è completato, viene visualizzato un valore di risposta HTTP simile al seguente:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Ora è possibile visualizzare che l'orchestrazione è stata completata e approssimativamente il tempo necessario per il completamento. Viene inoltre visualizzato un valore per il campo `output`, che indica che sono stati caricati circa 450 KB di log.

## <a name="visual-studio-sample-code"></a>Codice di esempio di Visual Studio

Di seguito è riportata l'orchestrazione come un unico file C# in un progetto di Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Passaggi successivi

In questo esempio è stato illustrato come implementare il criterio di fan-out/fan-in. Nell'esempio successivo viene illustrato come implementare il criterio [singleton con stato](durable-functions-singletons.md) in una [orchestrazione perenne](durable-functions-eternal-orchestrations.md).

> [!div class="nextstepaction"]
> [Eseguire l'esempio di singleton con stato](durable-functions-counter.md)
