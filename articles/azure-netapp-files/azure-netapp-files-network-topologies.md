---
title: Linee guida per la pianificazione della rete di file di rete di Azure NetApp Documenti Microsoft
description: Vengono descritte le linee guida che consentono di progettare un'architettura di rete efficace usando File NetApp di Azure.Describes guidelines that can help you design an effective network architecture by using Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 8e6a1c3472c6b20b27cf181edbeeb96ab71eb58d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242487"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Linee guida per la pianificazione della rete per Azure NetApp Files

La pianificazione dell'architettura di rete è un elemento chiave della progettazione di qualsiasi infrastruttura di applicazione. Questo articolo consente di progettare un'architettura di rete efficace per i carichi di lavoro per trarre vantaggio dalle funzionalità avanzate dei file NetApp di Azure.This article helps you design an effective network architecture for your workloads to benefit from the rich capabilities of Azure NetApp Files.

I volumi file NetApp di Azure sono progettati per essere contenuti in una subnet con scopo speciale [denominata subnet delegata](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) all'interno della rete virtuale di Azure.NetApp Files volumes are designed to be contained in a special purpose subnet called a delegated subnet within your Azure Virtual Network. Pertanto, è possibile accedere ai volumi direttamente dalla rete virtuale, dalle reti virtuali con peering nella stessa area o da un gateway di rete virtuale (ExpressRoute o gateway VPN) in base alle esigenze. The subnet is dedicated to Azure NetApp Files and there is no connectivity to other Azure services or the Internet.

## <a name="considerations"></a>Considerazioni  

Quando si pianifica la rete File NetApp di Azure, è necessario tenere in considerazione alcune considerazioni.

### <a name="constraints"></a>Vincoli

Le funzionalità seguenti non sono attualmente supportate per i file NetApp di Azure:The features below are currently unsupported for Azure NetApp Files: 

* Gruppi di sicurezza di rete (NSG) applicati alla subnet delegataNetwork security groups (NSGs) applied to the delegated subnet
* Route definite dall'utente (UDR) con prefisso dell'indirizzo come subnet dei file di Azure NetAppUser-defined routes (UDRs) with address prefix as Azure NetApp files subnet
* Criteri di Azure (ad esempio, criteri di denominazione personalizzati) nell'interfaccia dei file netApp di AzureAzure policies (for example, custom naming policies) on the Azure NetApp Files interface
* Servizi di bilanciamento del carico per il traffico dei file NetApp di AzureLoad balancers for Azure NetApp Files traffic

Le restrizioni di rete seguenti si applicano ai file NetApp di Azure:The following network restrictions apply to Azure NetApp Files:

* Il numero di indirizzi IP in uso in una rete virtuale con file NetApp di Azure (incluse le reti virtuali con peering) non può superare 1000.The number of Ip in use in a VNet with Azure NetApp Files (including peered VNets) cannot exceed 1000. Stiamo lavorando per aumentare questo limite per soddisfare le esigenze di scalabilità dei clienti. Nel frattempo, se avete bisogno di più IP, contattare il nostro team di supporto con il vostro caso d'uso e limite richiesto.
* In ogni rete virtuale di Azure (VNet), è possibile delegare solo una subnet in Azure NetApp Files.


### <a name="supported-network-topologies"></a>Topologie di rete supportate

The following table describes the network topologies supported by Azure NetApp Files.  Vengono inoltre descritte le soluzioni alternative per le topologie non supportate. 

|    Topologie    |    È supportato    |     Soluzione alternativa    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Connettività al volume in una rete virtuale locale    |    Sì    |         |
|    Connettività al volume in una rete virtuale con peered (stessa area)    |    Sì    |         |
|    Connettività al volume in una rete virtuale con peering (area trasversale o peering globale)Connectivity to volume in a peered VNet (Cross region or global peering)    |    No    |    nessuno    |
|    Connettività a un volume tramite gateway ExpressRouteConnectivity to a volume over ExpressRoute gateway    |    Sì    |         |
|    Connettività da locale a un volume in una rete virtuale spoke tramite gateway ExpressRoute e peering della rete virtuale con transito gateway    |    Sì    |        |
|    Connettività da locale a un volume in una rete virtuale spoke tramite gateway VPN    |    Sì    |         |
|    Connettività da locale a un volume in una rete virtuale spoke tramite gateway VPN e peering della rete virtuale con transito del gateway    |    Sì    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Rete virtuale per i volumi dei file NetApp di AzureVirtual network for Azure NetApp Files volumes

In questa sezione vengono illustrati i concetti che consentono di pianificare la rete virtuale.

### <a name="azure-virtual-networks"></a>Reti virtuali di Azure

Prima di eseguire il provisioning di un volume File NetApp di Azure, è necessario creare una rete virtuale di Azure o usarne una già esistente nella sottoscrizione. La rete virtuale definisce il limite di rete del volume.  Per altre informazioni sulla creazione di reti virtuali, vedere la documentazione relativa alla rete virtuale di Azure.For more information on creating virtual networks, see the [Azure Virtual Network documentation.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

### <a name="subnets"></a>Subnet

Le subnet segmentano la rete virtuale in spazi di indirizzi separati utilizzabili dalle risorse di Azure in esse in esse.  I volumi dei file NetApp di Azure sono contenuti in una subnet per scopi speciali [denominata subnet delegata.](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) 

La delega della subnet concede autorizzazioni esplicite al servizio File NetApp di Azure per creare risorse specifiche del servizio nella subnet.  Utilizza un identificatore univoco nella distribuzione del servizio. In this case, a network interface is created to enable connectivity to Azure NetApp Files.

Se si usa una nuova rete virtuale, è possibile creare una subnet e delegare la subnet ai file NetApp di Azure seguendo le istruzioni in [Delegare una subnet ai](azure-netapp-files-delegate-subnet.md)file NetApp di Azure. È inoltre possibile delegare una subnet vuota esistente che non è già delegata ad altri servizi.

Se la rete virtuale è sottoposta a peered con un'altra rete virtuale, è possibile espandere lo spazio di indirizzi della rete virtuale. Per questo motivo, la nuova subnet delegata deve essere creata all'interno dello spazio di indirizzi della rete virtuale. Se è necessario estendere lo spazio degli indirizzi, è necessario eliminare il peering della rete virtuale prima di espandere lo spazio di indirizzi.

### <a name="udrs-and-nsgs"></a>UDR e NSG

Le route definite dall'utente (UDR) e i gruppi di sicurezza di rete non sono supportati nelle subnet delegate per i file NetApp di Azure.User-defined routes (UDRs) and Network security groups (NSGs) are not supported on delegated subnets for Azure NetApp Files.

Per risolvere il problema, è possibile applicare gruppi di sicurezza di rete ad altre subnet che consentono o negano il traffico da e verso la subnet delegata File NetApp di Azure.As a workaround, you can apply NSGs to other subnets that want or deny the traffic to and from the Azure NetApp Files delegated subnet.  

## <a name="azure-native-environments"></a>Ambienti nativi di AzureAzure native environments

Il diagramma seguente illustra un ambiente nativo di Azure:The following diagram illustrates an Azure-native environment:

![Ambiente di rete nativo di AzureAzure-native networking environment](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Rete virtuale locale

Uno scenario di base consiste nel creare o connettersi a un volume file NetApp di Azure da una macchina virtuale (VM) nella stessa rete virtuale. Per la rete virtuale 2 nel diagramma precedente, il Volume 1 viene creato in una subnet delegata e può essere montato sulla macchina virtuale 1 nella subnet predefinita.

### <a name="vnet-peering"></a>Peering reti virtuali

Se si dispone di reti virtuali aggiuntive nella stessa area che devono accedere alle risorse reciproche, le reti virtuali possono essere connesse usando il peering della rete virtuale per abilitare la connettività sicura tramite l'infrastruttura di Azure.If you have additional VNets in the same region that need access to each other's resources, the VNets can be connected using [VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) to enable secure connectivity through the Azure infrastructure. 

Considerare VNet 2 e VNet 3 nel diagramma precedente. Se la macchina virtuale 1 deve connettersi alla macchina virtuale 2 o al volume 2 o se la macchina virtuale 2 deve connettersi alla macchina virtuale 1 o al volume 1, è necessario abilitare il peering della rete virtuale tra VNet 2 e VNet 3.If VM 1 needs to connect to VM 2 or Volume 2, or if VM 2 needs to connect to VM 1 or Volume 1, then you need to enable VNet peering between VNet 2 and VNet 3. 

Inoltre, si consideri uno scenario in cui la rete virtuale 1 viene sottoposta a peered con VNet 2 e la rete virtuale 2 viene sottoposta a peered con VNet 3 nella stessa area. Le risorse di VNet 1 possono connettersi alle risorse in VNet 2, ma non possono connettersi alle risorse in VNet 3, a meno che non si esemi la rete virtuale 1 e la rete virtuale 3. 

Nel diagramma precedente, anche se la macchina virtuale 3 può connettersi al volume 1, la macchina virtuale 4 non può connettersi al volume 2.In the diagram above, although VM 3 can connect to Volume 1, VM 4 cannot connect to Volume 2.  Il motivo è che le reti virtuali a raggi non sono sottoposte a peering e il _routing di transito non è supportato tramite peering della rete virtuale._

## <a name="hybrid-environments"></a>Ambienti ibridi

Il diagramma seguente illustra un ambiente ibrido:The following diagram illustrates a hybrid environment: 

![Ambiente di rete ibrido](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

In the hybrid scenario, applications from on-premises datacenters need access to the resources in Azure.  Questo è il caso se si vuole estendere il data center ad Azure o se si vogliono usare i servizi nativi di Azure o per il ripristino di emergenza. Per informazioni su come connettere più risorse locali alle risorse in Azure tramite una VPN da sito a sito o ExpressRoute, vedere Opzioni di pianificazione del [gateway VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)

In una topologia hub-spoke ibrida, la rete virtuale hub in Azure funge da punto centrale di connettività alla rete locale. Gli spoke sono reti virtuali con l'hub e possono essere usate per isolare i carichi di lavoro.

A seconda della configurazione, è possibile connettere le risorse locali alle risorse nell'hub e negli spoke.

Nella topologia illustrata in precedenza, la rete locale è connessa a una rete virtuale hub in Azure e sono presenti 2 reti virtuali a raggio nella stessa area con peering con la rete virtuale dell'hub.  In questo scenario, le opzioni di connettività supportate per i volumi di file NetApp di Azure sono le seguenti:In this scenario, the connectivity options supported for Azure NetApp Files volumes are as follows:

* Le risorse locali VM 1 e VM 2 possono connettersi al volume 1 nell'hub tramite una VPN da sito a sito o un circuito ExpressRoute.On-premises resources VM 1 and VM 2 can connect to Volume 1 in the hub over a site-to-site VPN or ExpressRoute circuit. 
* Le risorse locali VM 1 e VM 2 possono connettersi al Volume 2 o al Volume 3 tramite una VPN da sito a sito e un peering vnet regionale.
* La VM 3 nella rete virtuale dell'hub può connettersi al volume 2 nella rete virtuale a raggi 1 e nel volume 3 nella rete virtuale a raggi 2.
* VM 4 dalla rete virtuale a raggi 1 e dalla MACCHINA VIRTUALE 5 della rete virtuale a raggi 2 può connettersi al volume 1 nella rete virtuale dell'hub.

VM 4 nella rete virtuale a raggi 1 non può connettersi al volume 3 nella rete virtuale a raggi 2. Inoltre, VM 5 nella rete virtuale 2 spoke non può connettersi al volume 2 nella rete virtuale a raggi 1. Questo è il caso perché le reti virtuali a raggi non sono sottoposte a peering e il _routing di transito non è supportato tramite peering della rete virtuale._

## <a name="next-steps"></a>Passaggi successivi

[Delegare una subnet ad Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
