---
title: Abilitare l'eliminazione temporanea - Condivisioni file di Azure
description: Informazioni su come abilitare la funzionalità di eliminazione temporanea per le condivisioni file di Azure per il ripristino dei dati e la prevenzione dell'eliminazione accidentale.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: ea98b2d9812fb5c848c7e13b94d46a4142595cd4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492166"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Abilitare l'eliminazione temporanea in condivisioni file di Azure

Archiviazione di Azure offre l'eliminazione temporanea per le condivisioni file, per consentire di ripristinare più facilmente i dati nel caso in cui vengano erroneamente eliminati da un'applicazione o da un utente con un altro account di archiviazione. Per altre informazioni sull'eliminazione temporanea, vedere [Come evitare l'eliminazione accidentale di condivisioni file di Azure](storage-files-prevent-file-share-deletion.md).

Le sezioni seguenti illustrano come abilitare e usare l'eliminazione temporanea per le condivisioni file di Azure in un account di archiviazione esistente:

# <a name="portal"></a>[Portale](#tab/azure-portal)

## <a name="getting-started"></a>Introduzione

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare all'account di archiviazione e selezionare **Eliminazione temporanea** in **Servizio file**.
1. Per **Eliminazione temporanea della condivisione file**, selezionare **Abilitata**.
1. Selezionare **Periodo di conservazione della condivisione file in giorni** e immettere una scelta numerica.
1. Selezionare **Salva** per confermare le impostazioni di conservazione dei dati.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Screenshot del riquadro impostazioni di eliminazione temporanea dell'account di archiviazione. Evidenziando la sezione relativa alle condivisioni, abilitare l'opzione di attivazione, impostare un periodo di conservazione e salvarlo. Questa operazione consentirà l'eliminazione temporanea per tutte le condivisioni file nell'account di archiviazione.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I cmdlet per l'eliminazione temporanea sono disponibili nella versione 2.1.3 e successive del [modulo dell'interfaccia](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)della riga di comando di Azure.

## <a name="getting-started-with-cli"></a>Introduzione all'interfaccia della riga di comando

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client file. Nell'esempio seguente viene abilitata l'eliminazione temporanea per tutte le condivisioni file in un account di archiviazione:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Per verificare se l'eliminazione temporanea è abilitata e visualizzarne i criteri di conservazione, usare il comando seguente:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Prerequisito

I cmdlet per l'eliminazione temporanea sono disponibili in 4.8.0 e nelle versioni più recenti del modulo AZ. storage. 

## <a name="getting-started-with-powershell"></a>Introduzione a PowerShell

Per abilitare l'eliminazione temporanea, aggiornare le proprietà del servizio del client file. Nell'esempio seguente viene abilitata l'eliminazione temporanea per tutte le condivisioni file in un account di archiviazione:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Per verificare se l'eliminazione temporanea è abilitata e visualizzarne i criteri di conservazione, usare il comando seguente:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Ripristinare una condivisione file eliminata temporaneamente

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per ripristinare una condivisione file eliminata temporaneamente:

1. Passare all'account di archiviazione e selezionare **Condivisioni file**.
1. Nel pannello delle condivisioni file, abilitare **Mostra condivisioni eliminate** per visualizzare le condivisioni eliminate temporaneamente.

    Verranno visualizzate le condivisioni attualmente con stato **Eliminata**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Se la colonna Stato, ovvero la colonna accanto alla colonna Nome, è impostata su Eliminata, la condivisione file si trova in uno stato di eliminazione temporanea e verrà eliminata definitivamente dopo il periodo di conservazione specificato.":::

1. Selezionare la condivisione, quindi selezionare **Annulla eliminazione**: la condivisione verrà ripristinata.

    È possibile verificare che la condivisione è stata ripristinata perché lo stato passa ad **Attiva**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Se la colonna Stato, ovvero la colonna accanto alla colonna Nome, è impostata su Attiva, la condivisione file è stata ripristinata.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I cmdlet per l'eliminazione temporanea sono disponibili nella versione 2.1.3 dell'interfaccia della riga di comando di Azure. Per ripristinare una condivisione file eliminata temporaneamente, è innanzitutto necessario ottenere il `--deleted-version` valore della condivisione. Per ottenere tale valore, usare il comando seguente per elencare tutte le condivisioni eliminate per l'account di archiviazione:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Una volta identificata la condivisione che si vuole ripristinare, è possibile usarla con il comando seguente per ripristinarla:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I cmdlet per l'eliminazione temporanea sono disponibili in 4.8.0 e nelle versioni più recenti del modulo AZ. storage. Per ripristinare una condivisione file eliminata temporaneamente, è innanzitutto necessario ottenere il `-DeletedShareVersion` valore della condivisione. Per ottenere tale valore, usare il comando seguente per elencare tutte le condivisioni eliminate per l'account di archiviazione:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Una volta identificata la condivisione che si vuole ripristinare, è possibile usarla con il comando seguente per ripristinarla:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Disabilitare l'eliminazione temporanea

Per interrompere l'uso dell'eliminazione temporanea o eliminare definitivamente una condivisione file, procedere come segue:

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare all'account di archiviazione e selezionare **Eliminazione temporanea** in **Impostazioni**.
1. In **Condivisioni file**, selezionare **Disabilitata** per **Eliminazione temporanea per le condivisioni file**.
1. Selezionare **Salva** per confermare le impostazioni di conservazione dei dati.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Se si disabilita l'eliminazione temporanea, sarà possibile eliminare immediatamente e definitivamente tutte le condivisioni file nell'account di archiviazione.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I cmdlet per l'eliminazione temporanea sono disponibili nella versione 2.1.3 dell'interfaccia della riga di comando di Azure. Per disabilitare l'eliminazione temporanea nell'account di archiviazione è possibile usare il comando seguente:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I cmdlet per l'eliminazione temporanea sono disponibili in 4.8.0 e nelle versioni più recenti del modulo AZ. storage. Per disabilitare l'eliminazione temporanea nell'account di archiviazione è possibile usare il comando seguente:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su un'altra forma di protezione e ripristino dei dati, vedere l'articolo [Panoramica degli snapshot di condivisione per file di Azure](storage-snapshots-files.md).
