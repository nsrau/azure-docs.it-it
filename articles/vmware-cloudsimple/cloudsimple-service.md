---
title: Soluzione Azure VMware di CloudSimple - Service
description: Fornisce una panoramica del servizio e dei concetti di CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024955"
---
# <a name="cloudsimple-service-overview"></a>Panoramica del servizio CloudSimple

Il servizio CloudSimple consente di usare La soluzione Azure VMware di CloudSimple.The CloudSimple service allows you to consume Azure VMware Solution by CloudSimple.  La creazione del servizio consente di acquistare nodi, riservare nodi e creare cloud privati.  Il servizio CloudSimple viene creato in ogni area di Azure in cui è disponibile il servizio CloudSimple.You create the CloudSimple service in each Azure region where the CloudSimple service is available. The service defines the edge network of Azure VMware Solution by CloudSimple. La rete perimetrale supporta servizi che includono VPN, ExpressRoute e connettività Internet ai cloud privati.

## <a name="gateway-subnet"></a>Subnet gateway

È necessaria una subnet del gateway per ogni servizio CloudSimple ed è univoca per l'area in cui è stata creata. La subnet del gateway viene utilizzata durante la creazione della rete perimetrale e richiede un blocco CIDR /28.  Lo spazio di indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure.The networks that communicate with CloudSimple include on-premises networks and Azure virtual network.  Una subnet del gateway non può essere eliminata una volta creata.  La subnet del gateway viene rimossa quando il servizio viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple in Azure.](quickstart-create-cloudsimple-service.md)
