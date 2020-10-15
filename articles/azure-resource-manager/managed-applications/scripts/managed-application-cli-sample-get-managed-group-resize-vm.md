---
title: Ottenere un gruppo di risorse gestite e ridimensionare le macchine virtuali - Interfaccia della riga di comando di Azure
description: Questo articolo include uno script di esempio dell'interfaccia della riga di comando di Azure che ottiene un gruppo di risorse gestito in un'applicazione gestita di Azure. Lo script ridimensiona le VM.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 711b516d1ba1154e574b0d8bbd8d86a02d7df018
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87497818"
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
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | Elenca le applicazioni gestite. Fornire i valori di query per definire i risultati. |
| [az resource list](/cli/azure/resource#az-resource-list) | Elenca le risorse. Fornire un gruppo di risorse e i valori di query per definire i risultati. |
| [az vm resize](/cli/azure/vm#az-vm-resize) | Aggiorna le dimensioni di una macchina virtuale. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
