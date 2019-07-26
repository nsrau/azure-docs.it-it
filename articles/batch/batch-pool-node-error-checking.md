---
title: Verificare la presenza di errori in pool e nodi - Azure Batch
description: Informazioni sugli errori da controllare e su come evitarli durante la creazione di pool e nodi
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 9481263773cc919fecacce80191cf209ec2a1282
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359254"
---
# <a name="check-for-pool-and-node-errors"></a>Verificare la presenza di errori in pool e nodi

Durante la creazione e la gestione di pool di Azure Batch, alcune operazioni vengono eseguite immediatamente, Tuttavia, alcune operazioni sono asincrone ed eseguite in background, richiedendo alcuni minuti per il completamento.

Rilevare gli errori per le operazioni che si verificano immediatamente è semplice, in quanto tutti gli eventuali errori vengono restituiti immediatamente dall'API, dall'interfaccia della riga di comando o dall'interfaccia utente.

Questo articolo illustra le operazioni in background che possono verificarsi per pool e nodi di pool e spiega come rilevare ed evitare gli errori.

## <a name="pool-errors"></a>Errori dei pool

### <a name="resize-timeout-or-failure"></a>Timeout ridimensionamento o errore

Durante la creazione di un nuovo pool o il ridimensionamento di un pool esistente, si specifica il numero di nodi di destinazione.  L'operazione di creazione o ridimensionamento viene completata immediatamente, ma l'allocazione effettiva di nuovi nodi o la rimozione di nodi esistenti potrebbe richiedere diversi minuti.  Il timeout dell'operazione di ridimensionamento viene specificato nell'API di [creazione](https://docs.microsoft.com/rest/api/batchservice/pool/add) o [ridimensionamento](https://docs.microsoft.com/rest/api/batchservice/pool/resize). Se batch non è in grado di ottenere il numero di nodi di destinazione durante il periodo di timeout di ridimensionamento, il pool entra in uno stato stabile e segnala errori di ridimensionamento.

Nella proprietà [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) della valutazione più recente sono elencati gli errori che si sono verificati.

Le cause più comuni degli errori di ridimensionamento includono:

- Il timeout ridimensionamento è troppo breve
  - Nella maggior parte dei casi il timeout predefinito di 15 minuti è sufficiente per completare l'allocazione o la rimozione dei nodi del pool.
  - Se occorre allocare un numero elevato di nodi, è consigliabile impostare il timeout di ridimensionamento su 30 minuti. È il caso ad esempio del ridimensionamento a più di 1.000 nodi da un'immagine di Azure Marketplace o a più di 300 nodi da un'immagine di macchina virtuale personalizzata.
- Quota di core insufficiente
  - Un account Batch può allocare un numero limitato di core in tutti i pool. Quando viene raggiunta la quota limite, Batch smette di allocare nodi. È possibile [aumentare](https://docs.microsoft.com/azure/batch/batch-quota-limit) la quota di core per consentire a Batch di allocare più nodi.
- Indirizzi IP di subnet insufficienti quando un [pool è in una rete virtuale](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Una subnet di rete virtuale deve disporre di un numero sufficiente di indirizzi IP non assegnati da allocare a ogni nodo di pool richiesto. In caso contrario non è possibile creare i nodi.
- Risorse insufficienti quando un [pool è in una rete virtuale](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Se si creano risorse come servizi di bilanciamento del carico, IP pubblici e gruppi di sicurezza di rete nella stessa sottoscrizione dell'account Batch, verificare che le quote della sottoscrizione siano sufficienti per queste risorse.
- Pool di grandi dimensioni con immagini di macchina virtuale personalizzate
  - L'allocazione di pool di grandi dimensioni che usano immagini di macchina virtuale personalizzate può richiedere più tempo, pertanto possono verificarsi timeout di ridimensionamento.  Per suggerimenti sui limiti e sulla configurazione, vedere [Usare un'immagine personalizzata per creare un pool di macchine virtuali](https://docs.microsoft.com/azure/batch/batch-custom-images).

### <a name="automatic-scaling-failures"></a>Errori di ridimensionamento automatico

Si può anche impostare Azure Batch in modo da ridimensionare automaticamente il numero di nodi in un pool. A questo scopo occorre definire i parametri per la [formula di ridimensionamento automatico per un pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Il servizio Batch usa quindi la formula per valutare periodicamente il numero di nodi nel pool e impostare un nuovo numero di destinazione. Possono verificarsi i tipi di problemi seguenti:

- La valutazione del ridimensionamento automatico non riesce.
- L'operazione di ridimensionamento risultante non riesce con conseguente timeout.
- Un problema nella formula di ridimensionamento automatico genera valori di destinazione dei nodi non corretti. Il ridimensionamento riesce oppure raggiunge il timeout.

È possibile ottenere informazioni sull'ultima valutazione del ridimensionamento automatico usando la proprietà [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun). Questa proprietà indica il periodo della valutazione, i valori e il risultato, oltre agli eventuali errori di prestazioni.

L'[evento di completamento del ridimensionamento del pool](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) acquisisce le informazioni su tutte le valutazioni.

### <a name="delete"></a>Eliminare

Quando si elimina un pool che contiene nodi, Batch elimina prima di tutto i nodi, quindi l'oggetto pool stesso. L'eliminazione dei nodi del pool può richiedere alcuni minuti.

Lo [stato del pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) viene impostato su **deleting** durante il processo di eliminazione. L'applicazione chiamante può rilevare se l'eliminazione del pool richiede troppo tempo usando le proprietà **state** e **stateTransitionTime**.

## <a name="pool-compute-node-errors"></a>Errori dei nodi di calcolo dei pool

Anche quando batch esegue correttamente l'allocazione dei nodi in un pool, diversi problemi possono causare la non integrità di alcuni nodi e non è in grado di eseguire le attività. Questi nodi continuano a subire addebiti, quindi è importante rilevare i problemi per evitare di pagare per i nodi che non possono essere usati.

### <a name="start-task-failures"></a>Errori attività di avvio

È possibile specificare un'[attività di avvio](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) facoltativa per un pool. Come per qualsiasi attività, è possibile usare una riga di comando e file di risorse da scaricare dallo spazio di archiviazione. Una volta avviata, l'attività di avvio viene eseguita per ogni nodo. La proprietà **waitForSuccess** specifica se Batch deve attendere il corretto completamento dell'attività di avvio prima di pianificare qualsiasi attività su un nodo.

Se il nodo è stato configurato in modo da attendere che l'attività di avvio venga completata correttamente, ma invece questa non riesce, In tal caso, il nodo non sarà utilizzabile, ma continuerà a subire addebiti.

È possibile rilevare errori nell'attività di avvio mediante le proprietà [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) della proprietà del nodo [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) di primo livello.

Un'attività di avvio non riuscita causa anche l'impostazione [dello stato](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) del nodo su **starttaskfailed** se **waitForSuccess** è stato impostato su **true**.

Come per qualsiasi attività, l'esito negativo di un'attività di avvio può essere riconducibile a vari fattori.  Per risolvere il problema, controllare i file stdout, stderr e qualsiasi altro file di log specifico dell'attività.

Le attività di avvio devono essere rientranti, perché è possibile che l'attività di avvio venga eseguita più volte nello stesso nodo; l'attività di avvio viene eseguita quando viene ricreata l'immagine di un nodo o il riavvio viene riavviato. In rari casi, un'attività di avvio viene eseguita dopo un evento che ha causato il riavvio di un nodo, in cui è stato ricreata l'immagine di uno dei dischi del sistema operativo o temporaneo mentre l'altro non era. Poiché le attività di avvio di batch, come tutte le attività batch, vengono eseguite dal disco temporaneo, non si tratta in genere di un problema, ma in alcuni casi in cui l'attività di avvio installa un'applicazione nel disco del sistema operativo e mantiene altri dati sul disco temporaneo, ciò può causare problemi perché gli elementi non sono sincronizzati. Proteggere l'applicazione di conseguenza se si usano entrambi i dischi.

### <a name="application-package-download-failure"></a>Errore di download del pacchetto dell'applicazione

È possibile specificare uno o più pacchetti dell'applicazione per un pool. Batch Scarica i file di pacchetto specificati in ogni nodo e decomprime i file dopo l'avvio del nodo, ma prima della pianificazione delle attività. È prassi comune usare la riga di comando di un'attività di avvio insieme ai pacchetti dell'applicazione, ad esempio per copiare file n una posizione diversa o per eseguire il programma di installazione.

La proprietà [errori](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) nodo segnala un errore di download e annullamento della compressione di un pacchetto dell'applicazione. lo stato del nodo è impostato su inutilizzabile.

### <a name="container-download-failure"></a>Errore di download del contenitore

È possibile specificare uno o più riferimenti a contenitori in un pool. Batch Scarica i contenitori specificati in ogni nodo. La proprietà [errori](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) nodo segnala un errore di download di un contenitore e imposta lo stato del nodo su inutilizzabile.

### <a name="node-in-unusable-state"></a>Nodo in stato inutilizzabile

Azure Batch può impostare lo [stato del nodo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) su **unusable** per diversi motivi. Quando lo stato del nodo è impostato su **unusable**, non è possibile pianificare attività sul nodo, il quale però rimane soggetto ad addebiti.

I nodi in  uno stato inutilizzabile, ma senza [errori](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) significa che batch non è in grado di comunicare con la macchina virtuale. In questo caso, batch tenta sempre di ripristinare la macchina virtuale. Batch non tenterà automaticamente di ripristinare le macchine virtuali in cui non è stato possibile installare i pacchetti dell'applicazione o i contenitori anche se lo stato è inutilizzabile.

Se Batch è in grado di determinare la causa, questa viene indicata nella proprietà [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror).

Altri esempi di cause di nodi inutilizzabili, con stato **unusable**, includono:

- Un'immagine di macchina virtuale personalizzata non è valida. È il caso ad esempio di un'immagine preparata in modo non corretto.

- Una macchina virtuale viene spostata a causa di un errore di infrastruttura o un aggiornamento di basso livello. Batch recupera il nodo.

- Un'immagine di macchina virtuale è stata distribuita nell'hardware che non lo supporta. Ad esempio, il tentativo di eseguire un'immagine di CentOS HPC in una macchina virtuale [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) .

- Le macchine virtuali si trovano in una [rete virtuale di Azure](batch-virtual-network.md)e il traffico è stato bloccato sulle porte chiave.

- Le macchine virtuali si trovano in una rete virtuale, ma il traffico in uscita verso archiviazione di Azure è bloccato.

- Le macchine virtuali si trovano in una rete virtuale con una configurazione DNS del cliente e il server DNS non riesce a risolvere l'archiviazione di Azure.

### <a name="node-agent-log-files"></a>File di log dell'agente del nodo

Il processo dell'agente batch eseguito in ogni nodo del pool può fornire file di log che possono essere utili se è necessario contattare il supporto tecnico per un problema relativo al nodo del pool. I file di log relativi a un nodo possono essere caricati tramite il portale di Azure, Batch Explorer o un'[API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). È utile caricare e salvare i file di log. In seguito è possibile eliminare il nodo o il pool per evitare un addebito per i nodi in esecuzione.

### <a name="node-disk-full"></a>Disco del nodo pieno

L'unità temporanea per una macchina virtuale del nodo del pool viene utilizzata da batch per i file di processo, i file delle attività e i file condivisi. 

- File di pacchetti dell'applicazione
- File di risorse dell'attività
- File specifici dell'applicazione scaricati in una delle cartelle batch
- File stdout e stderr per ogni esecuzione dell'applicazione di attività
- File di output specifici dell'applicazione

Alcuni di questi file vengono scritti una sola volta quando vengono creati i nodi del pool, ad esempio i pacchetti dell'applicazione del pool o i file di risorse dell'attività di avvio del pool. Anche se viene scritto una sola volta quando viene creato il nodo, se questi file sono troppo grandi, è possibile riempire l'unità temporanea.

Gli altri file vengono scritti per ogni attività eseguita in un nodo, ad esempio stdout e stderr. Se un numero elevato di attività viene eseguito nello stesso nodo e/o se i file delle attività sono troppo grandi, è possibile che riempiano l'unità temporanea.

Le dimensioni dell'unità temporanea dipendono dalle dimensioni della macchina virtuale. Una considerazione quando si selezionano le dimensioni di una macchina virtuale è garantire che lo spazio disponibile nell'unità temporanea sia sufficiente.

- Nel portale di Azure durante l'aggiunta di un pool, è possibile visualizzare l'elenco completo delle dimensioni delle macchine virtuali ed è presente una colonna "dimensioni disco risorse".
- Gli articoli che descrivono tutte le dimensioni delle VM contengono tabelle con una colonna di archiviazione temporanea. ad esempio le [dimensioni delle macchine virtuali ottimizzate](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute) per il calcolo

Per i file scritti da ogni attività, è possibile specificare un periodo di conservazione per ogni attività che determina per quanto tempo i file delle attività vengono conservati prima di essere puliti automaticamente. Il tempo di conservazione può essere ridotto per ridurre i requisiti di archiviazione.

Se lo spazio su disco temporaneo viene riempito, attualmente il nodo smetterà di eseguire le attività. In futuro verrà segnalato un [errore del nodo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) .


## <a name="next-steps"></a>Passaggi successivi

Assicurarsi di aver configurato l'applicazione in modo da implementare un sistema completo di controllo degli errori, specialmente per le operazioni asincrone. Rilevare e diagnosticare tempestivamente i problemi può avere un'importanza fondamentale.
