---
title: includere file
description: includere file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e2a950037aed2a8ded4d4e55920721285cbfc05c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204616"
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
| Crittografia IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Integrità IKE | SHA384, SHA256 |
| Gruppo DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Crittografia IPsec | GCMAES256, GCMAES128, AES256, AES128, None |
| Integrità IPsec | GCMAES256, GCMAES128, SHA256 |
| Gruppo PFS | ECP384, ECP256, PFS24, PFS14, None |
