---
title: Aggiornare le destinazioni di archiviazione della cache HPC di Azure
description: Come modificare le destinazioni di archiviazione della cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 70f350204796099e02f7afe829a6e2e1fdf653c8
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613109"
---
# <a name="edit-storage-targets"></a>Modificare le destinazioni di archiviazione

È possibile rimuovere o modificare le destinazioni di archiviazione con la portale di Azure o usando l'interfaccia della riga di comando di Azure.

A seconda del tipo di archiviazione, è possibile modificare questi valori di destinazione di archiviazione:

* Per le destinazioni di archiviazione BLOB, è possibile modificare il percorso dello spazio dei nomi.

* Per le destinazioni di archiviazione NFS, è possibile modificare i valori seguenti:

  * Percorsi dello spazio dei nomi
  * Sottodirectory di esportazione o esportazione dell'archiviazione associata a un percorso dello spazio dei nomi
  * Modello di utilizzo

Non è possibile modificare il nome, il tipo o il sistema di archiviazione back-end di una destinazione di archiviazione (contenitore BLOB o nome host/indirizzo IP NFS). Se è necessario modificare queste proprietà, eliminare la destinazione di archiviazione e creare una sostituzione con il nuovo valore.

> [!TIP]
> Il [video sulla gestione della cache HPC di Azure](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) illustra come modificare una destinazione di archiviazione nel portale di Azure.

## <a name="remove-a-storage-target"></a>Rimuovere una destinazione di archiviazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per rimuovere una destinazione di archiviazione, aprire la pagina **destinazioni di archiviazione** . Selezionare la destinazione di archiviazione nell'elenco e fare clic sul pulsante **Elimina** .

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

L'eliminazione di una destinazione di archiviazione rimuove l'associazione del sistema di archiviazione con questo sistema di cache HPC di Azure, ma non modifica il sistema di archiviazione back-end. Ad esempio, se è stato usato un contenitore di archiviazione BLOB di Azure, il contenitore e il relativo contenuto continuano a esistere dopo averlo eliminato dalla cache. È possibile aggiungere il contenitore a una cache HPC di Azure diversa, aggiungerla nuovamente a questa cache o eliminarla con la portale di Azure.

Tutte le modifiche apportate ai file archiviati nella cache vengono scritte nel sistema di archiviazione back-end prima della rimozione della destinazione di archiviazione. Questo processo può richiedere più di un'ora se nella cache sono presenti numerosi dati modificati.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Modificare il percorso dello spazio dei nomi di una destinazione di archiviazione BLOB

I percorsi dello spazio dei nomi sono i percorsi utilizzati dai client per montare questa destinazione di archiviazione. Per altre informazioni, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) e [impostare lo spazio dei nomi aggregato](add-namespace-paths.md).

Il percorso dello spazio dei nomi è l'unico aggiornamento che è possibile eseguire in una destinazione di archiviazione BLOB di Azure. Usare il portale di Azure o l'interfaccia della riga di comando di Azure per modificarla.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Usare la pagina **spazio dei nomi** per la cache HPC di Azure. La pagina dello spazio dei nomi è descritta più dettagliatamente nell'articolo [configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

Fare clic sul nome del percorso che si desidera modificare e creare il nuovo percorso nella finestra di modifica visualizzata.

![Screenshot della pagina dello spazio dei nomi dopo aver fatto clic sul percorso di uno spazio dei nomi BLOB. i campi di modifica vengono visualizzati in un riquadro a destra](media/edit-namespace-blob.png)

Dopo aver apportato le modifiche, fare clic su **OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla per annullare** le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Per modificare lo spazio dei nomi della destinazione di archiviazione BLOB con l'interfaccia della riga di comando di Azure, usare il comando [AZ HPC-cache BLOB-Storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update). `--virtual-namespace-path`È possibile modificare solo il valore.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aggiornare una destinazione di archiviazione NFS

Per le destinazioni di archiviazione NFS, è possibile modificare o aggiungere percorsi dello spazio dei nomi virtuali, modificare i valori di esportazione o sottodirectory NFS a cui punta un percorso dello spazio dei nomi e modificare il modello di utilizzo.

Di seguito sono riportati i dettagli:

* [Modificare i valori di spazio dei nomi aggregati](#change-aggregated-namespace-values) (percorso dello spazio dei nomi virtuale, esportazione ed esportazione sottodirectory)
* [Modificare il modello di utilizzo](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Modificare i valori di spazio dei nomi aggregati

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per modificare il percorso dello spazio dei nomi per il client, l'esportazione dell'archiviazione e la sottodirectory Export (se utilizzata).

Leggere le linee guida in [aggiungere percorsi dello spazio dei nomi NFS](add-namespace-paths.md#nfs-namespace-paths) se è necessario un promemoria per la creazione di più percorsi validi in una destinazione di archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Usare la pagina **dello spazio dei nomi** per la cache HPC di Azure per aggiornare i valori dello spazio dei nomi. Questa pagina è descritta più dettagliatamente nell'articolo [configurare lo spazio dei nomi aggregato](add-namespace-paths.md).

![screenshot della pagina dello spazio dei nomi del portale con la pagina di aggiornamento di NFS aperta a destra](media/update-namespace-nfs.png)

1. Fare clic sul nome del percorso che si desidera modificare.
1. Utilizzare la finestra modifica per digitare nuovi valori di percorso virtuale, esportazione o sottodirectory.
1. Dopo avere apportato le modifiche, fare clic su **OK** per aggiornare la destinazione di archiviazione o su **Annulla** per annullare le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Usare l' ``--junction`` opzione nel comando [AZ HPC-cache NFS-storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) per modificare il percorso dello spazio dei nomi, l'esportazione NFS o l'esportazione della sottodirectory.

Il ``--junction`` parametro usa i valori seguenti:

* ``namespace-path`` -Percorso del file virtuale rivolte al client
* ``nfs-export`` -Esportazione del sistema di archiviazione da associare al percorso del client
* ``target-path`` (facoltativo): sottodirectory dell'esportazione, se necessario

Esempio: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

È necessario specificare tutti e tre i valori per ogni percorso nell' ``--junction`` istruzione. Utilizzare i valori esistenti per i valori che non si desidera modificare.

In tutti i comandi Update sono necessari anche il nome della cache, il nome della destinazione di archiviazione e il gruppo di risorse.

Comando di esempio:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Modificare il modello di utilizzo

Il modello di utilizzo influisce sul modo in cui la cache mantiene i dati. Per altre informazioni, vedere [scegliere un modello di utilizzo](hpc-cache-add-storage.md#choose-a-usage-model) .

Per modificare il modello di utilizzo per una destinazione di archiviazione NFS, usare uno di questi metodi.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Modificare il modello di utilizzo dalla pagina **destinazioni di archiviazione** nel portale di Azure. Fare clic sul nome della destinazione di archiviazione da modificare.

![screenshot della pagina di modifica per una destinazione di archiviazione NFS](media/edit-storage-nfs.png)

Usare il selettore a discesa per scegliere un nuovo modello di utilizzo. Fare clic su **OK** per aggiornare la destinazione di archiviazione oppure fare clic su **Annulla** per annullare le modifiche.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Usare il comando [AZ HPC-cache NFS-storage-target Update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target?view=azure-cli-latest#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) .

Il comando Update è quasi identico al comando utilizzato per aggiungere una destinazione di archiviazione NFS. Per informazioni dettagliate ed esempi, vedere [creare una destinazione di archiviazione NFS](hpc-cache-add-storage.md#create-an-nfs-storage-target) .

Per modificare il modello di utilizzo, aggiornare l' ``--nfs3-usage-model`` opzione. Esempio: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Sono necessari anche il nome della cache, il nome della destinazione di archiviazione e i valori del gruppo di risorse.

Se si desidera verificare i nomi dei modelli di utilizzo, utilizzare il comando [AZ HPC-cache Usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Se la cache viene arrestata o non è in uno stato integro, l'aggiornamento verrà applicato dopo l'integrità della cache.

---

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su queste opzioni, vedere [aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md) .
* Per altri suggerimenti sull'uso di percorsi virtuali, vedere [pianificare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .
