---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157566"
---
| **Fornitore** | **Famiglia di dispositivi** | **Versione del firmware** |
| --- | --- | --- |
|Cisco | ISR| iOS 15.1 (anteprima)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2 - Senza BGP) per ASA versione 9.8 o inferiore |
|Cisco | ASA | ASA RouteBased (IKEv2 - Senza BGP) per ASA versione 9.8 o superiore |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| VTI RouteBased EdgeOS versione 1.10.x|
|Ubiquiti| EdgeRouter| BGP RouteBased EdgeOS versione 1.10.x|

> [!NOTE]
> (*) Obbligatori: NarrowAzureTrafficSelectors (abilitare l'opzione UsePolicyBasedTrafficSelectors) e CustomAzurePolicies (IKE/IPsec)
>
