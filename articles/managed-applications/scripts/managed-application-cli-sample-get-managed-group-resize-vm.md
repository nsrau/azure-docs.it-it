---
title: Ottenere un gruppo di risorse gestite e ridimensionare le macchine virtuali - Interfaccia della riga di comando di Azure
description: Questo articolo include uno script di esempio dell'interfaccia della riga di comando di Azure che ottiene un gruppo di risorse gestito in un'applicazione gestita di Azure. Lo script ridimensiona le VM.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: ccf853f33eea054b450563747646f3d9ec560aa3
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528897"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Ottenere risorse in un gruppo di risorse gestite e ridimensionare le macchine virtuali con l'interfaccia della riga di comando di Azure

Questo script recupera le risorse da un gruppo di risorse gestite e ridimensiona le macchine virtuali in tale gruppo.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


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
