---
title: Spostare le macchine virtuali di Azure in una nuova sottoscrizione o in un gruppo di risorse
description: Usare Azure Resource Manager per spostare le macchine virtuali in un nuovo gruppo di risorse o una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: e5bd004b6619db9c9882b8e9e6005309317b8ca5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744628"
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

Per spostare le macchine virtuali configurate con backup di Azure, è necessario eliminare i punti di ripristino dall'insieme di credenziali.

Se l' [eliminazione](../../../backup/backup-azure-security-feature-cloud.md) temporanea è abilitata per la macchina virtuale, non è possibile spostare la macchina virtuale mentre vengono conservati i punti di ripristino. [Disabilitare l'eliminazione](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) temporanea o attendere 14 giorni dopo l'eliminazione dei punti di ripristino.

### <a name="portal"></a>Portale

1. Arrestare temporaneamente il backup e conservare i dati di backup.
2. Per spostare le macchine virtuali configurate con backup di Azure, seguire questa procedura:

   1. Trovare il percorso della macchina virtuale.
   2. Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<location of your VM>_1` . Ad esempio, *AzureBackupRG_westus2_1*
   3. Nella portale di Azure selezionare **Mostra tipi nascosti**.
   4. Trovare la risorsa con il tipo **Microsoft. Compute/restorePointCollections** con il modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
   6. Al termine dell'operazione di eliminazione, è possibile spostare la macchina virtuale.

3. Spostare la macchina virtuale nel gruppo di risorse di destinazione.
4. Riprendere il backup.

### <a name="powershell"></a>PowerShell

* Individuare la località della macchina virtuale.
* Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<location of your VM>_1`, ad esempio AzureBackupRG_westus2_1
* In PowerShell usare il cmdlet `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`
* Trovare la risorsa di tipo `Microsoft.Compute/restorePointCollections` con modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########`
* Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

* Individuare la località della macchina virtuale.
* Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<location of your VM>_1`, ad esempio AzureBackupRG_westus2_1
* Nella CLI usare il `az resource list -g AzureBackupRG_<location of your VM>_1`
* Trovare la risorsa di tipo `Microsoft.Compute/restorePointCollections` con modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########`
* Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.

## <a name="next-steps"></a>Passaggi successivi

* Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../move-resource-group-and-subscription.md).

* Per informazioni sullo spostamento degli insiemi di credenziali di Servizi di ripristino per il backup, vedere [Limitazioni di Servizi di ripristino](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
