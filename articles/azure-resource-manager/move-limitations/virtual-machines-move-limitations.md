---
title: Spostare le macchine virtuali di Azure in una nuova sottoscrizione o in un gruppo di risorse
description: Usare Azure Resource Manager per spostare le macchine virtuali in un nuovo gruppo di risorse o una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b03e9618e621216f74cb02828183df7ee6b502ea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150927"
---
# <a name="move-guidance-for-virtual-machines"></a>Spostare le linee guida per le macchine virtuali

Questo articolo descrive gli scenari attualmente non supportati e i passaggi per spostare le macchine virtuali con il backup.

## <a name="scenarios-not-supported"></a>Scenari non supportati

Non sono ancora supportati gli scenari seguenti:

* Non è possibile spostare Managed Disks in zone di disponibilità in una sottoscrizione diversa.
* Non è possibile spostare i set di scalabilità di macchine virtuali con SKU standard Load Balancer o IP pubblico dello SKU standard.
* Non è possibile spostare da un gruppo di risorse o una sottoscrizione a un'altra macchine virtuali create a partire da risorse Marketplace con piani assegnati. Effettuare il deprovisioning della macchina virtuale nella sottoscrizione corrente e ridistribuirla nella nuova sottoscrizione.
* Non è possibile spostare le macchine virtuali in una rete virtuale esistente in una nuova sottoscrizione quando non si spostano tutte le risorse nella rete virtuale.
* Le macchine virtuali con priorità bassa e i set di scalabilità di macchine virtuali con priorità bassa non possono essere spostati tra gruppi di risorse o sottoscrizioni.
* Non è possibile spostare le macchine virtuali in un set di disponibilità singolarmente.

## <a name="virtual-machines-with-azure-backup"></a>Macchine virtuali con backup di Azure

Per spostare le macchine virtuali configurate con Backup di Azure, usare la soluzione alternativa seguente:

* Individuare la località della macchina virtuale.
* Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<location of your VM>_1`, ad esempio AzureBackupRG_westus2_1
* Nel portale di Azure selezionare quindi l'opzione "Mostra tipi nascosti"
* In PowerShell usare il cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Nella CLI usare il `az resource list -g AzureBackupRG_<location of your VM>_1`
* Trovare la risorsa di tipo `Microsoft.Compute/restorePointCollections` con modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########`
* Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
* Una volta completata l'eliminazione, è possibile spostare l'insieme di credenziali e la macchina virtuale nella sottoscrizione di destinazione. Dopo lo spostamento è possibile continuare a eseguire i backup senza alcuna perdita di dati.
* Per informazioni sullo spostamento degli insiemi di credenziali di Servizi di ripristino per il backup, vedere [Limitazioni di Servizi di ripristino](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../resource-group-move-resources.md).
