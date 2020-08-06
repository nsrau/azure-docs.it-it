---
title: Domande frequenti sul centro sicurezza di Azure-Domande sulle autorizzazioni
description: Queste domande frequenti rispondono alle domande sulle autorizzazioni nel centro sicurezza di Azure, un prodotto che consente di prevenire, rilevare e rispondere alle minacce.
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
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824614"
---
# <a name="permissions"></a>Autorizzazioni

## <a name="how-do-permissions-work-in-azure-security-center"></a>Come funzionano le autorizzazioni nel centro sicurezza di Azure?

Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/role-assignments-portal.md), che fornisce [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

Per altre informazioni sui ruoli e sulle azioni consentite in Centro sicurezza, vedere [Permissions in Azure Security Center](security-center-permissions.md) (Autorizzazioni in Centro sicurezza di Azure).



## <a name="who-can-modify-a-security-policy"></a>Chi può modificare i criteri di sicurezza?

Per modificare i criteri di sicurezza, è necessario essere un amministratore della sicurezza o un proprietario o un collaboratore di tale sottoscrizione.

Per informazioni su come configurare i criteri di sicurezza, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](tutorial-security-policy.md).