---
title: Rimuovere un tipo di nodo in Azure Service Fabric | Microsoft Docs
description: Informazioni su come rimuovere un tipo di nodo da un cluster di Service Fabric in esecuzione in Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo
ms.openlocfilehash: 193a24aebff8f7de60752e53bbc1b18dd5c54f33
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787090"
---
# <a name="remove-a-service-fabric-node-type"></a>Rimuovere un tipo di nodo di Service Fabric
Questo articolo descrive come ridimensionare un cluster di Azure Service Fabric rimuovendo un tipo di nodo esistente da un cluster. Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente. Dopo aver creato un cluster di Service Fabric, è possibile ridimensionare un cluster orizzontalmente rimuovendo un tipo di nodo (set di scalabilità di macchine virtuali) e tutti i relativi nodi.  È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.  Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Uso [Remove-AzServiceFabricNodeType](https://docs.microsoft.com/powershell/module/az.servicefabric/remove-azservicefabricnodetype) per rimuovere un tipo di nodo di Service Fabric.

Le tre operazioni che si verificano quando viene chiamato Remove-AzServiceFabricNodeType sono:
1.  Viene eliminato il set di scalabilità di macchine virtuali che si trova dietro il tipo di nodo.
2.  Il tipo di nodo viene rimosso dal cluster.
3.  Per ogni nodo compreso nel tipo di nodo, viene rimosso dal sistema l'intero stato relativo al nodo. Se nel nodo sono presenti servizi, questi vengono prima spostati in un altro nodo. Se Cluster Manager non è in grado di trovare un nodo per una replica o un servizio, l'operazione viene posticipata o bloccata.

> [!WARNING]
> Non è consigliabile usare frequentemente il comando Remove-AzServiceFabricNodeType per rimuovere un tipo di nodo da un cluster di produzione. È un comando molto pericoloso perché elimina la risorsa set di scalabilità di macchine virtuali presente dietro il tipo di nodo. 

## <a name="durability-characteristics"></a>Caratteristiche di durabilità
Quando si usa Remove-AzServiceFabricNodeType Safety viene definita la priorità rispetto alla velocità. Il tipo di nodo deve avere un [livello di durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Silver o Gold, perché:
- Bronze non offre alcuna garanzia in merito al salvataggio delle informazioni sullo stato.
- I livelli di durabilità Silver e Gold consentono di intercettare qualsiasi modifica al set di scalabilità.
- Il livello Gold offre anche il controllo degli aggiornamenti di Azure nell'ambito del set di scalabilità.

Service Fabric "orchestra" le modifiche e gli aggiornamenti sottostanti in modo che i dati non vadano persi. Quando si rimuove un nodo con livello di durabilità Bronze, tuttavia, è possibile che vadano perse le informazioni sullo stato. Se si sta rimuovendo un tipo di nodo primario e l'applicazione è senza stato, il livello Bronze è accettabile. Se si eseguono carichi di lavoro con stato in fase di produzione, la configurazione minima deve essere Silver. Analogamente, per gli scenari di produzione, il tipo di nodo primario deve essere sempre Silver o Gold.

### <a name="more-about-bronze-durability"></a>Altre informazioni sul livello di durabilità Bronze

Quando si rimuove un tipo di nodo Bronze, tutti i nodi appartenenti a quel tipo di nodo vengono immediatamente disattivati. Service Fabric non riesce a intercettare eventuali aggiornamenti al set di scalabilità dei nodi Bronze e tutte le macchine virtuali vengono immediatamente disattivate. Se nei nodi fossero presenti elementi con stato, i dati andrebbero persi. Poiché tutti i nodi di Service Fabric fanno parte dell'anello, anche se fossero presenti solo elementi senza stato, andrebbero comunque persi i dati di tutti i nodi vicini, con una destabilizzazione del cluster stesso.

## <a name="recommended-node-type-removal-process"></a>Processo consigliato per la rimozione dei tipi di nodo

Per rimuovere il tipo di nodo, eseguire il cmdlet [Remove-AzServiceFabricNodeType](/powershell/module/az.servicefabric/remove-azservicefabricnodetype).  Il completamento del cmdlet richiede qualche minuto.  Eseguire quindi [Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) in ognuno dei nodi da rimuovere.

```powershell
$groupname = "mynodetype"
$nodetype = "nt2vm"
$clustername = "mytestcluster"

Remove-AzServiceFabricNodeType -Name $clustername  -NodeType $nodetype -ResourceGroupName $groupname

Connect-ServiceFabricCluster -ConnectionEndpoint mytestcluster.eastus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <thumbprint> `
          -FindType FindByThumbprint -FindValue <thumbprint> `
          -StoreLocation CurrentUser -StoreName My

$nodes = Get-ServiceFabricNode | Where-Object {$_.NodeType -eq $nodetype} | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending

Foreach($node in $nodes)
{
    Remove-ServiceFabricNodeState -NodeName $node.NodeName -TimeoutSec 300 -Force 
}
```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [caratteristiche di durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) dei cluster.
- Altre informazioni sui [tipi di nodo e i set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md).
- Altre informazioni sul [ridimensionamento di un cluster di Service Fabric](service-fabric-cluster-scaling.md).
