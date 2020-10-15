---
title: Autorità di certificazione consentita per l'abilitazione di HTTPS personalizzato in Azure
description: Se si usa il proprio certificato per abilitare HTTPS in un dominio personalizzato di Azure front door, è necessario usare un'autorità di certificazione (CA) consentita per crearla.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 20c5d611272ee2159ce8ddcc2865797a225a7ebb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613680"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autorità di certificazione consentite per l'abilitazione di HTTPS personalizzato in Azure front door

Quando si [Abilita la funzionalità HTTPS usando il proprio certificato](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate) per un dominio personalizzato di Azure front door. Per creare il certificato TLS/SSL è necessaria un'autorità di certificazione (CA) consentita. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
