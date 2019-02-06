---
title: Funzionalità Ripristino istantaneo di Azure
description: Funzionalità Ripristino istantaneo di Azure e domande frequenti per lo stack di backup di macchine virtuali e il modello di distribuzione Resource Manager
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: trinadhk
ms.openlocfilehash: a5afdd0dca55a5df075acb99e488c932196d95c5
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55092298"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Ottenere prestazioni migliori per backup e ripristino con la funzionalità Ripristino istantaneo di Backup di Azure

> [!NOTE]
> In base al feedback degli utenti lo **stack di backup di macchine virtuali V2** viene rinominato **Ripristino istantaneo** per evitare confusione con la funzionalità Azure Stack.

Il nuovo modello per Ripristino istantaneo offre i miglioramenti delle funzionalità seguenti:

* Possibilità di usare snapshot acquisiti durante il processo di backup disponibili per il ripristino senza attendere il completamento del trasferimento dei dati all'insieme di credenziali. Riduce il tempo di attesa per la copia degli snapshot nell'insieme di credenziali prima di attivare il ripristino.
* Riduzione dei tempi di backup e ripristino conservando gli snapshot insieme ai dischi per sette giorni.
* Supporto di dischi con dimensioni fino a 4 TB.
* Supporto di dischi SSD Standard.
* Possibilità di usare gli account di archiviazione originali (per ogni disco) di una macchina virtuale non gestita quando si esegue il ripristino. Questa possibilità vale anche quando i dischi della macchina virtuale sono distribuiti negli account di archiviazione. Le operazioni di ripristino per un'ampia gamma di configurazioni di macchine virtuali vengono velocizzate.


## <a name="whats-new-in-this-feature"></a>Novità di questa funzionalità

Il processo di backup è attualmente costituito da due fasi:

1.  Acquisizione di uno snapshot della macchina virtuale.
2.  Trasferimento dello snapshot della macchina virtuale all'insieme di credenziali di Servizi di ripristino.

Un punto di ripristino si considera creato solo dopo il completamento delle fasi 1 e 2. Come parte di questo aggiornamento, viene creato un punto di ripristino non appena lo snapshot viene completato e tale punto di ripristino di tipo snapshot può essere usato per eseguire un ripristino con lo stesso flusso di ripristino. È possibile identificare questo punto di ripristino nel portale di Azure con "snapshot" come tipo di punto di ripristino e dopo il trasferimento dello snapshot all'insieme di credenziali, il tipo di punto di ripristino diventa "snapshot e insieme di credenziali".

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: archiviazione e insieme di credenziali](./media/backup-azure-vms/instant-rp-flow.png)

Gli snapshot vengono conservati per sette giorni. Questa funzionalità consente l'operazione di ripristino da questi snapshot con conseguente riduzione dei tempi di ripristino. Riduce il tempo necessario per trasformare e copiare i dati dall'insieme di credenziali all'account di archiviazione dell'utente per gli scenari con dischi non gestiti, mentre per gli utenti di dischi gestiti crea dischi gestiti dai dati di backup.

## <a name="feature-considerations"></a>Considerazioni sulle funzionalità

* Gli snapshot vengono archiviati insieme ai dischi per ottimizzare la creazione del punto di ripristino e velocizzare le operazioni di ripristino. Verranno pertanto addebitati costi di archiviazione corrispondenti agli snapshot creati durante questo periodo.
* Gli snapshot incrementali vengono archiviati come BLOB di pagine. A tutti i clienti che usano dischi non gestiti vengono addebitati i costi per gli snapshot archiviati nell'account di archiviazione locale. Poiché le raccolte di punti di ripristino usate dai backup delle macchine virtuali gestite usano snapshot BLOB a livello di archiviazione sottostante, per i dischi gestiti verranno addebitati i costi corrispondenti ai prezzi degli snapshot BLOB, che sono incrementali.
* Per gli account di archiviazione Premium, gli snapshot creati per i punti di ripristino istantaneo vengono inclusi nel conteggio relativo al limite di 10 TB di spazio allocato.

## <a name="cost-impact"></a>Impatto sui costi

Gli snapshot incrementali vengono archiviati nell'account di archiviazione della macchina virtuale e vengono usati per il ripristino istantaneo. Snapshot incrementale significa che lo spazio occupato da uno snapshot è uguale allo spazio occupato dalle pagine scritte dopo la creazione dello snapshot. La fatturazione è ancora basata sullo spazio usato in GB occupato dallo snapshot e il prezzo per GB è quello indicato nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="upgrading-to-instant-restore"></a>Aggiornamento a Ripristino istantaneo

Se si usa il portale di Azure, verrà visualizzata una notifica nel dashboard dell'insieme di credenziali. Questa notifica si riferisce al supporto di dischi di grandi dimensioni e ai miglioramenti della velocità di backup e ripristino.

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: notifica relativa al supporto](./media/backup-azure-vms/instant-rp-banner.png)

Per aprire una schermata per l'aggiornamento a Ripristino istantaneo, selezionare il banner.

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: aggiornamento](./media/backup-azure-vms/instant-rp.png)

In alternativa, è possibile passare alla pagina **Proprietà** dell'insieme di credenziali per accedere all'opzione **Aggiorna** in **Stack di backup di macchine virtuali**.

![Processo di backup nello stack di backup di macchine virtuali - Pagina Proprietà](./media/backup-azure-vms/instant-restore-capability-properties.png)


## <a name="upgrade-to-instant-restore-using-powershell"></a>Eseguire l'aggiornamento a Ripristino istantaneo con PowerShell

Se si vuole procedere in autonomia ed eseguire l'aggiornamento a Ripristino istantaneo, eseguire i cmdlet seguenti da un terminale di PowerShell con privilegi elevati:

1.  Accedere all'account Azure:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Selezionare la sottoscrizione da registrare:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Registrare questa sottoscrizione:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="upgrade-to-instant-restore-using-cli"></a>Eseguire l'aggiornamento a Ripristino istantaneo tramite l'interfaccia della riga di comando

Eseguire i comandi seguenti da una shell:

1.  Accedere all'account Azure:

    ```
    az login
    ```

2.  Selezionare la sottoscrizione da registrare:

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Registrare questa sottoscrizione:

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>Verificare se l'aggiornamento è stato completato

### <a name="powershell"></a>PowerShell
Da un terminale di PowerShell con privilegi elevati eseguire il cmdlet seguente:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
Eseguire il comando seguente da una shell:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Se viene visualizzato "Registered", la sottoscrizione è aggiornata a Ripristino istantaneo.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quali sono le implicazioni a livello di costi di Ripristino istantaneo?
Gli snapshot vengono archiviati insieme ai dischi per velocizzare la creazione del punto di ripristino e le operazioni di ripristino. Di conseguenza, si noteranno costi di archiviazione corrispondenti al periodo di conservazione degli snapshot selezionato come parte dei criteri di backup della macchina virtuale.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Negli account di archiviazione Premium gli snapshot creati per un punto di ripristino istantaneo contribuiscono al limite per gli snapshot di 10 TB?
Sì, per gli account di archiviazione Premium gli snapshot creati per il punto di ripristino istantaneo occupano i 10 TB dello spazio allocato per gli snapshot.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Come funziona la conservazione degli snapshot durante il periodo di cinque giorni?
Ogni giorno viene creato un nuovo snapshot, quindi esistono cinque singoli snapshot incrementali. Le dimensioni dello snapshot dipendono dalla varianza dei dati, nella maggior parte dei casi compresa tra il 2% e il 7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Uno snapshot di ripristino istantaneo è uno snapshot incrementale o completo?
Gli snapshot creati come parte della funzionalità di ripristino istantaneo sono snapshot incrementali.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Come è possibile calcolare l'aumento dei costi approssimativo correlato alla funzionalità di ripristino istantaneo?
Dipende dalla varianza della macchina virtuale. In uno stato stabile, si può presupporre che l'aumento dei costi sia uguale a: periodo di conservazione snapshot * varianza giornaliera per ogni macchina virtuale * costi di archiviazione per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se il tipo di ripristino per un punto di ripristino è "Snapshot e insieme di credenziali" e si esegue un'operazione di ripristino, quale tipo di ripristino verrà usato?
Se il tipo di ripristino è "Snapshot e insieme di credenziali", il ripristino verrà eseguito automaticamente dallo snapshot locale e l'operazione sarà molto più veloce rispetto al ripristino eseguito dall'insieme di credenziali.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Che cosa accade se si seleziona un periodo di conservazione del punto di ripristino (livello 2) inferiore a quello dello snapshot (livello 1)?
Il nuovo modello non consente di eliminare il punto di ripristino (livello 2) senza eliminare lo snapshot (livello 1). È attualmente supportato un periodo di conservazione di sette giorni per l'eliminazione dello snapshot (livello 1), quindi un periodo di conservazione del punto di ripristino (livello 2) inferiore a sette giorni non viene rispettato. È consigliabile pianificare un periodo di conservazione del punto di ripristino (livello 2) superiore a sette giorni.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Perché lo snapshot è disponibile anche dopo il periodo di conservazione impostato nei criteri di backup?
Se il punto di ripristino contiene snapshot e si tratta dell'ultimo punto di ripristino disponibile, viene conservato fino al corretto completamento di un backup successivo. Questo comportamento è conforme ai criteri di GC progettati attuali, che prevedono che sia sempre presente almeno un punto di ripristino aggiornato nel caso tutti i backup successivi abbiano esito negativo a causa di un problema nella macchina virtuale. Negli scenari normali, i punti di ripristino vengono puliti entro massimo 48 ore dalla relativa scadenza.
