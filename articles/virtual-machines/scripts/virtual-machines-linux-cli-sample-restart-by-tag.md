---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Riavviare macchine virtuali | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Riavviare macchine virtuali in base al tag e all'ID
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: a9f7cf8ba492004cb6d9e359bfb392448dfbe813
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="restart-vms"></a>Riavviare le VM

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Questo esempio illustra un paio di modi per ottenere alcune VM e riavviarle.

Il primo riavvia tutte le VM nel gruppo di risorse.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

Il secondo ottiene le VM con tag tramite `az resouce list` e filtra le risorse che sono VM quindi riavvia queste VM.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../windows/cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).


## <a name="sample-script"></a>Script di esempio

L'esempio prevede tre script.
Il primo esegue il provisioning delle macchine virtuali.
Usa l'opzione senza attesa in modo che il comando ritorni senza attendere per ogni VM di cui eseguire il provisioning.
Il secondo attende il provisioning completo delle VM.
Il terzo script riavvia tutte le VM di cui è stato eseguito il provisioning e quindi solo le VM con tag.

### <a name="provision-the-vms"></a>Eseguire il provisioning delle VM

Questo script crea un gruppo di risorse e quindi crea tre VM da riavviare.
Due di queste vengono contrassegnate con tag.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Attesa

Questo script verifica lo stato del provisioning ogni 20 secondi, fino a quando viene completato il provisioning di tutte e tre le VM oppure il provisioning di una delle VM fallisce.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>Riavviare le VM

Questo script riavvia tutte le VM nel gruppo di risorse e quindi riavvia solo le VM con tag.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere i gruppi di risorse, le macchine virtuali e tutte le risorse correlate.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Crea le macchine virtuali.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Si usa con `--query` per assicurare che venga eseguito il provisionign delle VM prima del loro riavvio e poi per ottenere gli ID delle VM e riavviarle. |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Si usa con `--query` per ottenere gli ID delle VM con il tag. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Riavvia le macchine virtuali. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
