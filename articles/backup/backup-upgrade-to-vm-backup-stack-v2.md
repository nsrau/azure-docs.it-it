---
title: Eseguire l'aggiornamento alla versione 2 dello stack di backup di macchine virtuali di Azure
description: Processo di aggiornamento e domande frequenti per lo stack di backup di macchine virtuali e il modello di distribuzione Resource Manager
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 10/3/2018
ms.author: trinadhk
ms.openlocfilehash: 8882970471b554f6d05a9cf3028e7be572292ef6
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582701"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Eseguire l'aggiornamento alla versione 2 dello stack di macchine virtuali di Azure

Il modello di distribuzione Resource Manager per l'aggiornamento allo stack di backup di macchine virtuali offre i seguenti miglioramenti delle funzionalità:

* Possibilità di visualizzare lo snapshot acquisito durante il processo di backup che è disponibile per il ripristino senza attendere il completamento del trasferimento dei dati. Riduce il tempo di attesa per la copia degli snapshot nell'insieme di credenziali prima di attivare il ripristino. Elimina anche la necessità di spazio di archiviazione aggiuntivo per il backup di macchine virtuali Premium, tranne che per il primo backup.  

* Riduzione dei tempi di backup e ripristino conservando gli snapshot in locale per sette giorni.

* Supporto per dischi con dimensioni fino a 4 TB.

* Possibilità di usare gli account di archiviazione originali quando si esegue il ripristino di una macchina virtuale non gestita. Questa possibilità vale anche quando i dischi della macchina virtuale sono distribuiti negli account di archiviazione. Le operazioni di ripristino per un'ampia gamma di configurazioni di macchine virtuali vengono velocizzate.
    > [!NOTE]
    > Questa possibilità non corrisponde alla sostituzione dei dischi della macchina virtuale con i dati del punto di ripristino.


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

* Gli snapshot incrementali vengono archiviati come BLOB di pagine. A tutti i clienti che usano dischi non gestiti verranno addebitati gli snapshot per sette giorni archiviati nell'account di archiviazione locale del cliente. Poiché le raccolte di punti di ripristino usate dai backup delle macchine virtuali gestite usano gli snapshot dei BLOB a livello di archiviazione sottostante, per i dischi gestiti si noteranno i costi corrispondenti ai [prezzi degli snapshot dei BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-how-snapshots-accrue-charges), i quali sono incrementali.

* Se si ripristina una macchina virtuale Premium da un punto di ripristino dello snapshot, viene usato un percorso di archiviazione temporaneo durante la creazione della macchina virtuale stessa.

* Per gli account di archiviazione Premium, gli snapshot creati per i punti di ripristino istantaneo vengono inclusi nel conteggio relativo al limite di 10 TB di spazio allocato.

> [!NOTE]
> Passare allo stack V2 del backup di VM di Azure per ottenere il supporto di Backup di Azure per [dischi gestiti SDD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

## <a name="upgrade"></a>Aggiornamento
### <a name="the-azure-portal"></a>Portale di Azure
Se si usa il portale di Azure, viene visualizzata una notifica nel dashboard dell'insieme di credenziali. Questa notifica si riferisce al supporto di dischi di grandi dimensioni e ai miglioramenti della velocità di backup e ripristino. In alternativa, è possibile passare alla pagina delle proprietà dell'insieme di credenziali per ottenere l'opzione di aggiornamento.

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: notifica relativa al supporto](./media/backup-azure-vms/instant-rp-banner.png)

Per aprire una schermata per l'aggiornamento al nuovo stack, selezionare il banner.

![Processo di backup in uno stack di backup di macchine virtuali, modello di distribuzione Resource Manager: aggiornamento](./media/backup-azure-vms/instant-rp.png)

### <a name="powershell"></a>PowerShell
Eseguire i cmdlet seguenti da un terminale di PowerShell con privilegi elevati:
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
### <a name="cli"></a>CLI
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

## <a name="verify-that-the-upgrade-is-finished"></a>Verificare se l'aggiornamento è stato completato
### <a name="powershell"></a>PowerShell
Da un terminale di PowerShell con privilegi elevati eseguire il cmdlet seguente:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>CLI
Da una shell eseguire il comando seguente:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Se viene visualizzato "Registered", la sottoscrizione è aggiornata alla versione 2 dello stack di backup.

## <a name="frequently-asked-questions"></a>Domande frequenti

Le domande e le risposte seguenti sono state raccolte da forum e da domande dei clienti.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>L'aggiornamento alla versione 2 influirà sui backup correnti?
Se si esegue l'aggiornamento alla versione 2, i backup correnti non sono interessati e non è necessario riconfigurare l'ambiente. Eseguire l'aggiornamento e l'ambiente di backup continuerà a funzionare nel modo previsto.

### <a name="what-does-it-cost-to-upgrade-to-azure-vm-backup-stack-v2"></a>Qual è il costo per eseguire l'aggiornamento alla versione 2 dello stack di Backup delle macchine virtuali di Azure?
Non sono previsti costi per l'aggiornamento dello stack a v2. Gli snapshot vengono archiviati in locale per velocizzare la creazione di punti di ripristino e le operazioni di ripristino. Vengono visualizzati pertanto i costi di archiviazione che corrispondono agli snapshot creati durante il periodo di sette giorni.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>L'aggiornamento alla versione 2 dello stack comporta l'aumento del limite di snapshot degli account di archiviazione Premium da 10 TB?
No, limite totale di snapshot per ogni account archiviazione rimane comunque di 10 TB.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Negli account di archiviazione Premium gli snapshot creati per un punto di ripristino istantaneo occupano il limite di snapshot di 10 TB?
Sì, per gli account di archiviazione Premium gli snapshot creati per il punto di ripristino istantaneo occupano i 10 TB di spazio allocato.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>Come funziona lo snapshot durante il periodo di sette giorni?
Ogni giorno viene creato un nuovo snapshot. Sono presenti sette singoli snapshot. Il servizio **non** esegue una copia il primo giorno e aggiunge le modifiche per i sei giorni successivi.

### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Lo snapshot versione 2 è incrementale o completo?
Gli snapshot incrementali vengono usati per i dischi non gestiti. Per i dischi gestiti, la raccolta del punto di ripristino creata da Backup di Azure usa gli snapshot dei BLOB e di conseguenza questi sono incrementali.

### <a name="how-to-get-standard-ssd-managed-disk-support-for-a-virtual-machine"></a>Come ottenere il supporto Managed Disks - SSD Standard per una macchina virtuale?
Passare allo stack V2 del backup di VM di Azure per ottenere il supporto di Backup di Azure per [dischi gestiti SDD Standard](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).
