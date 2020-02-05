---
title: Azure VMware Solutions (AVS)-servizio
description: Viene fornita una panoramica del servizio AVS e dei concetti di.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024955"
---
# <a name="avs-service-overview"></a>Panoramica di AVS Service

Il servizio AVS consente di usare la soluzione VMware di Azure con AVS. La creazione del servizio consente di acquistare nodi, riservare nodi e creare cloud privati AVS. Il servizio AVS viene creato in ogni area di Azure in cui è disponibile il servizio AVS. Il servizio definisce la rete perimetrale della soluzione VMware di Azure tramite AVS. La rete perimetrale supporta servizi che includono VPN, ExpressRoute e connettività Internet ai cloud privati AVS.

## <a name="gateway-subnet"></a>Subnet gateway

È necessaria una subnet del gateway per ogni servizio AVS ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede un blocco CIDR/28. Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente AVS. Le reti che comunicano con AVS includono le reti locali e la rete virtuale di Azure. Una subnet del gateway non può essere eliminata dopo la creazione. La subnet del gateway viene rimossa quando il servizio viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio AVS in Azure](quickstart-create-cloudsimple-service.md).
