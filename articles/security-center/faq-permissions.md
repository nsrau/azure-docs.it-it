---
title: Domande frequenti sul Centro sicurezza di Azure- domande sulle autorizzazioniAzure Security Center FAQ - questions about permissions
description: Questa faq risponde alle domande sulle autorizzazioni nel Centro sicurezza di Azure, un prodotto che consente di prevenire, rilevare e rispondere alle minacce.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599392"
---
# <a name="permissions"></a>Autorizzazioni

## <a name="how-do-permissions-work-in-azure-security-center"></a>Come funzionano le autorizzazioni nel Centro sicurezza di Azure?

Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md), con [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

Per altre informazioni sui ruoli e sulle azioni consentite in Centro sicurezza, vedere [Permissions in Azure Security Center](security-center-permissions.md) (Autorizzazioni in Centro sicurezza di Azure).



## <a name="who-can-modify-a-security-policy"></a>Chi può modificare i criteri di sicurezza?

Per modificare i criteri di sicurezza, è necessario essere Amministratore della protezione, proprietario o collaboratore di tale sottoscrizione.

Per informazioni su come configurare i criteri di sicurezza, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md).