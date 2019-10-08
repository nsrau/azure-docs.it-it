---
title: Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure
description: In questa guida introduttiva si apprenderà come usare l'interfaccia della riga di comando di Azure per creare e crittografare una macchina virtuale Linux
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 88051a755ab1598edd2ccdc92968639f1baa2986
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829004"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Guida introduttiva: Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare e crittografare una macchina virtuale (VM) Linux.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo avvio rapido richiede la versione 2.0.30 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). L'esempio seguente crea una macchina virtuale denominata *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti. L'output di esempio seguente mostra che l'operazione di creazione della macchina virtuale ha avuto esito positivo.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Creare un insieme di credenziali delle chiavi configurato per le chiavi di crittografia

Crittografia dischi di Azure archivia la chiave di crittografia in Azure Key Vault. Creare un insieme di credenziali delle chiavi con [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Per consentire all'insieme di credenziali delle chiavi di archiviare le chiavi di crittografia, usare il parametro --enabled-for-disk-encryption.

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco in Azure. Negli esempi riportati di seguito sostituire <nome univoco dell'insieme di credenziali> con il nome scelto.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Crittografare la macchina virtuale

Crittografare la VM con [az vm encryption](/cli/azure/vm/encryption?view=azure-cli-latest), specificando il nome univoco dell'insieme di credenziali delle chiavi nel parametro --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Dopo qualche istante il processo restituirà il messaggio "La richiesta di crittografia è stata accettata. Usare il comando 'show' per monitorare lo stato di avanzamento". Il comando 'show' è [az vm show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

Quando è abilitata la crittografia, nell'output restituito verrà visualizzato quanto segue:

```azurecli-interactive
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group) per rimuovere il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare una macchina virtuale, come creare un insieme di credenziali delle chiavi abilitato per le chiavi di crittografia e come crittografare una macchina virtuale.  Passare all'articolo successivo per altre informazioni sulla Crittografia dischi di Azure per le macchine virtuali Linux.

> [!div class="nextstepaction"]
> [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)