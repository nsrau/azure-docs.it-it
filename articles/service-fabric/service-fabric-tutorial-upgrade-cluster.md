---
title: Aggiornare il runtime Service Fabric in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come usare PowerShell per aggiornare il runtime di un cluster di Service Fabric ospitato in Azure.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 5bb3760879682f9fc828d2a43690d34afb110403
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598752"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>Esercitazione: Aggiornare il runtime di un cluster di Service Fabric in Azure

Questa esercitazione, che costituisce la quarta parte di una serie, illustra come aggiornare il runtime di Service Fabric in un cluster di Azure Service Fabric. Questa parte dell'esercitazione è scritta per cluster di Service Fabric in esecuzione in Azure e non si applica a cluster di Service Fabric autonomi.

> [!WARNING]
> Questa parte dell'esercitazione richiede PowerShell. L'aggiornamento del runtime del cluster non è ancora supportato dagli strumenti dell'interfaccia della riga di comando di Azure. In alternativa, è possibile aggiornare un cluster nel portale. Per altre informazioni, vedere [Aggiornare un cluster di Azure Service Fabric](service-fabric-cluster-upgrade.md).

Se il cluster esegue già il runtime di Service Fabric più recente, non è necessario eseguire questo passaggio. Questo articolo può tuttavia essere usato per installare qualsiasi runtime supportato in un cluster di Azure Service Fabric.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Leggere la versione del cluster
> * Impostare la versione del cluster

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in Azure usando un modello
> * [Eseguire il monitoraggio di un cluster](service-fabric-tutorial-monitor-cluster.md)
> * [Aumentare o ridurre un cluster](service-fabric-tutorial-scale-cluster.md)
> * Aggiornare il runtime di un cluster
> * [Eliminare un cluster](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) o l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* Creare un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in Azure
* Configurare un ambiente di sviluppo Windows. Installare [Visual Studio 2019](https://www.visualstudio.com) e installare i carichi di lavoro per lo **sviluppo di Azure**, lo **sviluppo ASP.NET e Web** e lo **sviluppo multipiattaforma .NET Core**.  Configurare un [ambiente di sviluppo .NET](service-fabric-get-started.md).

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al proprio account di Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>Ottenere la versione del runtime

Dopo la connessione ad Azure e la selezione della sottoscrizione che contiene il cluster di Service Fabric, è possibile ottenere la versione del runtime del cluster.

```powershell
Get-AzServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

In alternativa, è possibile ottenere l'elenco di tutti i cluster nella sottoscrizione con l'esempio seguente:

```powershell
Get-AzServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

Si noti il valore **ClusterCodeVersion**. Questo valore verrà usato nella prossima sezione.

## <a name="upgrade-the-runtime"></a>Aggiornare il runtime

Usare il valore di **ClusterCodeVersion** della sezione precedente con il cmdlet `Get-ServiceFabricRuntimeUpgradeVersion` per individuare le versioni disponibili a cui eseguire l'aggiornamento. Questo cmdlet può essere eseguito solo da un computer connesso a Internet. Se ad esempio si vogliono visualizzare le versioni del runtime a cui è possibile eseguire l'aggiornamento dalla versione `5.7.198.9494`, usare il comando seguente:

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

Con un elenco delle versioni, è possibile indicare al cluster di Azure Service Fabric di eseguire l'aggiornamento a un runtime più recente. Se ad esempio, è possibile eseguire l'aggiornamento alla versione `6.0.219.9494`, per procedere usare il comando seguente.

```powershell
Set-AzServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> L'aggiornamento del runtime di un cluster può richiedere molto tempo. Durante l'esecuzione dell'aggiornamento PowerShell è bloccato. Per controllare lo stato dell'aggiornamento, è possibile usare un'altra sessione di PowerShell.

È possibile effettuare il monitoraggio dello stato dell'aggiornamento con PowerShell o con l'interfaccia della riga di comando di Azure Service Fabric (sfctl).

Prima connettersi al cluster con il certificato SSL creato nella prima parte dell'esercitazione. Usare il cmdlet `Connect-ServiceFabricCluster` o `sfctl cluster upgrade-status`.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Quindi usare `Get-ServiceFabricClusterUpgrade` o `sfctl cluster upgrade-status` per visualizzare lo stato. Il risultato visualizzato sarà simile al seguente.

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```azurecli
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Ottenere la versione del runtime del cluster
> * Aggiornare il runtime del cluster
> * Effettuare il monitoraggio dell'aggiornamento

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Eliminare un cluster](service-fabric-tutorial-delete-cluster.md)
