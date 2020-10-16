---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77111219"
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
> ( * ) obbligatorio: NarrowAzureTrafficSelectors (abilitare l'opzione UsePolicyBasedTrafficSelectors) e CustomAzurePolicies (IKE/IPsec)
>
