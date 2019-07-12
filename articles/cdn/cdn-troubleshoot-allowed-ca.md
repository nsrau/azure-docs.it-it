---
title: Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nella rete CDN di Azure | Microsoft Docs
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
ms.openlocfilehash: 754941163ddce9512870f0b76a96207472e5b2aa
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593354"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nella rete CDN di Azure

Per un dominio personalizzato di una rete di distribuzione di contenuti (CDN) di Azure in un endpoint **Standard della rete CDN di Azure di Microsoft**, quando si [abilita la funzionalità HTTPS usando un certificato proprio](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), è necessario usare un'autorità di certificazione (CA) consentita per creare il certificato SSL. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

> [!NOTE]
> La possibilità di usare un certificato proprio per abilitare HTTPS personalizzato è disponibile solo con i profili **standard della rete CDN di Azure di Microsoft**. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

