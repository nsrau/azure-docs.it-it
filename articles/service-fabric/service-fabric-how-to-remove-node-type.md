---
title: Come rimuovere un tipo di nodo in Azure Service Fabric | Microsoft Docs
description: Informazioni su come rimuovere un tipo di nodo in Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53981620"
---
# <a name="remove-a-service-fabric-node-type"></a>Rimuovere un tipo di nodo di Service Fabric

Usare [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) per rimuovere un tipo di nodo di Service Fabric.

Quando viene chiamato Remove-AzureRmServiceFabricNodeType, hanno luogo due operazioni:
1.  Viene eliminato il set di scalabilità di macchine virtuali che si trova dietro il tipo di nodo.
2.  Per tutti i nodi compresi nel tipo di nodo, viene rimosso dal sistema l'intero stato relativo al nodo. Se nel nodo sono presenti servizi, questi vengono prima spostati in un altro nodo. Se Cluster Manager non è in grado di trovare un nodo per una replica o un servizio, l'operazione viene posticipata o bloccata.

> [!NOTE]
> Non è consigliabile usare frequentemente il comando Remove-AzureRmServiceFabricNodeType per rimuovere un tipo di nodo da un cluster di produzione. In questo caso, infatti, è un comando molto pericoloso poiché elimina la risorsa Set di scalabilità di macchine virtuali presente dietro il tipo di nodo. Quando si chiama Remove-AzureRmServiceFabricNodeType, il sistema non può sapere se ci si è accertati che la rimozione venga eseguita in sicurezza. 

## <a name="durability-characteristics"></a>Caratteristiche di durabilità
Quando si usa Remove-AzureRmServiceFabricNodeType, la sicurezza ha la priorità rispetto alla velocità. Sono consigliate le [caratteristiche di durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Silver e Gold per i motivi seguenti:
- Bronze non offre alcuna garanzia in merito al salvataggio delle informazioni sullo stato.
- I livelli di durabilità Silver e Gold consentono di intercettare qualsiasi modifica al set di scalabilità di macchine virtuali.
- Il livello Gold offre anche il controllo degli aggiornamenti di Azure nell'ambito del set di scalabilità di macchine virtuali.

Service Fabric "orchestra" le modifiche e gli aggiornamenti sottostanti in modo che i dati non vadano persi. Quando si rimuove un nodo con livello di durabilità Bronze, tuttavia, è possibile che vadano perse le informazioni sullo stato. Se si sta rimuovendo un tipo di nodo primario e l'applicazione è senza stato, il livello Bronze è accettabile. Se si eseguono carichi di lavoro con stato in fase di produzione, la configurazione minima deve essere Silver. Analogamente, per gli scenari di produzione, il tipo di nodo primario deve essere sempre Silver o Gold.

### <a name="more-about-bronze-durability"></a>Altre informazioni sul livello di durabilità Bronze

Quando si rimuove un tipo di nodo Bronze, tutti i nodi appartenenti a quel tipo di nodo vengono immediatamente disattivati. Service Fabric non è in grado di intercettare eventuali aggiornamenti al set di scalabilità di macchine virtuali dei nodi Bronze e tutte le macchine virtuali vengono immediatamente disattivate. Se nei nodi fossero presenti elementi con stato, i dati andrebbero persi. Poiché tutti i nodi del Service Fabric fanno parte dell'anello, anche se fossero presenti solo elementi senza stato, andrebbero comunque persi i dati di tutti i nodi vicini, con ripercussioni sul cluster stesso.

È diverso invece se si rimuove un singolo nodo, poiché, in teoria, è possibile rimuovere un nodo alla volta. In questo caso, attendere il trasferimento delle repliche e dei servizi e aspettare che il sistema si stabilizzi prima di rimuovere il nodo successivo, e così via.  Se, al contrario, si rimuovono più nodi contemporaneamente, è possibile che il cluster venga disattivato (poiché Service Fabric non è in grado di intercettare alcun aggiornamento del set di stabilità di macchine virtuali con la durabilità Bronze).

## <a name="recommended-node-type-removal-process"></a>Processo consigliato per la rimozione dei tipi di nodo

Per rimuovere il tipo di nodo nel modo più rapido e sicuro possibile:
1.  Se si usa il livello di durabilità Bronze o si vuole evitare che il sistema sposti applicazioni contenenti informazioni sullo stato che andrebbero perse con l'eliminazione del tipo nodo, liberare prima dai dati con stato i nodi interessati dalla rimozione del tipo di nodo.
2.  Eseguire [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) in ognuno dei nodi da rimuovere.
3.  Eseguire [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) per le macchine virtuali interessate dalla rimozione del tipo di nodo.
4. Eseguire [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) per rimuovere il tipo di nodo.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [caratteristiche di durabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) dei cluster.
- Altre informazioni sui [tipi di nodo e i set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md).
- Altre informazioni sul [ridimensionamento di un cluster di Service Fabric](service-fabric-cluster-scaling.md).