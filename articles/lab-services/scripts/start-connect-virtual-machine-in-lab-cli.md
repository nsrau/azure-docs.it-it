---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Avviare una macchina virtuale in un lab | Microsoft Docs
description: Questo script dell'interfaccia della riga di comando di Azure avvia una macchina virtuale in Azure DevTest Labs.
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
ms.openlocfilehash: a635766c1a7fb9ae10a651d09ecd7da9a5f01e51
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763137"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Usare l'interfaccia della riga di comando di Azure per avviare una macchina virtuale in Azure DevTest Labs

Questo script dell'interfaccia della riga di comando di Azure avvia una macchina virtuale in un lab. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az lab vm start ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-start) | Avvia una macchina virtuale (VM) in un lab. Il completamento di questa operazione può richiedere tempo. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando per Azure Lab Services sono disponibili in [Azure Lab Services CLI samples](../samples-cli.md) (Esempi dell'interfaccia della riga di comando per Azure Lab Services).
