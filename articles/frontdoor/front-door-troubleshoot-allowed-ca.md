---
title: Autorità di certificazione consentita per l'abilitazione di HTTPS personalizzato in Azure
description: Se si usa il proprio certificato per abilitare HTTPS in un dominio 0custom di Azure front door, è necessario usare un'autorità di certificazione (CA) consentita per crearla.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269803"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autorità di certificazione consentite per l'abilitazione di HTTPS personalizzato in Azure front door

Per un dominio personalizzato di Azure front door, quando si [Abilita la funzionalità HTTPS usando il proprio certificato](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), è necessario usare un'autorità di certificazione (CA) consentita per creare il certificato TLS/SSL. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
