---
title: Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB - Archiviazione di Azure
description: Come archiviare i dati in uno stato non cancellabile e non modificabile per un intervallo specificato con WORM (Write Once, Read Many) per l'archiviazione di oggetti BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a09dbd7d778a4f7ea2a9aac9ca07b9e6d06bc1ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463621"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB

L'archiviazione non modificabile per BLOB di Azure consente agli utenti di archiviare oggetti dati critici per l'azienda nello stato WORM (Write Once, Read Many). Questo stato rende i dati non cancellabili e non modificabili per un intervallo di tempo specificato dall'utente. Per tutta la durata dell'intervallo di conservazione, i BLOB possono essere creati e letti, ma non modificati o eliminati. L'archiviazione non modificabile è disponibile negli account di archiviazione BLOB in tutte le aree di Azure e per utilizzo generico v2.

Questo articolo illustra come impostare e gestire i criteri di immutabilità e i blocchi a fini giudiziari per i dati nell'archivio BLOB usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni sull'archiviazione non modificabile, vedere [Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Impostare i criteri di conservazione e i blocchi a fini giudiziari

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Creare un nuovo contenitore o selezionare un contenitore esistente per archiviare i BLOB che devono essere mantenuti nello stato non modificabile. Il contenitore deve essere in un account di archiviazione per utilizzo generico v2 o BLOB.

2. Selezionare **Criteri di accesso** nelle impostazioni del contenitore. Fare quindi clic su **Aggiungi criteri** in **Archivio BLOB non modificabile**.

    ![Impostazioni del contenitore nel portale](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Per abilitare la conservazione basata sul tempo, scegliere **Conservazione basata su tempo** dal menu a discesa.

    ![Opzione "Conservazione basata su tempo" selezionata in "Tipo di criteri"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Immettere l'intervallo di conservazione in giorni (i valori accettabili sono compresi tra 1 e 146000).

    ![Casella "Aggiorna periodo di conservazione a"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Lo stato iniziale dei criteri è sbloccato e consente di testare la funzionalità e apportare modifiche ai criteri prima di bloccarlo. Il blocco dei criteri è essenziale per la conformità a normative come SEC 17a-4.

5. Bloccare i criteri. Fare clic con il pulsante destro del mouse sui puntini di sospensione ( **...** ). Verrà visualizzato il menu seguente con azioni aggiuntive:

    ![Comando per il blocco dei criteri nel menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selezionare **Bloccare i criteri** e confermare il blocco. I criteri bloccati non possono essere eliminati e sono consentite solo estensioni dell'intervallo di conservazione. Le eliminazioni di BLOB e le sostituzioni non sono consentite. 

    ![Confermare "Bloccare i criteri" nel menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Per abilitare i blocchi a fini giudiziari, selezionare **Aggiungi criteri**. Scegliere **Blocco a fini giudiziari** dal menu a discesa.

    !["Blocco a fini giudiziari" nel menu sotto "Tipo di criteri"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Creare un blocco a fini giudiziari con uno o più tag.

    ![Casella "Nome tag" sotto il tipo di criteri](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Per cancellare un blocco a fini giudiziari rimuovere il tag identificatore del blocco a fini giudiziari applicato.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

La funzionalità è inclusa nei gruppi di comandi seguenti: `az storage container immutability-policy` e `az storage container legal-hold`. Eseguire `-h` per visualizzare i comandi.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Il modulo Az.Storage supporta l'archiviazione non modificabile.  Per abilitare la funzionalità, seguire questa procedura:

1. Assicurarsi di avere la versione più recente di PowerShellGet installata: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Rimuovere eventuali installazioni precedenti di Azure PowerShell.
3. Installare Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

Lo script di PowerShell di esempio seguente viene fornito a scopo di riferimento. Questo script crea un nuovo account di archiviazione e un nuovo contenitore. Viene quindi illustrato come impostare e rimuovere i blocchi a fini giudiziari, creare e bloccare i criteri di conservazione basati sul tempo (noti anche come criteri di immutabilità) ed estendere l'intervallo di conservazione.

Prima di tutto, creare un account di Archiviazione di Azure:

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
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
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

Creare o aggiornare i criteri di immutabilità pianificati:

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Recuperare i criteri di immutabilità:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Bloccare i criteri di immutabilità (aggiungere `-Force` per ignorare il prompt):

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

Rimuovere un criterio di immutabilità sbloccato (aggiungere `-Force` per ignorare il prompt):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Abilitazione di scritture di BLOB Accodamento protette

### <a name="portal"></a>[Portale](#tab/azure-portal)

![Consentire scritture di Accodamento aggiuntive](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

La funzionalità è inclusa nei gruppi di comandi seguenti: `az storage container immutability-policy` e `az storage container legal-hold`. Eseguire `-h` per visualizzare i comandi.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Passaggi successivi

[Archiviare dati BLOB critici per l'azienda con archiviazione non modificabile](storage-blob-immutable-storage.md)
