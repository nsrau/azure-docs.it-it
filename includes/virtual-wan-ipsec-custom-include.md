---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168371"
---
Quando si utilizzano i criteri IPsec personalizzati, tenere presenti i requisiti seguenti:

* **IKE** : per IKE è possibile selezionare qualsiasi parametro dalla crittografia IKE, oltre a qualsiasi parametro dell'integrità IKE, più qualsiasi parametro del gruppo DH.
* **IPSec** : per IPSec è possibile selezionare qualsiasi parametro dalla crittografia IPSec, oltre a qualsiasi parametro dall'integrità IPSec, più PFS. Se uno dei parametri per la crittografia IPsec o l'integrità IPsec è GCM, i parametri per entrambe le impostazioni devono essere GCM.

>[!NOTE]
> Con i criteri IPsec personalizzati non esiste alcun concetto di risponditore e iniziatore (a differenza dei criteri IPsec predefiniti). Entrambi i lati (locale e gateway VPN di Azure) utilizzeranno le stesse impostazioni per IKE fase 1 e IKE fase 2. Sono supportati sia i protocolli IKEv1 che IKEv2. Non è disponibile alcun supporto per Azure come risponditore.
>

**Impostazioni e parametri disponibili**

| Impostazione | Parametri |
|--- |--- |
| Crittografia IKE | AES256, AES192, AES128 |
| Integrità IKE | SHA384, SHA256, SHA1 |
| Gruppo DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Crittografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Integrità IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Gruppo PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
