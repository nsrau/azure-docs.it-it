---
title: Eseguire l'aggiornamento alla versione 2 dello stack di backup di macchine virtuali di Azure
description: Processo di aggiornamento e domande frequenti per lo stack di backup di macchine virtuali e il modello di distribuzione Resource Manager
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 7/18/2018
ms.author: trinadhk
ms.openlocfilehash: c9dff77f6b9fffc02ec94caa3454500772651195
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136901"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Eseguire l'aggiornamento alla versione 2 dello stack di macchine virtuali di Azure

Il modello di distribuzione Resource Manager per l'aggiornamento allo stack di backup di macchine virtuali offre i seguenti miglioramenti delle funzionalità:

* Possibilità di visualizzare lo snapshot acquisito durante il processo di backup che è disponibile per il ripristino senza attendere il completamento del trasferimento dei dati. Riduce il tempo di attesa per la copia degli snapshot nell'insieme di credenziali prima di attivare il ripristino. Elimina anche la necessità di spazio di archiviazione aggiuntivo per il backup di macchine virtuali Premium, tranne che per il primo backup.  

* Riduzione dei tempi di backup e ripristino conservando gli snapshot in locale per sette giorni.

* Supporto per dischi con dimensioni fino a 4 TB.

* Possibilità di usare gli account di archiviazione originali quando si esegue il ripristino di una macchina virtuale non gestita. Questa possibilità vale anche quando i dischi della macchina virtuale sono distribuiti negli account di archiviazione. Le operazioni di ripristino per un'ampia gamma di configurazioni di macchine virtuali vengono velocizzate.
    > [!NOTE]
    > Non è come eseguire l'override della macchina virtuale originale. 
    >

## <a name="whats-changing-in-the-new-stack"></a>Modifiche nel nuovo stack
Il processo di backup è attualmente costituito da due fasi:
1.  Acquisizione di uno snapshot della macchina virtuale. 
2.  Trasferimento dello snapshot della macchina virtuale all'insieme di credenziali di Backup di Azure. 

Un punto di ripristino si considera creato solo dopo che sono state eseguite le fasi 1 e 2. Nell'ambito del nuovo stack viene creato un punto di ripristino non appena lo snapshot viene completato. È anche possibile eseguire il ripristino da questo punto usando lo stesso flusso di ripristino. È possibile identificare questo punto di ripristino nel portale di Azure usando lo "snapshot" come tipo di punto di ripristino. Dopo che lo snapshot è stato trasferito all'insieme di credenziali, il tipo di punto di ripristino diventa "snapshot e insieme di credenziali". 

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: archiviazione e insieme di credenziali](./media/backup-azure-vms/instant-rp-flow.jpg) 

Per impostazione predefinita, gli snapshot vengono conservati per sette giorni. Questa funzionalità consente di completare più rapidamente il ripristino da questi snapshot. Riduce il tempo necessario per copiare i dati dall'insieme di credenziali all'account di archiviazione del cliente. 

## <a name="considerations-before-upgrade"></a>Considerazioni prima dell'aggiornamento

* L'aggiornamento dello stack di backup delle macchine virtuali è unidirezionale e tutti i backup seguono questo flusso. La modifica viene apportata a livello di sottoscrizione e di conseguenza tutte le macchine virtuali seguono questo flusso. Tutte le aggiunte di nuove funzionalità si basano sullo stesso stack. Attualmente non è possibile controllare lo stack a livello di criteri.

* Gli snapshot vengono archiviati in locale per supportare la creazione del punto di ripristino e anche per velocizzare le operazioni di ripristino. Vengono visualizzati pertanto i costi di archiviazione che corrispondono agli snapshot creati durante il periodo di sette giorni.

* Gli snapshot incrementali vengono archiviati come BLOB di pagine. A tutti i clienti che usano dischi non gestiti verranno addebitati gli snapshot per sette giorni archiviati nell'account di archiviazione locale del cliente. In base al modello tariffario corrente, non sono previsti costi per i clienti dei dischi gestiti.

* Se si ripristina una macchina virtuale Premium da un punto di ripristino dello snapshot, viene usato un percorso di archiviazione temporaneo durante la creazione della macchina virtuale stessa.

* Per gli account di archiviazione Premium, gli snapshot creati per i punti di ripristino istantaneo vengono inclusi nel conteggio relativo al limite di 10 TB di spazio allocato.

## <a name="upgrade"></a>Aggiornamento
### <a name="the-azure-portal"></a>Portale di Azure
Se si usa il portale di Azure, viene visualizzata una notifica nel dashboard dell'insieme di credenziali. Questa notifica si riferisce al supporto di dischi di grandi dimensioni e ai miglioramenti della velocità di backup e ripristino.

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: notifica relativa al supporto](./media/backup-azure-vms/instant-rp-banner.png) 

Per aprire una schermata per l'aggiornamento al nuovo stack, selezionare il banner. 

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: aggiornamento](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Eseguire i cmdlet seguenti da un terminale di PowerShell con privilegi elevati:
1.  Accedere all'account Azure: 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Selezionare la sottoscrizione che si vuole registrare per l'anteprima:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Registrare la sottoscrizione per l'anteprima privata:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Verificare se l'aggiornamento è stato completato
Da un terminale di PowerShell con privilegi elevati eseguire il cmdlet seguente:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Se viene visualizzato "Registered" (Registrato), la sottoscrizione è aggiornata allo stack di backup di macchine virtuali del modello di distribuzione Resource Manager.

## <a name="frequently-asked-questions"></a>Domande frequenti

Le domande e le risposte seguenti sono state raccolte da forum e da domande dei clienti.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>L'aggiornamento alla versione 2 influirà sui backup correnti?

Se si esegue l'aggiornamento alla versione 2, i backup correnti non sono interessati e non è necessario riconfigurare l'ambiente. Eseguire l'aggiornamento e l'ambiente di backup continuerà a funzionare nel modo previsto.

### <a name="what-does-it-cost-to-upgrade-to-azure-backup-stack-v2"></a>Qual è il costo per eseguire l'aggiornamento alla versione 2 dello stack di Backup di Azure?

Non sono previsti costi per l'aggiornamento alla versione 2 dello stack di Backup di Azure. Gli snapshot vengono archiviati in locale per velocizzare la creazione di punti di ripristino e le operazioni di ripristino. Vengono visualizzati pertanto i costi di archiviazione che corrispondono agli snapshot creati durante il periodo di sette giorni.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>L'aggiornamento alla versione 2 dello stack comporta l'aumento del limite di snapshot degli account di archiviazione Premium da 10 TB?

No.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Negli account di archiviazione Premium gli snapshot creati per un punto di ripristino istantaneo occupano il limite di snapshot di 10 TB?

Sì, per gli account di archiviazione Premium gli snapshot creati per il punto di ripristino istantaneo occupano i 10 TB di spazio allocato.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>Come funziona lo snapshot durante il periodo di sette giorni? 

Ogni giorno viene creato un nuovo snapshot. Sono presenti sette singoli snapshot. Il servizio **non** esegue una copia il primo giorno e aggiunge le modifiche per i sei giorni successivi.

### <a name="what-happens-if-the-default-resource-group-is-deleted-accidentally"></a>Cosa accade se il gruppo di risorse predefinito viene eliminato per errore?

Se il gruppo di risorse viene eliminato, i punti di ripristino istantaneo per tutte le macchine virtuali protette nell'area specifica vengono persi. Quando viene eseguito il backup successivo, il gruppo di risorse viene creato nuovamente e i backup continuano nel modo previsto. Questa funzionalità non è esclusiva per i punti di ripristino istantaneo.

### <a name="can-i-delete-the-default-resource-group-created-for-instant-recovery-points"></a>È possibile eliminare il gruppo di risorse predefinito creato per i punti di ripristino istantaneo?

Il servizio Backup di Azure crea il gruppo di risorse gestite. Attualmente non è possibile cambiare o modificare il gruppo di risorse. Tenere presente anche che non è opportuno bloccare il gruppo di risorse. Queste indicazioni non sono esclusive solo per lo stack versione 2.
 
### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Lo snapshot versione 2 è incrementale o completo?

Gli snapshot incrementali vengono usati per i dischi non gestiti. Per i dischi gestiti, lo snapshot usato è quello completo.
