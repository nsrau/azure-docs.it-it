---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Arrestare ed eliminare una macchina virtuale in un lab | Microsoft Docs
description: Questo script dell'interfaccia della riga di comando di Azure arresta ed elimina una macchina virtuale in un lab.
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
ms.openlocfilehash: a0dbcc530bd799b9fa457ba9b948e9ad99ce2a67
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763324"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Usare l'interfaccia della riga di comando di Azure per arrestare ed eliminare una macchina virtuale in un lab in Azure DevTest Labs

Questo script dell'interfaccia della riga di comando di Azure arresta ed elimina una macchina virtuale (VM) in un lab. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Arresta una macchina virtuale in un lab. Il completamento di questa operazione può richiedere tempo. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Elimina una macchina virtuale in un lab. Il completamento di questa operazione può richiedere tempo. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando per Azure Lab Services sono disponibili in [Azure Lab Services CLI samples](../samples-cli.md) (Esempi dell'interfaccia della riga di comando per Azure Lab Services).
