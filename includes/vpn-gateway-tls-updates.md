---
title: file di inclusione
description: file di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "67179971"
---
>[!NOTE]
>A partire dal 1 luglio 2018, verrà rimosso il supporto per TLS 1.0 e 1.1 da Gateway VPN di Azure. Gateway VPN supporterà solo TLS 1.2. Per gestire il supporto, vedere gli [aggiornamenti per abilitare il supporto per TLS 1.2](#tls1).

Inoltre, anche gli algoritmi legacy seguenti saranno deprecati per TLS dal 1 luglio 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="tls1"></a>Come si abilita il supporto per TLS 1.2 in Windows 7 e Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
