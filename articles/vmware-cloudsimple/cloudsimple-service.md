---
title: Soluzione VMware per servizio CloudSimple
description: Viene descritta la panoramica del servizio e dei concetti relativi a CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812503"
---
# <a name="cloudsimple-service-overview"></a>Panoramica del servizio CloudSimple

Con il servizio CloudSimple, è possibile usare la soluzione VMware di Azure per CloudSimple. Dopo aver creato il servizio, è possibile effettuare il provisioning di nodi, riservare nodi e creare cloud privati. Si crea il servizio CloudSimple in ogni area di Azure in cui è disponibile il servizio CloudSimple. Il servizio definisce la rete perimetrale della soluzione VMware di Azure tramite CloudSimple. La rete perimetrale supporta servizi che includono VPN, Azure ExpressRoute e la connettività Internet ai cloud privati.

## <a name="gateway-subnet"></a>Subnet del gateway

È necessaria una subnet del gateway per ogni servizio CloudSimple ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure. Una subnet del gateway non può essere eliminata dopo la creazione. La subnet del gateway viene rimossa quando il servizio viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple in Azure](quickstart-create-cloudsimple-service.md).
