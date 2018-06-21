---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0377548a3b026657ae3282801523b1c0c6731265
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236691"
---
A partire dal 1 luglio 2018, verrà rimosso il supporto per TLS 1.0 e 1.1 da Gateway VPN di Azure. Gateway VPN supporterà solo TLS 1.2. Sono interessate solo le connessioni da punto a sito. Le connessioni da sito a sito non saranno interessate. Se si usa TLS per le reti VPN da punto a sito nei client Windows 10, non è necessario intervenire in alcun modo. Se si usa TLS per le connessioni da punto a sito nei client Windows 7 e Windows 8, vedere le [domande frequenti su Gateway VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) per istruzioni per l'aggiornamento.