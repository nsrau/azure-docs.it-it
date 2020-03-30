---
title: Creare una cache HPC di Azure
description: Come creare un'istanza di Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582186"
---
# <a name="plan-the-aggregated-namespace"></a>Pianificare lo spazio dei nomi aggregato

Cache HPC di Azure consente ai client di accedere a un'ampia gamma di sistemi di archiviazione tramite uno spazio dei nomi virtuale che nasconde i dettagli del sistema di archiviazione back-end.

Quando si aggiunge una destinazione di archiviazione, si imposta il percorso del file rivolto al client. I computer client montano questo percorso di file e possono effettuare richieste di lettura dei file nella cache anziché montare direttamente il sistema di archiviazione.

Poiché la cache HPC di Azure gestisce questo file system virtuale, è possibile modificare la destinazione di archiviazione senza modificare il percorso rivolto al client. Ad esempio, è possibile sostituire un sistema di archiviazione hardware con l'archiviazione cloud senza dover riscrivere le procedure rivolte al client.

## <a name="aggregated-namespace-example"></a>Esempio di spazio dei nomi aggregato

Pianificare lo spazio dei nomi aggregato in modo che i computer client possano raggiungere comodamente le informazioni necessarie e che gli amministratori e i tecnici del flusso di lavoro possano distinguere facilmente i percorsi.

Si consideri ad esempio un sistema in cui viene usata un'istanza della cache HPC di Azure per elaborare i dati archiviati nel BLOB di Azure.For example, consider a system where an Azure HPC Cache instance is being used to process data stored in Azure Blob. L'analisi richiede file di modello archiviati in un data center locale.

I dati del modello vengono archiviati in un data center e le informazioni necessarie per questo processo vengono archiviate in queste sottodirectory:The template data is stored in a datacenter, and the information needed for this job is stored in these subdirectories:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Il sistema di archiviazione del data center espone queste esportazioni:The datacenter storage system exposes these exports:

    /
    /goldline
    /goldline/templates

I dati da analizzare sono stati copiati in un contenitore di archiviazione BLOB di Azure denominato "sourcecollection" utilizzando [l'utilità CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Per consentire un facile accesso tramite la cache, è consigliabile creare destinazioni di archiviazione con questi percorsi dello spazio dei nomi virtuali:To allow easy access through the cache, consider creating storage targets with these virtual namespace paths:

| Sistema di archiviazione back-end <br/> (percorso file NFS o contenitore BLOB) | Percorso dello spazio dei nomi virtualeVirtual namespace path |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798 (informazioni in lingua inglese)      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source/               |

Una destinazione di archiviazione NFS può avere più percorsi dello spazio dei nomi virtuali, purché ognuno faccia riferimento a un percorso di esportazione univoco.

Poiché i percorsi di origine NFS sono sottodirectory della stessa esportazione, è necessario definire più percorsi dello spazio dei nomi dalla stessa destinazione di archiviazione.

| Nome host di destinazione dell'archiviazione  | Percorso di esportazione NFS      | Percorso sottodirectory | Percorso dello spazio dei nomiNamespace path    |
|--------------------------|----------------------|-------------------|-------------------|
| *Indirizzo IP o nome host* | /goldline/modelli  | acme2017/sku798   | /templates/sku798 (informazioni in lingua inglese) |
| *Indirizzo IP o nome host* | /goldline/modelli  | acme2017/sku980   | /templates/sku980 |

Un'applicazione client può montare la cache e ``/source`` ``/templates/sku798``accedere ``/templates/sku980``facilmente ai percorsi dei file dello spazio dei nomi aggregati, , e .

## <a name="next-steps"></a>Passaggi successivi

Dopo aver deciso come configurare il file system virtuale, [creare destinazioni](hpc-cache-add-storage.md) di archiviazione per eseguire il mapping dell'archiviazione back-end ai percorsi dei file virtuali rivolti al client.
