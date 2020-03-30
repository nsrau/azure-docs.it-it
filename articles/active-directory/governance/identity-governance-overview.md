---
title: Governance dell'identità - Azure Active Directory Documenti Microsoft
description: Azure Active Directory Identity Governance consente di bilanciare le esigenze dell'organizzazione per la sicurezza e la produttività dei dipendenti con i processi e la visibilità giusti.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063680"
---
# <a name="what-is-azure-ad-identity-governance"></a>Cos'è Azure AD Identity Governance?

Governance delle identità di Azure Active Directory (Azure AD) consente di bilanciare le esigenze dell'organizzazione in termini di sicurezza e produttività dei dipendenti con i processi e la visibilità giusti. Fornisce funzionalità per garantire alle persone giuste l'accesso alle risorse giuste. Queste e correlate funzionalità di Azure AD ed Enterprise Mobility - Security consentono di ridurre i rischi di accesso proteggendo, monitorando e controllando l'accesso alle risorse critiche, garantendo al contempo la produttività dei dipendenti e dei partner commerciali.  

Identity Governance consente alle organizzazioni di eseguire le attività seguenti tra dipendenti, partner commerciali e fornitori e servizi e applicazioni sia in locale che nei cloud:

- Governance del ciclo di vita delle identità
- Governance del ciclo di vita degli accessi
- Accesso con privilegi sicuri per l'amministrazione

In particolare, consente alle organizzazioni di rispondere a queste quattro domande principali:

- Quali utenti hanno accesso a quali risorse?
- In che modo questi utenti usano l'accesso?
- Esistono controlli efficaci a livello aziendale per la gestione degli accessi?
- I revisori possono verificare l'operatività dei controlli?

## <a name="identity-lifecycle"></a>Ciclo di vita delle identità

Identity Governance consente alle organizzazioni di raggiungere un equilibrio tra produttività e *produttività:* quanto velocemente una persona può avere accesso alle risorse di cui ha bisogno, ad esempio quando si unisce all'organizzazione? e *sicurezza* (in che modo dovrebbe cambiare nel tempo l'accesso degli utenti, ad esempio a causa di cambiamenti nello status lavorativo).  La gestione del ciclo di vita delle identità è alla base della governance delle identità e una governance efficace su larga scala richiede la modernizzazione dell'infrastruttura di gestione del ciclo di vita delle identità per le applicazioni.

![Ciclo di vita delle identità](./media/identity-governance-overview/identity-lifecycle.png)

Per molte organizzazioni, il ciclo di vita delle identità dei dipendenti è associato alla rappresentazione dell'utente in un sistema di gestione delle risorse umane (HCM).  Azure AD Premium gestisce automaticamente le identità utente per le persone rappresentate in Workday in Active Directory e Azure Active Directory, come descritto nell'esercitazione sul provisioning in ingresso di [Workday.](../saas-apps/workday-inbound-tutorial.md)  Azure AD Premium include inoltre [Microsoft Identity Manager](/microsoft-identity-manager/), che può importare record dai sistemi HCM locali, come SAP, Oracle eBusiness e Oracle PeopleSoft.

Sempre più spesso, gli scenari aziendali richiedono la collaborazione con utenti esterni. Le funzionalità di [Collaborazione B2B di Azure AD](/azure/active-directory/b2b/) consentono di condividere in modo sicuro applicazioni e servizi con utenti guest e partner esterni provenienti da qualsiasi organizzazione, mantenendo al tempo stesso il controllo sui dati aziendali.  La [gestione dei diritti](entitlement-management-overview.md) di Azure AD consente di selezionare gli utenti dell'organizzazione a cui è consentito richiedere l'accesso e di essere aggiunti come guest B2B alla directory dell'organizzazione e garantisce che questi guest vengano rimossi quando non hanno più bisogno dell'accesso.

## <a name="access-lifecycle"></a>Ciclo di vita degli accessi

Le organizzazioni hanno bisogno di un processo per gestire l'accesso oltre il provisioning iniziale eseguito al momento della creazione dell'identità di un utente.  Inoltre, le organizzazioni aziendali devono disporre di scalabilità efficiente per poter sviluppare e applicare criteri e controlli di accesso in modo continuativo.

![Ciclo di vita degli accessi](./media/identity-governance-overview/access-lifecycle.png)

In genere, l'IT delega le decisioni in merito all'approvazione dell'accesso ai decision maker aziendali.  Inoltre, l'IT può comprendere gli utenti stessi.  Ad esempio, gli utenti che accedono a dati riservati dei clienti nell'applicazione di marketing di un'azienda in Europa devono conoscere i criteri dell'azienda. Gli utenti guest potrebbero non essere a conoscenza dei requisiti di gestione dei dati in un'organizzazione in cui sono stati invitati.

Le organizzazioni possono automatizzare il processo di gestione del ciclo di vita degli accessi grazie a tecnologie come i [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md), associati al provisioning degli utenti in [app SaaS](../saas-apps/tutorial-list.md) o [app integrate con SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Possono anche controllare quali [utenti guest hanno accesso alle applicazioni locali](../b2b/hybrid-cloud-to-on-premises.md).  I diritti di accesso possono quindi essere riesaminati regolarmente usando le [verifiche di accesso ricorrenti di Azure AD](access-reviews-overview.md).   La gestione dei diritti di Azure AD consente inoltre di definire il modo in cui gli utenti richiedono l'accesso tra pacchetti di appartenenze a gruppi e team, ruoli applicazione e ruoli di SharePoint [Online.Azure AD entitlement management](entitlement-management-overview.md) also enables you to define how users request access across packages of group and team memberships, application roles, and SharePoint Online roles.

Quando un utente tenta di accedere alle applicazioni, Azure AD applica i criteri [di accesso condizionale.](/azure/active-directory/conditional-access/) Ad esempio, i criteri di accesso condizionale possono includere la visualizzazione di una [condizione per l'utilizzo](../conditional-access/terms-of-use.md) e [la verifica che l'utente abbia accettato tali termini](../conditional-access/require-tou.md) prima di poter accedere a un'applicazione.

## <a name="privileged-access-lifecycle"></a>Ciclo di vita dell'accesso con privilegi

Storicamente, l'accesso con privilegi è stato descritto da altri fornitori come una funzionalità separata da Identity Governance. Tuttavia, in Microsoft, riteniamo che la gestione dell'accesso con privilegi sia una parte fondamentale di Identity Governance, in particolare data la possibilità di un uso improprio associato a tali diritti di amministratore può causare a un'organizzazione. I dipendenti, fornitori e terzisti che acquisiscono diritti amministrativi devono essere controllati.

![Ciclo di vita dell'accesso con privilegi](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) offre controlli aggiuntivi su misura per la protezione dei diritti di accesso per le risorse, in Azure AD, Azure e altri Microsoft Online Services.  Le funzionalità di accesso just-in-time e di avviso di modifica dei ruoli fornite da Azure AD PIM, oltre all'autenticazione a più fattori e all'accesso condizionale, forniscono un set completo di controlli di governance per proteggere le risorse aziendali (directory, Ruoli delle risorse di Office 365 e Azure). Come con altre forme di accesso, le organizzazioni possono usare le verifiche di accesso per configurare la ricertificazione ricorrente dell'accesso per tutti gli utenti con ruoli di amministratore.

## <a name="getting-started"></a>Introduzione

Vedere la scheda Introduzione di **Identity Governance** nel portale di Azure per iniziare a usare la gestione dei diritti, le verifiche di accesso, la gestione delle identità con privilegi e le condizioni per l'utilizzo.

![Introduzione alla governance dell'identità](./media/identity-governance-overview/getting-started.png)


Se sono presenti commenti e suggerimenti sulle funzionalità di Identity Governance, fare clic su **Commenti e suggerimenti nel** portale di Azure per inviare commenti e suggerimenti. Il team esamina regolarmente il tuo feedback.

Sebbene non esista una soluzione o un suggerimento perfetto per ogni cliente, le guide alla configurazione seguenti forniscono anche i criteri di base che Microsoft consiglia di seguire per garantire una forza lavoro più sicura e produttiva.

- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protezione dell'accesso con privilegi](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Appendice - ruoli con privilegi minimi per la gestione nelle funzionalità di Identity GovernanceAppendix - least privileged roles for managing in Identity Governance features

È consigliabile utilizzare il ruolo con privilegi minimi per eseguire attività amministrative in Identity Governance. È consigliabile usare Azure AD PIM per attivare un ruolo in base alle esigenze per eseguire queste attività. Di seguito sono riportati i ruoli di directory con privilegi minimi per configurare le funzionalità di Identity Governance:

| Funzionalità | Ruolo con privilegi minimi |
| ------- | --------------------- |
| Gestione entitlement | Amministratore utente (ad eccezione dell'aggiunta di siti di SharePoint Online ai cataloghi, che richiede l'amministratore globale) |
| Verifiche di accesso | Amministratore utente (ad eccezione delle verifiche di accesso dei ruoli di Azure o Azure AD, che richiede l'amministratore dei ruoli con privilegi)User administrator (as the exception of access reviews of Azure or Azure AD roles, which requires Privileged role administrator) |
|Privileged Identity Management | Amministratore dei ruoli con privilegi |
| Condizioni per l'utilizzo | Amministratore della sicurezza o amministratore di accesso condizionale |

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Gestione entitlement di Azure AD](entitlement-management-overview.md)
- [Che cosa sono le verifiche di accesso di Azure AD?](access-reviews-overview.md)
- [Che cos'è Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Operazioni supportate da Condizioni per l'utilizzo](active-directory-tou.md)


