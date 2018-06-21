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
ms.openlocfilehash: 383c4a53913333a3e25006980dd7533b9e243a4a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236681"
---
A partire dal 1 luglio 2018, verrà rimosso il supporto per TLS 1.0 e 1.1 da Gateway VPN di Azure. Gateway VPN supporterà solo TLS 1.2. Per garantire il supporto TLS e connettività per i client da punto a sito Windows 7 e Windows 8 che usano TLS, è consigliabile installare gli aggiornamenti seguenti:

•   [Aggiornamento per l'implementazione di Microsoft EAP che consente l'uso di TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [Aggiornamento per l'abilitazione di TLS 1.1 e TLS 1.2 come protocolli sicuri predefiniti in WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

Anche gli algoritmi legacy seguenti saranno deprecati per TLS dal 1 luglio 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)
* SHA-1 (Secure Hash Algorithm 1)