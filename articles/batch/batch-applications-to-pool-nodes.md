---
title: Copia di applicazioni e dati nei nodi del pool
description: Informazioni su come copiare le applicazioni e i dati nei nodi del pool.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385584"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Copia di applicazioni e dati nei nodi del pool

Azure Batch supporta diversi modi per ottenere i dati e le applicazioni nei nodi di calcolo, in modo che i dati e le applicazioni siano disponibili per l'utilizzo da parte delle attività. È possibile che i dati e le applicazioni siano necessari per eseguire l'intero processo ed è quindi necessario installarli in ogni nodo. Alcune potrebbero essere necessarie solo per un'attività specifica o devono essere installate per il processo, ma non devono trovarsi in ogni nodo. In batch sono disponibili strumenti per ognuno di questi scenari.

- **File di risorse dell'attività di avvio del pool**: per le applicazioni o i dati che devono essere installati in ogni nodo del pool. Utilizzare questo metodo insieme a un pacchetto dell'applicazione o alla raccolta di file di risorse dell'attività di avvio per eseguire un comando di installazione.  

Esempi: 
- Utilizzare la riga di comando avvia attività per spostare o installare applicazioni

- Specificare un elenco di file o contenitori specifici in un account di archiviazione di Azure. Per altre informazioni, vedere [aggiungere # resourceFile nella documentazione Rest](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- Ogni processo in esecuzione nel pool esegue MyApplication. exe che deve prima essere installato con MyApplication. msi. Se si usa questo meccanismo, è necessario impostare la proprietà **wait for Success** dell'attività di avvio su **true**. Per ulteriori informazioni, vedere la [documentazione relativa all'aggiunta di # StartTask in Rest](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask).

- **Riferimenti ai pacchetti dell'applicazione** nel pool: per le applicazioni o i dati che devono essere installati in ogni nodo del pool. Non è presente alcun comando di installazione associato a un pacchetto dell'applicazione, ma è possibile usare un'attività di avvio per eseguire qualsiasi comando di installazione. Se l'applicazione non richiede l'installazione o è costituita da un numero elevato di file, è possibile usare questo metodo. I pacchetti dell'applicazione sono particolarmente adatti per un numero elevato di file in quanto combinano un numero elevato di riferimenti a file in un payload ridotto. Se si tenta di includere più di 100 file di risorse separati in un'unica attività, il servizio batch potrebbe presentarsi a limitazioni di sistema interne per una singola attività. Usare inoltre i pacchetti dell'applicazione se si dispone di requisiti di controllo delle versioni rigorosi in cui è possibile disporre di molte versioni diverse della stessa applicazione ed è necessario sceglierle tra loro. Per altre informazioni, vedere [distribuire le applicazioni nei nodi di calcolo con i pacchetti dell'applicazione batch](https://docs.microsoft.com/azure/batch/batch-application-packages).

- **File di risorse dell'attività di preparazione dei processi**: per le applicazioni o i dati che devono essere installati per l'esecuzione del processo, ma non devono essere installati nell'intero pool. Ad esempio, se il pool presenta molti tipi diversi di processi e un solo tipo di processo richiede l'esecuzione di MyApplication. msi, è opportuno inserire il passaggio di installazione in un'attività di preparazione del processo. Per altre informazioni sulle attività di preparazione dei processi [, vedere eseguire le attività di preparazione e rilascio dei processi nei nodi di calcolo di batch](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/).

- **File di risorse dell'attività**: quando un'applicazione o i dati sono rilevanti solo per una singola attività. Ad esempio, sono disponibili cinque attività, ciascuna elabora un file diverso e quindi scrive l'output nell'archivio BLOB.  In questo caso, il file di input deve essere specificato nella raccolta di **file di risorse delle attività** perché ogni attività dispone di un proprio file di input.

## <a name="determine-the-scope-required-of-a-file"></a>Determinare l'ambito richiesto di un file

È necessario determinare l'ambito di un file, ovvero il file necessario per un pool, un processo o un'attività. I file che hanno come ambito il pool devono usare i pacchetti dell'applicazione del pool o un'attività di avvio. I file che hanno come ambito il processo devono usare un'attività di preparazione del processo. Un valido esempio di file con ambito a livello di pool o di processo sono le applicazioni. I file che hanno come ambito l'attività devono usare i file di risorse dell'attività.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Altri modi per recuperare i dati sui nodi di calcolo batch

Esistono altri modi per recuperare i dati nei nodi di calcolo batch che non sono ufficialmente integrati nell'API REST di batch. Poiché si ha il controllo sui nodi Azure Batch e si possono eseguire file eseguibili personalizzati, è possibile eseguire il pull dei dati da un numero qualsiasi di origini personalizzate, purché il nodo batch disponga della connettività alla destinazione e di disporre delle credenziali per tale origine nel nodo Azure Batch. Alcuni esempi comuni sono:

- Download dei dati da SQL
- Download dei dati da altri servizi Web/percorsi personalizzati
- Mapping di una condivisione di rete

### <a name="azure-storage"></a>Archiviazione di Azure

L'archiviazione BLOB ha obiettivi di scalabilità per il download. Gli obiettivi di scalabilità della condivisione file di archiviazione di Azure sono identici a quelli per un singolo BLOB. La dimensione influirà sul numero di nodi e pool necessari.

