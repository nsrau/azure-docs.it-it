---
title: Funzionalità Ripristino istantaneo di Azure
description: Funzionalità Ripristino istantaneo di Azure e domande frequenti per lo stack di backup di macchine virtuali e il modello di distribuzione Resource Manager
ms.reviewer: sogup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dacurwin
ms.openlocfilehash: f2485786f323bc6d19d39f4da6ffa7cab14bbb4c
ms.sourcegitcommit: acffa72239413c62662febd4e39ebcb6c6c0dd00
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951829"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Ottenere prestazioni migliori per backup e ripristino con la funzionalità Ripristino istantaneo di Backup di Azure

> [!NOTE]
> In base al feedback degli utenti lo **stack di backup di macchine virtuali V2** viene rinominato **Ripristino istantaneo** per evitare confusione con la funzionalità Azure Stack.<br/><br/> Tutti gli utenti di backup di Azure sono stati aggiornati per il **ripristino immediato**.

Il nuovo modello per Ripristino istantaneo offre i miglioramenti delle funzionalità seguenti:

* Possibilità di usare snapshot acquisiti durante il processo di backup disponibili per il ripristino senza attendere il completamento del trasferimento dei dati all'insieme di credenziali. Riduce il tempo di attesa per la copia degli snapshot nell'insieme di credenziali prima di attivare il ripristino.
* Riduce i tempi di backup e ripristino conservando gli snapshot in locale per due giorni per impostazione predefinita. Questo valore predefinito di memorizzazione dello snapshot è configurabile con qualsiasi valore compreso tra 1 e 5 giorni.
* Supporto di dischi con dimensioni fino a 4 TB. Il ridimensionamento dei dischi non è consigliato da backup di Azure. Per iscriversi a una versione di anteprima pubblica limitata del supporto per dischi di backup su disco di Azure con dimensioni maggiori di 4 TB e fino a 30 TB, vedere [una panoramica del backup delle macchine virtuali di Azure](backup-azure-vms-introduction.md#limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb).
* Supporta SDD Standard dischi insieme a dischi HDD Standard e dischi di SSD Premium.
* Possibilità di usare gli account di archiviazione originali di macchine virtuali non gestite (per disco) durante il ripristino. Questa possibilità vale anche quando i dischi della macchina virtuale sono distribuiti negli account di archiviazione. Le operazioni di ripristino per un'ampia gamma di configurazioni di macchine virtuali vengono velocizzate.
* Per il backup delle macchine virtuali che usano archiviazione Premium, con il ripristino immediato è consigliabile allocare lo spazio disponibile del *50%* dello spazio di archiviazione totale allocato, che è necessario **solo** per il primo backup. Il 50% di spazio disponibile non è un requisito per i backup dopo il completamento del primo backup.


## <a name="whats-new-in-this-feature"></a>Novità di questa funzionalità

Il processo di backup è attualmente costituito da due fasi:

1.  Acquisizione di uno snapshot della macchina virtuale.
2.  Trasferimento dello snapshot della macchina virtuale all'insieme di credenziali di Servizi di ripristino.

Un punto di ripristino si considera creato solo dopo il completamento delle fasi 1 e 2. Come parte di questo aggiornamento, viene creato un punto di ripristino non appena lo snapshot viene completato e tale punto di ripristino di tipo snapshot può essere usato per eseguire un ripristino con lo stesso flusso di ripristino. È possibile identificare questo punto di ripristino nel portale di Azure usando "snapshot" come tipo di punto di ripristino e dopo che lo snapshot è stato trasferito nell'insieme di credenziali, il tipo di punto di ripristino viene modificato in "snapshot and Vault".

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: archiviazione e insieme di credenziali](./media/backup-azure-vms/instant-rp-flow.png)

Per impostazione predefinita, gli snapshot vengono conservati per due giorni. Questa funzionalità consente di eseguire l'operazione di ripristino da tali snapshot, riducendo i tempi di ripristino. Riduce il tempo necessario per la trasformazione e la copia dei dati dall'insieme di credenziali.

## <a name="feature-considerations"></a>Considerazioni sulle funzionalità

* Gli snapshot vengono archiviati insieme ai dischi per ottimizzare la creazione del punto di ripristino e velocizzare le operazioni di ripristino. Verranno pertanto addebitati costi di archiviazione corrispondenti agli snapshot creati durante questo periodo.
* Gli snapshot incrementali vengono archiviati come BLOB di pagine. A tutti i clienti che usano dischi non gestiti vengono addebitati i costi per gli snapshot archiviati nell'account di archiviazione locale. Poiché le raccolte di punti di ripristino usate dai backup delle macchine virtuali gestite usano snapshot BLOB a livello di archiviazione sottostante, per i dischi gestiti verranno addebitati i costi corrispondenti ai prezzi degli snapshot BLOB, che sono incrementali.
* Per gli account di archiviazione Premium, gli snapshot presi per i punti di ripristino istantanei vengono conteggiati fino al limite di 10 TB di spazio allocato.
* Si ottiene la possibilità di configurare la conservazione degli snapshot in base alle esigenze di ripristino. A seconda delle esigenze, è possibile impostare il periodo di conservazione degli snapshot su un minimo di un giorno nel pannello dei criteri di backup, come spiegato di seguito. Ciò consentirà di risparmiare sui costi per la conservazione degli snapshot se i ripristini non vengono eseguiti di frequente.
* Si tratta di un aggiornamento direzionale, una volta aggiornato a un ripristino immediato, non è possibile tornare indietro.

>[!NOTE]
>Con questo aggiornamento immediato del ripristino la durata della conservazione degli snapshot di tutti i clienti (**sia nuovi che esistenti**) verrà impostata su un valore predefinito di due giorni. È tuttavia possibile impostare la durata su un qualsiasi valore compreso tra 1 e 5 giorni, in base alle esigenze.

## <a name="cost-impact"></a>Impatto sui costi

Gli snapshot incrementali vengono archiviati nell'account di archiviazione delle macchine virtuali, che viene usato per il ripristino istantaneo. Snapshot incrementale significa che lo spazio occupato da uno snapshot è uguale allo spazio occupato dalle pagine scritte dopo la creazione dello snapshot. La fatturazione è ancora basata sullo spazio usato in GB occupato dallo snapshot e il prezzo per GB è quello indicato nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/managed-disks/).

>[!NOTE]
> La conservazione degli snapshot è fissata a 5 giorni per i criteri settimanali.

## <a name="configure-snapshot-retention"></a>Configurare la conservazione degli snapshot

### <a name="using-azure-portal"></a>Uso del portale di Azure

Nella portale di Azure è possibile visualizzare un campo aggiunto nel pannello criteri di **backup della macchina virtuale** nella sezione **ripristino immediato** . È possibile cambiare la durata della conservazione degli snapshot nel pannello **Criteri di backup di macchine virtuali** per tutte le macchine virtuali associate al criterio di backup specifico.

![Funzionalità Ripristino istantaneo](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Tramite PowerShell

>[!NOTE]
> Da AZ PowerShell versione 1.6.0 in poi, è possibile aggiornare il periodo di conservazione degli snapshot di ripristino istantaneo nei criteri usando PowerShell

```powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```
La conservazione predefinita degli snapshot per ciascun criterio è impostata su 2 giorni. L'utente può modificare il valore con un minimo di 1 e un massimo di 5 giorni. Per i criteri settimanali, la conservazione dello snapshot è fissata a 5 giorni.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quali sono le implicazioni a livello di costi di Ripristino istantaneo?
Gli snapshot vengono archiviati insieme ai dischi per velocizzare la creazione del punto di ripristino e le operazioni di ripristino. Di conseguenza, si noteranno costi di archiviazione corrispondenti al periodo di conservazione degli snapshot selezionato come parte dei criteri di backup della macchina virtuale.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Negli account di archiviazione Premium, gli snapshot presi per il punto di ripristino istantaneo occupano il limite di snapshot di 10 TB?
Sì, per gli account di archiviazione Premium gli snapshot presi per il punto di ripristino istantaneo occupano 10 TB di spazio di snapshot allocato.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Come funziona la conservazione degli snapshot durante il periodo di cinque giorni?
Ogni giorno viene creato un nuovo snapshot, quindi esistono cinque singoli snapshot incrementali. Le dimensioni dello snapshot dipendono dalla varianza dei dati, che nella maggior parte dei casi è intorno al 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Uno snapshot di ripristino istantaneo è uno snapshot incrementale o completo?
Gli snapshot creati come parte della funzionalità di ripristino istantaneo sono snapshot incrementali.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Come è possibile calcolare l'aumento dei costi approssimativo correlato alla funzionalità di ripristino istantaneo?
Dipende dalla varianza della macchina virtuale. In uno stato stabile, si può presupporre che l'aumento dei costi sia uguale a: periodo di conservazione snapshot *varianza giornaliera per ogni macchina virtuale* costi di archiviazione per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se il tipo di ripristino per un punto di ripristino è "Snapshot e insieme di credenziali" e si esegue un'operazione di ripristino, quale tipo di ripristino verrà usato?
Se il tipo di ripristino è "Snapshot e insieme di credenziali", il ripristino verrà eseguito automaticamente dallo snapshot locale e l'operazione sarà molto più veloce rispetto al ripristino eseguito dall'insieme di credenziali.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Che cosa accade se si seleziona un periodo di conservazione del punto di ripristino (livello 2) inferiore a quello dello snapshot (livello 1)?
Il nuovo modello non consente di eliminare il punto di ripristino (livello 2) senza eliminare lo snapshot (livello 1). È consigliabile pianificare un periodo di conservazione del punto di ripristino (livello 2) superiore al periodo di conservazione degli snapshot.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Perché lo snapshot è disponibile anche dopo il periodo di conservazione impostato nei criteri di backup?
Se il punto di ripristino contiene snapshot e si tratta dell'ultimo punto di ripristino disponibile, viene conservato fino al corretto completamento di un backup successivo. Questo è possibile in base ai criteri di "Garbage Collection" progettati (GC) che impone che almeno un RP più recente sia sempre presente nel caso in cui tutti i backup abbiano esito negativo a causa di un problema nella macchina virtuale. Negli scenari normali, i punti di ripristino vengono puliti entro 24 ore dalla relativa scadenza.
