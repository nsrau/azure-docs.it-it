---
title: Eseguire l'aggiornamento al modello di distribuzione Azure Resource Manager per lo stack di backup di macchine virtuali di Azure
description: Processo di aggiornamento e domande frequenti per lo stack di backup di macchine virtuali e il modello di distribuzione Resource Manager
services: backup, virtual-machines
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: e822e0c354fd671ee2802506e0e268d4078b395e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606903"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Eseguire l'aggiornamento al modello di distribuzione Azure Resource Manager per lo stack di backup di macchine virtuali di Azure
Il modello di distribuzione Resource Manager per l'aggiornamento allo stack di backup di macchine virtuali offre i seguenti miglioramenti delle funzionalità:
* Possibilità di visualizzare lo snapshot acquisito durante il processo di backup che è disponibile per il ripristino senza attendere il completamento del trasferimento dei dati. Riduce il tempo di attesa per la copia degli snapshot nell'insieme di credenziali prima di attivare il ripristino. Elimina anche la necessità di spazio di archiviazione aggiuntivo per il backup di macchine virtuali Premium, tranne che per il primo backup.  

* Riduzione dei tempi di backup e ripristino conservando gli snapshot in locale per sette giorni.

* Supporto per dischi con dimensioni fino a 4 TB.

* Possibilità di usare gli account di archiviazione originali quando si esegue il ripristino di una macchina virtuale non gestita. Questa possibilità vale anche quando i dischi della macchina virtuale sono distribuiti negli account di archiviazione. In questo modo le operazioni di ripristino sono più veloci per un'ampia gamma di configurazioni di macchina virtuale.
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
* Poiché l'aggiornamento dello stack di backup di macchine virtuali è unidirezionale, tutti i backup vengono inseriti in questo flusso. Dato che viene abilitato a livello di sottoscrizione, tutte le macchine virtuali vengono inserite in questo flusso. Tutte le aggiunte di nuove funzionalità si basano sullo stesso stack. Nelle versioni future sarà possibile avere il controllo a livello di criteri.

* Gli snapshot vengono archiviati in locale per supportare la creazione del punto di ripristino e anche per velocizzare il ripristino. Pertanto si osserveranno costi di archiviazione corrispondenti agli snapshot durante il periodo di sette giorni.

* Gli snapshot incrementali vengono archiviati come BLOB di pagine. A tutti i clienti che usano dischi non gestiti verranno addebitati gli snapshot per sette giorni archiviati nell'account di archiviazione locale del cliente. In base al modello tariffario corrente, non sono previsti costi per i clienti dei dischi gestiti.

* Se si esegue un ripristino da un punto di ripristino di uno snapshot per una macchina virtuale Premium, si noterà che viene usata una posizione di archiviazione temporanea mentre la macchina virtuale viene creata durante il ripristino.

* Per gli account di archiviazione Premium, gli snapshot usati per il ripristino istantaneo occupano 10 TB di spazio allocato.

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
