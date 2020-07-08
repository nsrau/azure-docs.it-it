---
title: Copiare applicazioni e dati nei nodi del pool
description: Informazioni su come copiare applicazioni e dati nei nodi del pool.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: e21b8551fb62c4335910fd05bb9590eaf6f7e35a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954894"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Copiare applicazioni e dati nei nodi del pool

Azure Batch supporta diversi metodi per ottenere i dati e le applicazioni nei nodi di calcolo in modo che siano disponibili per l'utilizzo da parte delle attività. Poiché è possibile che dati e applicazioni siano necessari per eseguire l'intero processo, è necessario installarli in ogni nodo. Alcuni potrebbero essere necessari solo per un'attività specifica o devono essere installati per il processo, ma non devono essere presenti in ogni nodo. Batch offre strumenti per ognuno di questi scenari.

- **File di risorse dell'attività iniziale del pool**: per le applicazioni o i dati che devono essere installati in ogni nodo del pool. Usare questo metodo insieme a un pacchetto dell'applicazione o alla raccolta file di risorse dell'attività iniziale per eseguire un comando di installazione.  

Esempi: 
- Usare la riga di comando dell'attività iniziale per spostare o installare le applicazioni

- Specificare un elenco di file o contenitori specifici in un account di archiviazione di Azure. Per altre informazioni, vedere [add#resourcefile nella documentazione REST](/rest/api/batchservice/pool/add#resourcefile)

- Ogni processo in esecuzione nel pool esegue MyApplication.exe che deve prima essere installato con MyApplication.msi. Se si usa questo meccanismo, è necessario impostare la proprietà **Attendere il completamento dell'operazione** dell'attività iniziale su **true**. Per altre informazioni, vedere [add#starttask nella documentazione REST](/rest/api/batchservice/pool/add#starttask).

- **Riferimenti ai pacchetti dell'applicazione** nel pool: per le applicazioni o i dati che devono essere installati in ogni nodo del pool. Sebbene non sia presente alcun comando di installazione associato a un pacchetto dell'applicazione, è possibile usare un'attività iniziale per eseguire qualsiasi comando di installazione. Se l'applicazione non richiede l'installazione o è costituita da un numero elevato di file, è possibile usare questo metodo. I pacchetti dell'applicazione sono particolarmente adatti per un numero elevato di file poiché combinano un numero elevato di riferimenti a file in un payload ridotto. Se si tenta di includere più di 100 file di risorse separati in un'unica attività, il servizio Batch potrebbe riscontrare limitazioni di sistema interne per una singola attività. Inoltre, usare i pacchetti dell'applicazione in presenza di requisiti di controllo delle versioni rigorosi in cui possono essere presenti molte versioni diverse della stessa applicazione ed è necessario effettuare una scelta. Per altre informazioni, vedere [Distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione Batch](./batch-application-packages.md).

- **File di risorse dell'attività di preparazione del processo**: per le applicazioni o i dati che devono essere installati per l'esecuzione del processo, ma non devono essere installati nell'intero pool. Ad esempio, se il pool include molti tipi diversi di processi e solo un tipo di processo richiede l'esecuzione di MyApplication.msi, è opportuno inserire la fase di installazione in un'attività di preparazione del processo. Per altre informazioni sulle attività di preparazione del processo, vedere [Eseguire attività di preparazione e rilascio del processo in nodi di calcolo di Batch](./batch-job-prep-release.md).

- **File di risorse dell'attività**: per applicazioni o dati che riguardano solo una singola attività. Ad esempio: si supponga di avere cinque attività, ognuna delle quali elabora un file diverso e quindi scrive l'output nell'archiviazione BLOB.  In questo caso, è necessario specificare il file di input nella raccolta **file di risorse delle attività** perché ogni attività ha un proprio file di input.

## <a name="determine-the-scope-required-of-a-file"></a>Determinare l'ambito richiesto di un file

È necessario determinare l'ambito di un file, ovvero il file necessario per un pool, un processo o un'attività. I file che hanno come ambito il pool devono usare i pacchetti dell'applicazione del pool o un'attività iniziale. I file che hanno come ambito il processo devono usare un'attività di preparazione del processo. Un valido esempio di file con ambito a livello di pool o di processo sono le applicazioni. I file che hanno come ambito l'attività devono usare i file di risorse dell'attività.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Altri metodi per ottenere i dati nei nodi di calcolo di Batch

Esistono altri metodi per ottenere i dati nei nodi di calcolo di Batch che non sono ufficialmente integrati nell'API REST di Batch. Poiché si ha il controllo sui nodi di Azure Batch e si possono eseguire file eseguibili personalizzati, è possibile eseguire il pull dei dati da un numero qualsiasi di origini personalizzate, a condizione che il nodo di Batch abbia la connettività alla destinazione e si abbiano le credenziali per l'origine nel nodo di Azure Batch. Alcuni esempi comuni sono:

- Download dei dati da SQL
- Download dei dati da altri servizi Web/percorsi personalizzati
- Mapping di una condivisione di rete

### <a name="azure-storage"></a>Archiviazione di Azure

L'archiviazione BLOB ha soglie di scalabilità per il download. Le soglie di scalabilità della condivisione file dell'archiviazione di Azure sono uguali a quelli di un singolo BLOB. La dimensione influirà sul numero di nodi e pool necessari.

