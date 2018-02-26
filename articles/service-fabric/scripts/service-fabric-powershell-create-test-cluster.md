---
title: 'Esempio di script di Azure PowerShell: creare un cluster di Service Fabric | Microsoft Docs'
description: Esempio di script di Azure PowerShell - Creare un cluster di Service Fabric di test a tre nodi.
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
ms.topic: sample
ms.date: 01/29/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: fd94a5dd9630cc65dedc180cdfd7aafea83c4866
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Creare un cluster di Service Fabric di test a tre nodi

Questo script di esempio crea un cluster di Service Fabric di test a tre nodi protetto con un certificato X.509. La configurazione del cluster a tre nodi è supportata per gli scenari di sviluppo/test perché permette di eseguire aggiornamenti in tutta sicurezza e di ovviare agli errori di singoli nodi, purché non si verifichino contemporaneamente. Il cluster di produzione richiede cinque o più nodi per poter offrire la resilienza a errori simultanei.  

Il comando crea un certificato autofirmato e lo carica in un nuovo insieme di credenziali delle chiavi, creato nello stesso gruppo di risorse del cluster. Il certificato viene copiato anche in una directory locale.  Impostare il parametro *-OS* per scegliere la versione di Windows o Linux che viene eseguita sui nodi del cluster.  Personalizzare i parametri in base alle esigenze.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

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
