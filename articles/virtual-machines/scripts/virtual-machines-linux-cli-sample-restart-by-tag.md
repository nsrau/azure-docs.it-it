---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Riavviare macchine virtuali | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Riavviare macchine virtuali in base al tag e all&quot;ID
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b292a02770fa74812e74fa38f870e47ed7473b63
ms.lasthandoff: 03/04/2017

---

# <a name="restart-vms-by-tag"></a>Riavviare le macchine virtuali in base al tag

In questo esempio vengono create macchine virtuali con un tag specifico in più gruppi di risorse.
Le macchine virtuali vengono create in parallelo tramite `--no-wait`, quindi attendono il completamento collettivo.

Dopo la creazione, le macchine virtuali vengono riavviate tramite due meccanismi di query diversi.

Il primo riavvia le macchine virtuali della stessa query usata per attenderne la creazione asincrona.
```bash
az vm restart --ids $(az vm list --query "join(' ', ${GROUP_QUERY}] | [].id)" \
    -o tsv) $1>/dev/null
```

Il secondo usa un elenco di risorse e una query generici per reperire i relativi ID in base al tag.
```bash
az vm restart --ids $(az resource list --tag ${TAG} \
    --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv) $1>/dev/null
```

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart-by-tag.sh "Riavviare le macchine virtuali in base al tag")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere i gruppi di risorse, le macchine virtuali e tutte le risorse correlate.

```azurecli
az group delete -n GROUP1 --no-wait --yes && \ 
az group delete -n GROUP2 --no-wait --yes && \
az group delete -n GROUP3 --no-wait --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | Crea le macchine virtuali.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | Usato con `--query` per assicurare il provisioning delle macchine virtuali prima di riavviarle. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#list) | Riavvia le macchine virtuali. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

