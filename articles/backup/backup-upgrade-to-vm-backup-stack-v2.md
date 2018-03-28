---
title: Aggiornamento allo stack di backup di macchine virtuali V2 | Microsoft Docs
description: Processo di aggiornamento e domande frequenti per lo stack di backup di macchine virtuali V2
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: b7e9f45c61d2af1940be50a368b87cd35c85b1dd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>Eseguire l'aggiornamento allo stack di backup di macchine virtuali V2
L'aggiornamento allo stack di backup di macchine virtuali (VM) V2 offre i miglioramenti delle funzionalità seguenti:
* Possibilità di visualizzare lo snapshot acquisito durante il processo di backup come disponibile per il ripristino senza attendere il completamento del trasferimento dei dati.
Ridurrà l'attesa per la copia dello snapshot nell'insieme di credenziali prima di attivare il ripristino. Eliminerà anche la necessità di spazio di archiviazione aggiuntivo per il backup di VM Premium, tranne che per il primo backup.  

* Riduzione dei tempi di backup e ripristino conservando gli snapshot in locale per 7 giorni. 

* Supporto per dischi con dimensioni fino a 4 TB.  

* Possibilità di usare gli account di archiviazione originali (anche quando i dischi della VM sono distribuiti negli account di archiviazione) quando si esegue un ripristino di una VM non gestita. In questo modo le operazioni di ripristino saranno più veloci per un'ampia gamma di configurazioni di macchina virtuale. 
    > [!NOTE] 
    > Non è lo stesso che eseguire l'override della macchina virtuale originale. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>Modifiche nel nuovo stack
Il processo di backup è attualmente costituito da due fasi:
1.  Acquisizione di uno snapshot della VM. 
2.  Trasferimento dello snapshot della macchina virtuale all'insieme di credenziali di Backup di Azure. 

Un punto di ripristino si considera creato solo dopo che sono state eseguite le fasi 1 e 2. Nell'ambito del nuovo stack viene creato un punto di ripristino non appena lo snapshot viene completato. È anche possibile eseguire il ripristino da questo punto usando lo stesso flusso di ripristino. È possibile identificare questo punto di ripristino nel portale di Azure usando lo "snapshot" come tipo di punto di ripristino. Dopo che lo snapshot è stato trasferito all'insieme di credenziali, il tipo di punto di ripristino diventa "Snapshot e insieme di credenziali". 

![Processo di backup nello stack di backup di macchine virtuali V2](./media/backup-azure-vms/instant-rp-flow.jpg) 

Per impostazione predefinita, gli snapshot verranno conservati per sette giorni. In questo modo è possibile completare più rapidamente il ripristino dagli snapshot riducendo il tempo necessario per copiare i dati dall'insieme di credenziali all'account di archiviazione del cliente. 

## <a name="considerations-before-upgrade"></a>Considerazioni prima dell'aggiornamento
* Poiché questo è un aggiornamento unidirezionale dello stack di backup di macchine virtuali, tutti i backup futuri verranno inseriti in questo flusso. Poiché **viene abilitato a livello di sottoscrizione, tutte le VM verranno inserite in questo flusso**. Tutte le aggiunte di nuove funzionalità si baseranno sullo stesso stack. Nelle versioni future sarà possibile avere il controllo a livello di criteri. 
* Per le VM con dischi Premium, durante il primo backup, verificare che nell'account di archiviazione sia disponibile uno spazio di archiviazione equivalente alle dimensioni della VM fino al termine del primo backup. 
* Poiché gli snapshot vengono archiviati in locale per supportare la creazione del punto di ripristino e anche per velocizzare il ripristino, si osserveranno costi di archiviazione corrispondenti agli snapshot durante il periodo di sette giorni.
Per i dischi gestiti, *non sono previsti aumenti di prezzo* perché gli elementi restorePointCollection sono gratuiti. 
* Se si esegue un ripristino da un punto di ripristino di uno snapshot per una VM Premium, si noterà che viene usata una posizione di archiviazione temporanea mentre la VM viene creata durante il ripristino. 

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento
### <a name="the-azure-portal"></a>Portale di Azure
Se si usa il portale di Azure, nel dashboard dell'insieme di credenziali verrà visualizzata una notifica relativa al supporto di dischi di grandi dimensioni e ai miglioramenti della velocità di backup e ripristino.

![Processo di backup nello stack di backup di macchine virtuali V2](./media/backup-azure-vms/instant-rp-banner.png) 

Per aprire una schermata per l'aggiornamento al nuovo stack, selezionare il banner. 

![Processo di backup nello stack di backup di macchine virtuali V2](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Eseguire i cmdlet seguenti da un terminale di PowerShell con privilegi elevati:
1.  Accedere all'account Azure. 

```
PS C:> Login-AzureRmAccount
```

2.  Selezionare la sottoscrizione che si vuole registrare per l'anteprima:

```
PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
```

3.  Registrare la sottoscrizione per l'anteprima privata:

```
PS C:>  Register-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

## <a name="verify-whether-the-upgrade-is-complete"></a>Verificare se l'aggiornamento è stato completato
Da un terminale di PowerShell con privilegi elevati eseguire il cmdlet seguente:

```
Get-AzureRmProviderFeature -FeatureName “InstantBackupandRecovery” –ProviderNamespace Microsoft.RecoveryServices
```

Se viene visualizzato Registered, la sottoscrizione è aggiornata allo stack di backup di macchine virtuali V2. 



