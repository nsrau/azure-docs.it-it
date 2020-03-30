---
title: Soluzione VMware di CloudSimple - Connessioni di rete di Azure
description: Informazioni sulla connessione della rete virtuale di Azure alla rete di aree CloudSimpleLearn about connecting your Azure virtual network to your CloudSimple region network
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025079"
---
# <a name="azure-network-connections-overview"></a>Panoramica delle connessioni di rete di AzureAzure network connections overview

Quando si crea un servizio CloudSimple in un'area e si creano nodi, è possibile:When you create a CloudSimple service in a region and create nodes, you can:

* Richiedere un circuito Azure ExpressRoute e collegarlo alla rete CloudSimple in tale area.
* Connettere la rete di aree CloudSimple alla rete virtuale di Azure o alla rete locale usando Azure ExpressRoute.Connect your CloudSimple region network to your Azure virtual network or your on-premises network using Azure ExpressRoute.
* Fornire l'accesso ai servizi in esecuzione nella sottoscrizione di Azure o nella rete locale dall'ambiente cloud privato.

La connessione ExpressRoute è un'elevata larghezza di banda con bassa latenza.

## <a name="benefits"></a>Vantaggi

La connessione di rete di Azure consente di:Azure network connection allows you to:

* Usare Azure come destinazione di backup per le macchine virtuali nel cloud privato.
* Distribuire i server KMS nella sottoscrizione di Azure per crittografare l'archivio dati vSAN del cloud privato.
* Usare applicazioni ibride in cui il livello Web dell'applicazione viene eseguito nel cloud pubblico mentre i livelli applicazione e database vengono eseguiti nel cloud privato.

## <a name="azure-virtual-network-connection"></a>Connessione di rete virtuale di AzureAzure virtual network connection

I cloud privati possono essere connessi alle risorse di Azure usando ExpressRoute.Private Clouds can be connected to your Azure resources using ExpressRoute.  La connessione ExpressRoute consente di accedere alle risorse in esecuzione nella sottoscrizione di Azure dal cloud privato.  Questa connessione consente di estendere la rete cloud privata alla rete virtuale di Azure.This connection allows you to extend your Private Cloud network to your Azure virtual network.  Le route dalla rete CloudSimple verranno scambiate con la rete virtuale di Azure tramite BGP.  Se è configurato il peering di rete virtuale, tutte le reti virtuali con peering saranno accessibili dalla rete CloudSimple.If you have virtual network peering configured, all peered virtual networks will be accessible from your CloudSimple network.

![Connessione ExpressRoute di Azure alla rete virtualeAzure ExpressRoute Connection to virtual network](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>Connessione ExpressRoute alla rete localeExpressRoute connection to on-premises network

È possibile connettere il circuito di Azure ExpressRoute esistente all'area CloudSimple.You can connect your existing Azure ExpressRoute circuit to your CloudSimple region. La funzionalità Di reach globale ExpressRoute viene usata per connettere i due circuiti tra loro.  Viene stabilita una connessione tra i circuiti locale e CloudSimple ExpressRoute.  Questa connessione consente di estendere le reti locali alla rete Cloud privata. I percorsi dalla rete CloudSimple verranno scambiati tramite BGP con la rete locale.

![Connessione ExpressRoute locale - Copertura globale](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Connessione alla rete locale e alla rete virtuale di AzureConnection to on-premises network and Azure virtual network

Le connessioni alla rete locale e alla rete virtuale di Azure possono coesistere dalla rete CloudSimple.Connections to on-premises network and Azure virtual network can coexist from your CloudSimple network.  La connessione usa BGP per scambiare route tra la rete locale, la rete virtuale di Azure e la rete CloudSimple.The connection uses BGP to exchange routes between on-premises network, Azure virtual network, and CloudSimple network.  Quando si connette la rete CloudSimple alla rete virtuale di Azure in presenza di connessione A portata globale, le route della rete virtuale di Azure saranno visibili nella rete locale.  Lo scambio di route avviene in Azure tra i router perimetrali.

![Connessione ExpressRoute locale con connessione di rete virtuale di AzureOn-premises ExpressRoute Connection with Azure virtual network connection](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Considerazioni importanti

La connessione alla rete CloudSimple dalla rete locale e dalla rete virtuale di Azure consente lo scambio di route tra tutte le reti.

* La rete virtuale di Azure sarà visibile sia dalla rete locale che dalla rete CloudSimple.Azure virtual network will be visible from both on-premises network and CloudSimple network.
* Se si è connessi alla rete virtuale di Azure dalla rete locale, la connessione alla rete CloudSimple tramite Global Reach consentirà l'accesso alle reti virtuali dalla rete CloudSimple.If you have connected to your Azure virtual network from on-premises network, connection to CloudSimple network using Global Reach will allow access to virtual networks from CloudSimple network.
* Gli indirizzi di subnet **non devono** sovrapporsi tra le reti connesse.
* CloudSimple **non** annuncierà la route predefinita per le connessioni ExpressRoute
* Se il router locale annuncia la route predefinita, il traffico dalla rete CloudSimple e dalla rete virtuale di Azure utilizzerà la route predefinita annunciata.  Di conseguenza, non è possibile accedere alle macchine virtuali in Azure usando indirizzi IP pubblici.

## <a name="next-steps"></a>Passaggi successivi

* [Connettere la rete virtuale di Azure a CloudSimple usando ExpressRouteConnect Azure virtual network to CloudSimple using ExpressRoute](virtual-network-connection.md)
* [Connettersi da locale a CloudSimple usando ExpressRouteConnect from on-premises to CloudSimple using ExpressRoute](on-premises-connection.md)
