---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare e verificare una macchina virtuale in un lab | Microsoft Docs
description: Questo script dell'interfaccia della riga di comando di Azure crea una macchina virtuale in un lab e verifica che sia disponibile.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 225e62cedfea79875d56a50b4b04bdf0ad4c7892
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872030"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Usare l'interfaccia della riga di comando di Azure per creare e verificare la disponibilità di una macchina virtuale in un lab in Azure DevTest Labs

Questo script dell'interfaccia della riga di comando di Azure crea una macchina virtuale in un lab. La VM creata si basa su un'immagine del Marketplace con autenticazione SSH. Lo script verifica quindi che la VM sia disponibile per l'uso. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az lab vm create](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Crea una macchina virtuale in un lab. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Visualizza lo stato della macchina virtuale in un lab. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando per Azure Lab Services sono disponibili in [Azure Lab Services CLI samples](../samples-cli.md) (Esempi dell'interfaccia della riga di comando per Azure Lab Services).
