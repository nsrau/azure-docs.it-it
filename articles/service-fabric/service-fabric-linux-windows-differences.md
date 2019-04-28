---
title: Differenze di Azure Service Fabric in Linux e Windows | Microsoft Docs
description: Differenze tra Azure Service Fabric in Linux e Azure Service Fabric in Windows.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 829a3ea6e8fafa24831f1731e402a3991c0258c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719992"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Differenze tra Service Fabric in Linux e in Windows

Alcune funzionalità sono supportate in Windows ma non ancora in Linux. Questo gap di funzionalità verrà ridotto con ogni versione e i set di funzionalità saranno infine gli stessi. Le versioni più recenti disponibili presentano le differenze seguenti.

* Envoy (ReverseProxy) è disponibile in anteprima in Linux
* Programma di installazione autonomo per Linux non ancora disponibile in Linux
* Reindirizzamento della console (non supportato nei cluster di produzione Windows o Linux)
* Servizio di analisi degli errori in Linux
* Servizio DNS per i servizi di Service Fabric (il servizio DNS è supportato per i contenitori in Linux)
* Comandi dell'interfaccia della riga di comando equivalenti ad alcuni comandi di Powershell (vedere l'elenco seguente, che si applica per la maggior parte solo a cluster autonomi)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>Cmdlet di Powershell che non funzionano in un cluster di Service Fabric in Linux

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>Passaggi successivi
* [Preparare l'ambiente di sviluppo in Linux](service-fabric-get-started-linux.md)
* [Preparare l'ambiente di sviluppo in OSX](service-fabric-get-started-mac.md)
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md) (Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse)
* [Creare la prima applicazione CSharp in Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Usare l'interfaccia della riga di comando di Service Fabric per gestire le applicazioni](service-fabric-application-lifecycle-sfctl.md)
