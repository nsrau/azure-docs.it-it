---
title: File di inclusione
description: File di inclusione
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 9fe9ef5549ced3b73d18d553fa0b62ec019684fe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553887"
---
| Risorsa                                | Limite        |
|-----------------------------------------|------------------------------|
| Prefissi di indirizzi VNet                   | 600 per gateway VPN          |
| Route BGP aggregate                    | 4.000 per gateway VPN        |
| Prefissi di indirizzo del gateway di rete locale  | 1000 per gateway di rete locale               |
| connessioni da sito a sito                         | [Dipende dallo SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)|
| Connessioni da punto a sito                         | [Dipende dallo SKU del gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |
| Limite Route P2S-IKEv2                 | 256 per non Windows **/** 25 per Windows           |
| Limite Route P2S-OpenVPN               | 1000                         |
| Max. flows                              | 100K per VpnGw1/AZ  **/**  512k per VpnGw2-4/AZ|