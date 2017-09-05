---
title: Differenze di Azure Service Fabric in Linux e Windows | Microsoft Docs
description: Differenze tra l'anteprima di Azure Service Fabric in Linux e Azure Service Fabric in Windows.
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
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 7b80bb7d4a4e6a1b4cf47ce87200f47339785c53
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="differences-between-service-fabric-on-linux-preview-and-windows-generally-available"></a>Differenze tra Service Fabric in Linux (anteprima) e in Windows (disponibile a livello generale)

Poiché Service Fabric in Linux è un'anteprima, alcune funzionalità sono supportate in Windows, ma non ancora in Linux. Quando Service Fabric in Linux sarà disponibile a livello generale, i set di funzionalità saranno gli stessi. Con le versioni future, questo gap di funzionalità verrà ridotto. Tra le versioni disponibili più recenti, ovvero tra la versione 5.6 in Windows e la versione 5.5 in Linux, esistono le differenze seguenti: 

* Reliable Collections e Reliable Stateful Services 
* ReverseProxy 
* Programma di installazione autonomo 
* Convalida XML Schema per i file manifesto 
* Reindirizzamento della console 
* Servizio di analisi degli errori
* Docker Compose e driver di volume e registrazione per i contenitori 
* Governance delle risorse per contenitori e servizi 
* Servizio DNS
* Supporto di Azure Active Directory
* Comandi dell'interfaccia della riga di comando equivalenti ad alcuni comandi di Powershell 
* Solo un subset di comandi di Powershell può essere eseguito in un cluster Linux, come illustrato nella sezione successiva.

>[!NOTE]
>Il reindirizzamento della console non è supportato nei cluster di produzione, nemmeno in Windows.

Anche gli strumenti di sviluppo presentano differenze tra Windows e Linux. VisualStudio, Powershell, VSTS e ETW vengono usati in Windows mentre Yeoman, Eclipse, Jenkins, e LTTng vengono usati in Linux.

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
* Cmd
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

