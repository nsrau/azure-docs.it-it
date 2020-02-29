---
title: CA consentita per l'abilitazione di HTTPS personalizzato nella rete CDN di Azure
description: Se si usa un certificato proprio per abilitare HTTPS in un dominio personalizzato, è necessario usare un'autorità di certificazione (CA) consentita per crearlo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919975"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nella rete CDN di Azure

Quando si [Abilita la funzionalità HTTPS usando il proprio certificato](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) per un dominio personalizzato della rete per la distribuzione di contenuti (CDN) di Azure, è necessario soddisfare requisiti specifici del certificato. Il profilo della rete **CDN standard di Azure di Microsoft** richiede un certificato di una delle autorità di certificazione (CA) approvate nell'elenco seguente. Se viene usato un certificato da una CA non approvata o se viene usato un certificato autofirmato, la richiesta viene rifiutata. I profili della rete **CDN standard di Azure di Verizon** e **della rete CDN Premium di Azure da Verizon** accettano tutti i certificati validi da qualsiasi CA valida.

> [!NOTE]
> La possibilità di usare il proprio certificato per abilitare la funzionalità HTTPS del dominio personalizzato *non* è disponibile per la rete **CDN standard di Azure dai profili Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

