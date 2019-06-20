---
title: Soluzione VMware dal servizio CloudSimple
description: Descrive la panoramica del servizio CloudSimple e concetti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6a4c0bc6070d372a279b74f81ac1f84f565559c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165234"
---
# <a name="cloudsimple-service-overview"></a>Panoramica del servizio CloudSimple

Con il servizio CloudSimple, è possibile usare Azure VMware Solution by CloudSimple. Dopo aver creato il servizio, è possibile effettuare il provisioning di nodi, di riservarlo nodi e creare cloud privati. Si crea il servizio CloudSimple in ogni area di Azure in cui il servizio CloudSimple è disponibile. Il servizio definisce la rete perimetrale di Azure VMware Solution by CloudSimple. La rete perimetrale supporta i servizi che includono la connettività internet per i cloud privati, Azure ExpressRoute e VPN.

## <a name="gateway-subnet"></a>Subnet gateway

Una subnet del gateway è necessaria per ogni servizio CloudSimple ed è univoca nell'area in cui viene creato. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede / 28 blocco CIDR. Lo spazio degli indirizzi di subnet del gateway deve essere univoco. Non deve sovrapporsi con reti che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure. Impossibile eliminare una subnet del gateway dopo averlo creato. La subnet del gateway viene rimossa quando il servizio viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple in Azure](quickstart-create-cloudsimple-service.md).
