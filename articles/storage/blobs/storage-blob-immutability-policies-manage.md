---
title: Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB-archiviazione di Azure
description: Informazioni su come usare il supporto WORM (Write Once, Read Many) per l'archiviazione BLOB (oggetto) per archiviare i dati in uno stato non cancellabile e non modificabile per un intervallo specificato.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 79d7454722900eb1d9d6280e35313ef2f4a5cd54
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555678"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB

L'archiviazione non modificabile per l'archiviazione BLOB di Azure consente agli utenti di archiviare oggetti dati cruciali per l'azienda in un WORM (scrivere una sola volta, leggere molti) stato. Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente. Per la durata dell'intervallo di conservazione, è possibile creare e leggere i BLOB, ma non modificarli o eliminarli. L'archiviazione non modificabile è disponibile per gli account di archiviazione BLOB e V2 per utilizzo generico in tutte le aree di Azure.

Questo articolo illustra come impostare e gestire i criteri di immutabilità e le concessioni legali per i dati nell'archivio BLOB usando l'interfaccia della riga di comando di portale di Azure, PowerShell o Azure. Per altre informazioni sull'archiviazione non modificabile, vedere [archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Impostare i criteri di conservazione e le esenzioni legali

### <a name="portaltabazure-portal"></a>[di Microsoft Azure](#tab/azure-portal)

1. Creare un nuovo contenitore o selezionare un contenitore esistente per archiviare i BLOB che devono essere mantenuti nello stato non modificabile. Il contenitore deve trovarsi in un account di archiviazione BLOB o V2 per utilizzo generico.

2. Selezionare **Criteri di accesso** nelle impostazioni del contenitore. Selezionare quindi **Aggiungi criterio** nell' **Archivio BLOB non modificabile**.

    ![Impostazioni del contenitore nel portale](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Per abilitare la conservazione basata sul tempo, scegliere **Conservazione basata su tempo** dal menu a discesa.

    ![Opzione "Conservazione basata su tempo" selezionata in "Tipo di criteri"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Immettere l'intervallo di conservazione in giorni (i valori accettabili sono compresi tra 1 e 146000 giorni).

    ![Casella "Aggiorna periodo di conservazione a"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Lo stato iniziale del criterio è sbloccato, che consente di testare la funzionalità e apportare modifiche ai criteri prima di bloccarlo. Il blocco del criterio è essenziale per la conformità a normative come SEC 17a-4.

5. Bloccare i criteri. Fare clic con il pulsante destro del mouse sui puntini di sospensione ( **...** ) e il menu seguente viene visualizzato con azioni aggiuntive:

    ![Comando per il blocco dei criteri nel menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selezionare **blocca criterio** e confermare il blocco. Il criterio è ora bloccato e non può essere eliminato. saranno consentite solo le estensioni dell'intervallo di conservazione. Le eliminazioni BLOB e le sostituzioni non sono consentite. 

    ![Confermare "blocca criterio" nel menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Per abilitare le esenzioni legali, selezionare **Aggiungi criterio**. Scegliere **Blocco a fini giudiziari** dal menu a discesa.

    !["Blocco a fini giudiziari" nel menu sotto "Tipo di criteri"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Creare un blocco a fini giudiziari con uno o più tag.

    ![Casella "Nome tag" sotto il tipo di criteri](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Per cancellare una tenuta legale, è sufficiente rimuovere il tag dell'identificatore di esenzione legale applicato.

### <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

La funzionalità è inclusa nei gruppi di comandi seguenti: `az storage container immutability-policy` e `az storage container legal-hold`. Eseguire `-h` per visualizzare i comandi.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Il modulo AZ. Storage supporta l'archiviazione non modificabile.  Per abilitare la funzionalità, seguire questa procedura:

1. Assicurarsi di avere la versione più recente di PowerShellGet installata: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Rimuovere eventuali installazioni precedenti di Azure PowerShell.
3. Installare Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

Lo script di PowerShell di esempio seguente viene fornito a scopo di riferimento. Questo script crea un nuovo account di archiviazione e un nuovo contenitore. Viene quindi illustrato come impostare e rimuovere i blocchi a fini giudiziari, creare e bloccare i criteri di conservazione basati sul tempo (noti anche come criteri di immutabilità) ed estendere l'intervallo di conservazione.

Prima di tutto, creare un account di archiviazione di Azure:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Impostare e cancellare i blocchi a fini giudiziari:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Creare o aggiornare i criteri di immutabilità:

```powershell
# Create an immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Recuperare i criteri di immutabilità:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Blocca i criteri di immutabilità (aggiungere `-Force` per chiudere la richiesta):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Estendere i criteri di immutabilità:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Rimuovere i criteri di immutabilità sbloccati (aggiungere `-Force` per chiudere la richiesta):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="next-steps"></a>Passaggi successivi

[Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](storage-blob-immutable-storage.md)