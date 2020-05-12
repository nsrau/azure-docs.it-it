---
title: Abilitare e gestire il ripristino temporizzato per i BLOB in blocchi (anteprima)
titleSuffix: Azure Storage
description: Informazioni su come usare il ripristino temporizzato (anteprima) per ripristinare i BLOB in blocchi in uno stato in un momento precedente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118211"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Abilitare e gestire il ripristino temporizzato per i BLOB in blocchi (anteprima)

È possibile usare il ripristino temporizzato (anteprima) per ripristinare i BLOB in blocchi al proprio stato in un momento precedente. Questo articolo descrive come abilitare il ripristino temporizzato per un account di archiviazione con PowerShell. Viene inoltre illustrato come eseguire un'operazione di ripristino con PowerShell.

Per altre informazioni e per informazioni su come eseguire la registrazione per l'anteprima, vedere [ripristino temporizzato per BLOB in blocchi (anteprima)](point-in-time-restore-overview.md).

> [!CAUTION]
> Il ripristino temporizzato supporta il ripristino di operazioni solo su BLOB in blocchi. Non è possibile ripristinare le operazioni sui contenitori. Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione [Delete Container](/rest/api/storageservices/delete-container) durante l'anteprima del ripristino temporizzato, il contenitore non può essere ripristinato con un'operazione di ripristino. Durante l'anteprima, anziché eliminare un contenitore, eliminare i singoli BLOB se si desidera ripristinarli.

> [!IMPORTANT]
> L'anteprima del ripristino temporizzato è destinata solo all'uso non in produzione. I contratti di servizio (SLA) di produzione non sono al momento disponibili.

## <a name="install-the-preview-module"></a>Installare il modulo di anteprima

Per configurare il ripristino temporizzato di Azure con PowerShell, installare prima di tutto la versione [1.14.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) del modulo AZ. storage PowerShell. Per installare il modulo di anteprima, seguire questa procedura:

1. Disinstallare le installazioni precedenti di Azure PowerShell da Windows usando l'impostazione **app & features** in **Settings**.

1. Assicurarsi di avere la versione più recente di PowerShellGet installata. Aprire una finestra di Windows PowerShell ed eseguire i seguenti comandi per installare la versione più recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Chiudere e riaprire la finestra di PowerShell dopo l'installazione di PowerShellGet.

1. Installare la versione più recente di Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installare il modulo AZ. storage Preview:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Per ulteriori informazioni sull'installazione di Azure PowerShell, vedere [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Abilitare e configurare il ripristino temporizzato

Prima di abilitare e configurare il ripristino temporizzato, abilitare i relativi prerequisiti: eliminazione temporanea, feed delle modifiche e controllo delle versioni dei BLOB. Per ulteriori informazioni sull'abilitazione di ognuna di queste funzionalità, vedere i seguenti articoli:

- [Abilitare l'eliminazione temporanea per i BLOB](soft-delete-enable.md)
- [Abilitare e disabilitare il feed delle modifiche](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)

Per configurare il ripristino temporizzato di Azure con PowerShell, chiamare il comando Enable-AzStorageBlobRestorePolicy. L'esempio seguente abilita l'eliminazione temporanea e imposta il periodo di memorizzazione dell'eliminazione temporanea, Abilita il feed delle modifiche e quindi Abilita il ripristino temporizzato. Prima di eseguire l'esempio, usare il portale di Azure o un modello Azure Resource Manager per abilitare anche il controllo delle versioni dei BLOB.

Quando si esegue l'esempio, ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

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

Per avviare un'operazione di ripristino, chiamare il comando Restore-AzStorageBlobRange, specificando il punto di ripristino come valore **DateTime** UTC. È possibile specificare uno o più intervalli di lessicografico di BLOB da ripristinare oppure omettere un intervallo per ripristinare tutti i BLOB in tutti i contenitori nell'account di archiviazione. Il completamento dell'operazione di ripristino può richiedere alcuni minuti.

Quando si specifica un intervallo di BLOB da ripristinare, tenere presenti le regole seguenti:

- Il modello di contenitore specificato per l'intervallo iniziale e l'intervallo finale deve includere un minimo di tre caratteri. La barra (/) utilizzata per separare il nome di un contenitore da un nome di BLOB non viene conteggiata in base a questo valore minimo.
- È possibile specificare un solo intervallo per ogni operazione di ripristino.
- I caratteri jolly non sono supportati. Vengono trattati come caratteri standard.
- È possibile ripristinare i BLOB nei `$root` `$web` contenitori e specificandone in modo esplicito in un intervallo passato a un'operazione di ripristino. I `$root` `$web` contenitori e vengono ripristinati solo se sono specificati in modo esplicito. Non è possibile ripristinare altri contenitori di sistema.

### <a name="restore-all-containers-in-the-account"></a>Ripristinare tutti i contenitori nell'account

Per ripristinare tutti i contenitori e i BLOB nell'account di archiviazione, chiamare il comando Restore-AzStorageBlobRange, omettendo il `-BlobRestoreRange` parametro. Nell'esempio seguente vengono ripristinati i contenitori nell'account di archiviazione nello stato 12 ore prima del momento attuale:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Ripristinare un singolo intervallo di BLOB in blocchi

Per ripristinare un intervallo di BLOB, chiamare il comando Restore-AzStorageBlobRange e specificare un intervallo lessicografico di nomi di contenitori e BLOB per il `-BlobRestoreRange` parametro. L'inizio dell'intervallo è incluso e la fine dell'intervallo è esclusiva.

Per ripristinare i BLOB in un singolo contenitore denominato *Sample-Container*, ad esempio, è possibile specificare un intervallo che inizia con *Sample-Container* e termina con *Sample-container1*. Non è necessario che i contenitori denominati nell'intervallo iniziale e finale esistano. Poiché la fine dell'intervallo è esclusiva, anche se l'account di archiviazione include un contenitore denominato *Sample-container1*, verrà ripristinato solo il contenitore denominato *Sample-Container* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Per specificare un subset di BLOB in un contenitore da ripristinare, usare una barra (/) per separare il nome del contenitore dal modello BLOB. L'intervallo seguente, ad esempio, consente di selezionare i BLOB in un singolo contenitore i cui nomi iniziano con le lettere da *d* a *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Specificare quindi l'intervallo per il comando Restore-AzStorageBlobRange. Specificare il punto di ripristino fornendo un valore **DateTime** UTC per il `-TimeToRestore` parametro. Nell'esempio seguente vengono ripristinati i BLOB nell'intervallo specificato con lo stato 3 giorni precedenti al momento attuale:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Ripristinare più intervalli di BLOB in blocchi

Per ripristinare più intervalli di BLOB in blocchi, specificare una matrice di intervalli per il `-BlobRestoreRange` parametro. Nell'esempio seguente viene ripristinato il contenuto completo di *container1* e *container4*:

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Passaggi successivi

- [Ripristino temporizzato per BLOB in blocchi (anteprima)](point-in-time-restore-overview.md)
- [Eliminazione temporanea](soft-delete-overview.md)
- [Feed delle modifiche (anteprima)](storage-blob-change-feed.md)
- [Controllo delle versioni dei BLOB (anteprima)](versioning-overview.md)
