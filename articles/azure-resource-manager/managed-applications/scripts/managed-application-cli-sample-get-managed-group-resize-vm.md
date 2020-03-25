---
title: Ottenere un gruppo di risorse gestite e ridimensionare le macchine virtuali - Interfaccia della riga di comando di Azure
description: Questo articolo include uno script di esempio dell'interfaccia della riga di comando di Azure che ottiene un gruppo di risorse gestito in un'applicazione gestita di Azure. Lo script ridimensiona le VM.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 878a8d660495c932cc8fa44bb1ed06e79576729b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75648778"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Ottenere risorse in un gruppo di risorse gestite e ridimensionare le macchine virtuali con l'interfaccia della riga di comando di Azure

Questo script recupera le risorse da un gruppo di risorse gestite e ridimensiona le macchine virtuali in tale gruppo.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per distribuire l'applicazione gestita. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-list) | Elenca le applicazioni gestite. Fornire i valori di query per definire i risultati. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az-resource-list) | Elenca le risorse. Fornire un gruppo di risorse e i valori di query per definire i risultati. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az-vm-resize) | Aggiorna le dimensioni di una macchina virtuale. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).
