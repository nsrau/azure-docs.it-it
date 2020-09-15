---
title: Abilitare e gestire il ripristino temporizzato per i BLOB in blocchi (anteprima)
titleSuffix: Azure Storage
description: Informazioni su come usare il ripristino temporizzato (anteprima) per ripristinare un set di BLOB in blocchi a uno stato precedente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 140e1203a29dcebec9d6483e73e906591b2213fb
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068502"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Abilitare e gestire il ripristino temporizzato per i BLOB in blocchi (anteprima)

È possibile usare il ripristino temporizzato (anteprima) per ripristinare un set di BLOB in blocchi a uno stato precedente. Questo articolo descrive come abilitare il ripristino temporizzato per un account di archiviazione con PowerShell. Illustra anche come eseguire un'operazione di ripristino con PowerShell.

Per altre informazioni, anche su come registrarsi per l'anteprima, vedere [Ripristino temporizzato per i BLOB in blocchi (anteprima)](point-in-time-restore-overview.md).

> [!CAUTION]
> Il ripristino temporizzato supporta solo il ripristino di operazioni su BLOB in blocchi. Non è possibile ripristinare le operazioni su contenitori. Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione [Elimina contenitore](/rest/api/storageservices/delete-container) durante l'anteprima del ripristino temporizzato, il contenitore non può essere ripristinato con un'operazione di ripristino. Durante l'anteprima, invece di eliminare un contenitore, eliminare i singoli BLOB se si prevede di volerli ripristinarli.

> [!IMPORTANT]
> L'anteprima del ripristino temporizzato è destinata all'uso solo in ambienti non di produzione.

## <a name="enable-and-configure-point-in-time-restore"></a>Abilitare e configurare il ripristino temporizzato

Prima di abilitare e configurare il ripristino temporizzato, abilitare i relativi prerequisiti per l'account di archiviazione: eliminazione temporanea, feed delle modifiche e controllo delle versioni dei BLOB. Per altre informazioni su come abilitare queste funzionalità, vedere gli articoli seguenti:

- [Abilitare l'eliminazione temporanea per i BLOB](soft-delete-enable.md)
- [Abilitare e disabilitare il feed di modifiche](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per configurare il ripristino temporizzato con il portale di Azure, attenersi alla procedura seguente:

1. Passare all'account di archiviazione nel portale di Azure.
1. In **Impostazioni**scegliere **protezione dati**.
1. Selezionare **Attiva ripristino temporizzato** . Quando si seleziona questa opzione, vengono abilitati anche l'eliminazione temporanea per i BLOB, il controllo delle versioni e il feed delle modifiche.
1. Impostare il punto di ripristino massimo per il ripristino temporizzato, in giorni. Questo numero deve essere almeno un giorno inferiore rispetto al periodo di memorizzazione specificato per l'eliminazione temporanea del BLOB.
1. Salvare le modifiche.

L'immagine seguente illustra un account di archiviazione configurato per il ripristino temporizzato con un punto di ripristino di sette giorni fa e un periodo di conservazione per l'eliminazione temporanea del BLOB di 14 giorni.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Screenshot che illustra come configurare il ripristino temporizzato nel portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per configurare il ripristino temporizzato con PowerShell, installare prima di tutto il modulo AZ. storage Preview versione 1.14.1-Preview o una versione successiva del modulo Preview. Rimuovere tutte le altre versioni del modulo AZ. storage.

Verificare di aver installato la versione 2.2.4.1 o successiva di PowerShellGet. Per determinare la versione attualmente installata, eseguire il comando seguente:

```powershell
Get-InstalledModule PowerShellGet
```

Installare quindi il modulo AZ. storage Preview. Il comando seguente installa la versione [2.5.2-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) del modulo AZ. storage:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
```

Per ulteriori informazioni sull'installazione di Azure PowerShell, vedere [installazione di PowerShellGet](/powershell/scripting/gallery/installing-psget) e [installare Azure PowerShell con PowerShellGet](/powershell/azure/install-az-ps).

Per configurare il ripristino temporizzato di Azure con PowerShell, chiamare il comando Enable-AzStorageBlobRestorePolicy. L'esempio seguente abilita l'eliminazione temporanea e imposta il relativo periodo di conservazione, abilita il feed di modifiche e quindi abilita il ripristino temporizzato. Prima di eseguire l'esempio, usare il portale di Azure o un modello di Azure Resource Manager per abilitare anche il controllo delle versioni dei BLOB.

Quando si esegue l'esempio, assicurarsi di sostituire i valori tra parentesi uncinate con i propri valori:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

---

## <a name="perform-a-restore-operation"></a>Eseguire un'operazione di ripristino

Quando si esegue un'operazione di ripristino, è necessario specificare il punto di ripristino come valore **DateTime** UTC. I contenitori e i BLOB verranno ripristinati allo stato corrispondente in quel giorno e ora. L'operazione di ripristino può richiedere diversi minuti.

È possibile ripristinare tutti i contenitori nell'account di archiviazione oppure è possibile ripristinare un intervallo di BLOB in uno o più contenitori. Un intervallo di BLOB è definito lessicografico, vale a dire nell'ordine del dizionario. Per ogni operazione di ripristino sono supportati fino a dieci intervalli lessicografico. L'inizio dell'intervallo è inclusivo e la fine dell'intervallo è esclusiva.

Il modello di contenitore specificato per l'intervallo iniziale e l'intervallo finale deve includere un minimo di tre caratteri. La barra (/) usata per separare il nome di un contenitore dal nome di un BLOB non viene conteggiata ai fini di questo valore minimo.

I caratteri jolly non sono supportati in un intervallo di lessicografico. Tutti i caratteri jolly vengono considerati caratteri standard.

È possibile ripristinare i BLOB nei contenitori `$root` e `$web` specificandoli esplicitamente in un intervallo passato a un'operazione di ripristino. I contenitori `$root` e `$web` vengono ripristinati solo se vengono specificati esplicitamente. Gli altri contenitori di sistema non possono essere ripristinati.

Vengono ripristinati solo i BLOB in blocchi. I BLOB di pagine e i BLOB di Accodamento non sono inclusi in un'operazione di ripristino. Per ulteriori informazioni sulle limitazioni correlate ai BLOB di Accodamento, vedere [problemi noti](#known-issues).

> [!IMPORTANT]
> Quando si esegue un'operazione di ripristino, archiviazione di Azure blocca le operazioni sui dati nei BLOB negli intervalli da ripristinare per la durata dell'operazione. Le operazioni di lettura, scrittura ed eliminazione sono bloccate nella posizione primaria. Per questo motivo, è possibile che le operazioni come l'elenco dei contenitori nell'portale di Azure non vengano eseguite come previsto mentre è in corso l'operazione di ripristino.
>
> Quando l'account di archiviazione viene replicato geograficamente, è possibile che le operazioni di lettura dalla posizione secondaria continuino durante l'operazione di ripristino.

### <a name="restore-all-containers-in-the-account"></a>Ripristinare tutti i contenitori nell'account

È possibile ripristinare tutti i contenitori nell'account di archiviazione per ripristinarne lo stato precedente in un determinato momento.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per ripristinare tutti i contenitori e i BLOB nell'account di archiviazione con la portale di Azure, attenersi alla procedura seguente:

1. Passare all'elenco di contenitori per l'account di archiviazione.
1. Sulla barra degli strumenti scegliere **Ripristina contenitori**, quindi **Ripristina tutto**.
1. Nel riquadro **Ripristina tutti i contenitori** specificare il punto di ripristino fornendo una data e un'ora.
1. Confermare che si desidera procedere selezionando la casella.
1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Screenshot che illustra come ripristinare tutti i contenitori in un punto di ripristino specificato":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per ripristinare tutti i contenitori e i BLOB nell'account di archiviazione con PowerShell, chiamare il comando **Restore-AzStorageBlobRange** , omettendo il `-BlobRestoreRange` parametro. L'esempio seguente ripristina i contenitori allo stato di 12 ore precedenti al momento attuale nell'account di archiviazione:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

---

### <a name="restore-ranges-of-block-blobs"></a>Ripristinare intervalli di BLOB in blocchi

È possibile ripristinare uno o più intervalli di lessicografico di BLOB all'interno di un singolo contenitore o tra più contenitori per restituire tali BLOB allo stato precedente in un determinato momento.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per ripristinare un intervallo di BLOB in uno o più contenitori con la portale di Azure, attenersi alla seguente procedura:

1. Passare all'elenco di contenitori per l'account di archiviazione.
1. Selezionare il contenitore o i contenitori da ripristinare.
1. Sulla barra degli strumenti scegliere **Ripristina contenitori**, quindi **Ripristina selezionato**.
1. Nel riquadro **Ripristina contenitori selezionati** specificare il punto di ripristino fornendo una data e un'ora.
1. Specificare gli intervalli da ripristinare. Usare una barra (/) per delineare il nome del contenitore dal prefisso del BLOB.
1. Per impostazione predefinita, il riquadro **Ripristina contenitori selezionati** specifica un intervallo che include tutti i BLOB nel contenitore. Eliminare questo intervallo se non si desidera ripristinare l'intero contenitore. L'intervallo predefinito è illustrato nell'immagine seguente.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Screenshot che mostra l'intervallo di BLOB predefinito da eliminare prima di specificare un intervallo personalizzato":::

1. Confermare che si desidera procedere selezionando la casella.
1. Selezionare **Ripristina** per avviare l'operazione di ripristino.

Nell'immagine seguente viene illustrata un'operazione di ripristino in un set di intervalli.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Screenshot che illustra come ripristinare intervalli di BLOB in uno o più contenitori":::

L'operazione di ripristino mostrata nell'immagine esegue le azioni seguenti:

- Ripristina il contenuto completo di *container1*.
- Ripristina i BLOB nell'intervallo lessicografico *blob1* tramite *blob5* in *container2*. Questo intervallo consente di ripristinare i BLOB con nomi come *blob1*, *blob11*, *blob100*, *blob2*e così via. Poiché la fine dell'intervallo è esclusiva, ripristina i BLOB i cui nomi iniziano con *blob4*, ma non ripristina i BLOB i cui nomi iniziano con *blob5*.
- Ripristina tutti i BLOB in *container3* e *container4*. Poiché la fine dell'intervallo è esclusiva, questo intervallo non consente di ripristinare *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per ripristinare un singolo intervallo di BLOB, chiamare il comando **Restore-AzStorageBlobRange** e specificare un intervallo lessicografico di nomi di contenitori e BLOB per il `-BlobRestoreRange` parametro. Ad esempio, per ripristinare i BLOB in un singolo contenitore denominato *sample-container*, è possibile specificare un intervallo che inizia con *sample-container* e termina con *sample-container1*. Non è necessario che i contenitori denominati nell'intervallo iniziale e finale esistano. Poiché la fine dell'intervallo è esclusa, anche se l'account di archiviazione include un contenitore denominato *sample-container1*, verrà ripristinato solo il contenitore denominato *sample-container*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container `
    -EndRange sample-container1
```

Per specificare un subset di BLOB in un contenitore da ripristinare, usare una barra (/) per separare il nome del contenitore dal modello di prefisso del BLOB. L'intervallo seguente, ad esempio, seleziona i BLOB in un singolo contenitore i cui nomi iniziano con le lettere da *d* fino a *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d `
    -EndRange sample-container/g
```

Specificare quindi l'intervallo per il comando **Restore-AzStorageBlobRange** . Specificare il punto di ripristino fornendo un valore **DateTime** UTC per il parametro `-TimeToRestore`. L'esempio seguente ripristina i BLOB inclusi nell'intervallo specificato allo stato di 3 giorni precedenti al momento attuale:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Per ripristinare più intervalli di BLOB in blocchi, specificare una matrice di intervalli per il parametro `-BlobRestoreRange`. Nell'esempio seguente vengono specificati due intervalli per ripristinare il contenuto completo di *container1* e *container4*:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

---

### <a name="restore-block-blobs-asynchronously-with-powershell"></a>Ripristinare i BLOB in blocchi in modo asincrono con PowerShell

Per eseguire un'operazione di ripristino in modo asincrono, aggiungere il `-AsJob` parametro alla chiamata a **Restore-AzStorageBlobRange** e archiviare il risultato della chiamata in una variabile. Il comando **Restore-AzStorageBlobRange** restituisce un oggetto di tipo **AzureLongRunningJob**. È possibile controllare la proprietà **state** di questo oggetto per determinare se l'operazione di ripristino è stata completata. Il valore della proprietà **state** può essere **in esecuzione** o **completato**.

Nell'esempio seguente viene illustrato come chiamare un'operazione di ripristino in modo asincrono:

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

Per attendere il completamento dell'operazione di ripristino dopo l'esecuzione, chiamare il comando [Wait-Job](/powershell/module/microsoft.powershell.core/wait-job) , come illustrato nell'esempio seguente:

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>Problemi noti

Per un subset di operazioni di ripristino in cui sono presenti BLOB di Accodamento, l'operazione di ripristino avrà esito negativo. Microsoft consiglia di non eseguire un ripristino temporizzato durante l'anteprima se nell'account sono presenti BLOB di Accodamento.

## <a name="next-steps"></a>Passaggi successivi

- [Ripristino temporizzato per i BLOB in blocchi (anteprima)](point-in-time-restore-overview.md)
- [Eliminazione temporanea](soft-delete-overview.md)
- [Feed di modifiche (anteprima)](storage-blob-change-feed.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)
