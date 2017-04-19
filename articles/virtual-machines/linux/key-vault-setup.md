---
title: Configurare Azure Key Vault per le macchine virtuali Linux | Microsoft Docs
description: Come configurare Azure Key Vault da usare con una macchina virtuale di Azure Resource Manager con l&quot;interfaccia della riga di comando 2.0.
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 846941ae095a7d6f428bd0d189abc9f0c1848aa8
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Come configurare Key Vault per le macchine virtuali con l'interfaccia della riga di comando 2.0

Nello stack di Azure Resource Manager i segreti e i certificati vengono modellati come risorse offerte tramite Key Vault. Per altre informazioni sugli insiemi di credenziali delle chiavi di Azure, vedere [Informazioni sull'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-whatis.md) Per consentire l'uso di Key Vault con le macchine virtuali di Azure Resource Manager è necessario impostare su true la proprietà *EnabledForDeployment* in Key Vault. In questo articolo viene illustrato come configurare Key Vault delle chiavi per l'uso con macchine virtuali di Azure tramite l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per eseguire questi passaggi è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver effettuato l'accesso a un account Azure con il comando [az login](/cli/azure/#login).

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi
Creare un insieme di credenziali delle chiavi e assegnare i criteri di distribuzione con [az keyvault create](/cli/azure/keyvault#create). Nell'esempio seguente viene creato un insieme di credenziali delle chiavi denominato `myKeyVault` nel gruppo di risorse `myResourceGroup`:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aggiornare un insieme di credenziali delle chiavi per l'uso con le macchine virtuali
Impostare i criteri di distribuzione per un insieme di credenziali delle chiavi esistente con [az keyvault update](/cli/azure/keyvault#update). Il comando seguente aggiorna l'insieme di credenziali delle chiavi denominato `myKeyVault` nel gruppo di risorse `myResourceGroup`:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Utilizzare modelli per configurare l'insieme di credenziali delle chiavi
Se si usa un modello è necessario impostare come segue la proprietà `enabledForDeployment` su `true` per la risorsa Key Vault:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per altre opzioni che è possibile configurare quando si crea un insieme di credenziali delle chiavi usando i modelli vedere [Crea un insieme di credenziali delle chiavi](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).

