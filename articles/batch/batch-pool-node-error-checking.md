---
title: Verificare la presenza di errori in pool e nodi - Azure Batch
description: In questo articolo vengono illustrate le operazioni in background che possono verificarsi, insieme agli errori da controllare e come evitarli durante la creazione di pool e nodi.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472946"
---
# <a name="check-for-pool-and-node-errors"></a>Verificare la presenza di errori in pool e nodi

Durante la creazione e la gestione di pool di Azure Batch, alcune operazioni vengono eseguite immediatamente, Tuttavia, alcune operazioni sono asincrone ed eseguite in background, richiedendo alcuni minuti per il completamento.

Rilevare gli errori per le operazioni che si verificano immediatamente è semplice, in quanto tutti gli eventuali errori vengono restituiti immediatamente dall'API, dall'interfaccia della riga di comando o dall'interfaccia utente.

Questo articolo illustra le operazioni in background che possono verificarsi per pool e nodi di pool e spiega come rilevare ed evitare gli errori.

## <a name="pool-errors"></a>Errori dei pool

### <a name="resize-timeout-or-failure"></a>Timeout ridimensionamento o errore

Durante la creazione di un nuovo pool o il ridimensionamento di un pool esistente, si specifica il numero di nodi di destinazione.  L'operazione di creazione o ridimensionamento viene completata immediatamente, ma l'allocazione effettiva di nuovi nodi o la rimozione dei nodi esistenti potrebbe richiedere alcuni minuti.  Il timeout dell'operazione di ridimensionamento viene specificato nell'API di [creazione](https://docs.microsoft.com/rest/api/batchservice/pool/add) o [ridimensionamento](https://docs.microsoft.com/rest/api/batchservice/pool/resize). Se Batch non è in grado di ottenere il numero di nodi di destinazione durante il periodo di timeout di ridimensionamento, il pool passa allo stato stabile e segnala gli errori di ridimensionamento.

La proprietà [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) per la valutazione più recente elenca gli errori che si sono verificati.

Le cause più comuni degli errori di ridimensionamento includono:Common causes for resize errors include:

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
  - L'allocazione di pool di grandi dimensioni che usano immagini di macchina virtuale personalizzate può richiedere più tempo, pertanto possono verificarsi timeout di ridimensionamento.  Per consigli sui limiti e sulla configurazione, vedere [Creare un pool con la raccolta immagini condivise.](batch-sig-images.md)

### <a name="automatic-scaling-failures"></a>Errori di ridimensionamento automatico

Si può anche impostare Azure Batch in modo da ridimensionare automaticamente il numero di nodi in un pool. A questo scopo occorre definire i parametri per la [formula di ridimensionamento automatico per un pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Il servizio Batch usa quindi la formula per valutare periodicamente il numero di nodi nel pool e impostare un nuovo numero di destinazione. Possono verificarsi i tipi di problemi seguenti:

- La valutazione del ridimensionamento automatico non riesce.
- L'operazione di ridimensionamento risultante non riesce con conseguente timeout.
- Un problema nella formula di ridimensionamento automatico genera valori di destinazione dei nodi non corretti. Il ridimensionamento riesce oppure raggiunge il timeout.

È possibile ottenere informazioni sull'ultima valutazione del ridimensionamento automatico usando la proprietà [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun). Questa proprietà indica il periodo della valutazione, i valori e il risultato, oltre agli eventuali errori di prestazioni.

L'[evento di completamento del ridimensionamento del pool](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) acquisisce le informazioni su tutte le valutazioni.

### <a name="delete"></a>Delete

Quando si elimina un pool che contiene nodi, Batch elimina prima di tutto i nodi, quindi l'oggetto pool stesso. L'eliminazione dei nodi del pool può richiedere alcuni minuti.

Lo [stato del pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) viene impostato su **deleting** durante il processo di eliminazione. L'applicazione chiamante può rilevare se l'eliminazione del pool richiede troppo tempo usando le proprietà **state** e **stateTransitionTime**.

## <a name="pool-compute-node-errors"></a>Errori dei nodi di calcolo dei pool

Anche quando Batch alloca correttamente i nodi in un pool, vari problemi possono causare alcuni dei nodi non integri e impossibilità di eseguire attività. Questi nodi devono ancora sostenere costi, pertanto è importante rilevare problemi per evitare di pagare per i nodi che non possono essere utilizzati. Oltre agli errori comuni dei nodi, conoscere [lo stato](/rest/api/batchservice/job/get#jobstate) del processo corrente è utile per la risoluzione dei problemi.

### <a name="start-task-failures"></a>Avviare gli errori delle attivitàStart task failures

È possibile specificare un'[attività di avvio](/rest/api/batchservice/pool/add#starttask) facoltativa per un pool. Come per qualsiasi attività, è possibile usare una riga di comando e file di risorse da scaricare dallo spazio di archiviazione. Una volta avviata, l'attività di avvio viene eseguita per ogni nodo. La proprietà **waitForSuccess** specifica se Batch deve attendere il corretto completamento dell'attività di avvio prima di pianificare qualsiasi attività su un nodo.

Se il nodo è stato configurato in modo da attendere che l'attività di avvio venga completata correttamente, ma invece questa non riesce, In tal caso, il nodo non sarà utilizzabile, ma continuerà a incorrere in addebiti.

È possibile rilevare errori nell'attività di avvio mediante le proprietà [result](/rest/api/batchservice/computenode/get#taskexecutionresult) e [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) della proprietà del nodo [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) di primo livello.

Un'attività di avvio non riuscita fa in modo che Batch imposti [lo stato](/rest/api/batchservice/computenode/get#computenodestate) del nodo su **starttaskfailed** se **waitForSuccess** è stato impostato su **true**.

Come per qualsiasi attività, l'esito negativo di un'attività di avvio può essere riconducibile a vari fattori.  Per risolvere il problema, controllare i file stdout, stderr e qualsiasi altro file di log specifico dell'attività.

Le attività di avvio devono essere rientranti, poiché è possibile che l'attività di avvio venga eseguita più volte sullo stesso nodo. l'attività di avvio viene eseguita quando viene ricreato o riavviato un nodo. In rari casi, un'attività di avvio verrà eseguita dopo che un evento ha causato il riavvio di un nodo, in cui uno dei dischi operativi o effimeri è stato ricreato l'immagine mentre l'altro no. Poiché batch avvia le attività (come tutte le attività Batch) eseguite dal disco effimero, questo non è in genere un problema, ma in alcuni casi in cui l'attività di avvio sta installando un'applicazione sul disco del sistema operativo e mantenendo altri dati sul disco effimero, ciò può causare problemi perché le cose non sono sincronizzate. Proteggere l'applicazione di conseguenza se si utilizzano entrambi i dischi.

### <a name="application-package-download-failure"></a>Errore di download del pacchetto dell'applicazione

È possibile specificare uno o più pacchetti dell'applicazione per un pool. Batch scarica i file del pacchetto specificato in ogni nodo e decomprime i file dopo l'avvio del nodo, ma prima che le attività vengano pianificate. È prassi comune usare la riga di comando di un'attività di avvio insieme ai pacchetti dell'applicazione, ad esempio per copiare file n una posizione diversa o per eseguire il programma di installazione.

La proprietà [errors](/rest/api/batchservice/computenode/get#computenodeerror) del nodo segnala un errore di download e decompressione di un pacchetto dell'applicazione. lo stato del nodo è impostato su **inutilizzabile**.

### <a name="container-download-failure"></a>Errore di download del contenitore

È possibile specificare uno o più riferimenti al contenitore in un pool. Batch scarica i contenitori specificati in ogni nodo. La proprietà [errors](/rest/api/batchservice/computenode/get#computenodeerror) del nodo segnala un errore di download di un contenitore e imposta lo stato del nodo su **unusable.**

### <a name="node-in-unusable-state"></a>Nodo in stato inutilizzabile

Azure Batch può impostare lo [stato del nodo](/rest/api/batchservice/computenode/get#computenodestate) su **unusable** per diversi motivi. Quando lo stato del nodo è impostato su **unusable**, non è possibile pianificare attività sul nodo, il quale però rimane soggetto ad addebiti.

Nodi in uno stato **inutilizzabile,** ma senza [errori](/rest/api/batchservice/computenode/get#computenodeerror) significa che Batch non è in grado di comunicare con la macchina virtuale. In questo caso, Batch tenta sempre di ripristinare la macchina virtuale. Batch non tenterà automaticamente di ripristinare le macchine virtuali che non sono riuscite a installare pacchetti o contenitori dell'applicazione anche se il loro stato è **inutilizzabile.**

Se Batch è in grado di determinare la causa, questa viene indicata nella proprietà [errors](/rest/api/batchservice/computenode/get#computenodeerror).

Altri esempi di cause di nodi inutilizzabili, con stato **unusable**, includono:

- Un'immagine di macchina virtuale personalizzata non è valida. È il caso ad esempio di un'immagine preparata in modo non corretto.

- Una macchina virtuale viene spostata a causa di un errore di infrastruttura o un aggiornamento di basso livello. Batch recupera il nodo.

- Un'immagine di macchina virtuale è stata distribuita su hardware che non la supporta. Ad esempio, il tentativo di eseguire un'immagine HPC CentOS in una macchina virtuale [di Standard_D1_v2.](../virtual-machines/dv2-dsv2-series.md)

- Le macchine virtuali si trovano in una [rete virtuale](batch-virtual-network.md)di Azure e il traffico è stato bloccato fino alle porte chiave.

- Le macchine virtuali si trovano in una rete virtuale, ma il traffico in uscita verso Archiviazione di Azure è bloccato.

- Le macchine virtuali si trovano in una rete virtuale con una configurazione DNS del cliente e il server DNS non è in grado di risolvere l'archiviazione di Azure.The VMs are in a virtual network with a customer DNS configuration and the DNS server cannot resolve Azure storage.

### <a name="node-agent-log-files"></a>File di log dell'agente del nodo

Il processo agente Batch in esecuzione in ogni nodo del pool può fornire file di log che potrebbero essere utili se è necessario contattare il supporto tecnico relativo a un problema del nodo pool. I file di log relativi a un nodo possono essere caricati tramite il portale di Azure, Batch Explorer o un'[API](/rest/api/batchservice/computenode/uploadbatchservicelogs). È utile caricare e salvare i file di log. In seguito è possibile eliminare il nodo o il pool per evitare un addebito per i nodi in esecuzione.

### <a name="node-disk-full"></a>Disco nodo pieno

L'unità temporanea per una macchina virtuale del nodo del pool viene usata da Batch per i file di processo, i file di attività e i file condivisi.

- File dei pacchetti dell'applicazione
- File di risorse delle attività
- File specifici dell'applicazione scaricati in una delle cartelle Batch
- File Stdout e stderr per l'esecuzione di ogni applicazione di attivitàStdout and stderr files for each task application execution
- File di output specifici dell'applicazioneApplication-specific output files

Alcuni di questi file vengono scritti una sola volta quando vengono creati nodi del pool, ad esempio pacchetti di applicazioni del pool o file di risorse delle attività di avvio del pool. Anche se scritto una sola volta quando viene creato il nodo, se questi file sono troppo grandi potrebbero riempire l'unità temporanea.

Altri file vengono scritti per ogni attività eseguita su un nodo, ad esempio stdout e stderr. Se un numero elevato di attività viene eseguito sullo stesso nodo e/o i file delle attività sono troppo grandi, è possibile che l'unità temporanea riempia.

La dimensione dell'unità temporanea dipende dalle dimensioni della macchina virtuale. Una considerazione quando si sceglie una dimensione di macchina virtuale è per garantire che l'unità temporanea disponga di spazio sufficiente.

- Nel portale di Azure quando si aggiunge un pool, è possibile visualizzare l'elenco completo delle dimensioni delle macchine virtuali ed è presente una colonna "Dimensioni disco risorse".
- Gli articoli che descrivono tutte le dimensioni delle macchine virtuali conpossono tabelle con una colonna 'Temp Storage'; ad esempio [Calcolo dimensioni delle macchine virtuali ottimizzate](/azure/virtual-machines/windows/sizes-compute)

Per i file scritti da ogni attività, è possibile specificare un tempo di conservazione per ogni attività che determina per quanto tempo i file dell'attività vengono conservati prima di essere puliti automaticamente. Il tempo di conservazione può essere ridotto per ridurre i requisiti di archiviazione.


Se il disco temporaneo esaurisce lo spazio (o è molto vicino all'esaurimento dello spazio), il nodo si sposterà sullo stato [inutilizzabile](/rest/api/batchservice/computenode/get#computenodestate) e verrà segnalato un errore del nodo che indica che il disco è pieno.

### <a name="what-to-do-when-a-disk-is-full"></a>Cosa fare quando un disco è pieno

Determinare il motivo per cui il disco è pieno: se non si è certi di cosa occupa spazio nel nodo, è consigliabile eseguire informazioni sul nodo in modo remoto e analizzare manualmente il punto in cui è disponibile lo spazio. È anche possibile utilizzare [l'API Batch List Files](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) per esaminare i file nelle cartelle gestite Batch (ad esempio, gli output delle attività). Si noti che questa API elenca solo i file nelle directory gestite Batch e se le attività create file altrove non verranno visualizzati.

Assicurarsi che tutti i dati necessari siano stati recuperati dal nodo o caricati in un archivio durevole. L'attenuazione del problema del disco pieno comporta l'eliminazione dei dati per liberare spazio.

### <a name="recovering-the-node"></a>Ripristino del nodo

1. Se il pool è un pool [C.loudServiceConfiguration,](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) è possibile ricreare l'immagine del nodo tramite [l'API di riimage Batch](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage). Questo pulirà l'intero disco. La riimageing image non è attualmente supportata per i pool VirtualMachineConfiguration.Re-image is not currently supported for [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration) pools.

2. Se il pool è [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration), è possibile rimuovere il nodo dal pool utilizzando [l'API di rimozione dei nodi.](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes) Quindi, è possibile far crescere nuovamente il pool per sostituire il nodo non valido con uno nuovo.

3.  Eliminare i processi completati precedenti o le attività completate i cui dati dell'attività sono ancora nei nodi. Per un suggerimento sui dati di processi/attività nei nodi che è possibile cercare [nell'insieme RecentTasks](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) nel nodo o nei [file nel nodo](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode). L'eliminazione del processo comporta l'eliminazione di tutte le attività nel processo e l'eliminazione delle attività nel processo attiverà i dati nelle directory delle attività del nodo da eliminare, liberando così spazio. Una volta liberato abbastanza spazio, riavviare il nodo e dovrebbe uscire dallo stato "Inutilizzabile" e in "Inattivo" di nuovo.

## <a name="next-steps"></a>Passaggi successivi

Assicurarsi di aver configurato l'applicazione in modo da implementare un sistema completo di controllo degli errori, specialmente per le operazioni asincrone. Rilevare e diagnosticare tempestivamente i problemi può avere un'importanza fondamentale.
