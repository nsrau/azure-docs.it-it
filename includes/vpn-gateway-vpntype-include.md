---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159304"
---
* **PolicyBased:** Nel modello di distribuzione classica le VPN PolicyBased sono state definite gateway con routing statico. Le VPN basate su criteri crittografano e reindirizzano i pacchetti tramite tunnel IPsec basati sui criteri IPsec configurati con le combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I criteri o selettori di traffico vengono in genere definiti come un elenco di accesso nella configurazione del dispositivo VPN. Il valore per una VPN basata su criteri è *PolicyBased*. Quando si usa una VPN PolicyBased, tenere presenti le limitazioni seguenti:
  
  * Le VPN PolicyBased possono essere usate **solo** su SKU del gateway Basic. Questo tipo di VPN non è compatibile con altri SKU del gateway.
  * Quando si usa una VPN PolicyBased, è disponibile solo 1 tunnel.
  * Queste VPN possono essere usate solo per connessioni S2S ed esclusivamente per determinate configurazioni. La maggior parte delle configurazioni di gateway VPN richiede una VPN RouteBased.
* **RouteBased**: Nel modello di distribuzione classica le VPN RouteBased sono state definite gateway con routing dinamico. Le VPN RouteBased usano "route" nella tabella di routing o di inoltro IP per reindirizzare i pacchetti nelle interfacce tunnel corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o il selettore di traffico per le VPN RouteBased vengono configurati come any-to-any (o caratteri jolly). Il valore per un tipo di VPN RouteBased è *RouteBased*.
