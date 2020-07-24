---
title: Configurare la replica di oggetti (anteprima)
titleSuffix: Azure Storage
description: Informazioni su come configurare la replica di oggetti per copiare in modo asincrono BLOB in blocchi da un contenitore in un account di archiviazione a un contenitore in un altro account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: ddf9a1309cb4f9156cec3aeb5d2ddd9d22cde485
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011449"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Configurare la replica di oggetti per i BLOB in blocchi (anteprima)

La replica di oggetti (anteprima) copia in modo asincrono i BLOB in blocchi tra un account di archiviazione di origine e uno di destinazione. Per altre informazioni sulla replica di oggetti, vedere [Replica di oggetti (anteprima)](object-replication-overview.md).

Quando si configura la replica di oggetti, si crea un criterio di replica che specifica l'account di archiviazione di origine e l'account di destinazione. Un criterio di replica include una o più regole che specificano un contenitore di origine e un contenitore di destinazione e indicano i BLOB in blocchi del contenitore di origine che verranno replicati.

Questo articolo descrive come configurare la replica di oggetti per l'account di archiviazione usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per configurare la replica di oggetti è anche possibile usare una delle librerie client del provider di risorse di Archiviazione di Azure.

## <a name="create-a-replication-policy-and-rules"></a>Creare criteri e regole di replica

Prima di configurare la replica di oggetti, creare gli account di archiviazione di origine e di destinazione, se non esistono già. Entrambi gli account devono essere account di archiviazione v2 per uso generico. Per altre informazioni, vedere [Creare un account di archiviazione di Azure](../common/storage-account-create.md).

Un account di archiviazione può fungere da account di origine per un massimo di due account di destinazione. E un account di destinazione non può avere più di due account di origine. Gli account di origine e di destinazione possono trovarsi in aree diverse. È possibile configurare criteri di replica distinti per replicare i dati in ogni account di destinazione.

Prima di iniziare, assicurarsi di aver effettuato la registrazione per le anteprime delle funzionalità seguenti:

- [Replica di oggetti (anteprima)](object-replication-overview.md)
- [Controllo delle versioni dei BLOB (anteprima)](versioning-overview.md)
- [Supporto del feed di modifiche in Archiviazione BLOB di Azure (anteprima)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Prima di configurare la replica di oggetti nel portale di Azure, creare i contenitori di origine e di destinazione nei rispettivi account di archiviazione, se non esistono già. Abilitare anche il controllo delle versioni dei BLOB e il feed di modifiche nell'account di origine e il controllo delle versioni dei BLOB nell'account di destinazione.

Per creare un criterio di replica nel portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione di origine nel portale di Azure.
1. In **servizio BLOB**selezionare **replica oggetti**.
1. Selezionare **Configurare la replica**.
1. Selezionare la sottoscrizione di destinazione e l'account di archiviazione.
1. Nella sezione **Container pairs** (Coppie di contenitori) selezionare un contenitore di origine nell'account di origine e un contenitore di destinazione nell'account di destinazione. È possibile creare fino a 10 coppie di contenitori per ogni criterio di replica.

    L'immagine seguente illustra un set di regole di replica.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Screenshot che illustra regole di replica nel portale di Azure":::

1. Se si vuole, specificare uno o più filtri per copiare solo i BLOB che corrispondono a un criterio di prefisso. Se ad esempio si specifica il prefisso `b`, verranno replicati solo i BLOB il cui nome inizia con tale lettera. È possibile specificare una directory virtuale come parte del prefisso. La stringa di prefisso non supporta i caratteri jolly.

    L'immagine seguente illustra i filtri che limitano quali BLOB vengono copiati come parte di una regola di replica.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Screenshot che illustra i filtri per una regola di replica":::

1. Per impostazione predefinita, l'ambito di copia è impostato in modo che vengano copiati solo i nuovi oggetti. Per copiare tutti gli oggetti nel contenitore o per copiare oggetti a partire da una data e un'ora personalizzate, selezionare il collegamento **cambia** e configurare l'ambito di copia per la coppia di contenitori.

    L'immagine seguente illustra un ambito di copia personalizzato.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Screenshot che illustra un ambito di copia personalizzato per la replica di oggetti":::

1. Selezionare **Salva e applica** per creare i criteri di replica e avviare la replica dei dati.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per creare un criterio di replica con PowerShell, installare prima la versione [2.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) o successiva del modulo AZ. storage PowerShell. Per installare il modulo di anteprima, seguire questa procedura:

1. Disinstallare eventuali installazioni precedenti di Azure PowerShell da Windows usando l'opzione **App e funzionalità** in **Impostazioni**.

1. Assicurarsi di avere la versione più recente di PowerShellGet installata. Aprire una finestra di Windows PowerShell ed eseguire i seguenti comandi per installare la versione più recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    Chiudere e riaprire la finestra di PowerShell dopo l'installazione di PowerShellGet.

1. Installare la versione più recente di Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installare il modulo di anteprima Az.Storage:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Per altre informazioni sull'installazione di Azure PowerShell, vedere [Installare Azure PowerShell con PowerShellGet](/powershell/azure/install-az-ps).

L'esempio seguente illustra come creare un criterio di replica per gli account di origine e di destinazione. Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per creare un criterio di replica con l'interfaccia della riga di comando di Azure, installare prima l'estensione di anteprima di Archiviazione di Azure:

```azurecli
az extension add -n storage-or-preview
```

Quindi accedere con le credenziali di Azure:

```azurecli
az login
```

Abilitare il controllo delle versioni dei BLOB negli account di archiviazione di origine e di destinazione e abilitare il feed delle modifiche nell'account di origine. Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```azurecli
az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Creare i contenitori di origine e di destinazione nei rispettivi account di archiviazione.

```azurecli
az storage container create --account-name <source-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <source-storage-account> --name source-container4 --auth-mode login

az storage container create --account-name <dest-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <dest-storage-account> --name source-container4 --auth-mode login
```

Creare un nuovo criterio di replica e le regole associate nell'account di destinazione.

```azurecli
az storage account or-policy create --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Creare il criterio nell'account di origine usando l'ID criterio.

```azurecli
az storage account or-policy show --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Rimuovere un criterio di replica

Per rimuovere un criterio di replica e le regole associate, usare il portale di Azure, PowerShell o l'interfaccia della riga di comando.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per rimuovere un criterio di replica nel portale di Azure, seguire questa procedura:

1. Passare all'account di archiviazione di origine nel portale di Azure.
1. In **Impostazioni** selezionare **Replica di oggetti**.
1. Fare clic sul pulsante **Altro** accanto al nome del criterio.
1. Selezionare **Elimina regole**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per rimuovere un criterio di replica, eliminarlo sia dall'account di origine che dall'account di destinazione. L'eliminazione del criterio comporta anche l'eliminazione delle eventuali regole associate.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per rimuovere un criterio di replica, eliminarlo sia dall'account di origine che dall'account di destinazione. L'eliminazione del criterio comporta anche l'eliminazione delle eventuali regole associate.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della replica di oggetti (anteprima)](object-replication-overview.md)
