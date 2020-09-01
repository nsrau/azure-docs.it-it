---
title: includere file
description: includere file
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069911"
---
## <a name="about-cognitive-services-encryption"></a>Informazioni sulla crittografia di servizi cognitivi

I dati vengono crittografati e decrittografati usando la crittografia [AES a 256 bit](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) conforme allo standard [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) . La crittografia e la decrittografia sono trasparenti, ovvero la crittografia e l'accesso vengono gestite per l'utente. I dati sono protetti per impostazione predefinita e non è necessario modificare il codice o le applicazioni per sfruttare la crittografia.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft. È anche possibile gestire la sottoscrizione con chiavi personalizzate denominate chiavi gestite dal cliente (CMK). CMK offre una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati. Se CMK è configurato per la sottoscrizione, viene fornita la crittografia doppia, che offre un secondo livello di protezione, consentendo al contempo di controllare la chiave di crittografia tramite il Azure Key Vault.