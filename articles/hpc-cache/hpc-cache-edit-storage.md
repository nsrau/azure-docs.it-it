---
title: Aggiornare le destinazioni di archiviazione della cache HPC di AzureUpdate Azure HPC Cache storage targets
description: Come modificare le destinazioni di archiviazione della cache HPC di AzureHow to edit Azure HPC Cache storage targets
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866996"
---
# <a name="edit-storage-targets"></a>Modificare le destinazioni di archiviazione

È possibile rimuovere o modificare una destinazione di archiviazione dalla pagina **Destinazioni** di archiviazione della cache.

## <a name="remove-a-storage-target"></a>Rimuovere una destinazione di archiviazioneRemove a storage target

Per rimuovere una destinazione di archiviazione, selezionarla nell'elenco e fare clic sul pulsante **Elimina.**

Questa azione rimuove l'associazione della destinazione di archiviazione con questo sistema di cache HPC di Azure, ma non modifica il sistema di archiviazione back-end. Ad esempio, se è stato usato un contenitore di archiviazione BLOB di Azure, il contenitore e il relativo contenuto esistono ancora dopo averlo eliminato dalla cache. È possibile aggiungere il contenitore a una cache HPC di Azure diversa, aggiungerlo nuovamente a questa cache o eliminarlo con il portale di Azure.You can add the container to a different Azure HPC Cache, re-add it to this cache, or delete it with the Azure portal.

Tutte le modifiche ai file archiviate nella cache vengono scritte nel sistema di archiviazione back-end prima della rimozione della destinazione di archiviazione. Questo processo può richiedere un'ora o più se molti dati modificati si trova nella cache.

## <a name="update-storage-targets"></a>Aggiornare gli obiettivi di archiviazioneUpdate storage targets

È possibile modificare le destinazioni di archiviazione per modificarne alcune proprietà. Proprietà diverse sono modificabili per diversi tipi di archiviazione:Different properties are editable for different types of storage:

* Per le destinazioni di archiviazione BLOB, è possibile modificare il percorso dello spazio dei nomi.

* Per le destinazioni di archiviazione NFS, è possibile modificare queste proprietà:For NFS storage targets, you can change these properties:

  * Percorso dello spazio dei nomiNamespace path
  * Modello di utilizzo
  * Esportazione
  * Sottodirectory di esportazione

Non è possibile modificare il nome, il tipo o il sistema di archiviazione back-end di una destinazione di archiviazione (contenitore BLOB o nome host/indirizzo IP NFS). Se è necessario modificare queste proprietà, eliminare la destinazione di archiviazione e creare una sostituzione con il nuovo valore.

Per modificare una destinazione di archiviazione, fare clic sul nome della destinazione di archiviazione per aprire la relativa pagina dei dettagli. Alcuni campi della pagina sono modificabili.

![Schermata della pagina di modifica per una destinazione di archiviazione NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aggiornare una destinazione di archiviazione NFSUpdate an NFS storage target

Per una destinazione di archiviazione NFS, è possibile aggiornare diverse proprietà. (Fare riferimento alla schermata precedente per una pagina di modifica di esempio.)

* Modello di **utilizzo:** il modello di utilizzo influisce sul modo in cui la cache conserva i dati. Per altre informazioni, vedere Scegliere un modello di [utilizzo.](hpc-cache-add-storage.md#choose-a-usage-model)
* **Percorso dello spazio dei nomi virtuale:** percorso utilizzato dai client per montare questa destinazione di archiviazione. Per informazioni dettagliate, vedere [Pianificare lo spazio dei nomi aggregato.](hpc-cache-namespace.md)
* **Percorso di esportazione NFS:** esportazione del sistema di archiviazione da utilizzare per questo percorso dello spazio dei nomi.
* **Percorso sottodirectory** - La sottodirectory (nell'esportazione) da associare al percorso dello spazio dei nomi. Lasciare vuoto questo campo se non è necessario specificare una sottodirectory.

Ogni percorso dello spazio dei nomi necessita di una combinazione univoca di esportazione e sottodirectory. Ovvero, non è possibile creare due percorsi rivolti al client diversi per la stessa directory nel sistema di archiviazione back-end.

Dopo aver apportato le modifiche, fare clic **su OK** per aggiornare la destinazione di archiviazione **oppure** fare clic su Annulla per annullare le modifiche.

## <a name="update-an-azure-blob-storage-target"></a>Aggiornare una destinazione di archiviazione BLOB di AzureUpdate an Azure Blob storage target

La pagina dei dettagli per una destinazione di archiviazione BLOB consente di modificare il percorso dello spazio dei nomi virtuale.

![Screenshot della pagina di modifica per una destinazione di archiviazione BLOB](media/hpc-cache-edit-storage-blob.png)

Al termine, fare clic **su OK** per aggiornare la destinazione di archiviazione **oppure** fare clic su Annulla per annullare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su queste opzioni, vedere [Aggiungere destinazioni di archiviazione.](hpc-cache-add-storage.md)
* Per altri suggerimenti sull'uso dei percorsi virtuali, vedere [Pianificare lo spazio dei nomi aggregato.](hpc-cache-namespace.md)
