---
title: Creare una VM Linux in Azure da un modello | Microsoft Docs
description: Procedura per usare l'interfaccia della riga di comando 2.0 di Azure per creare una VM Linux da un modello di Resource Manager
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 908a8a0c82b2d21fb25c9b33dbd714570d1ac272
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Procedura per creare una macchina virtuale Linux con i modelli di Azure Resource Manager
Questo articolo illustra come distribuire rapidamente una macchina virtuale (VM) Linux con i modelli di Azure Resource Manager e l'interfaccia della riga di comando 2.0 di Azure. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](create-ssh-secured-vm-from-template-nodejs.md).


## <a name="templates-overview"></a>Panoramica dei modelli
I modelli di Azure Resource Manager sono file JSON che definiscono l'infrastruttura e la configurazione della soluzione Azure. Usando il modello è possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente. Per altre informazioni sul formato del modello e sulla sua costruzione, vedere [Creare il primo modello di Azure Resource Manager](../../azure-resource-manager/resource-manager-create-first-template.md). Per visualizzare la sintassi JSON per i tipi di risorse, vedere [Define resources in Azure Resource Manager templates](/azure/templates/) (Definire le risorse nei modelli di Azure Resource Manager).


## <a name="create-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupVM* nell'area *stati uniti orientali*:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale
L'esempio seguente crea una VM da [questo modello di Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) con [az group deployment create](/cli/azure/group/deployment#create). Fornire il valore della chiave pubblica SSH, ad esempio il contenuto di *~/.ssh/id_rsa.pub*. Se è necessario creare una coppia di chiavi SSH, vedere [Come creare e usare una coppia di chiavi SSH pubblica e privata per le VM Linux in Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
  --parameters '{"sshKeyData": {"value": "ssh-rsa AAAAB3N{snip}B9eIgoZ"}}'
```

In questo esempio è stato specificato un modello archiviato in GitHub. È possibile anche scaricare o creare un modello e specificare il percorso locale con lo stesso parametro `--template-file`.

Per la connessione SSH alla VM, ottenere l'indirizzo IP pubblico con [az network public-ip show](/cli/azure/network/public-ip#show):

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name sshPublicIP \
    --query [ipAddress] \
    --output tsv
```

È quindi possibile usare la connessione SSH alla VM nel modo usuale:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Passaggi successivi
In questo esempio è stata creata una VM Linux di base. Per altri modelli di Resource Manager che includono i framework di applicazioni o creano ambienti più complessi, visitare la [Raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
