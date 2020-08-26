---
title: includere file
description: File di inclusione
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854085"
---
| Risorsa                                | Limite        |
|-----------------------------------------|------------------------------|
| Prefissi di indirizzi VNet                   | 600 per gateway VPN          |
| Route BGP aggregate                    | 4.000 per gateway VPN        |
| Prefissi di indirizzo del gateway di rete locale  | 1000 per gateway di rete locale               |
| connessioni da sito a sito                         | [Dipende dallo SKU del gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| Connessioni da punto a sito                         | [Dipende dallo SKU del gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| Limite Route P2S-IKEv2                 | 256 per non Windows **/** 25 per Windows           |
| Limite Route P2S-OpenVPN               | 1000                         |
| Max. flows                              | 100K per VpnGw1/AZ  **/**  512k per VpnGw2-4/AZ|