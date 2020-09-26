---
title: Eseguire un ripristino temporizzato sui dati BLOB in blocchi
titleSuffix: Azure Storage
description: Informazioni su come usare il ripristino temporizzato per ripristinare un set di BLOB in blocchi nello stato precedente in un determinato momento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 828b5c34aaccf2a53aa197f921a8ef02d46821ae
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280471"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Eseguire un ripristino temporizzato sui dati BLOB in blocchi

È possibile usare il ripristino temporizzato per ripristinare uno stato precedente di uno o più set di BLOB in blocchi. Questo articolo descrive come abilitare il ripristino temporizzato per un account di archiviazione e come eseguire un'operazione di ripristino.

Per altre informazioni sul ripristino temporizzato, vedere [ripristino temporizzato per i BLOB in blocchi](point-in-time-restore-overview.md).

> [!CAUTION]
> Il ripristino temporizzato supporta solo il ripristino di operazioni su BLOB in blocchi. Non è possibile ripristinare le operazioni su contenitori. Se si elimina un contenitore dall'account di archiviazione chiamando l'operazione [Delete Container](/rest/api/storageservices/delete-container) , il contenitore non può essere ripristinato con un'operazione di ripristino. Anziché eliminare un contenitore, eliminare i singoli BLOB se si desidera ripristinarli.

## <a name="enable-and-configure-point-in-time-restore"></a>Abilitare e configurare il ripristino temporizzato

Prima di abilitare e configurare il ripristino temporizzato, abilitare i relativi prerequisiti per l'account di archiviazione: eliminazione temporanea, feed delle modifiche e controllo delle versioni dei BLOB. Per altre informazioni su come abilitare queste funzionalità, vedere gli articoli seguenti:

- [Abilitare l'eliminazione temporanea per i BLOB](soft-delete-enable.md)
- [Abilitare e disabilitare il feed di modifiche](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Abilitare e gestire il controllo delle versioni dei BLOB](versioning-enable.md)

> [!IMPORTANT]
> L'abilitazione dell'eliminazione temporanea, del feed delle modifiche e del controllo delle versioni BLOB può comportare addebiti aggiuntivi. Per altre informazioni, vedere [eliminazione temporanea per i BLOB](soft-delete-blob-overview.md), [supporto del feed delle modifiche nell'archivio BLOB di Azure](storage-blob-change-feed.md)e [controllo delle versioni dei BLOB](versioning-overview.md).

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

Per configurare il ripristino temporizzato con PowerShell, installare prima il modulo [AZ. storage](https://www.powershellgallery.com/packages/Az.Storage) versione 2.6.0 o successiva. Chiamare quindi il comando Enable-AzStorageBlobRestorePolicy per abilitare il ripristino temporizzato per l'account di archiviazione.

L'esempio seguente abilita l'eliminazione temporanea e imposta il periodo di memorizzazione dell'eliminazione temporanea, Abilita il feed delle modifiche e il controllo delle versioni, quindi Abilita il ripristino temporizzato.    Quando si esegue l'esempio, assicurarsi di sostituire i valori tra parentesi uncinate con i propri valori:

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

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

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

Vengono ripristinati solo i BLOB in blocchi. I BLOB di pagine e i BLOB di Accodamento non sono inclusi in un'operazione di ripristino. Per altre informazioni sulle limitazioni correlate ai BLOB di Accodamento, vedere [ripristino temporizzato per i BLOB in blocchi](point-in-time-restore-overview.md).

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

Per ripristinare tutti i contenitori e i BLOB nell'account di archiviazione con PowerShell, chiamare il comando **Restore-AzStorageBlobRange** . Per impostazione predefinita, il comando **Restore-AzStorageBlobRange** viene eseguito in modo asincrono e restituisce un oggetto di tipo **PSBlobRestoreStatus** che è possibile usare per controllare lo stato dell'operazione di ripristino.

Nell'esempio seguente vengono ripristinati in modo asincrono i contenitori nell'account di archiviazione nello stato 12 ore prima del momento corrente e vengono controllate alcune delle proprietà dell'operazione di ripristino:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Per eseguire l'operazione di ripristino in modo sincrono, includere il parametro **-WaitForComplete** nel comando. Quando il parametro **-WaitForComplete** è presente, in PowerShell viene visualizzato un messaggio che include l'ID ripristino per l'operazione e quindi si blocca in fase di esecuzione fino al completamento dell'operazione di ripristino. Tenere presente che il tempo necessario per un'operazione di ripristino dipende dalla quantità di dati da ripristinare e che un'operazione di ripristino di grandi dimensioni può richiedere fino a un'ora per il completamento.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
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

Per ripristinare un singolo intervallo di BLOB, chiamare il comando **Restore-AzStorageBlobRange** e specificare un intervallo lessicografico di nomi di contenitori e BLOB per il `-BlobRestoreRange` parametro. Ad esempio, per ripristinare i BLOB in un singolo contenitore denominato *container1*, è possibile specificare un intervallo che inizia con *container1* e termina con *container2*. Non è necessario che i contenitori denominati nell'intervallo iniziale e finale esistano. Poiché la fine dell'intervallo è esclusiva, anche se l'account di archiviazione include un contenitore denominato *container2*, verrà ripristinato solo il contenitore denominato *container1* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Per specificare un subset di BLOB in un contenitore da ripristinare, usare una barra (/) per separare il nome del contenitore dal modello di prefisso del BLOB. L'intervallo seguente, ad esempio, seleziona i BLOB in un singolo contenitore i cui nomi iniziano con le lettere da *d* fino a *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Specificare quindi l'intervallo per il comando **Restore-AzStorageBlobRange** . Specificare il punto di ripristino fornendo un valore **DateTime** UTC per il parametro `-TimeToRestore`. L'esempio seguente ripristina i BLOB inclusi nell'intervallo specificato allo stato di 3 giorni precedenti al momento attuale:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Per impostazione predefinita, il comando **Restore-AzStorageBlobRange** viene eseguito in modo asincrono. Quando si avvia un'operazione di ripristino in modo asincrono, in PowerShell viene immediatamente visualizzata una tabella di proprietà per l'operazione:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Per ripristinare più intervalli di BLOB in blocchi, specificare una matrice di intervalli per il parametro `-BlobRestoreRange`. Nell'esempio seguente vengono specificati due intervalli per ripristinare il contenuto completo di *container1* e *container4* allo stato 24 ore fa e il risultato viene salvato in una variabile:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Per eseguire l'operazione di ripristino in modo sincrono e bloccarsi in fase di esecuzione fino al completamento, includere il parametro **-WaitForComplete** nel comando.

---

## <a name="next-steps"></a>Passaggi successivi

- [Ripristino temporizzato per BLOB in blocchi](point-in-time-restore-overview.md)
- [Eliminazione temporanea](soft-delete-overview.md)
- [Feed delle modifiche](storage-blob-change-feed.md)
- [Controllo delle versioni dei BLOB](versioning-overview.md)
