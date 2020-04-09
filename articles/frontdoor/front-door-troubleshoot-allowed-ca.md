---
title: CA consentita per abilitare HTTPS personalizzato nella porta anteriore di AzureAllowed CA for enabling custom HTTPS on Azure Front Door
description: Se si usa un certificato proprio per abilitare HTTPS in un dominio personalizzato, è necessario usare un'autorità di certificazione (CA) consentita per crearlo.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874671"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autorità di certificazione consentite per abilitare HTTPS personalizzato in Azure Front DoorAllowed certificate authorities for enabling custom HTTPS on Azure Front Door

Per un dominio personalizzato di Azure Front Door, quando si [abilita la funzionalità HTTPS usando il proprio certificato,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)è necessario usare un'autorità di certificazione (CA) consentita per creare il certificato TLS/SSL. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
