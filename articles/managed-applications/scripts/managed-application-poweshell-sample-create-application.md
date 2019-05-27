---
title: Esempio di script di Azure PowerShell - Distribuire un'applicazione gestita | Microsoft Docs
description: Esempio di script di Azure PowerShell - Distribuire una definizione di applicazione gestita
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 03b453e441b92d21f29e7e1f401bebae0b8589da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171457"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Distribuire un'applicazione gestita per un catalogo di servizi con PowerShell

Questo script distribuisce una definizione di applicazione gestita dal catalogo di servizi.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per distribuire l'applicazione gestita. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplication) | Crea un'applicazione gestita. Fornire l'ID della definizione e i parametri per il modello. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni su PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
