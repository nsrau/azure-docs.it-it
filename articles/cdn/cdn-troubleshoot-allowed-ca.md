---
title: CA consentita per l'abilitazione di HTTPS personalizzato nella rete CDN di AzureAllowed CA for enabling custom HTTPS on Azure CDN
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919975"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nella rete CDN di Azure

È necessario soddisfare requisiti specifici del certificato quando si [abilita la funzionalità HTTPS usando il proprio certificato](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) per un dominio personalizzato della rete per la distribuzione di contenuti (CDN) di Azure.You must meet specific certificate requirements when you enable the HTTPS feature by using your own certificate for an Azure Content Delivery Network (CDN) custom domain. Il profilo **Standard della rete CDN di Azure da Microsoft** richiede un certificato da una delle autorità di certificazione (CA) approvate nell'elenco seguente. Se viene certificato di una CA non approvata o se viene utilizzato un certificato autofirmato, la richiesta viene rifiutata. La rete CDN standard di **Azure di Verizon** e i profili **di Azure CDN Premium di Verizon** accettano qualsiasi certificato valido da qualsiasi CA valida.

> [!NOTE]
> L'opzione di usare il proprio certificato per abilitare la funzionalità HTTPS di dominio personalizzato *non* è disponibile per Azure CDN Standard dai profili **Akamai.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

