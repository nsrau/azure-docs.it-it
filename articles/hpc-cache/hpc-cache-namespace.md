---
title: Creare una cache HPC di Azure
description: Come creare un'istanza di Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: aaa939051a1aeafdb0650119772fc7214506aa8d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582186"
---
# <a name="plan-the-aggregated-namespace"></a>Pianificare lo spazio dei nomi aggregato

La cache HPC di Azure consente ai client di accedere a un'ampia gamma di sistemi di archiviazione tramite uno spazio dei nomi virtuale che nasconde i dettagli del sistema di archiviazione back-end.

Quando si aggiunge una destinazione di archiviazione, è necessario impostare il percorso del file per il client. I computer client montano questo percorso di file e possono eseguire richieste di lettura del file alla cache anziché montare direttamente il sistema di archiviazione.

Poiché la cache HPC di Azure gestisce questa file system virtuale, è possibile modificare la destinazione di archiviazione senza modificare il percorso per il client. Ad esempio, è possibile sostituire un sistema di archiviazione hardware con archiviazione cloud senza che sia necessario riscrivere le procedure rivolte ai client.

## <a name="aggregated-namespace-example"></a>Esempio di spazio dei nomi aggregato

Pianificare lo spazio dei nomi aggregato in modo che i computer client possano comodamente raggiungere le informazioni necessarie, in modo che gli amministratori e i tecnici del flusso di lavoro possano distinguere facilmente i percorsi.

Si consideri, ad esempio, un sistema in cui un'istanza di cache HPC di Azure viene usata per elaborare i dati archiviati nel BLOB di Azure. Per l'analisi sono necessari file di modello archiviati in un Data Center locale.

I dati del modello vengono archiviati in un Data Center e le informazioni necessarie per questo processo vengono archiviate nelle sottodirectory seguenti:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

Il sistema di archiviazione dei Data Center espone le esportazioni seguenti:

    /
    /goldline
    /goldline/templates

I dati da analizzare sono stati copiati in un contenitore di archiviazione BLOB di Azure denominato "SourceCollection" tramite l' [utilità CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Per semplificare l'accesso tramite la cache, provare a creare destinazioni di archiviazione con questi percorsi dello spazio dei nomi virtuali:

| Sistema di archiviazione back-end <br/> (Percorso file NFS o contenitore BLOB) | Percorso dello spazio dei nomi virtuale |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| SourceCollection                        | /Source               |

Una destinazione di archiviazione NFS può avere più percorsi dello spazio dei nomi virtuali, purché ognuno faccia riferimento a un percorso di esportazione univoco.

Poiché i percorsi di origine NFS sono sottodirectory della stessa esportazione, sarà necessario definire più percorsi dello spazio dei nomi dalla stessa destinazione di archiviazione.

| Nome host destinazione archiviazione  | Percorso di esportazione NFS      | Percorso sottodirectory | Percorso dello spazio dei nomi    |
|--------------------------|----------------------|-------------------|-------------------|
| *Indirizzo IP o nome host* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *Indirizzo IP o nome host* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Un'applicazione client può montare la cache e accedere facilmente ai percorsi di file dello spazio dei nomi aggregati ``/source``, ``/templates/sku798`` e ``/templates/sku980``.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver deciso come configurare la file system virtuale, [creare destinazioni di archiviazione](hpc-cache-add-storage.md) per eseguire il mapping dell'archiviazione back-end ai percorsi dei file virtuali per il client.
