---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una macchina virtuale Windows Server 2016 con il monitoraggio di OMS | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Creare una macchina virtuale Windows Server 2016 con il monitoraggio di OMS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 70df053ba31974e753830487f1320d0d2edc4403
ms.lasthandoff: 03/21/2017

---

# <a name="monitor-a-vm-with-operations-management-suite"></a>Monitorare una macchina virtuale con Operations Management Suite

Questo script consente di creare una macchina virtuale di Azure, installare l'agente Operations Management Suite (OMS) e registrare il sistema in un'area di lavoro OMS. Dopo l'esecuzione dello script, la macchina virtuale sarà visibile nella console di OMS.

Se necessario, installare l'interfaccia della riga di comando di Azure usando l'istruzione presente nella [Guida all'installazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e quindi eseguire `az login` per creare una connessione con Azure. È anche necessario aggiornare la chiave dell'area di lavoro e l'ID area di lavoro OMS. È anche necessario modificare la variabile $AdminPassword all'inizio dello script in modo che soddisfi i requisiti di complessità delle password univoci.

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure in Windows, vedere [Running the Azure CLI in Windows](../virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-monitor-oms.sh "Creazione rapida della macchina virtuale")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup --yes
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

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della macchina virtuale Windows Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

