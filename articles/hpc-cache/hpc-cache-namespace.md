---
title: Creare una cache HPC di Azure (anteprima)
description: Come creare un'istanza di Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 677d42dfa1c468417f18ba4222cb0d5fd3ebb189
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180981"
---
# <a name="plan-the-aggregated-namespace"></a>Pianificare lo spazio dei nomi aggregato

Cache HPC di Azure (anteprima) consente ai client di accedere a un'ampia gamma di sistemi di archiviazione tramite uno spazio dei nomi virtuale che nasconde i dettagli del sistema di archiviazione back-end.

Quando si aggiunge una destinazione di archiviazione, è necessario impostare il FilePath per il client. I computer client montano questo FilePath. È possibile modificare la destinazione di archiviazione associata a tale percorso. Ad esempio, è possibile sostituire un sistema di archiviazione hardware con archiviazione cloud senza che sia necessario riscrivere le procedure rivolte ai client.

## <a name="aggregated-namespace-example"></a>Esempio di spazio dei nomi aggregato

Pianificare lo spazio dei nomi aggregato in modo che i computer client possano comodamente raggiungere le informazioni necessarie, mentre gli amministratori e i progettisti di flussi di lavoro possono distinguere facilmente i percorsi.

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

| FilePath NFS back-end o contenitore BLOB | Percorso spazio dei nomi virtuale |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| SourceCollection                        | /Source               |

Poiché i percorsi di origine NFS sono sottodirectory della stessa esportazione, sarà necessario definire più percorsi dello spazio dei nomi dalla stessa destinazione di archiviazione. 

| Nome host destinazione archiviazione  | Percorso di esportazione NFS      | Percorso sottodirectory | Percorso dello spazio dei nomi    |
|--------------------------|----------------------|-------------------|-------------------|
| *Indirizzo IP o nome host* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *Indirizzo IP o nome host* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Un'applicazione client può montare la cache e accedere con facilità ai FilePath dello spazio dei nomi aggregati/source,/templates/sku798 e/templates/sku980.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver deciso come configurare il file system virtuale, [creare destinazioni di archiviazione](hpc-cache-add-storage.md) per eseguire il mapping dell'archiviazione back-end ai percorsi FilePath virtuali per il client.
