---
title: Aggiornare le destinazioni di archiviazione della cache HPC di Azure
description: Come modificare le destinazioni di archiviazione della cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092457"
---
# <a name="edit-storage-targets"></a>Modificare le destinazioni di archiviazione

È possibile rimuovere o modificare una destinazione di archiviazione dalla pagina del portale **destinazioni di archiviazione** della cache o usando l'interfaccia della riga di comando di Azure.

> [!TIP]
> Il [video sulla gestione della cache HPC di Azure](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) illustra come modificare una destinazione di archiviazione nel portale di Azure.

## <a name="remove-a-storage-target"></a>Rimuovere una destinazione di archiviazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per rimuovere una destinazione di archiviazione, selezionarla nell'elenco e fare clic sul pulsante **Elimina** .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Usare [AZ HPC-cache storage-target Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) per eliminare una destinazione di archiviazione dalla cache.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Questa azione rimuove l'associazione di destinazione di archiviazione con questo sistema di cache HPC di Azure, ma non modifica il sistema di archiviazione back-end. Ad esempio, se è stato usato un contenitore di archiviazione BLOB di Azure, il contenitore e il relativo contenuto continuano a esistere dopo averlo eliminato dalla cache. È possibile aggiungere il contenitore a una cache HPC di Azure diversa, aggiungerla nuovamente a questa cache o eliminarla con la portale di Azure.

Tutte le modifiche apportate ai file archiviati nella cache vengono scritte nel sistema di archiviazione back-end prima della rimozione della destinazione di archiviazione. Questo processo può richiedere più di un'ora se nella cache sono presenti numerosi dati modificati.

## <a name="update-storage-targets"></a>Aggiornare le destinazioni di archiviazione

È possibile modificare le destinazioni di archiviazione per modificare alcune delle relative proprietà. Diverse proprietà sono modificabili per diversi tipi di archiviazione:

* Per le destinazioni di archiviazione BLOB, è possibile modificare il percorso dello spazio dei nomi.

* Per le destinazioni di archiviazione NFS, è possibile modificare queste proprietà:

  * Percorso dello spazio dei nomi
  * Modello di utilizzo
  * Esportazione
  * Esporta sottodirectory

Non è possibile modificare il nome, il tipo o il sistema di archiviazione back-end di una destinazione di archiviazione (contenitore BLOB o nome host/indirizzo IP NFS). Se è necessario modificare queste proprietà, eliminare la destinazione di archiviazione e creare una sostituzione con il nuovo valore.

Nella portale di Azure è possibile visualizzare i campi modificabili facendo clic sul nome della destinazione di archiviazione e aprendo la relativa pagina dei dettagli. È anche possibile modificare le destinazioni di archiviazione con l'interfaccia della riga di comando di Azure.

![screenshot della pagina di modifica per una destinazione di archiviazione NFS](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Aggiornare una destinazione di archiviazione NFS

Per una destinazione di archiviazione NFS, è possibile aggiornare diverse proprietà. (Per una pagina di modifica di esempio, fare riferimento alla schermata precedente).

* **Modello di utilizzo** : il modello di utilizzo influisce sul modo in cui la cache mantiene i dati. Per altre informazioni, vedere [scegliere un modello di utilizzo](hpc-cache-add-storage.md#choose-a-usage-model) .
* **Percorso dello spazio dei nomi virtuale** : percorso usato dai client per montare questa destinazione di archiviazione. Per informazioni dettagliate, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .
* **Percorso di esportazione NFS** : esportazione del sistema di archiviazione da usare per questo percorso dello spazio dei nomi.
* **Percorso sottodirectory** : sottodirectory (sotto l'esportazione) da associare a questo percorso dello spazio dei nomi. Lasciare vuoto questo campo se non è necessario specificare una sottodirectory.

Ogni percorso dello spazio dei nomi richiede una combinazione univoca di esportazione e sottodirectory. In altre termini, non è possibile creare due percorsi diversi per il client nella stessa directory del sistema di archiviazione back-end.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dopo aver apportato le modifiche, fare clic su **OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla per annullare** le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Usare il comando [AZ NFS-storage-target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) per modificare il modello di utilizzo, il percorso dello spazio dei nomi virtuale e i valori di esportazione o sottodirectory NFS per una destinazione di archiviazione.

* Per modificare il modello di utilizzo, utilizzare l' ``--nfs3-usage-model`` opzione. Esempio: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Per modificare il percorso dello spazio dei nomi, esportare o esportare la sottodirectory, utilizzare l' ``--junction`` opzione.

  Il ``--junction`` parametro usa i valori seguenti:

  * ``namespace-path``-Percorso del file virtuale rivolte al client
  * ``nfs-export``-Esportazione del sistema di archiviazione da associare al percorso del client
  * ``target-path``(facoltativo): sottodirectory dell'esportazione, se necessario

  Esempio: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Il nome della cache, il nome della destinazione di archiviazione e il gruppo di risorse sono necessari in tutti i comandi di aggiornamento.

Esempio del comando: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Se la cache viene arrestata o non è in uno stato integro, l'aggiornamento viene applicato dopo l'integrità della cache.

---

## <a name="update-an-azure-blob-storage-target"></a>Aggiornare una destinazione di archiviazione BLOB di Azure

Per una destinazione di archiviazione BLOB, è possibile modificare il percorso dello spazio dei nomi virtuale.

### <a name="portal"></a>[Portale](#tab/azure-portal)

La pagina dettagli per una destinazione di archiviazione BLOB consente di modificare il percorso dello spazio dei nomi virtuale.

![screenshot della pagina di modifica per una destinazione di archiviazione BLOB](media/hpc-cache-edit-storage-blob.png)

Al termine, fare clic su **OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla per annullare** le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Usare [AZ HPC-cache BLOB-Storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) per aggiornare il percorso dello spazio dei nomi di una destinazione.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Se la cache viene arrestata o non è in uno stato integro, l'aggiornamento verrà applicato dopo l'integrità della cache.

---

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su queste opzioni, vedere [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md) .
* Per altri suggerimenti sull'uso di percorsi virtuali, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .
