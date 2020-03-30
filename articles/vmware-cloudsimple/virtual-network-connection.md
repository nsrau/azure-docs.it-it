---
title: Connettere la rete virtuale di Azure a CloudSimple usando ExpressRoute - Soluzione VMware di Azure da CloudSimpleConnect Azure virtual network to CloudSimple using ExpressRoute - Azure VMware Solution by CloudSimple
description: Descrive come ottenere informazioni di peering per una connessione tra la rete virtuale di Azure e l'ambiente CloudSimpleDescribes how to obtain peering information for a connection between the Azure virtual network and your CloudSimple environment
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563984"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Connettere la rete virtuale di Azure a CloudSimple usando ExpressRouteConnect Azure virtual network to CloudSimple using ExpressRoute

È possibile estendere la rete di cloud privato alla rete virtuale di Azure e alle risorse di Azure.You can extend your Private Cloud network to your Azure virtual network and Azure resources. Una connessione ExpressRoute consente di accedere alle risorse in esecuzione nella sottoscrizione di Azure dal cloud privato.

## <a name="request-authorization-key"></a>Chiave di autorizzazione richiesta

È necessaria una chiave di autorizzazione per la connessione ExpressRoute tra il cloud privato e la rete virtuale di Azure.An authorization key is required for the ExpressRoute connection between your Private Cloud and the Azure virtual network. Per ottenere una chiave, presentare un ticket con <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">supporto</a>tecnico .  Utilizzare le seguenti informazioni nella richiesta:

* Tipo di problema: **Tecnico**
* Sottoscrizione: **selezionare la sottoscrizione in cui viene distribuito il servizio CloudSimpleSubscription: Select the subscription where CloudSimple service is deployed**
* Servizio: **VMware Solution di CloudSimple**
* Tipo di problema: **Richiesta di servizio**
* Sottotipo di problema: chiave di autorizzazione per la connessione VNET di AzureProblem subtype: **Authorization key for Azure VNET connection**
* Oggetto: Richiesta di chiave di autorizzazione per la connessione VNET di **AzureSubject: Request for authorization key for Azure VNET connection**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Ottenere informazioni sul peering dal portale CloudSimpleGet peering information from CloudSimple portal

Per configurare la connessione, è necessario stabilire una connessione tra la rete virtuale di Azure e l'ambiente CloudSimple.To set up the connection, you must establish a connection between Azure virtual network and your CloudSimple environment.  Come parte della procedura, è necessario fornire l'URI del circuito peer e la chiave di autorizzazione. Ottenere l'URI e la chiave di autorizzazione dal [portale CloudSimple](access-cloudsimple-portal.md).  Selezionare **Rete** nel menu laterale e quindi Selezionare **Connessione di rete di Azure**. In alternativa, selezionare **Account** nel menu laterale e quindi selezionare **Connessione di rete di Azure**.

Copiare l'URI del circuito peer e per la chiave di autorizzazione per ognuna delle aree usando l'icona di *copia.* Per ogni area CloudSimple che vuoi connettere:

1. Fare clic su **Copia** per copiare l'URI. Incollarlo in un file in cui può essere disponibile per l'aggiunta al portale di Azure.Paste it into a file where it can be available to add to the Azure portal.  
2. Fare clic su **Copia** per copiare la chiave di autorizzazione e incollarla anche nel file.

Copiare la chiave di autorizzazione e l'URI del circuito peer in stato **Disponibile.**  **Lo** stato utilizzato indica che la chiave è già stata utilizzata per creare una connessione di rete virtuale.

![Pagina Connessione rete virtuale](media/virtual-network-connection.png)

Per informazioni dettagliate sulla configurazione della rete virtuale di Azure in CloudSimple, vedere Connettere l'ambiente CloudSimple Private Cloud alla rete virtuale di Azure usando ExpressRoute.For details on setting up the Azure virtual network to CloudSimple link, see [Connect your CloudSimple Private Cloud environment to the Azure virtual network using ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Passaggi successivi

* [Azure virtual network connection to Private Cloud](azure-expressroute-connection.md)
* [Connettersi alla rete locale usando Azure ExpressRouteConnect to on-premises network using Azure ExpressRoute](on-premises-connection.md)
