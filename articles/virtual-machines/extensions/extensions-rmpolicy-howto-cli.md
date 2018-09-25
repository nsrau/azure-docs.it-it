---
title: Usare Criteri di Azure per limitare l'installazione di estensioni di macchina virtuale | Microsoft Docs
description: Usare Criteri di Azure per limitare le distribuzioni di estensioni di macchina virtuale.
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 08fcb244ada319dccf62c134a0155dd117e79184
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977041"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Usare Criteri di Azure per limitare l'installazione di estensioni in macchine virtuali Linux

Se si vuole impedire l'uso o l'installazione di determinate estensioni nelle macchine virtuali Linux, è possibile creare criteri di Azure tramite l'interfaccia della riga di comando per limitare le estensioni per le macchine virtuali all'interno di un gruppo di risorse. 

Questa esercitazione usa l'interfaccia della riga di comando in Azure Cloud Shell, che viene costantemente aggiornato alla versione più recente. Se si vuole eseguire l'interfaccia della riga di comando di Azure in locale, è necessario installare la versione 2.0.26 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Creare un file delle regole

Per limitare le estensioni che è possibile installare, è necessario definire una [regola](/azure/azure-policy/policy-definition#policy-rule) per fornire la logica necessaria per identificare l'estensione.

Questo esempio mostra come negare l'installazione di estensioni pubblicate da "Microsoft.OSTCExtensions" creando un file delle regole in Azure Cloud Shell, ma se si usa l'interfaccia della riga di comando in locale è anche possibile creare un file locale e sostituire il percorso (~/clouddrive) con quello del file locale nel computer in uso.

In [Bash in Cloud Shell](https://shell.azure.com/bash) digitare:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Copiare e incollare il codice JSON seguente nel file.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Al termine, premere **ESC** e quindi digitare **:wq** per salvare e chiudere il file.


## <a name="create-a-parameters-file"></a>Creare un file di parametri

È anche necessario un file di [parametri](/azure/azure-policy/policy-definition#parameters) per la creazione di una struttura da usare per passare un elenco delle estensioni da bloccare. 

Questo esempio mostra come creare un file dei parametri per macchine virtuali Linux in Cloud Shell, ma se si usa l'interfaccia della riga di comando in locale è anche possibile creare un file locale e sostituire il percorso (~/clouddrive) con quello del file locale nel computer in uso.

In [Bash in Cloud Shell](https://shell.azure.com/bash) digitare:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Copiare e incollare il codice JSON seguente nel file.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Al termine, premere **ESC** e quindi digitare **:wq** per salvare e chiudere il file.

## <a name="create-the-policy"></a>Creare i criteri

Una definizione di criteri è un oggetto usato per archiviare la configurazione che si desidera usare. La definizione dei criteri usa i file delle regole e dei parametri per definire i criteri. Creare la definizione dei criteri usando [az policy definition create](/cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create).

In questo esempio le regole e i parametri sono i file creati e archiviati come file JSON in Cloud Shell.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Assegnare i criteri

Questo esempio assegna i criteri a un gruppo di risorse usando [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create). Qualsiasi macchina virtuale creata nel gruppo di risorse **myResourceGroup** non potrà installare le estensioni dell'accesso alle macchine virtuali Linux o di script personalizzato per Linux. Prima di poter assegnare i criteri, il gruppo di risorse deve essere già presente.

Usare [az account list](/cli/azure/account?view=azure-cli-latest#az_account_list) per ottenere l'ID sottoscrizione da usare al posto di quello nell'esempio.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testare i criteri

Testare i criteri creando una nuova macchina virtuale e provando ad aggiungere un nuovo utente.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Provare a creare un nuovo utente denominato **myNewUser** usando l'estensione dell'accesso alle macchine virtuali.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Rimuovere l'assegnazione

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Rimuovere i criteri

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Criteri di Azure](../../azure-policy/azure-policy-introduction.md).
