---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81274884"
---
Le route definite dall'utente con destinazione 0.0.0.0/0 e gruppi di sicurezza di rete in GatewaySubnet **non sono supportate**. I gateway creati con questa configurazione verranno bloccati. Per il corretto funzionamento è necessario che i gateway possano accedere ai controller di gestione. Per assicurare la disponibilità del gateway, l'opzione [Propagazione route BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) deve essere impostata su "Abilitato" in GatewaySubnet. Se è impostata su Disabilitato, il gateway non funziona.
