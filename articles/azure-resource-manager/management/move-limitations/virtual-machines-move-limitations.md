---
title: Spostare le macchine virtuali di Azure in una nuova sottoscrizione o in un gruppo di risorse
description: Usare Azure Resource Manager per spostare le macchine virtuali in un nuovo gruppo di risorse o una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 3878113f6874c40953bec87518a89519bdc6cb1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230960"
---
# <a name="move-guidance-for-virtual-machines"></a>Spostare le linee guida per le macchine virtuali

Questo articolo descrive gli scenari attualmente non supportati e i passaggi per spostare le macchine virtuali con il backup.

## <a name="scenarios-not-supported"></a>Scenari non supportati

Non sono ancora supportati gli scenari seguenti:

* Non è possibile spostare i set di scalabilità di macchine virtuali con SKU standard Load Balancer o IP pubblico dello SKU standard.
* Le macchine virtuali create dalle risorse del Marketplace con i piani collegati non possono essere spostate tra le sottoscrizioni. Effettuare il deprovisioning della macchina virtuale nella sottoscrizione corrente e ridistribuirla nella nuova sottoscrizione.
* Non è possibile spostare le macchine virtuali in una rete virtuale esistente in una nuova sottoscrizione quando non si spostano tutte le risorse nella rete virtuale.
* Le macchine virtuali con priorità bassa e i set di scalabilità di macchine virtuali con priorità bassa non possono essere spostati tra gruppi di risorse o sottoscrizioni.
* Non è possibile spostare le macchine virtuali in un set di disponibilità singolarmente.

## <a name="azure-disk-encryption"></a>Crittografia dischi di Azure

Non è possibile spostare una macchina virtuale integrata con un insieme di credenziali delle chiavi per implementare [crittografia dischi di Azure per macchine virtuali Linux](../../../virtual-machines/linux/disk-encryption-overview.md) o [crittografia dischi di Azure per macchine virtuali Windows](../../../virtual-machines/windows/disk-encryption-overview.md). Per spostare la macchina virtuale, è necessario disabilitare la crittografia.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Macchine virtuali con backup di Azure

Per spostare le macchine virtuali configurate con backup di Azure, è necessario eliminare i punti di ripristino dall'insieme di credenziali.

Se l' [eliminazione](../../../backup/backup-azure-security-feature-cloud.md) temporanea è abilitata per la macchina virtuale, non è possibile spostare la macchina virtuale mentre vengono conservati i punti di ripristino. [Disabilitare l'eliminazione](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) temporanea o attendere 14 giorni dopo l'eliminazione dei punti di ripristino.

### <a name="portal"></a>Portale

1. Arrestare temporaneamente il backup e conservare i dati di backup.
2. Per spostare le macchine virtuali configurate con backup di Azure, seguire questa procedura:

   1. Trovare il percorso della macchina virtuale.
   2. Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<VM location>_1` . Ad esempio, il nome è nel formato *AzureBackupRG_westus2_1*.
   3. Nella portale di Azure selezionare **Mostra tipi nascosti**.
   4. Trovare la risorsa con il tipo **Microsoft. Compute/restorePointCollections** con il modello di denominazione `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
   6. Al termine dell'operazione di eliminazione, è possibile spostare la macchina virtuale.

3. Spostare la macchina virtuale nel gruppo di risorse di destinazione.
4. Riprendere il backup.

### <a name="powershell"></a>PowerShell

1. Trovare il percorso della macchina virtuale.

1. Trovare un gruppo di risorse con il modello di denominazione- `AzureBackupRG_<VM location>_1` . Ad esempio, il nome potrebbe essere `AzureBackupRG_westus2_1` .

1. Usare il comando seguente per ottenere la raccolta di punti di ripristino.

   ```azurepowershell
   $RestorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

1. Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.

   ```azurepowershell
   Remove-AzResource -ResourceId $RestorePointCollection.ResourceId -Force
   ```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. Trovare il percorso della macchina virtuale.

1. Trovare un gruppo di risorse con il modello di denominazione- `AzureBackupRG_<VM location>_1` . Ad esempio, il nome potrebbe essere `AzureBackupRG_westus2_1` .

1. Usare il comando seguente per ottenere la raccolta di punti di ripristino.

   ```azurecli
   az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections
   ```

1. Trovare l'ID risorsa per la risorsa con il modello di denominazione `AzureBackup_<VM name>_###########`

1. Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.

   ```azurecli
   az resource delete --ids /subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.Compute/restorePointCollections/<name>
   ```

## <a name="next-steps"></a>Passaggi successivi

* Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../move-resource-group-and-subscription.md).

* Per informazioni sullo spostamento degli insiemi di credenziali di Servizi di ripristino per il backup, vedere [Limitazioni di Servizi di ripristino](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
