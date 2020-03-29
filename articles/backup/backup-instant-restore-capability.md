---
title: Funzionalità Ripristino istantaneo di Azure
description: Funzionalità Ripristino istantaneo di Azure e domande frequenti per lo stack di backup di macchine virtuali e il modello di distribuzione Resource Manager
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7cf437c6b20ea6b688e8e93e401cf71ef0260888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705429"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Ottenere prestazioni migliori per backup e ripristino con la funzionalità Ripristino istantaneo di Backup di Azure

> [!NOTE]
> In base al feedback degli utenti lo **stack di backup di macchine virtuali V2** viene rinominato **Ripristino istantaneo** per evitare confusione con la funzionalità Azure Stack.
> Tutti gli utenti di backup di Azure sono stati aggiornati a **Ripristino immediato**.

Il nuovo modello per Ripristino istantaneo offre i miglioramenti delle funzionalità seguenti:

* Possibilità di usare snapshot acquisiti durante il processo di backup disponibili per il ripristino senza attendere il completamento del trasferimento dei dati all'insieme di credenziali. Riduce il tempo di attesa per la copia degli snapshot nell'insieme di credenziali prima di attivare il ripristino.
* Riduce i tempi di backup e ripristino conservando gli snapshot in locale per due giorni per impostazione predefinita. Questo valore di conservazione dello snapshot predefinito è configurabile su qualsiasi valore compreso tra 1 e 5 giorni.
* Supporta dimensioni disco fino a 32 TB. Il ridimensionamento dei dischi non è consigliato da Backup di Azure.Resizing of disks is not recommended by Azure Backup.
* Supporta dischi SSD standard insieme a dischi HDD standard e dischi SSD Premium.
* Possibilità di usare un account di archiviazione originale delle macchine virtuali non gestite (per disco), durante il ripristino. Questa possibilità vale anche quando i dischi della macchina virtuale sono distribuiti negli account di archiviazione. Le operazioni di ripristino per un'ampia gamma di configurazioni di macchine virtuali vengono velocizzate.
* Per il backup delle macchine virtuali che usano l'archiviazione Premium, con Ripristino immediato, è consigliabile allocare il *50%* di spazio libero dello spazio di archiviazione totale allocato, necessario **solo** per il primo backup. Il 50% di spazio libero non è un requisito per i backup al termine del primo backup.

## <a name="whats-new-in-this-feature"></a>Novità di questa funzionalità

Il processo di backup è attualmente costituito da due fasi:

1. Acquisizione di uno snapshot della macchina virtuale.
2. Trasferimento dello snapshot della macchina virtuale all'insieme di credenziali di Servizi di ripristino.

Un punto di ripristino si considera creato solo dopo il completamento delle fasi 1 e 2. Come parte di questo aggiornamento, viene creato un punto di ripristino non appena lo snapshot viene completato e tale punto di ripristino di tipo snapshot può essere usato per eseguire un ripristino con lo stesso flusso di ripristino. È possibile identificare questo punto di ripristino nel portale di Azure usando "snapshot" come tipo di punto di ripristino e dopo il trasferimento dello snapshot nell'insieme di credenziali, il tipo di punto di ripristino diventa "snapshot e insieme di credenziali".

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: archiviazione e insieme di credenziali](./media/backup-azure-vms/instant-rp-flow.png)

Per impostazione predefinita, gli snapshot vengono mantenuti per due giorni. Questa funzionalità consente di ripristinare l'operazione da queste istantanee riducendo i tempi di ripristino. Riduce il tempo necessario per trasformare e copiare i dati dal vault.

## <a name="feature-considerations"></a>Considerazioni sulle funzionalità

* Gli snapshot vengono archiviati insieme ai dischi per ottimizzare la creazione del punto di ripristino e velocizzare le operazioni di ripristino. Verranno pertanto addebitati costi di archiviazione corrispondenti agli snapshot creati durante questo periodo.
* Gli snapshot incrementali vengono archiviati come BLOB di pagine. A tutti i clienti che usano dischi non gestiti vengono addebitati i costi per gli snapshot archiviati nell'account di archiviazione locale. Poiché le raccolte di punti di ripristino usate dai backup delle macchine virtuali gestite usano snapshot BLOB a livello di archiviazione sottostante, per i dischi gestiti verranno addebitati i costi corrispondenti ai prezzi degli snapshot BLOB, che sono incrementali.
* Per gli account di archiviazione Premium, gli snapshot creati per i punti di ripristino istantaneo vengono inclusi nel conteggio relativo al limite di 10 TB di spazio allocato.
* Si ottiene la possibilità di configurare la conservazione degli snapshot in base alle esigenze di ripristino. A seconda delle esigenze, è possibile impostare il periodo di conservazione degli snapshot su un minimo di un giorno nel pannello dei criteri di backup, come spiegato di seguito. Ciò consente di risparmiare sui costi per la conservazione degli snapshot se non si eseguono frequentemente i ripristini.
* Si tratta di un aggiornamento undirezionale, una volta aggiornato al ripristino istantaneo, non si può tornare indietro.

>[!NOTE]
>Con questo aggiornamento immediato del ripristino la durata della conservazione degli snapshot di tutti i clienti (**sia nuovi che esistenti**) verrà impostata su un valore predefinito di due giorni. È tuttavia possibile impostare la durata su un qualsiasi valore compreso tra 1 e 5 giorni, in base alle esigenze.

## <a name="cost-impact"></a>Impatto sui costi

Gli snapshot incrementali vengono archiviati nell'account di archiviazione della macchina virtuale, usato per il ripristino istantaneo. Snapshot incrementale significa che lo spazio occupato da uno snapshot è uguale allo spazio occupato dalle pagine scritte dopo la creazione dello snapshot. La fatturazione è ancora per lo spazio utilizzato per GB occupato dallo snapshot e il prezzo per GB è lo stesso indicato nella [pagina dei prezzi.](https://azure.microsoft.com/pricing/details/managed-disks/) Per le macchine virtuali che usano dischi non gestiti, gli snapshot possono essere visualizzati nel menu per il file VHD di ogni disco. Per i dischi gestiti, gli snapshot vengono archiviati in una risorsa di raccolta dei punti di ripristino in un gruppo di risorse designato e gli snapshot stessi non sono direttamente visibili.

>[!NOTE]
> La conservazione degli snapshot è fissata a 5 giorni per i criteri settimanali.

## <a name="configure-snapshot-retention"></a>Configurare la conservazione degli snapshot

### <a name="using-azure-portal"></a>Uso del portale di Azure

Nel portale di Azure è possibile visualizzare un campo aggiunto nel pannello **Criteri di backup della macchina virtuale** nella sezione Ripristino **immediato.** È possibile cambiare la durata della conservazione degli snapshot nel pannello **Criteri di backup di macchine virtuali** per tutte le macchine virtuali associate al criterio di backup specifico.

![Funzionalità Ripristino istantaneo](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Utilizzo di PowerShell

>[!NOTE]
> Da Az PowerShell versione 1.6.0 in poi, è possibile aggiornare il periodo di conservazione dello snapshot di ripristino immediato nei criteri usando PowerShellFrom Az PowerShell version 1.6.0 onwards, you can update the instant restore snapshot retention period in policy using PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

La conservazione dello snapshot predefinito per ogni criterio è impostata su due giorni. L'utente può modificare il valore in un minimo di 1 e un massimo di cinque giorni. Per i criteri settimanali, la conservazione dello snapshot è fissata a cinque giorni.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quali sono le implicazioni a livello di costi di Ripristino istantaneo?

Gli snapshot vengono archiviati insieme ai dischi per velocizzare la creazione del punto di ripristino e le operazioni di ripristino. Di conseguenza, si noteranno costi di archiviazione corrispondenti al periodo di conservazione degli snapshot selezionato come parte dei criteri di backup della macchina virtuale.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Negli account di archiviazione Premium gli snapshot creati per un punto di ripristino istantaneo contribuiscono al limite per gli snapshot di 10 TB?

Sì, per gli account di archiviazione Premium gli snapshot acquisiti per il punto di ripristino immediato occupano 10 TB di spazio snapshot allocato.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Come funziona la conservazione degli snapshot durante il periodo di cinque giorni?

Ogni giorno viene creato un nuovo snapshot, quindi esistono cinque singoli snapshot incrementali. La dimensione dello snapshot dipende dalla varianza dei dati, che nella maggior parte dei casi si aggiunde intorno al 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Uno snapshot di ripristino istantaneo è uno snapshot incrementale o completo?

Gli snapshot creati come parte della funzionalità di ripristino istantaneo sono snapshot incrementali.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Come è possibile calcolare l'aumento dei costi approssimativo correlato alla funzionalità di ripristino istantaneo?

Dipende dalla varianza della macchina virtuale. In uno stato stabile, si può presupporre che l'aumento dei costi sia uguale a: periodo di conservazione snapshot * varianza giornaliera per ogni macchina virtuale * costi di archiviazione per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se il tipo di ripristino per un punto di ripristino è "Snapshot e insieme di credenziali" e si esegue un'operazione di ripristino, quale tipo di ripristino verrà usato?

Se il tipo di ripristino è "Snapshot e insieme di credenziali", il ripristino verrà eseguito automaticamente dallo snapshot locale e l'operazione sarà molto più veloce rispetto al ripristino eseguito dall'insieme di credenziali.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Che cosa accade se si seleziona un periodo di conservazione del punto di ripristino (livello 2) inferiore a quello dello snapshot (livello 1)?

Il nuovo modello non consente di eliminare il punto di ripristino (livello 2) senza eliminare lo snapshot (livello 1). È consigliabile pianificare un periodo di conservazione del punto di ripristino (livello 2) superiore al periodo di conservazione degli snapshot.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Perché lo snapshot è disponibile anche dopo il periodo di conservazione impostato nei criteri di backup?

Se il punto di ripristino contiene snapshot e si tratta dell'ultimo punto di ripristino disponibile, viene conservato fino al corretto completamento di un backup successivo. Questo è secondo il criterio di "garbage collection" (GC) progettato oggi che impone almeno un RP più recente per essere sempre presente nel caso in cui tutti i backup ulteriormente in caso di esito negativo a causa di un problema nella macchina virtuale. Negli scenari normali, i punti di ripristino vengono puliti entro 24 ore dalla relativa scadenza.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Non ho bisogno della funzionalità di ripristino istantaneo. Può essere disabilitato?

La funzione di ripristino immediato è abilitata per tutti e non può essere disabilitata. È possibile ridurre la conservazione dello snapshot a un minimo di un giorno.

>[!NOTE]
> **Backup di Azure supporta ora il backup e il ripristino selettivi del disco tramite la soluzione di backup della macchina virtuale di Azure.Azure Backup now supports selective disk backup and restore using the Azure Virtual Machine backup solution.**
>
>Oggi, Backup di Azure supporta il backup di tutti i dischi (sistema operativo e dati) in una macchina virtuale insieme usando la soluzione di backup della macchina virtuale. Con la funzionalità exclude-disk, è possibile eseguire il backup di uno o pochi dischi dati dai molti dischi dati in una macchina virtuale. Ciò offre una soluzione efficiente ed economica per le vostre esigenze di backup e ripristino. Ogni punto di ripristino contiene i dati dei dischi inclusi nell'operazione di backup, che consente di ripristinare un sottoinsieme di dischi dal punto di ripristino specificato durante l'operazione di ripristino. Questo vale per il ripristino sia dallo snapshot che dal vault.
>
>**Per iscriverti all'anteprima, scrivici all'utenteAskAzureBackupTeam@microsoft.com**
