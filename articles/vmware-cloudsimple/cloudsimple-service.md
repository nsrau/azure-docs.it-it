---
title: Soluzione VMware dal servizio CloudSimple
description: Descrive la panoramica del servizio CloudSimple e concetti.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0bebc68129ee2ff79241bcefec1ce0c3ca0b472d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595307"
---
# <a name="cloudsimple-service-overview"></a>Panoramica del servizio CloudSimple

Con il servizio CloudSimple, è possibile usare Azure VMware Solution by CloudSimple. Dopo aver creato il servizio, è possibile effettuare il provisioning di nodi, di riservarlo nodi e creare cloud privati. Si crea il servizio CloudSimple in ogni area di Azure in cui il servizio CloudSimple è disponibile. Il servizio definisce la rete perimetrale di Azure VMware Solution by CloudSimple. La rete perimetrale supporta i servizi che includono la connettività internet per i cloud privati, Azure ExpressRoute e VPN.

## <a name="gateway-subnet"></a>Subnet gateway

Una subnet del gateway è necessaria per ogni servizio CloudSimple ed è univoca nell'area in cui viene creato. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede / 28 blocco CIDR. Lo spazio degli indirizzi di subnet del gateway deve essere univoco. Non deve sovrapporsi con reti che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e reti virtuali di Azure. Impossibile eliminare una subnet del gateway dopo averlo creato. La subnet del gateway viene rimossa quando il servizio viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple in Azure](quickstart-create-cloudsimple-service.md).
