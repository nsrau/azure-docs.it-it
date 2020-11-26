---
title: Spostare le macchine virtuali di Azure in una nuova sottoscrizione o in un gruppo di risorse
description: Usare Azure Resource Manager per spostare le macchine virtuali in un nuovo gruppo di risorse o una nuova sottoscrizione.
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: ace1fb6bf3944df539ec8f7301357e67d2b315a9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184077"
---
# <a name="move-guidance-for-virtual-machines"></a>Spostare le linee guida per le macchine virtuali

Questo articolo descrive gli scenari attualmente non supportati e i passaggi per spostare le macchine virtuali con il backup.

## <a name="scenarios-not-supported"></a>Scenari non supportati

Non sono ancora supportati gli scenari seguenti:

* Non è possibile spostare i set di scalabilità di macchine virtuali con SKU standard Load Balancer o IP pubblico dello SKU standard.
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

## <a name="virtual-machines-with-marketplace-plans"></a>Macchine virtuali con piani di Marketplace

Le macchine virtuali create dalle risorse del Marketplace con i piani collegati non possono essere spostate tra le sottoscrizioni. Per ovviare a questa limitazione, è possibile effettuare il deprovisioning della macchina virtuale nella sottoscrizione corrente e distribuirla nuovamente nella nuova sottoscrizione. La procedura seguente consente di ricreare la macchina virtuale nella nuova sottoscrizione. Tuttavia, potrebbero non funzionare per tutti gli scenari. Se il piano non è più disponibile nel Marketplace, questa procedura non funzionerà.

1. Copiare le informazioni relative al piano.

1. Clonare il disco del sistema operativo nella sottoscrizione di destinazione oppure spostare il disco originale dopo l'eliminazione della macchina virtuale dalla sottoscrizione di origine.

1. Nella sottoscrizione di destinazione accettare le condizioni del Marketplace per il piano. È possibile accettare i termini eseguendo il comando PowerShell seguente:

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   In alternativa, è possibile creare una nuova istanza di una macchina virtuale con il piano tramite il portale. È possibile eliminare la macchina virtuale dopo aver accettato i termini nella nuova sottoscrizione.

1. Nella sottoscrizione di destinazione ricreare la macchina virtuale dal disco del sistema operativo clonato usando PowerShell, l'interfaccia della riga di comando o un modello di Azure Resource Manager. Includere il piano del Marketplace collegato al disco. Le informazioni sul piano devono corrispondere al piano acquistato nella nuova sottoscrizione.

## <a name="virtual-machines-with-azure-backup"></a>Macchine virtuali con backup di Azure

Per spostare le macchine virtuali configurate con backup di Azure, è necessario eliminare i punti di ripristino dall'insieme di credenziali.

Se l' [eliminazione](../../../backup/backup-azure-security-feature-cloud.md) temporanea è abilitata per la macchina virtuale, non è possibile spostare la macchina virtuale mentre vengono conservati i punti di ripristino. [Disabilitare l'eliminazione](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) temporanea o attendere 14 giorni dopo l'eliminazione dei punti di ripristino.

### <a name="portal"></a>Portale

1. Arrestare temporaneamente il backup e salvare i dati di backup.
2. Per spostare le macchine virtuali configurate con backup di Azure, seguire questa procedura:

   1. Trovare il percorso della macchina virtuale.
   2. Trovare un gruppo di risorse con il modello di denominazione seguente: `AzureBackupRG_<VM location>_1` . Ad esempio, il nome è nel formato *AzureBackupRG_westus2_1*.
   3. Nella portale di Azure selezionare **Mostra tipi nascosti**.
   4. Trovare la risorsa con il tipo **Microsoft. Compute/restorePointCollections** con il modello di denominazione `AzureBackup_<VM name>_###########` .
   5. Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.
   6. Al termine dell'operazione di eliminazione, è possibile spostare la macchina virtuale.

3. Spostare la macchina virtuale nel gruppo di risorse di destinazione.
4. Riprendere il backup.

### <a name="powershell"></a>PowerShell

1. Trovare il percorso della macchina virtuale.

1. Trovare un gruppo di risorse con il modello di denominazione- `AzureBackupRG_<VM location>_1` . Ad esempio, il nome potrebbe essere `AzureBackupRG_westus2_1` .

1. Se si sta muovendo una sola macchina virtuale, ottenere la raccolta di punti di ripristino per la macchina virtuale.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Se si stanno migrando tutte le macchine virtuali con backup in questa posizione, ottenere le raccolte di punti di ripristino per tali macchine virtuali.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Eliminare ogni risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. Trovare il percorso della macchina virtuale.

1. Trovare un gruppo di risorse con il modello di denominazione- `AzureBackupRG_<VM location>_1` . Ad esempio, il nome potrebbe essere `AzureBackupRG_westus2_1` .

1. Se si sta muovendo una sola macchina virtuale, ottenere la raccolta di punti di ripristino per la macchina virtuale.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Eliminare la risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Se si stanno migrando tutte le macchine virtuali con backup in questa posizione, ottenere le raccolte di punti di ripristino per tali macchine virtuali.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Eliminare ogni risorsa. Con questa operazione vengono eliminati solo i punti di ripristino istantaneo, non i dati di backup presenti nell'insieme di credenziali.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Passaggi successivi

* Per i comandi sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../move-resource-group-and-subscription.md).

* Per informazioni sullo spostamento degli insiemi di credenziali di Servizi di ripristino per il backup, vedere [Limitazioni di Servizi di ripristino](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
