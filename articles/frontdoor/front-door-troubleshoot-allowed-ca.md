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
ms.openlocfilehash: cac6bc9895f2b8778f2b27cc6b1dff4d4b898ae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471524"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autorità di certificazione consentite per abilitare HTTPS personalizzato in Azure Front DoorAllowed certificate authorities for enabling custom HTTPS on Azure Front Door

Per un dominio personalizzato di Azure Front Door, quando si [abilita la funzionalità HTTPS usando il proprio certificato,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)è necessario usare un'autorità di certificazione (CA) consentita per creare il certificato SSL. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
