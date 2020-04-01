---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501368"
---
Le route definite dall'utente con una destinazione 0.0.0.0/0/0 e i gruppi di sicurezza di rete in GatewaySubnet **non sono supportati.** I gateway creati con questa configurazione verranno bloccati dalla creazione. I gateway richiedono l'accesso ai controller di gestione per funzionare correttamente. [La propagazione delle](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) route BGP deve essere impostata su "Abilitato" su GatewaySubnet per garantire la disponibilità del gateway. Se è impostato su disabled, il gateway non funzionerà.
