---
title: Funzionalità Ripristino istantaneo di Azure
description: Funzionalità Ripristino istantaneo di Azure e domande frequenti per lo stack di backup di macchine virtuali e il modello di distribuzione Resource Manager
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: df5af780730f4f5b972107f50387dc0c58b80eaa
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761747"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Ottenere prestazioni migliori per backup e ripristino con la funzionalità Ripristino istantaneo di Backup di Azure

> [!NOTE]
> In base al feedback degli utenti lo **stack di backup di macchine virtuali V2** viene rinominato **Ripristino istantaneo** per evitare confusione con la funzionalità Azure Stack.
> Tutti gli utenti di backup di Azure sono stati aggiornati per il **ripristino immediato**.

Il nuovo modello per Ripristino istantaneo offre i miglioramenti delle funzionalità seguenti:

* Possibilità di usare snapshot acquisiti durante il processo di backup disponibili per il ripristino senza attendere il completamento del trasferimento dei dati all'insieme di credenziali. Riduce il tempo di attesa per la copia degli snapshot nell'insieme di credenziali prima di attivare il ripristino.
* Riduce i tempi di backup e ripristino conservando gli snapshot in locale per due giorni per impostazione predefinita. Questo valore predefinito di memorizzazione dello snapshot è configurabile con qualsiasi valore compreso tra 1 e 5 giorni.
* Supporta dimensioni del disco fino a 32 TB. Il ridimensionamento dei dischi non è consigliato da backup di Azure.
* Supporta SDD Standard dischi insieme a dischi HDD Standard e dischi di SSD Premium.
* Possibilità di usare gli account di archiviazione originali di macchine virtuali non gestite (per disco) durante il ripristino. Questa possibilità vale anche quando i dischi della macchina virtuale sono distribuiti negli account di archiviazione. Le operazioni di ripristino per un'ampia gamma di configurazioni di macchine virtuali vengono velocizzate.
* Per il backup delle macchine virtuali che usano dischi Premium non gestiti negli account di archiviazione, con il ripristino istantaneo è consigliabile allocare il *50%* di spazio disponibile dello spazio di archiviazione totale allocato, che è necessario **solo** per il primo backup. Il 50% di spazio disponibile non è un requisito per i backup dopo il completamento del primo backup.

## <a name="whats-new-in-this-feature"></a>Novità di questa funzionalità

Il processo di backup è attualmente costituito da due fasi:

1. Acquisizione di uno snapshot della macchina virtuale.
2. Trasferimento dello snapshot della macchina virtuale all'insieme di credenziali di Servizi di ripristino.

Un punto di ripristino si considera creato solo dopo il completamento delle fasi 1 e 2. Come parte di questo aggiornamento, viene creato un punto di ripristino non appena lo snapshot viene completato e tale punto di ripristino di tipo snapshot può essere usato per eseguire un ripristino con lo stesso flusso di ripristino. È possibile identificare questo punto di ripristino nel portale di Azure usando "snapshot" come tipo di punto di ripristino e dopo che lo snapshot è stato trasferito nell'insieme di credenziali, il tipo di punto di ripristino viene modificato in "snapshot and Vault".

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: archiviazione e insieme di credenziali](./media/backup-azure-vms/instant-rp-flow.png)

Per impostazione predefinita, gli snapshot vengono conservati per due giorni. Questa funzionalità consente di eseguire l'operazione di ripristino da tali snapshot, riducendo i tempi di ripristino. Riduce il tempo necessario per la trasformazione e la copia dei dati dall'insieme di credenziali.

## <a name="feature-considerations"></a>Considerazioni sulle funzionalità

* Gli snapshot vengono archiviati insieme ai dischi per ottimizzare la creazione del punto di ripristino e velocizzare le operazioni di ripristino. Verranno pertanto addebitati costi di archiviazione corrispondenti agli snapshot creati durante questo periodo.
* Gli snapshot incrementali vengono archiviati come BLOB di pagine. A tutti i clienti che usano dischi non gestiti vengono addebitati i costi per gli snapshot archiviati nell'account di archiviazione locale. Poiché le raccolte di punti di ripristino usate dai backup di macchine virtuali gestite usano snapshot di BLOB a livello di archiviazione sottostante, per i dischi gestiti vengono visualizzati i costi corrispondenti ai prezzi degli snapshot BLOB e sono incrementali.
* Per gli account di archiviazione Premium, gli snapshot creati per i punti di ripristino istantaneo vengono inclusi nel conteggio relativo al limite di 10 TB di spazio allocato.
* Si ottiene la possibilità di configurare la conservazione degli snapshot in base alle esigenze di ripristino. A seconda del requisito, è possibile impostare la conservazione degli snapshot su un minimo di un giorno nel riquadro Criteri di backup, come illustrato di seguito. Ciò consentirà di risparmiare sui costi per la conservazione degli snapshot se i ripristini non vengono eseguiti di frequente.
* Si tratta di un aggiornamento direzionale. Una volta eseguito l'aggiornamento a Instant Restore, non è possibile tornare indietro.

>[!NOTE]
>Con questo aggiornamento immediato del ripristino la durata della conservazione degli snapshot di tutti i clienti (**sia nuovi che esistenti**) verrà impostata su un valore predefinito di due giorni. Tuttavia, è possibile impostare la durata in base alle esigenze su un valore compreso tra 1 e 5 giorni.

## <a name="cost-impact"></a>Impatto sui costi

Gli snapshot incrementali vengono archiviati nell'account di archiviazione della macchina virtuale, usato per il ripristino istantaneo. Snapshot incrementale significa che lo spazio occupato da uno snapshot è uguale allo spazio occupato dalle pagine scritte dopo la creazione dello snapshot. La fatturazione è ancora per lo spazio usato per GB occupato dallo snapshot e il prezzo per GB è uguale a quello indicato nella [pagina dei prezzi](https://azure.microsoft.com/pricing/details/managed-disks/). Per le macchine virtuali che usano dischi non gestiti, gli snapshot possono essere visualizzati nel menu per il file VHD di ogni disco. Per i dischi gestiti, gli snapshot vengono archiviati in una risorsa di raccolta di punti di ripristino in un gruppo di risorse designato e gli snapshot non sono direttamente visibili.

>[!NOTE]
> La conservazione degli snapshot è fissata a 5 giorni per i criteri settimanali.

## <a name="configure-snapshot-retention"></a>Configurare la conservazione degli snapshot

### <a name="using-azure-portal"></a>Uso del portale di Azure

Nella portale di Azure è possibile visualizzare un campo aggiunto nel riquadro Criteri di **backup della macchina virtuale** nella sezione **ripristino immediato** . È possibile modificare la durata del periodo di memorizzazione dello snapshot dal riquadro **criteri di backup della macchina virtuale** per tutte le macchine virtuali associate al criterio di backup specifico.

![Funzionalità Ripristino istantaneo](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Utilizzo di PowerShell

>[!NOTE]
> Da AZ PowerShell versione 1.6.0 in poi, è possibile aggiornare il periodo di conservazione degli snapshot di ripristino istantaneo nei criteri usando PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

La conservazione predefinita degli snapshot per ciascun criterio è impostata su due giorni. L'utente può modificare il valore in un minimo di 1 e un massimo di cinque giorni. Per i criteri settimanali, la conservazione dello snapshot è fissata a cinque giorni.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quali sono le implicazioni a livello di costi di Ripristino istantaneo?

Gli snapshot vengono archiviati insieme ai dischi per velocizzare la creazione del punto di ripristino e le operazioni di ripristino. Di conseguenza, si noteranno costi di archiviazione corrispondenti al periodo di conservazione degli snapshot selezionato come parte dei criteri di backup della macchina virtuale.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Negli account di archiviazione Premium gli snapshot creati per un punto di ripristino istantaneo contribuiscono al limite per gli snapshot di 10 TB?

Sì, per gli account di archiviazione Premium gli snapshot presi per il punto di ripristino istantaneo occupano 10 TB di spazio di snapshot allocato.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Come funziona la conservazione degli snapshot durante il periodo di cinque giorni?

Ogni giorno viene creato un nuovo snapshot, quindi esistono cinque singoli snapshot incrementali. Le dimensioni dello snapshot dipendono dalla varianza dei dati, che nella maggior parte dei casi è intorno al 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Uno snapshot di ripristino istantaneo è uno snapshot incrementale o completo?

Gli snapshot creati come parte della funzionalità di ripristino istantaneo sono snapshot incrementali.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Come è possibile calcolare l'aumento dei costi approssimativo correlato alla funzionalità di ripristino istantaneo?

Dipende dalla varianza della macchina virtuale. In uno stato stabile, si può presupporre che l'aumento dei costi sia uguale a: periodo di conservazione snapshot * varianza giornaliera per ogni macchina virtuale * costi di archiviazione per GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Se il tipo di ripristino per un punto di ripristino è "Snapshot e insieme di credenziali" e si esegue un'operazione di ripristino, quale tipo di ripristino verrà usato?

Se il tipo di ripristino è "Snapshot e insieme di credenziali", il ripristino verrà eseguito automaticamente dallo snapshot locale e l'operazione sarà molto più veloce rispetto al ripristino eseguito dall'insieme di credenziali.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Che cosa accade se si seleziona un periodo di conservazione del punto di ripristino (livello 2) inferiore a quello dello snapshot (livello 1)?

Il nuovo modello non consente l'eliminazione del punto di ripristino (livello 2) a meno che non venga eliminato lo snapshot (Tier1). È consigliabile pianificare un periodo di conservazione del punto di ripristino (livello 2) superiore al periodo di conservazione degli snapshot.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Perché lo snapshot è disponibile anche dopo il periodo di conservazione impostato nei criteri di backup?

Se il punto di ripristino dispone di uno snapshot e la versione più recente di RP è disponibile, viene mantenuta fino al successivo backup completato. Questa situazione si verifica in base ai criteri di "Garbage Collection" progettati (GC) che impone che almeno un RP più recente sia sempre presente nel caso in cui tutti i backup abbiano esito negativo a causa di un problema nella macchina virtuale. Negli scenari normali, i punti di ripristino vengono puliti entro 24 ore dalla relativa scadenza.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Non è necessaria la funzionalità di ripristino immediato. Può essere disabilitato?

La funzionalità di ripristino istantaneo è abilitata per tutti e non può essere disabilitata. È possibile ridurre la conservazione degli snapshot fino a un minimo di un giorno.
