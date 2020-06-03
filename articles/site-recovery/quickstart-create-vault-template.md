---
title: Guida di avvio rapido per creare un insieme di credenziali di Servizi di ripristino di Azure con un modello di Azure Resource Manager.
description: Questa guida di avvio rapido illustra come creare un insieme di credenziali di Servizi di ripristino con un modello di Azure Resource Manager.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
author: davidsmatlak
ms.author: v-dasmat
ms.openlocfilehash: cf85939a1dbaf8d3e8a90a3acf10bda9faac83bc
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84217293"
---
# <a name="quickstart-create-a-recovery-services-vault-using-a-resource-manager-template"></a>Avvio rapido: Creare un insieme di credenziali di Servizi di ripristino con un modello di Resource Manager

Questa guida di avvio rapido descrive come configurare un insieme di credenziali di Servizi di ripristino con un modello di Azure Resource Manager. Il servizio [Azure Site Recovery](site-recovery-overview.md) contribuisce a realizzare la strategia di continuità aziendale e ripristino di emergenza (BCDR) mantenendo online le applicazioni aziendali durante le interruzioni pianificate e non pianificate. Site Recovery gestisce il ripristino di emergenza di computer locali e macchine virtuali di Azure, incluse le operazioni di replica, failover e ripristino.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se non si ha una sottoscrizione di Azure attiva, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

No.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json" range="1-66" highlight="41-65":::

Nel modello sono definite due risorse di Azure:

- [Microsoft.RecoveryServices vaults](/azure/templates/microsoft.recoveryservices/vaults): crea l'insieme di credenziali.
- [Microsoft.RecoveryServices/vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): configura le impostazioni di ridondanza del backup dell'insieme di credenziali.

Il modello include i parametri facoltativi per la configurazione di backup dell'insieme di credenziali. Le impostazioni di ridondanza dell'archiviazione sono l'archiviazione con ridondanza locale o l'archiviazione con ridondanza geografica. Per altre informazioni, vedere [Impostare le opzioni di ridondanza](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Per altri modelli di Servizi di ripristino di Azure, vedere i [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Distribuire il modello

Per distribuire il modello, è necessario specificare i valori per **Sottoscrizione**, **Gruppo di risorse** e **Nome dell'insieme di credenziali**.

1. Selezionare l'immagine **Distribuisci in Azure** per accedere ad Azure e aprire il modello.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Selezionare o immettere i valori seguenti:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Modello per creare un insieme di credenziali di Servizi di ripristino.":::

   - **Sottoscrizione**: selezionare la sottoscrizione di Azure.
   - **Gruppo di risorse**: selezionare un gruppo esistente o selezionare **Crea nuovo** per aggiungere un gruppo.
   - **Località**: il valore predefinito è la località del gruppo di risorse e diventa non disponibile dopo aver selezionato un gruppo di risorse.
   - **Vault Name**: Specificare un nome per l'insieme di credenziali.
   - **Change Storage Type** (Cambia tipo di archiviazione): Il valore predefinito è **false**. Selezionare **true** solo se è necessario modificare il tipo di archiviazione dell'insieme di credenziali.
   - **Vault Storage Type** (Tipo di archiviazione dell'insieme di credenziali): Il valore predefinito è **GloballyRedundant**. Se il tipo di archiviazione è stato impostato su **true**, selezionare **LocallyRedundant**.
   - **Località**: la funzione `[resourceGroup().location]` assume per impostazione predefinita la località del gruppo di risorse. Per modificare la località, immettere un valore, ad esempio **westus**.
   - Selezionare la casella di controllo **Accetto le condizioni riportate sopra**.

1. Per avviare la distribuzione dell'insieme di credenziali, fare clic sul pulsante **Acquista**. Una volta completata la distribuzione, viene visualizzata una notifica.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="La distribuzione dell'insieme di credenziali è riuscita.":::

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Per verificare che l'insieme di credenziali sia stato creato, usare l'interfaccia della riga di comando di Azure o Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

L'output seguente è un estratto delle informazioni dell'insieme di credenziali:

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di usare le nuove risorse, non è richiesto alcun intervento. In caso contrario, è possibile rimuovere il gruppo di risorse e l'insieme di credenziali creato in questa guida di avvio rapido. Per eliminare il gruppo di risorse e le risorse al suo interno, usare l'interfaccia della riga di comando di Azure oppure Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un insieme di credenziali di Servizi di ripristino. Per altre informazioni sul ripristino di emergenza, continuare con l'articolo avvio rapido successivo.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza](azure-to-azure-quickstart.md)
