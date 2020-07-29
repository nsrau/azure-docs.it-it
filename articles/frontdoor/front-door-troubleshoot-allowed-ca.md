---
title: Autorità di certificazione consentita per l'abilitazione di HTTPS personalizzato in Azure
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80874671"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autorità di certificazione consentite per l'abilitazione di HTTPS personalizzato in Azure front door

Per un dominio personalizzato di Azure front door, quando si [Abilita la funzionalità HTTPS usando il proprio certificato](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), è necessario usare un'autorità di certificazione (CA) consentita per creare il certificato TLS/SSL. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
