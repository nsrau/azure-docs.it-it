---
title: Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB - Archiviazione di AzureSet and manage immutability policies for Blob storage - Azure Storage
description: Informazioni su come usare il supporto WORM (Write Once, Read Many) per l'archiviazione BLOB (oggetto) per archiviare i dati in uno stato non cancellabile e non modificabile per un intervallo specificato.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970107"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Impostare e gestire i criteri di immutabilità per l'archiviazione BLOBSet and manage immutability policies for Blob storage

L'archiviazione non modificabile per l'archiviazione BLOB di Azure consente agli utenti di archiviare oggetti dati critici per l'azienda in uno stato WORM (Write Once, Read Many). Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente. Per la durata dell'intervallo di conservazione, i BLOB possono essere creati e letti, ma non possono essere modificati o eliminati. L'archiviazione non modificabile è disponibile per gli account di archiviazione BLOB e v2 generici in tutte le aree di Azure.Immutable storage is available for general-purpose v2 and Blob storage accounts in all Azure regions.

Questo articolo illustra come impostare e gestire i criteri di immutabilità e le conservazioni legali per i dati nell'archiviazione BLOB usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.This article shows how to set and manage immutability policies and legal holds for data in Blob storage using the Azure portal, PowerShell or Azure CLI. Per altre informazioni sull'archiviazione non modificabile, vedere Archiviare dati BLOB critici per il [business con archiviazione non modificabile](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Impostare criteri di conservazione e conservazioni legali

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Creare un nuovo contenitore o selezionare un contenitore esistente per archiviare i BLOB che devono essere mantenuti nello stato non modificabile. Il contenitore deve trovarsi in un account di archiviazione generico v2 o BLOB.

2. Selezionare **Criteri di accesso** nelle impostazioni del contenitore. Selezionare **quindi Aggiungi criterio** in Archiviazione BLOB non **modificabile**.

    ![Impostazioni del contenitore nel portale](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Per abilitare la conservazione basata sul tempo, scegliere **Conservazione basata su tempo** dal menu a discesa.

    ![Opzione "Conservazione basata su tempo" selezionata in "Tipo di criteri"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Immettere l'intervallo di conservazione in giorni (i valori accettabili sono da 1 a 146000 giorni).

    ![Casella "Aggiorna periodo di conservazione a"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Lo stato iniziale del criterio viene sbloccato consentendo di testare la funzionalità e apportare modifiche al criterio prima di bloccarla. Bloccare la politica è essenziale per la conformità a normative come SEC 17a-4.

5. Bloccare i criteri. Fare clic con il pulsante destro del mouse sui puntilips (**...**) e viene visualizzato il menu seguente con azioni aggiuntive:

    ![Comando per il blocco dei criteri nel menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selezionare **Blocca criterio** e confermare il blocco. Il criterio è ora bloccato e non può essere eliminato, saranno consentite solo le estensioni dell'intervallo di conservazione. Le eliminazioni e le sostituzioni BLOB non sono consentite. 

    ![Confermare "Blocca criterio" nel menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Per abilitare le conservazioni legali, selezionare **Aggiungi criterio**. Scegliere **Blocco a fini giudiziari** dal menu a discesa.

    !["Blocco a fini giudiziari" nel menu sotto "Tipo di criteri"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Creare un blocco a fini giudiziari con uno o più tag.

    ![Casella "Nome tag" sotto il tipo di criteri](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Per cancellare una conservazione a fini giudiziari, rimuovere il tag dell'identificatore di sospensione legale applicato.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

La funzionalità è inclusa nei gruppi di comandi seguenti: `az storage container immutability-policy` e `az storage container legal-hold`. Eseguire `-h` per visualizzare i comandi.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Il modulo Az.Storage supporta l'archiviazione non modificabile.  Per abilitare la funzionalità, seguire questa procedura:

1. Assicurarsi di avere la versione più recente di PowerShellGet installata: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Rimuovere eventuali installazioni precedenti di Azure PowerShell.
3. Installare Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

Lo script di PowerShell di esempio seguente viene fornito a scopo di riferimento. Questo script crea un nuovo account di archiviazione e un nuovo contenitore. Viene quindi illustrato come impostare e cancellare le conservazioni legali, creare e bloccare un criterio di conservazione basato sul tempo (noto anche come criterio di immutabilità) ed estendere l'intervallo di conservazione.

Creare innanzitutto un account di archiviazione di Azure:First, create an Azure Storage account:

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

Creare o aggiornare criteri di immutabilità basati sul tempo:Create or update time-based immutability policies:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Recuperare i criteri di immutabilità:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Bloccare i criteri di `-Force` immutabilità (aggiungere per ignorare il prompt):Lock immutability policies (add to dismiss the prompt):

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

Rimuovere un criterio di immutabilità sbloccato (aggiungere per ignorare il prompt):Remove an unlocked immutability policy (add `-Force` to dismiss the prompt):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Consentire lo scrittura di BLOB di accodamento protettoEnabling allow protected append blobs writes

### <a name="portal"></a>[Portale](#tab/azure-portal)

![Consenti scritture aggiuntive di accodamento](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

La funzionalità è inclusa nei gruppi di comandi seguenti: `az storage container immutability-policy` e `az storage container legal-hold`. Eseguire `-h` per visualizzare i comandi.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Passaggi successivi

[Archiviare dati BLOB critici per il business con archiviazione non modificabileStore business-critical blob data with immutable storage](storage-blob-immutable-storage.md)
