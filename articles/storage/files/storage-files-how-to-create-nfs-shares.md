---
title: Creare una condivisione NFS-File di Azure
description: Informazioni su come creare una condivisione file di Azure che può essere montata usando il protocollo Network File System.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 7680e251d8411ce154e1f7dfb8af1d66514dd579
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629462"
---
# <a name="how-to-create-an-nfs-share"></a>Come creare una condivisione NFS

Le condivisioni file di Azure sono condivisioni file completamente gestite che si trovano nel cloud. È possibile accedervi usando il protocollo Server Message Block o il protocollo NFS (Network File System). Questo articolo illustra la creazione di una condivisione file che usa il protocollo NFS. Per altre informazioni su entrambi i protocolli, vedere [protocolli di condivisione file di Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitazioni

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Prerequisiti

- Creare un [account filestorage](storage-how-to-create-premium-fileshare.md).

    > [!IMPORTANT]
    > È possibile accedere alle condivisioni NFS solo da reti attendibili. Le connessioni alla condivisione NFS devono avere origine da una delle origini seguenti:

    - È possibile [creare un endpoint privato](storage-files-networking-endpoints.md#create-a-private-endpoint) (scelta consigliata) o [limitare l'accesso all'endpoint pubblico](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configurare una VPN da punto a sito (P2S) in Linux per l'uso con file di Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configurare una VPN da sito a sito per l'uso con file di Azure](storage-files-configure-s2s-vpn.md).
    - Configurare [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Se si intende usare l'interfaccia della riga di comando di Azure, [installare l'ultima versione](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Registrare il protocollo NFS 4,1

Se si usa il modulo Azure PowerShell o l'interfaccia della riga di comando di Azure, registrare la funzionalità usando i comandi seguenti:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-that-the-feature-is-registered"></a>Verificare che la funzionalità sia registrata

L'approvazione della registrazione può richiedere fino a un'ora. Per verificare che la registrazione sia stata completata, usare i comandi seguenti:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="create-an-nfs-share"></a>Creare una condivisione NFS

# <a name="portal"></a>[Portale](#tab/azure-portal)

Ora che è stato creato un account filestorage e configurato la rete, è possibile creare una condivisione file NFS. Il processo è simile alla creazione di una condivisione SMB. quando si crea la condivisione, è necessario selezionare **NFS** anziché **SMB** .

1. Passare all'account di archiviazione e selezionare **Condivisioni file**.
1. Selezionare **+ condivisione file** per creare una nuova condivisione file.
1. Assegnare un nome alla condivisione file e selezionare una capacità con provisioning.
1. Per **protocollo** selezionare **NFS (anteprima)**.
1. Per lo **squash radice** effettuare una selezione.

    - Squash radice (impostazione predefinita)-l'accesso per l'utente remoto (radice) viene mappato a UID (65534) e GID (65534).
    - Nessuno squash radice-utente remoto (radice) riceve l'accesso come radice.
    - Tutti gli accessi squash-all utente sono mappati a UID (65534) e GID (65534).
    
1. Selezionare **Crea**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Screenshot del pannello di creazione della condivisione file":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verificare che sia installato .NET Framework. Vedere [scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Verificare che la versione di PowerShell installata sia `5.1` o superiore usando il comando seguente.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Per aggiornare la versione di PowerShell, vedere [aggiornamento di Windows PowerShell esistente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. Installare la versione più recente del modulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Chiudere e riaprire la console di PowerShell.

1. Installare il modulo **AZ. storage** Preview versione **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Per altre informazioni su come installare i moduli di PowerShell, vedere [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. Per creare una condivisione file Premium con il modulo Azure PowerShell, usare il cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

> [!NOTE]
> Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni con provisioning Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/).

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare una condivisione file Premium con l'interfaccia della riga di comando di Azure, usare il comando [AZ storage Share create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Le dimensioni della condivisione di cui è stato effettuato il provisioning sono specificate dalla quota di condivisione, le condivisioni file vengono fatturate in base alle dimensioni con provisioning Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata creata una condivisione NFS, per usarla è necessario montarla nel client Linux. Per informazioni dettagliate, vedere [come montare una condivisione NFS](storage-files-how-to-mount-nfs-shares.md).

Se si verificano problemi, vedere [risolvere i problemi relativi alle condivisioni file NFS di Azure](storage-troubleshooting-files-nfs.md).