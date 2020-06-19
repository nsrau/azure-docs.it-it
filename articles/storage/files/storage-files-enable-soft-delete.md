---
title: Abilitare l'eliminazione temporanea - Condivisioni file di Azure
description: Informazioni su come abilitare la funzionalità di eliminazione temporanea per le condivisioni file di Azure per il ripristino dei dati e la prevenzione dell'eliminazione accidentale.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 755ccd266b8a88471bf7dbc0d2deb790f35f3457
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83883014"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Abilitare l'eliminazione temporanea in condivisioni file di Azure

Archiviazione di Azure offre l'eliminazione temporanea per le condivisioni file, per consentire di ripristinare più facilmente i dati nel caso in cui vengano erroneamente eliminati da un'applicazione o da un utente con un altro account di archiviazione. Per altre informazioni sull'eliminazione temporanea, vedere [Come evitare l'eliminazione accidentale di condivisioni file di Azure](storage-files-prevent-file-share-deletion.md).

Le sezioni seguenti illustrano come abilitare e usare l'eliminazione temporanea per le condivisioni file di Azure in un account di archiviazione esistente:

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare all'account di archiviazione e selezionare **Eliminazione temporanea** in **Servizio file**.
1. Per **Eliminazione temporanea della condivisione file**, selezionare **Abilitata**.
1. Selezionare **Periodo di conservazione della condivisione file in giorni** e immettere una scelta numerica.
1. Selezionare **Salva** per confermare le impostazioni di conservazione dei dati.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Screenshot del riquadro impostazioni di eliminazione temporanea dell'account di archiviazione. Evidenziando la sezione relativa alle condivisioni, abilitare l'opzione di attivazione, impostare un periodo di conservazione e salvarlo. Questa operazione consentirà l'eliminazione temporanea per tutte le condivisioni file nell'account di archiviazione.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per ripristinare una condivisione file temporaneamente eliminata, usare il comando seguente:

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per disabilitare l'eliminazione temporanea nell'account di archiviazione è possibile usare il comando seguente:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---