---
title: Abilitare e gestire l'eliminazione temporanea per i BLOB
titleSuffix: Azure Storage
description: Abilitare l'eliminazione temporanea per i BLOB per ripristinare i dati in modo più semplice quando vengono erroneamente modificati o eliminati.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 07e306c6c428a0e3a3f64a29a2574cb0378bb81f
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230229"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Abilitare e gestire l'eliminazione temporanea per i BLOB

L'eliminazione temporanea BLOB protegge i dati da modifiche o modifiche accidentali o eliminate in modo errato. Quando l'eliminazione temporanea BLOB è abilitata per un account di archiviazione, i BLOB, le versioni BLOB e gli snapshot nell'account di archiviazione possono essere ripristinati dopo l'eliminazione, entro un periodo di conservazione specificato.

Se è possibile che i dati vengano accidentalmente modificati o eliminati da un'applicazione o da un altro utente dell'account di archiviazione, Microsoft consiglia di attivare l'eliminazione temporanea del BLOB. Questo articolo illustra come abilitare l'eliminazione temporanea per i BLOB. Per altri dettagli sull'eliminazione temporanea dei BLOB, vedere [eliminazione temporanea per i BLOB](soft-delete-blob-overview.md).

Per informazioni su come abilitare anche l'eliminazione temporanea per i contenitori, vedere [Enable and Manage soft delete for Containers](soft-delete-container-enable.md).

## <a name="enable-blob-soft-delete"></a>Abilita eliminazione temporanea BLOB

# <a name="portal"></a>[Portale](#tab/azure-portal)

Abilitare l'eliminazione temporanea per i BLOB nell'account di archiviazione usando il portale di Azure:

1. Nel [portale di Azure](https://portal.azure.com/) passare all'account di archiviazione.
1. Individuare l'opzione **protezione dati** in **servizio BLOB**.
1. Impostare la proprietà **eliminazione temporanea BLOB** su *abilitato*.
1. In **criteri di conservazione**specificare per quanto tempo i BLOB eliminati temporaneamente vengono conservati da archiviazione di Azure.
1. Salvare le modifiche.

![Screenshot del portale di Azure con il servizio BLOB di protezione dati eletto.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

Per visualizzare i BLOB eliminati temporaneamente, selezionare la casella di controllo **Mostra BLOB eliminati**.

![Screenshot della pagina del servizio BLOB di protezione dati con l'opzione Mostra BLOB eliminati evidenziata.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Per visualizzare gli snapshot eliminati temporaneamente per un determinato BLOB, selezionare il BLOB, quindi fare clic su **Visualizza snapshot**.

![Screenshot della pagina del servizio BLOB di protezione dati con l'opzione Visualizza Snapshots evidenziata.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Verificare che la casella di controllo **Mostra snapshot eliminati** sia selezionata.

![Screenshot della pagina Visualizza snapshot con l'opzione Mostra BLOB eliminati evidenziata.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando si fa clic su un BLOB o uno snapshot eliminato temporaneamente, si notino le nuove proprietà del BLOB. Esse indicano quando l'oggetto è stato eliminato e il numero di giorni rimanenti fino alla scadenza definitiva del BLOB o dello snapshot del BLOB. Se l'oggetto eliminato temporaneamente non è uno snapshot, è anche possibile annullarne l'eliminazione.

![Screenshot dei dettagli di un oggetto eliminato temporaneamente.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

Tenere presente che annullando l'eliminazione di un BLOB verrà annullata anche l'eliminazione di tutti gli snapshot associati ad esso. Per annullare l'eliminazione degli snapshot eliminati temporaneamente per un BLOB attivo, fare clic sul BLOB e selezionare **Undelete all snapshots** (Annulla l'eliminazione di tutti gli snapshot).

![Screenshot dei dettagli di un BLOB eliminato temporaneamente.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Dopo aver annullato l'eliminazione degli snapshot di un BLOB, è possibile fare clic su **Alza di livello** per copiare uno snapshot sul BLOB radice, ripristinando in questo modo il BLOB allo snapshot.

![Screenshot della pagina Visualizza snapshot con l'opzione promo evidenziata.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB. L'esempio seguente abilita l'eliminazione temporanea per un subset di account in una sottoscrizione:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

Per verificare l'attivazione dell'eliminazione temporanea, usare questo comando:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Per ripristinare i BLOB eliminati accidentalmente, chiamare **Undelete BLOB** su tali BLOB. Tenere presente che chiamando **Undelete Blob** sia sui BLOB attivi che su quelli eliminati temporaneamente, tutti gli snapshot eliminati temporaneamente associati ad essi verranno ripristinati come attivi. Nell'esempio seguente viene chiamato **Undelete BLOB** su tutti i BLOB eliminati temporaneamente e attivi in un contenitore:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Per trovare i criteri di conservazione dell'eliminazione temporanea corrente, usare questo comando:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Per verificare se l'eliminazione temporanea è attivata, usare il comando seguente: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Per ripristinare i BLOB eliminati accidentalmente, chiamare Undelete su tali BLOB. Tenere presente che chiamando **Annulla Elimina** sia sui BLOB attivi che su quelli eliminati temporaneamente, tutti gli snapshot eliminati temporaneamente associati ad essi verranno ripristinati come attivi. L'esempio seguente chiama Undelete su tutti i BLOB eliminati temporaneamente e attivi presenti in un contenitore:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Per eseguire il ripristino a una versione di BLOB specifica, chiamare prima Undelete su un BLOB, quindi copiare lo snapshot desiderato su di esso. L'esempio seguente ripristina un BLOB in blocchi al relativo ultimo snapshot generato:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client BLOB:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Per ripristinare i BLOB eliminati accidentalmente, chiamare **Undelete BLOB** su tali BLOB. Tenere presente che chiamando **Undelete Blob** sia sui BLOB attivi che su quelli eliminati temporaneamente, tutti gli snapshot eliminati temporaneamente associati ad essi verranno ripristinati come attivi. Nell'esempio seguente viene chiamato **Undelete BLOB** su tutti i BLOB eliminati temporaneamente e attivi in un contenitore:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Per eseguire il ripristino in una versione specifica del BLOB, chiamare prima l'operazione di **annullamento dell'eliminazione del BLOB** , quindi copiare lo snapshot desiderato sul BLOB. L'esempio seguente ripristina un BLOB in blocchi al relativo ultimo snapshot generato:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>Passaggi successivi

- [Eliminazione temporanea per l'archiviazione BLOB](soft-delete-overview.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)
