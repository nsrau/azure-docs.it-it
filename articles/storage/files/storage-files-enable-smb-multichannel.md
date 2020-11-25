---
title: Abilita SMB multicanale
description: Informazioni su come abilitare SMB multicanale in condivisioni file Premium di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2444ec28a2618b638f78926e214de468f56c5e52
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "95995479"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Abilitare SMB multicanale in un account filestorage (anteprima) 

Gli account Azure filestorage supportano SMB multicanale (anteprima), che aumenta le prestazioni di un client SMB 3. x stabilendo più connessioni di rete alle condivisioni file Premium. Questo articolo fornisce istruzioni dettagliate per abilitare SMB multicanale in un account di archiviazione esistente. Per informazioni dettagliate su File di Azure SMB multicanale, vedere prestazioni SMB multicanale.

## <a name="limitations"></a>Limitazioni

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Creare un account filestorage](storage-how-to-create-premium-fileshare.md).
- Se si intende usare il modulo Azure PowerShell, [installare la versione 3.0.1-Preview del modulo](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Introduzione

Aprire una finestra di PowerShell e accedere alla sottoscrizione di Azure. Da qui è possibile eseguire la registrazione per l'anteprima SMB multicanale con i comandi seguenti.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> La registrazione può richiedere fino a un'ora.

### <a name="verify-that-feature-registration-is-complete"></a>Verificare che la registrazione delle funzionalità sia stata completata

Poiché può richiedere fino a un'ora per abilitare la funzionalità nell'account di archiviazione, è possibile usare il comando seguente per verificare che sia registrato per la sottoscrizione:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Abilitare SMB multicanale 
Dopo aver creato un account filestorage, è possibile seguire le istruzioni per aggiornare le impostazioni SMB multicanale per l'account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Accedere al portale di Azure e passare all'account di archiviazione filestorage su cui si vuole configurare SMB multicanale.
1. Selezionare **condivisioni file** in **servizio file**, quindi selezionare **Impostazioni condivisione file**.
1. Impostare **SMB multicanale** **su on** (o **off** per disabilitare) e selezionare **Save (Salva**).

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Screenshot dell'account di archiviazione, SMB multicanale attivato.":::

Se l'opzione SMB multicanale non è visibile nelle **impostazioni di condivisione file** o se si verifica un errore durante l'aggiornamento della configurazione, verificare che la sottoscrizione sia registrata e che l'account si trovi in una delle [aree supportate](#regional-availability) con il tipo di account e la replica supportati.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per abilitare SMB multicanale con il modulo Azure PowerShell, è necessario [installare la versione 3.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) del modulo.

`$resourceGroupName` `$storageAccountName` Prima di eseguire questi comandi di PowerShell, impostare le variabili e il gruppo di risorse e l'account di archiviazione.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
L'interfaccia della riga di comando di Azure non supporta ancora la configurazione di SMB multicanale. Vedere le istruzioni del portale per configurare SMB multicanale nell'account di archiviazione.

---

> [!NOTE]
> Tutte le modifiche apportate alle impostazioni di configurazione SMB multicanale verranno applicate a tutte le condivisioni file nell'account di archiviazione. Per rendere effettive le modifiche, sarà necessario rimontare la condivisione sul client.


## <a name="next-steps"></a>Passaggi successivi 

- [Rimontare la condivisione file](storage-how-to-use-files-windows.md) per sfruttare i vantaggi di SMB multicanale.
- [Risolvere eventuali problemi correlati a SMB multicanale](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Per ulteriori informazioni sui miglioramenti, vedere [prestazioni SMB multicanale](storage-files-smb-multichannel-performance.md)
 - Per ulteriori informazioni sulla funzionalità multicanale SMB di Windows, vedere [Manage SMB multicanale](/azure-stack/hci/manage/manage-smb-multichannel).
