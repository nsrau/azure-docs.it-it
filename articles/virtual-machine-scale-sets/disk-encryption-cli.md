---
title: Crittografare i dischi per i set di scalabilità di Azure con la CLI
description: Informazioni su come usare Azure PowerShell per crittografare istanze di macchine virtuali e i dischi collegati in set di scalabilità di macchine virtuali di Windows
services: virtual-machine-scale-sets
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 2577eef95cad3405eb42af22e4c57511a660c1a6
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530810"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Crittografare il sistema operativo e i dischi dati collegati in un set di scalabilità di macchine virtuali con l'interfaccia della riga

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa Guida introduttiva illustra come usare l'interfaccia della riga di comando di Azure per creare e crittografare un set di scalabilità di macchine virtuali. Per altre informazioni sull'applicazione di crittografia dischi di Azure a un set di scalabilità di macchine virtuali, vedere [crittografia dischi di Azure per i set di scalabilità di macchine virtuali](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.31 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Creare un set di scalabilità

Per poter creare un set di scalabilità, è prima necessario creare un gruppo di risorse con il comando [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Si può ora creare un set di scalabilità di macchine virtuali con il comando [az vmss create](/cli/azure/vmss). L'esempio seguente crea un set di scalabilità denominato *myScaleSet* che è impostato per l'aggiornamento automatico man mano che vengono applicate modifiche e genera le chiavi SSH se non esistono in *~/.ssh/id_rsa*. Un disco dati da 32 GB è collegato a ogni istanza di macchina virtuale e viene usata l'[estensione dello script personalizzata](../virtual-machines/linux/extensions-customscript.md) di Azure per preparare i dischi dati con il comando [az vmss extension set](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Creare un insieme di credenziali delle chiavi di Azure abilitato per la crittografia dei dischi

L'insieme di credenziali delle chiavi di Azure consente di archiviare chiavi, chiavi private o password da implementare in tutta sicurezza in applicazioni e servizi. Le chiavi di crittografia vengono archiviate nell'insieme di credenziali delle chiavi di Azure che usano la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. Queste chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla VM. È possibile esercitare il controllo su queste chiavi di crittografia e sul loro uso.

Definire un *nome_keyvault* univoco. Creare quindi un KeyVault con il comando [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) nella stessa sottoscrizione e nella stessa area del set di scalabilità e impostare il criterio di accesso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Usare un insieme di credenziali delle chiavi esistente

Questo passaggio è necessario solo se si vuole usare un insieme di credenziali delle chiavi già esistente con la crittografia dei dischi. Ignorare questo passaggio se si è creato un insieme di credenziali delle chiavi nella sezione precedente.

Definire un *nome_keyvault* univoco. Aggiornare quindi il KeyVault con [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) e impostare il criterio di accesso *--enabled-for-disk-encryption*.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Abilitare la crittografia

Per crittografare le istanze di macchine virtuali in un set di scalabilità, ottenere prima alcune informazioni sull'ID risorsa dell'insieme di credenziali delle chiavi con [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Queste variabili vengono quindi usate per avviare il processo di crittografia con il comando [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Il completamento del processo di crittografia potrebbe richiedere un minuto o due.

Poiché il criterio di aggiornamento per il set di scalabilità creato in un passaggio precedente è impostato come *automatico*, le istanze di macchine virtuali avviano automaticamente il processo di crittografia. Nei set di scalabilità in cui il criterio di aggiornamento è manuale, avviare il criterio di crittografia nelle istanze di macchine virtuali con [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Abilitare la crittografia con KEK per eseguire il wrapping della chiave

È anche possibile usare una chiave di crittografia della chiave per la sicurezza aggiuntiva durante la crittografia del set di scalabilità di macchine virtuali.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  La sintassi per il valore del parametro Disk-Encryption-Vault è la stringa dell'identificatore completo:</br>
/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nome-insieme-credenziali]</br></br>
> La sintassi per il valore del parametro Key-Encryption-Key è l'URI completo di KEK come in:</br>
https:///[nome-insieme-credenziali]. Vault. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]

## <a name="check-encryption-progress"></a>Verificare lo stato della crittografia

Per controllare lo stato della crittografia dei dischi, usare [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Quando le istanze di macchine virtuali sono crittografate, il codice di stato indica *EncryptionState/encrypted*, come mostrato nell'output di esempio seguente:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Disabilitare la crittografia

Se non si vogliono più usare i dischi delle istanze di macchine virtuali crittografate, è possibile disabilitare la crittografia con [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable), come segue:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Passaggi successivi

- In questo articolo è stato crittografato un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure. È anche possibile usare [Azure PowerShell](disk-encryption-powershell.md) o [modelli Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Se si vuole che crittografia dischi di Azure venga applicato dopo il provisioning di un'altra estensione, è possibile usare la [sequenziazione delle estensioni](virtual-machine-scale-sets-extension-sequencing.md). 
- Un esempio di file batch end-to-end per la crittografia del disco dati del set di scalabilità di Linux è disponibile [qui](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). In questo esempio vengono creati un gruppo di risorse e il set di scalabilità di Linux, viene montato un disco dati di 5 GB e viene crittografato il set di scalabilità di macchine virtuali.
