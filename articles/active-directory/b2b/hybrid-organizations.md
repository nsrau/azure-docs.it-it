---
title: Collaborazione B2B per organizzazioni ibride - Azure Active Directory | Microsoft Docs
description: Concedere ai partner l'accesso alle risorse locali e cloud con Collaborazione B2B di Azure AD.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: b22917d27498787984d27d4bcba443b08d99ba9b
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424512"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Collaborazione B2B di Azure Active Directory per organizzazioni ibride

Collaborazione B2B di Azure Active Directory (Azure AD) consente di concedere in modo semplice ai partner esterni l'accesso alle app e alle risorse aziendali. Ciò vale anche in una configurazione ibrida in cui sono presenti risorse sia locali che cloud. Non è importante se attualmente si gestiscono gli account partner esterni in locale nel sistema di identità locale oppure se si gestiscono gli account esterni nel cloud come utenti di Azure AD B2B. Ora è possibile concedere a questi utenti l'accesso alle risorse in entrambe le posizioni, con le stesse credenziali di accesso per entrambi gli ambienti.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Concedere agli utenti B2B in Azure AD l'accesso alle app locali

Se l'organizzazione usa Collaborazione B2B di Azure AD per invitare gli utenti guest di organizzazioni partner in Azure AD può ora fornire a questi utenti B2B l'accesso alle app locali.

È possibile rendere facilmente disponibili le app che usano l'autenticazione basata su SAML per gli utenti B2B tramite il portale di Azure, usando Azure AD Application Proxy per l'autenticazione.

Anche per le app che usano l'autenticazione integrata di Windows con la delega vincolata Kerberos si usa il proxy di Azure AD per l'autenticazione. Affinché l'autorizzazione funzioni, è tuttavia necessario un oggetto utente nell'istanza locale di Windows Server Active Directory. Ci sono due modi per creare oggetti utente locali che rappresentano gli utenti guest B2B.

- È possibile usare Microsoft Identity Manager (MIM) 2016 SP1 e l'agente di gestione MIM per Microsoft Graph.
- È possibile usare uno script di PowerShell. Questa soluzione non richiede MIM.

Per informazioni dettagliate su come implementare queste soluzioni, vedere [Concedere agli utenti B2B in Azure AD l'accesso alle applicazioni locali](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Concedere agli account partner gestiti in locale l'accesso alle risorse cloud

Prima di Azure AD, le organizzazioni con sistemi di identità locali gestivano gli account partner nella propria directory locale. In un'organizzazione di questo tipo, è necessario assicurarsi che i partner continuino ad avere accesso quando si spostano le app e altre risorse nel cloud. Idealmente, questi utenti devono poter usare lo stesso set di credenziali per accedere alle risorse sia locali che cloud. 

È ora possibile usare Azure AD Connect per sincronizzare questi account locali nel cloud come "utenti guest", in cui gli account si comportano come utenti di Azure AD B2B.

Per proteggere i dati aziendali, è possibile controllare l'accesso alle risorse appropriate e configurare criteri di autorizzazione che trattano questi utenti guest in modo diverso dai propri dipendenti.

Per informazioni dettagliate sull'implementazione, vedere [Concedere agli account partner gestiti in locale l'accesso alle risorse cloud usando Collaborazione B2B di Azure AD](hybrid-on-premises-to-cloud.md).
 
## <a name="next-steps"></a>Passaggi successivi

- [Concedere agli utenti B2B in Azure AD l'accesso alle applicazioni locali](hybrid-cloud-to-on-premises.md)
- [Concedere agli account partner gestiti in locale l'accesso alle risorse cloud usando Collaborazione B2B di Azure AD](hybrid-on-premises-to-cloud.md)


