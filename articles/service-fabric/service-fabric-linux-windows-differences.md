---
title: Differenze di Azure Service Fabric in Linux e Windows | Microsoft Docs
description: Differenze tra Azure Service Fabric in Linux e Azure Service Fabric in Windows.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 25976ba919454e26f1dd7965de5db7c4f80b9355
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Differenze tra Service Fabric in Linux e in Windows

Alcune funzionalità sono supportate in Windows ma non ancora in Linux. Questo gap di funzionalità verrà ridotto con ogni versione e i set di funzionalità saranno infine gli stessi. Tra le versioni disponibili più recenti, ossia tra la versione 6.0 in Windows e la versione 6.0 in Linux, esistono le differenze seguenti: 

* Tutti i modelli di programmazione disponibili in anteprima (Reliable Services senza stato, Reliable Services con stato e Reliable Actors Java/C#)
* Envoy (ReverseProxy) disponibile in anteprima in Linux
* Programma di installazione autonomo per Linux non ancora disponibile in Linux
* Reindirizzamento della console (non supportato nei cluster di produzione Windows o Linux)
* Servizio di analisi degli errori in Linux
* Servizio DNS per i servizi di Service Fabric (il servizio DNS è supportato per i contenitori in Linux)
* Comandi dell'interfaccia della riga di comando equivalenti ad alcuni comandi di Powershell (vedere l'elenco seguente, che si applica per la maggior parte solo a cluster autonomi)

Anche gli strumenti di sviluppo presentano differenze tra Windows e Linux. Visual Studio, Powershell, VSTS ed ETW vengono usati in Windows, mentre Yeoman, Eclipse, Jenkins e LTTng vengono usati in Linux.

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

