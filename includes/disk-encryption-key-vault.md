---
title: includere file
description: includere file
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 3fe622d2ff4f6f8aff546452db0f475cfd44eb1b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015376"
---
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

*Se si ha già un gruppo di risorse, è possibile passare a [Creare un insieme di credenziali delle chiavi](#create-a-key-vault).*

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

Creare un gruppo di risorse usando il comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) dell'interfaccia della riga di comando di Azure, il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) di Azure PowerShell oppure il [portale di Azure](https://portal.azure.com).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

*Se si ha già un insieme di credenziali delle chiavi, è possibile passare a [Impostare i criteri di accesso avanzati per l'insieme di credenziali delle chiavi](#set-key-vault-advanced-access-policies).*

Creare un insieme di credenziali delle chiavi usando il comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) dell'interfaccia della riga di comando di Azure, il comando [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) di Azure Powershell, il [portale di Azure](https://portal.azure.com) o un [modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> L'insieme di credenziali delle chiavi e le VM devono essere nella stessa sottoscrizione. Inoltre, per garantire che i segreti di crittografia non superino i confini a livello di area, Crittografia dischi di Azure richiede che l'insieme di credenziali delle chiavi e le macchine virtuali si trovino nella stessa area. Creare e usare un insieme di credenziali delle chiavi nella stessa sottoscrizione e nella stessa area delle VM da crittografare. 

Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Se si crea un insieme di credenziali delle chiavi con l'interfaccia della riga di comando di Azure, aggiungere il flag "--enabled-for-disk-encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Se si crea un insieme di credenziali delle chiavi con Azure PowerShell, aggiungere il flag "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modello di Resource Manager

È possibile creare un insieme di credenziali delle chiavi anche usando il [modello di Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Nel modello di avvio rapido di Azure fare clic su **Deploy to Azure**.
2. Selezionare la sottoscrizione, il gruppo di risorse, la località del gruppo di risorse, il nome dell'insieme di credenziali delle chiavi, l'ID oggetto, le condizioni legali e il contratto, quindi fare clic su **Acquista**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Impostare i criteri di accesso avanzati per l'insieme di credenziali delle chiavi

La piattaforma Azure deve avere accesso alle chiavi di crittografia o i segreti nell'insieme di credenziali delle chiavi per renderli disponibili alla macchina virtuale per l'avvio e la decrittografia dei volumi. 

Se al momento della creazione l'insieme di credenziali delle chiavi non è stato abilitato per la crittografia del disco, la distribuzione o la distribuzione di modelli, come illustrato nel passaggio precedente, è necessario aggiornare i criteri di accesso avanzati.  

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare [az keyvault update](/cli/azure/keyvault#az-keyvault-update) per abilitare la crittografia del disco per l'insieme di credenziali delle chiavi. 

 - **Abilitare l'insieme di credenziali delle chiavi per la crittografia del disco:** Enabled-for-disk-encryption è obbligatorio. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Abilitare l'insieme di credenziali delle chiavi per la distribuzione, se necessario:** in questo modo si consente al provider di risorse Microsoft.Compute di recuperare segreti da questo insieme di credenziali delle chiavi quando vi viene fatto riferimento durante la creazione di risorse, ad esempio quando si crea una macchina virtuale.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Abilitare l'insieme di credenziali delle chiavi per la distribuzione dei modelli:** consente a Resource Manager di recuperare i segreti dall'insieme di credenziali.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Usare il cmdlet di PowerShell relativo all'insieme di credenziali delle chiavi [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) per abilitare la crittografia del disco per l'insieme di credenziali delle chiavi.

  - **Abilitare l'insieme di credenziali delle chiavi per la crittografia del disco:** EnabledForDiskEncryption è obbligatorio per Crittografia dischi di Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Abilitare l'insieme di credenziali delle chiavi per la distribuzione, se necessario:** in questo modo si consente al provider di risorse Microsoft.Compute di recuperare segreti da questo insieme di credenziali delle chiavi quando vi viene fatto riferimento durante la creazione di risorse, ad esempio quando si crea una macchina virtuale.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Abilitare l'insieme di credenziali delle chiavi per la distribuzione di modelli, se necessario:** in questo modo si consente a Azure Resource Manager di recuperare segreti da questo insieme di credenziali delle chiavi quando vi viene fatto riferimento durante la distribuzione di un modello.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portale di Azure

1. Selezionare l'insieme di credenziali delle chiavi, passare a **Criteri di accesso** e selezionare **Fare clic per visualizzare i criteri di accesso avanzati**.
2. Selezionare la casella **Abilita l'accesso a Crittografia dischi di Azure per la crittografia dei volumi**.
3. Selezionare **Abilita l'accesso alle macchine virtuali di Azure per la distribuzione** e/o **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli**, se necessario. 
4. Fare clic su **Save**.

    ![Criteri di accesso avanzati per l'insieme di credenziali delle chiavi di Azure](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configurare una chiave di crittografia della chiave

Se si vuole usare una chiave di crittografia della chiave (KEK) per un livello aggiuntivo di sicurezza per le chiavi di crittografia, aggiungere una KEK all'insieme di credenziali delle chiavi. Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure la usa per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi.

È possibile generare una nuova chiave di crittografia della chiave usando il comando [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) dell'interfaccia della riga di comando di Azure, il cmdlet di Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) o il [portale di Azure](https://portal.azure.com/). È necessario generare un tipo di chiave RSA. Crittografia dischi di Azure non supporta ancora l'uso delle chiavi a curva ellittica.

Si può invece importare una chiave di crittografia della chiave dal modulo di protezione hardware di gestione delle chiavi locale. Per altre informazioni, vedere la [documentazione di Key Vault](../articles/key-vault/keys/hsm-protected-keys.md).

È necessario applicare il controllo delle versioni all'URL della chiave di crittografia della chiave dell'insieme di credenziali delle chiavi. Azure applica questa restrizione relativa al controllo delle versioni. Per informazioni sugli URL del segreto e della chiave di crittografia della chiave validi, vedere gli esempi seguenti:

* Esempio di URL del segreto valido: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Esempio di URL della chiave di crittografia della chiave valido: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Crittografia dischi di Azure non supporta la possibilità di specificare i numeri di porta come parte dei segreti dell'insieme di credenziali delle chiavi e degli URL della chiave di crittografia della chiave. Ecco alcuni esempi di URL di insiemi di credenziali delle chiavi non supportati e supportati:

  * URL dell'insieme di credenziali delle chiavi accettabile: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL dell'insieme di credenziali delle chiavi non accettabile: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare il comando [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) dell'interfaccia della riga di comando di Azure per generare una nuova chiave di crittografia della chiave nell'insieme di credenziali delle chiavi.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Si può in alternativa importare una chiave privata usando il comando [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) dell'interfaccia della riga di comando di Azure:

In entrambi i casi occorre fornire il nome della chiave di crittografia della chiave al parametro --key-encryption-key del comando [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) dell'interfaccia della riga di comando di Azure. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Usare il cmdlet di Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) per generare una nuova chiave di crittografia della chiave e archiviarla nell'insieme di credenziali delle chiavi.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Si può in alternativa importare una chiave privata usando il comando [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) di Azure PowerShell.

In entrambi i casi occorre fornire l'ID dell'insieme di credenziali delle chiavi e l'URL della chiave di crittografia della chiave ai parametri -KeyEncryptionKeyVaultId e -KeyEncryptionKeyUrl del cmdlet di Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0). Tenere presente che questo esempio presuppone che si usi lo stesso insieme di credenziali delle chiavi sia per la chiave di crittografia del disco che per la chiave di crittografia della chiave.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```