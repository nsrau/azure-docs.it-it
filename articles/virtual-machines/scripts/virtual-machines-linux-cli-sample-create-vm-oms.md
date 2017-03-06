---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una VM Linux con il monitoraggio di OMS | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una VM Linux con il monitoraggio di OMS
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 07d91dfa905d22334bd051f9d5f3d936d38efc88
ms.openlocfilehash: 6eae6db78e85bd4d02678c0875e9bf9163af5276
ms.lasthandoff: 02/28/2017

---

# <a name="monitor-a-vm-with-operations-management-suite"></a>Monitorare una macchina virtuale con Operations Management Suite

Questo script consente di creare una macchina virtuale di Azure, installare l'agente Operations Management Suite (OMS) e registrare il sistema in un'area di lavoro OMS. Dopo l'esecuzione dello script, la macchina virtuale sarà visibile nella console di OMS.

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il comando `az login`. Inoltre l'ID dell'area di lavoro di OMS e la chiave dell'area di lavoro devono essere aggiornati nello script.

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure nel client Windows, vedere [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-vm-monitor-oms.sh "Creazione rapida della macchina virtuale")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eseguire un'estensione di VM in una macchina virtuale. In questo caso, l'estensione dell'agente Operations Management Suite viene usata per installare l'agente OMS e registrare la VM in un'area di lavoro OMS. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

