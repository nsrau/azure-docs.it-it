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
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71838173"
---
Le route definite dall'utente con una destinazione 0.0.0.0/0 e i gruppi di sicurezza di rete su GatewaySubnet **non sono supportati.** I gateway creati con questa configurazione verranno bloccati dalla creazione. I gateway richiedono l'accesso ai controller di gestione per funzionare correttamente.
