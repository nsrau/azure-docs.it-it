---
title: CA consentita per l'abilitazione di HTTPS personalizzato nella rete CDN di Azure
description: Se si usa un certificato proprio per abilitare HTTPS in un dominio personalizzato, è necessario usare un'autorità di certificazione (CA) consentita per crearlo.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 29b6cb25e021e86ce6663b4db5c89217aaf70a37
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887409"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nella rete CDN di Azure

Quando si [Abilita la funzionalità HTTPS usando il proprio certificato](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) per un dominio personalizzato della rete per la distribuzione di contenuti (CDN) di Azure, è necessario soddisfare requisiti specifici del certificato. Il profilo della rete **CDN standard di Azure di Microsoft** richiede un certificato di una delle autorità di certificazione (CA) approvate nell'elenco seguente. Se viene usato un certificato da una CA non approvata o se viene usato un certificato autofirmato, la richiesta viene rifiutata. I profili della rete **CDN standard di Azure di Verizon** e **della rete CDN Premium di Azure da Verizon** accettano tutti i certificati validi da qualsiasi CA valida.

> [!NOTE]
> La possibilità di usare il proprio certificato per abilitare la funzionalità HTTPS del dominio personalizzato *non* è disponibile per la rete **CDN standard di Azure dai profili Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

