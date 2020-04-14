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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81274884"
---
Le route definite dall'utente con una destinazione 0.0.0.0/0/0 e i gruppi di sicurezza di rete in GatewaySubnet **non sono supportati.** I gateway creati con questa configurazione verranno bloccati dalla creazione. I gateway richiedono l'accesso ai controller di gestione per funzionare correttamente. [La propagazione delle](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) route BGP deve essere impostata su "Abilitato" su GatewaySubnet per garantire la disponibilità del gateway. Se è impostato su disabled, il gateway non funzionerà.
