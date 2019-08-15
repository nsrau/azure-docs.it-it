---
title: Esempio di script di Azure PowerShell - Rimuovere un'applicazione da un cluster | Microsoft Docs
description: Esempio di script di Azure PowerShell - Rimuovere un'applicazione da un cluster di Service Fabric.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: f572996b4e460480335dbe32c4599bde206251d4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035504"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Rimuovere un'applicazione da un cluster di Service Fabric

Questo script di esempio elimina un'istanza di applicazione di Service Fabric in esecuzione e annulla la registrazione della versione e del tipo dell'applicazione dal cluster.  L'eliminazione dell'istanza dell'applicazione elimina anche tutte le istanze del servizio associate a questa applicazione. Personalizzare i parametri in base alle esigenze. 

Se necessario, installare il modulo PowerShell in Service Fabric con il [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Rimuove un'istanza di applicazione di Service Fabric in esecuzione dal cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Annulla la registrazione di un tipo e una versione di applicazione di Service Fabric dal cluster. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo PowerShell in Service Fabric, vedere la [documentazione di Azure PowerShell](/powershell/azure/service-fabric/?view=azureservicefabricps).

Altri esempi di PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).
