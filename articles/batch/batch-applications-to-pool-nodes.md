---
title: Copia di applicazioni e dati nei nodi del pool
description: Informazioni su come copiare applicazioni e dati nel pool di nodi.
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

Azure Batch supporta diversi modi per ottenere dati e applicazioni nei nodi di calcolo in modo che i dati e le applicazioni siano disponibili per l'uso da parte delle attività. I dati e le applicazioni potrebbero essere necessari per eseguire l'intero processo e pertanto devono essere installati in ogni nodo. Alcuni potrebbero essere necessari solo per un'attività specifica o devono essere installati per il processo, ma non è necessario essere in ogni nodo. Batch dispone di strumenti per ognuno di questi scenari.

- File di risorse di attività di **avvio**pool: per le applicazioni o i dati che devono essere installati in ogni nodo del pool. Utilizzare questo metodo insieme a un pacchetto dell'applicazione o alla raccolta di file di risorse dell'attività di avvio per eseguire un comando di installazione.  

Esempi: 
- Usare la riga di comando dell'attività di avvio per spostare o installare applicazioni

- Specificare un elenco di file o contenitori specifici in un account di archiviazione di Azure.Specify a list of specific files or containers in an Azure storage account. Per altre informazioni, vedere [add-resourcefile nella documentazione RESTFor](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile) more information see add-resourcefile in REST documentation

- Ogni processo eseguito nel pool esegue MyApplication.exe che deve prima essere installato con MyApplication.msi. Se si utilizza questo meccanismo, è necessario impostare la proprietà di attesa dell'attività di avvio **per l'esito positivo su** **true**. Per ulteriori informazioni, vedere [l'istruzione add-starttask nella documentazione REST.](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)

- **Riferimenti** al pacchetto dell'applicazione nel pool: per le applicazioni o i dati che devono essere installati in ogni nodo del pool. Non è presente alcun comando di installazione associato a un pacchetto dell'applicazione, ma è possibile utilizzare un'attività di avvio per eseguire qualsiasi comando di installazione. Se l'applicazione non richiede l'installazione o è costituita da un numero elevato di file, è possibile utilizzare questo metodo. I pacchetti di applicazioni sono adatti per un numero elevato di file perché combinano un numero elevato di riferimenti ai file in un payload di piccole dimensioni. Se si tenta di includere più di 100 file di risorse separati in un'attività, il servizio Batch potrebbe trovarsi in conflitto con limitazioni interne del sistema per una singola attività. Inoltre, usare i pacchetti dell'applicazione se si dispone di requisiti rigorosi di controllo delle versioni in cui potrebbero essere presenti molte versioni diverse della stessa applicazione ed è necessario scegliere tra di esse. Per altre informazioni, vedere [Distribuire applicazioni per calcolare nodi con pacchetti di applicazioni Batch.For more](https://docs.microsoft.com/azure/batch/batch-application-packages)information, read Deploy applications to compute nodes with Batch application packages .

- File di risorse delle attività di preparazione del **processo:** per le applicazioni o i dati che devono essere installati per l'esecuzione del processo, ma non è necessario installare nell'intero pool. Ad esempio: se il pool dispone di molti tipi diversi di processi e per l'esecuzione di un solo tipo di processo è necessario MyApplication.msi, è opportuno inserire il passaggio di installazione in un'attività di preparazione del processo. Per ulteriori informazioni sulle attività di preparazione dei processi, vedere Eseguire attività di preparazione e rilascio dei processi [nei nodi di calcolo Batch](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/).

- **File di risorse attività:** per quando un'applicazione o i dati sono rilevanti solo per una singola attività. Ad esempio: si dispone di cinque attività, ognuna elabora un file diverso e quindi scrive l'output nell'archiviazione BLOB.  In questo caso, il file di input deve essere specificato nella raccolta di file di risorse delle **attività** perché ogni attività ha il proprio file di input.

## <a name="determine-the-scope-required-of-a-file"></a>Determinare l'ambito richiesto per un file

È necessario determinare l'ambito di un file: è il file necessario per un pool, un processo o un'attività. I file con ambito per il pool devono utilizzare pacchetti di applicazioni del pool o un'attività di avvio. I file con ambito per il processo devono utilizzare un'attività di preparazione del processo. Un buon esempio di file con ambito a livello di pool o di processo sono le applicazioni. I file con ambito per l'attività devono utilizzare i file di risorse dell'attività.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Altri modi per ottenere dati nei nodi di calcolo Batch

Esistono altri modi per ottenere dati nei nodi di calcolo Batch che non sono ufficialmente integrati nell'API REST batch. Poiché si ha il controllo sui nodi batch di Azure ed è possibile eseguire eseguibili personalizzati, è possibile estrarre i dati da qualsiasi numero di origini personalizzate, purché il nodo Batch disponga di connettività alla destinazione e le credenziali per tale origine nel nodo Batch di Azure.Because you have control over Azure Batch nodes, and can run custom executables, you are able to pull data from any number of custom sources as long as the Batch node has connectivity to the target and you have the credentials to that source onto the Azure Batch node. Alcuni esempi comuni sono:

- Download di dati da SQLDownloading data from SQL
- Download di dati da altri servizi Web/percorsi personalizzati
- Mapping di una condivisione di rete

### <a name="azure-storage"></a>Archiviazione di Azure

L'archiviazione BLOB ha obiettivi di scalabilità di download. Gli obiettivi di scalabilità della condivisione file di archiviazione di Azure sono gli stessi di un singolo BLOB. Le dimensioni influiranno sul numero di nodi e pool necessari.

