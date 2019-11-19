---
title: Aggiornare le destinazioni di archiviazione della cache HPC di Azure
description: Come modificare le destinazioni di archiviazione della cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 115e75c0149a35104d9c3696710bf8231a98743d
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168512"
---
# <a name="edit-storage-targets"></a>Modificare le destinazioni di archiviazione

È possibile modificare le destinazioni di archiviazione per modificare alcune delle relative proprietà. Diverse proprietà sono modificabili per diversi tipi di archiviazione:

* Per le destinazioni di archiviazione BLOB, è possibile modificare il percorso dello spazio dei nomi.

* Per le destinazioni di archiviazione NFS, è possibile modificare queste proprietà:

  * Percorso dello spazio dei nomi
  * Modello di utilizzo
  * Export
  * Esporta sottodirectory

Non è possibile modificare il nome, il tipo o il sistema di archiviazione back-end di una destinazione di archiviazione (contenitore BLOB o nome host/indirizzo IP NFS). Se è necessario modificare queste proprietà, eliminare la destinazione di archiviazione e creare una sostituzione con il nuovo valore.

Per modificare una destinazione di archiviazione, fare clic sul nome della destinazione di archiviazione per aprirne la pagina dei dettagli. Alcuni campi della pagina sono modificabili.

![screenshot della pagina di modifica per una destinazione di archiviazione NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aggiornare una destinazione di archiviazione NFS

Per una destinazione di archiviazione NFS, è possibile aggiornare diverse proprietà. (Per una pagina di modifica di esempio, fare riferimento alla schermata precedente).

* **Modello di utilizzo** : il modello di utilizzo influisce sul modo in cui la cache mantiene i dati. Per altre informazioni, vedere [scegliere un modello di utilizzo](hpc-cache-add-storage.md#choose-a-usage-model) .
* **Percorso dello spazio dei nomi virtuale** : percorso usato dai client per montare questa destinazione di archiviazione. Per informazioni dettagliate, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .
* **Percorso di esportazione NFS** : esportazione del sistema di archiviazione da usare per questo percorso dello spazio dei nomi.
* **Percorso sottodirectory** : sottodirectory (sotto l'esportazione) da associare a questo percorso dello spazio dei nomi. Lasciare vuoto questo campo se non è necessario specificare una sottodirectory.

Ogni percorso dello spazio dei nomi richiede una combinazione univoca di esportazione e sottodirectory. In altre termini, non è possibile creare due percorsi diversi per il client nella stessa directory del sistema di archiviazione back-end.

Dopo aver apportato le modifiche, fare clic su **OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla per annullare** le modifiche.

## <a name="update-an-azure-blob-storage-target"></a>Aggiornare una destinazione di archiviazione BLOB di Azure

La pagina dettagli per una destinazione di archiviazione BLOB consente di modificare il percorso dello spazio dei nomi virtuale.

![screenshot della pagina di modifica per una destinazione di archiviazione BLOB](media/hpc-cache-edit-storage-blob.png)

Al termine, fare clic su **OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla per annullare** le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su queste opzioni, vedere [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md) .
* Per altri suggerimenti sull'uso di percorsi virtuali, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .
