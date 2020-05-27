---
title: Abilitare e gestire il ripristino temporizzato per i BLOB in blocchi (anteprima)
titleSuffix: Azure Storage
description: Informazioni su come usare il ripristino temporizzato (anteprima) per ripristinare uno stato precedente dei BLOB in blocchi.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118211"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Abilitare e gestire il ripristino temporizzato per i BLOB in blocchi (anteprima)

È possibile usare il ripristino temporizzato (anteprima) per ripristinare uno stato precedente dei BLOB in blocchi. Questo articolo descrive come abilitare il ripristino temporizzato per un account di archiviazione con PowerShell. Illustra anche come eseguire un'operazione di ripristino con PowerShell.

Per altre informazioni, anche su come registrarsi per l'anteprima, vedere [Ripristino temporizzato per i BLOB in blocchi (anteprima)](point-in-time-restore-overview.md).

> [!CAUTION]
> Il ripristino temporizzato supporta solo il ripristino di operazioni su BLOB in blocchi. Non è possibile ripristinare le operazioni su contenitori. Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione [Elimina contenitore](/rest/api/storageservices/delete-container) durante l'anteprima del ripristino temporizzato, il contenitore non può essere ripristinato con un'operazione di ripristino. Durante l'anteprima, invece di eliminare un contenitore, eliminare i singoli BLOB se si prevede di volerli ripristinarli.

> [!IMPORTANT]
> L'anteprima del ripristino temporizzato è destinata all'uso solo in ambienti non di produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.

## <a name="install-the-preview-module"></a>Installare il modulo di anteprima

Per configurare il ripristino temporizzato di Azure con PowerShell, installare prima di tutto la versione [1.14.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) del modulo Az.Storage di PowerShell. Per installare il modulo di anteprima, seguire questa procedura:

1. Disinstallare eventuali installazioni precedenti di Azure PowerShell da Windows usando l'opzione **App e funzionalità** in **Impostazioni**.

1. Assicurarsi di avere la versione più recente di PowerShellGet installata. Aprire una finestra di Windows PowerShell ed eseguire i seguenti comandi per installare la versione più recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Chiudere e riaprire la finestra di PowerShell dopo l'installazione di PowerShellGet.

1. Installare la versione più recente di Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installare il modulo di anteprima Az.Storage:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Per altre informazioni sull'installazione di Azure PowerShell, vedere [Installare Azure PowerShell con PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Abilitare e configurare il ripristino temporizzato

Prima di abilitare e configurare il ripristino temporizzato, abilitare i relativi prerequisiti: eliminazione temporanea, feed di modifiche e controllo delle versioni dei BLOB. Per altre informazioni su come abilitare queste funzionalità, vedere gli articoli seguenti:

- [Abilitare l'eliminazione temporanea per i BLOB](soft-delete-enable.md)
- [Abilitare e disabilitare il feed di modifiche](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)

Per configurare il ripristino temporizzato di Azure con PowerShell, chiamare il comando Enable-AzStorageBlobRestorePolicy. L'esempio seguente abilita l'eliminazione temporanea e imposta il relativo periodo di conservazione, abilita il feed di modifiche e quindi abilita il ripristino temporizzato. Prima di eseguire l'esempio, usare il portale di Azure o un modello di Azure Resource Manager per abilitare anche il controllo delle versioni dei BLOB.

Quando si esegue l'esempio, assicurarsi di sostituire i valori tra parentesi uncinate con i propri valori:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Eseguire un'operazione di ripristino

Per avviare un'operazione di ripristino, chiamare il comando Restore-AzStorageBlobRange, specificando il punto di ripristino come valore **DateTime** UTC. È possibile specificare uno o più intervalli lessicografici dei BLOB da ripristinare oppure omettere un intervallo per ripristinare tutti i BLOB in tutti i contenitori dell'account di archiviazione. L'operazione di ripristino può richiedere diversi minuti.

Quando si specifica un intervallo di BLOB da ripristinare, tenere presenti le regole seguenti:

- Il modello di contenitore specificato per l'intervallo iniziale e l'intervallo finale deve includere un minimo di tre caratteri. La barra (/) usata per separare il nome di un contenitore dal nome di un BLOB non viene conteggiata ai fini di questo valore minimo.
- È possibile specificare un solo intervallo per ogni operazione di ripristino.
- I caratteri jolly non sono supportati. Vengono trattati come caratteri standard.
- È possibile ripristinare i BLOB nei contenitori `$root` e `$web` specificandoli esplicitamente in un intervallo passato a un'operazione di ripristino. I contenitori `$root` e `$web` vengono ripristinati solo se vengono specificati esplicitamente. Gli altri contenitori di sistema non possono essere ripristinati.

### <a name="restore-all-containers-in-the-account"></a>Ripristinare tutti i contenitori nell'account

Per ripristinare tutti i contenitori e i BLOB nell'account di archiviazione, chiamare il comando Restore-AzStorageBlobRange, omettendo il parametro `-BlobRestoreRange`. L'esempio seguente ripristina i contenitori allo stato di 12 ore precedenti al momento attuale nell'account di archiviazione:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Ripristinare un singolo intervallo di BLOB in blocchi

Per ripristinare un intervallo di BLOB, chiamare il comando Restore-AzStorageBlobRange e specificare un intervallo lessicografico di nomi di contenitori e di BLOB per il parametro `-BlobRestoreRange`. L'inizio dell'intervallo è incluso, mentre la fine è esclusa.

Ad esempio, per ripristinare i BLOB in un singolo contenitore denominato *sample-container*, è possibile specificare un intervallo che inizia con *sample-container* e termina con *sample-container1*. Non è necessario che i contenitori denominati nell'intervallo iniziale e finale esistano. Poiché la fine dell'intervallo è esclusa, anche se l'account di archiviazione include un contenitore denominato *sample-container1*, verrà ripristinato solo il contenitore denominato *sample-container*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Per specificare un sottoinsieme di BLOB in un contenitore da ripristinare, usare una barra (/) per separare il nome del contenitore dal modello di BLOB. L'intervallo seguente, ad esempio, seleziona i BLOB in un singolo contenitore i cui nomi iniziano con le lettere da *d* fino a *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Specificare quindi l'intervallo nel comando Restore-AzStorageBlobRange. Specificare il punto di ripristino fornendo un valore **DateTime** UTC per il parametro `-TimeToRestore`. L'esempio seguente ripristina i BLOB inclusi nell'intervallo specificato allo stato di 3 giorni precedenti al momento attuale:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Ripristinare più intervalli di BLOB in blocchi

Per ripristinare più intervalli di BLOB in blocchi, specificare una matrice di intervalli per il parametro `-BlobRestoreRange`. L'esempio seguente ripristina il contenuto completo di *container1* e *container4*:

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Passaggi successivi

- [Ripristino temporizzato per i BLOB in blocchi (anteprima)](point-in-time-restore-overview.md)
- [Eliminazione temporanea](soft-delete-overview.md)
- [Feed di modifiche (anteprima)](storage-blob-change-feed.md)
- [Controllo delle versioni dei BLOB (anteprima)](versioning-overview.md)
