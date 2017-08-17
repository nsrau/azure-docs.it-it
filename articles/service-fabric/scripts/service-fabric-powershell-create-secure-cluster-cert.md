---
title: Esempio di script di Azure PowerShell - Creare un cluster di Service Fabric | Microsoft Docs
description: Esempio di script di Azure PowerShell - Creare un cluster di Service Fabric.
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7322570c90b3c83fc79d428a7ac0b57ecb2e4b75
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-service-fabric-cluster"></a>Creare un cluster di Service Fabric

Questo script di esempio crea un cluster con cinque nodi di Service Fabric protetto con un certificato X.509.  Il comando crea un certificato autofirmato e lo carica in un nuovo insieme di credenziali delle chiavi. Il certificato viene copiato anche in una directory locale.  Impostare il parametro *-OS* per scegliere la versione di Windows o Linux che viene eseguita sui nodi del cluster.  Personalizzare i parametri in base alle esigenze.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Creare un cluster di Service Fabric")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo avere eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, il cluster e tutte le risorse correlate.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Crea un nuovo cluster di Service Fabric. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).

