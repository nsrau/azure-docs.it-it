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
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838173"
---
Le route definite dall'utente con una destinazione 0.0.0.0/0 e gruppi in GatewaySubnet **non sono supportate**. I gateway creati con questa configurazione verranno bloccati dalla creazione. Per il corretto funzionamento dei gateway è necessario l'accesso ai controller di gestione.
