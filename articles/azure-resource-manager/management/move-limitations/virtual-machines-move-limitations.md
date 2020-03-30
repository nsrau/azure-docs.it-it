---
title: Spostare le macchine virtuali di Azure in una nuova sottoscrizione o gruppo di risorseMove Azure VMs to new subscription or resource group
description: Usare Azure Resource Manager per spostare le macchine virtuali in un nuovo gruppo di risorse o in una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 97c49f90dab2aafd89de322e57ad44ff1fc9d367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479761"
---
# <a name="move-guidance-for-virtual-machines"></a>Spostare le indicazioni per le macchine virtualiMove guidance for virtual machines

In questo articolo vengono descritti gli scenari attualmente non supportati e i passaggi per spostare le macchine virtuali con il backup.

## <a name="scenarios-not-supported"></a>Scenari non supportati

Non sono ancora supportati gli scenari seguenti:

* I dischi gestiti nelle zone di disponibilità non possono essere spostati in una sottoscrizione diversa.
* Non è possibile spostare i set di scalabilità di macchine virtuali con bilanciamento del carico SKU Standard o IP pubblico sKU standard.
* Non è possibile spostare da un gruppo di risorse o una sottoscrizione a un'altra macchine virtuali create a partire da risorse Marketplace con piani assegnati. Annullare il provisioning della macchina virtuale nella sottoscrizione corrente e distribuire nuovamente nella nuova sottoscrizione.
* Le macchine virtuali in una rete virtuale esistente non possono essere spostate in una nuova sottoscrizione quando non si spostano tutte le risorse nella rete virtuale.
* Le macchine virtuali con priorità bassa e i set di scalabilità di macchine virtuali con priorità bassa non possono essere spostati tra gruppi di risorse o sottoscrizioni.
* Le macchine virtuali in un set di disponibilità non possono essere spostate singolarmente.

## <a name="virtual-machines-with-azure-backup"></a>Macchine virtuali con Backup di AzureVirtual machines with Azure Backup

Per spostare le macchine virtuali configurate con Backup di Azure, usare la soluzione alternativa seguente:

* Individuare la località della macchina virtuale.
* Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<location of your VM>_1`, ad esempio AzureBackupRG_westus2_1
* Nel portale di Azure selezionare quindi l'opzione "Mostra tipi nascosti"
* In PowerShell usare il cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Nella CLI usare il `az resource list -g AzureBackupRG_<location of your VM>_1`
* Trovare la risorsa di tipo `Microsoft.Compute/restorePointCollections` con modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########`
* Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
* Al termine dell'eliminazione, è possibile spostare l'insieme di credenziali e la macchina virtuale nella sottoscrizione di destinazione. Dopo lo spostamento è possibile continuare a eseguire i backup senza alcuna perdita di dati.
* Per informazioni sullo spostamento degli insiemi di credenziali di Servizi di ripristino per il backup, vedere [Limitazioni di Servizi di ripristino](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Passaggi successivi

Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../move-resource-group-and-subscription.md).
