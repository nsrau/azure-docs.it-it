---
title: Informazioni sullo spazio dei nomi aggregato della cache HPC di Azure
description: Come pianificare lo spazio dei nomi virtuale per la cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 1c28f549cf93d77f6aef6bcde6a2225345a79cc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612949"
---
# <a name="plan-the-aggregated-namespace"></a>Pianificare lo spazio dei nomi aggregato

La cache HPC di Azure consente ai client di accedere a un'ampia gamma di sistemi di archiviazione tramite uno spazio dei nomi virtuale che nasconde i dettagli del sistema di archiviazione back-end.

Dopo aver aggiunto una destinazione di archiviazione, è necessario configurare uno o più percorsi dello spazio dei nomi per la destinazione di archiviazione. I computer client montano questo percorso di file e possono eseguire richieste di lettura del file alla cache anziché montare direttamente il sistema di archiviazione.

Poiché la cache HPC di Azure gestisce questa file system virtuale, è possibile modificare la destinazione di archiviazione senza modificare il percorso per il client. Ad esempio, è possibile sostituire un sistema di archiviazione hardware con archiviazione cloud senza dover riscrivere le procedure lato client.

## <a name="aggregated-namespace-example"></a>Esempio di spazio dei nomi aggregato

Pianificare lo spazio dei nomi aggregato in modo che i computer client possano comodamente raggiungere le informazioni necessarie, in modo che gli amministratori e i tecnici del flusso di lavoro possano distinguere facilmente i percorsi.

Si consideri, ad esempio, un sistema in cui un'istanza di cache HPC di Azure viene usata per elaborare i dati archiviati nel BLOB di Azure. Per l'analisi sono necessari file di modello archiviati in un Data Center locale.

I dati del modello vengono archiviati in un Data Center e le informazioni necessarie per questo processo vengono archiviate nelle sottodirectory seguenti:

* */goldline/templates/acme2017/sku798*
* */goldline/templates/acme2017/sku980*

Il sistema di archiviazione dei Data Center espone le esportazioni seguenti:

* */*
* */goldline*
* */goldline/templates*

I dati da analizzare sono stati copiati in un contenitore di archiviazione BLOB di Azure denominato "SourceCollection" tramite l' [utilità CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Per semplificare l'accesso tramite la cache, provare a creare destinazioni di archiviazione con questi percorsi dello spazio dei nomi virtuali:

| Sistema di archiviazione back-end <br/> (Percorso file NFS o contenitore BLOB) | Percorso dello spazio dei nomi virtuale |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| SourceCollection                        | /Source               |

Una destinazione di archiviazione NFS può avere più percorsi dello spazio dei nomi virtuali, purché ognuno faccia riferimento a un percorso di esportazione univoco. (Leggere i [percorsi dello spazio dei nomi NFS](add-namespace-paths.md#nfs-namespace-paths) per conoscere il numero massimo consigliato di percorsi dello spazio dei nomi per ogni destinazione di archiviazione NFS).

Poiché i percorsi di origine NFS sono sottodirectory della stessa esportazione, sarà necessario definire più percorsi dello spazio dei nomi dalla stessa destinazione di archiviazione.

| Nome host destinazione archiviazione  | Percorso di esportazione NFS     | Percorso sottodirectory | Percorso dello spazio dei nomi    |
|--------------------------|---------------------|-------------------|-------------------|
| *Indirizzo IP o nome host* | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| *Indirizzo IP o nome host* | /goldline/templates | acme2017/sku980   | /templates/sku980 |

Un'applicazione client può montare la cache e accedere facilmente ai percorsi di file dello spazio dei nomi aggregati ``/source`` , ``/templates/sku798`` e ``/templates/sku980`` .

Un approccio alternativo potrebbe consistere nel creare un percorso virtuale simile `/templates` a quello collegato alla directory padre, `acme2017` , e quindi fare in modo che i client accedano alle singole `sku798` `sku980` Directory e dopo il montaggio della cache. Tuttavia, non è possibile creare un percorso dello spazio dei nomi che è una sottodirectory di un altro percorso dello spazio dei nomi. Se quindi si crea un percorso alla `acme2017` Directory, non è possibile creare percorsi dello spazio dei nomi per accedere direttamente alle relative sottodirectory.

La pagina delle impostazioni **dello spazio dei nomi** della cache HPC di Azure Mostra il file System per il client e consente di aggiungere o modificare i percorsi. Per informazioni dettagliate, vedere [configurare lo spazio dei nomi aggregato](add-namespace-paths.md) .

## <a name="next-steps"></a>Passaggi successivi

Dopo aver deciso come configurare la file system virtuale, eseguire la procedura seguente per crearla:

* [Creare destinazioni di archiviazione](hpc-cache-add-storage.md) per aggiungere i sistemi di archiviazione back-end alla cache HPC di Azure
* [Aggiungere i percorsi dello spazio dei nomi](add-namespace-paths.md) per creare lo spazio dei nomi aggregato utilizzato dai computer client per accedere ai file
