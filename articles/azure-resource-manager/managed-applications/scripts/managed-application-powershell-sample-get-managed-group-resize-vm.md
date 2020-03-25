---
title: Ottenere un gruppo di risorse gestite e ridimensionare le macchine virtuali - Azure PowerShell
description: Questo articolo include uno script di esempio di Azure PowerShell che ottiene un gruppo di risorse gestito per un'applicazione gestita di Azure. Lo script ridimensiona le VM.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 695023f4700370cbe5e9b345f513e38d1cf1fc0c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75648958"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Ottenere risorse in un gruppo di risorse gestite e ridimensionare le macchine virtuali con PowerShell

Questo script recupera le risorse da un gruppo di risorse gestite e ridimensiona le macchine virtuali in tale gruppo.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per distribuire l'applicazione gestita. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Elenca le applicazioni gestite. Fornire il nome del gruppo di risorse per definire i risultati. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Elenca le risorse. Fornire un gruppo e un tipo di risorse per definire i risultati. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Aggiorna le dimensioni di una macchina virtuale. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni su PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
