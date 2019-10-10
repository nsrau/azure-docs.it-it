---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: e145c7af3ff3cdd6d3cca2a7b0da0fdc23a418b7
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245253"
---
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

*Se si dispone già di un gruppo di risorse, è possibile passare a [creare un](#create-a-key-vault)insieme di credenziali delle chiavi.*

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

Creare un gruppo di risorse usando il comando [AZ Group create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure dell'interfaccia della riga di comando, il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell o dall' [portale di Azure](https://portal.azure.com).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

*Se si dispone già di un insieme di credenziali delle chiavi, è possibile ignorare i [criteri di accesso avanzati di Key Vault](#set-key-vault-advanced-access-policies).*

Creare un insieme di credenziali delle chiavi usando il comando [AZ Key Vault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure dell'interfaccia della riga di comando, il comando [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) di azure PowerShell, il [portale di Azure](https://portal.azure.com)o un [modello di gestione risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Per assicurarsi che i segreti di crittografia non superino i limiti regionali, crittografia dischi di Azure richiede che il Key Vault e le macchine virtuali siano condivise nella stessa area. Creare e usare un Key Vault che si trova nella stessa area delle VM da crittografare. 

Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Quando si crea un insieme di credenziali delle chiavi tramite l'interfaccia della riga di comando di Azure, aggiungere il flag "--enabled-for-Disk-Encryption".

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Quando si crea un insieme di credenziali delle chiavi usando Azure PowerShell, aggiungere il flag "-EnabledForDiskEncryption".

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Modello di Resource Manager

È anche possibile creare un insieme di credenziali delle chiavi usando il [modello di gestione risorse](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Nel modello di avvio rapido di Azure fare clic su **Deploy to Azure**.
2. Selezionare la sottoscrizione, il gruppo di risorse, la posizione del gruppo di risorse, il nome Key Vault, l'ID oggetto, le note legali e il contratto, quindi fare clic su **Acquista**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Impostare i criteri di accesso avanzati di Key Vault

La piattaforma Azure deve avere accesso alle chiavi di crittografia o i segreti nell'insieme di credenziali delle chiavi per renderli disponibili alla macchina virtuale per l'avvio e la decrittografia dei volumi. 

Se l'insieme di credenziali delle chiavi non è stato abilitato per la crittografia del disco, la distribuzione o la distribuzione di modelli al momento della creazione, come illustrato nel passaggio precedente, è necessario aggiornare i criteri di accesso avanzati.  

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare [az keyvault update](/cli/azure/keyvault#az-keyvault-update) per abilitare la crittografia del disco per l'insieme di credenziali delle chiavi. 

 - **Abilitare Key Vault per la crittografia dischi:** È richiesto Enabled-for-disk-encryption. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Abilitare Key Vault per la distribuzione, se necessario:** in questo modo si consente al provider di risorse Microsoft.Compute di recuperare segreti da questo insieme di credenziali delle chiavi quando vi viene fatto riferimento durante la creazione di risorse, ad esempio quando si crea una macchina virtuale.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Abilitare Key Vault per la distribuzione di modelli, se necessario:** consente a Resource Manager di recuperare i segreti dall'insieme di credenziali.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Usare il cmdlet PowerShell di Key Vault [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) per abilitare la crittografia del disco per l'insieme di credenziali delle chiavi.

  - **Abilitare Key Vault per la crittografia dischi:** EnabledForDiskEncryption è obbligatorio per Crittografia dischi di Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Abilitare Key Vault per la distribuzione, se necessario:** in questo modo si consente al provider di risorse Microsoft.Compute di recuperare segreti da questo insieme di credenziali delle chiavi quando vi viene fatto riferimento durante la creazione di risorse, ad esempio quando si crea una macchina virtuale.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Abilitare Key Vault per la distribuzione di modelli, se necessario:** in questo modo si consente a Azure Resource Manager di recuperare segreti da questo insieme di credenziali delle chiavi quando vi viene fatto riferimento durante la distribuzione di un modello.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Portale di Azure

1. Selezionare l'insieme di credenziali delle chiavi, passare a **criteri di accesso**e **fare clic su per visualizzare i criteri di accesso avanzati**.
2. Selezionare la casella **Abilita l'accesso a Crittografia dischi di Azure per la crittografia dei volumi**.
3. Selezionare **Abilita l'accesso alle macchine virtuali di Azure per la distribuzione** e/o **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli**, se necessario. 
4. Fare clic su **Salva**.

    ![Criteri di accesso avanzati per l'insieme di credenziali delle chiavi di Azure](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Configurare una chiave di crittografia della chiave (KEK)

Se si vuole usare una chiave di crittografia della chiave (KEK) per un livello aggiuntivo di sicurezza per le chiavi di crittografia, aggiungere una KEK all'insieme di credenziali delle chiavi. Quando viene specificata una chiave di crittografia della chiave, Crittografia dischi di Azure la usa per eseguire il wrapping dei segreti di crittografia prima di scrivere nell'insieme di credenziali delle chiavi.

È possibile generare un nuovo KEK usando l'interfaccia della riga di comando di Azure [AZ Key Vault Key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) , il Azure PowerShell cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) o il [portale di Azure](https://portal.azure.com/). È necessario generare un tipo di chiave RSA. Crittografia dischi di Azure non supporta ancora l'uso di chiavi a curva ellittica.

È invece possibile importare un KEK dal modulo di protezione hardware di gestione delle chiavi locale. Per altre informazioni, vedere la [documentazione di Key Vault](/key-vault/key-vault-hsm-protected-keys). 

È necessario avere il controllo delle versioni degli URL KEK dell'insieme di credenziali delle chiavi. Azure applica questa restrizione relativa al controllo delle versioni. Per informazioni sugli URL del segreto e della chiave di crittografia della chiave validi, vedere gli esempi seguenti:

* Esempio di URL segreto valido: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Esempio di URL KEK valido: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Crittografia dischi di Azure non supporta la possibilità di specificare i numeri di porta come parte dei segreti dell'insieme di credenziali delle chiavi e degli URL della chiave di crittografia della chiave. Ecco alcuni esempi di URL di insiemi di credenziali delle chiavi non supportati e supportati:

  * URL dell'insieme di credenziali delle chiavi accettabile: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL dell'insieme di credenziali delle chiavi non accettabile: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare l'interfaccia della riga di comando di Azure [AZ Key Vault Key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) per generare un nuovo KEK e archiviarlo nell'insieme di credenziali delle chiavi.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. È invece possibile importare una chiave privata usando l'interfaccia della riga di comando di Azure [AZ chiave Vault Key Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) :

In entrambi i casi, si fornirà il nome della KEK all'interfaccia della riga di comando di Azure [AZ VM Encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-Encryption-Key. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Usare il cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) di Azure PowerShell per generare un nuovo KEK e archiviarlo nell'insieme di credenziali delle chiavi.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

È invece possibile importare una chiave privata usando il Azure PowerShell comando [AZ Key Vault Import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) .

In entrambi i casi, si fornirà l'ID dell'insieme di credenziali delle chiavi KEK e l'URL della chiave KEK ai parametri Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId e-KeyEncryptionKeyUrl. Si noti che in questo esempio si presuppone che si usi lo stesso insieme di credenziali delle chiavi sia per la chiave di crittografia del disco che per la KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
