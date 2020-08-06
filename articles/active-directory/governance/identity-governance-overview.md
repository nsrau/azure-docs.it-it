---
title: Identity Governance - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance consente di bilanciare le esigenze dell'organizzazione in termini di sicurezza e produttività dei dipendenti con la visibilità e i processi adeguati.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb7ab566d861e0c4256acda8f8facd1e94d4ad6
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87796778"
---
# <a name="what-is-azure-ad-identity-governance"></a>Cos'è Azure AD Identity Governance?

Azure Active Directory (Azure AD) Identity Governance consente di bilanciare le esigenze dell'organizzazione in termini di sicurezza e produttività dei dipendenti con la visibilità e i processi adeguati. Fornisce funzionalità per garantire che le persone giuste abbiano l'accesso appropriato alle risorse giuste. Queste funzionalità e quelle correlate di Azure AD e Enterprise Mobility + Security consentono di ridurre i rischi dell'accesso proteggendo, monitorando e controllando l'accesso agli asset critici, garantendo al tempo stesso la produttività di dipendenti e partner aziendali.  

Identity Governance offre alle organizzazioni la possibilità di eseguire le attività seguenti per dipendenti, partner aziendali e fornitori, con i servizi e le operazioni in locale e nei cloud:

- Governance del ciclo di vita delle identità
- Governance del ciclo di vita degli accessi
- Accesso sicuro con privilegi per l'amministrazione

In particolare, consente alle organizzazioni di rispondere a queste quattro domande principali:

- Quali utenti hanno accesso a quali risorse?
- In che modo questi utenti usano l'accesso?
- Esistono controlli efficaci a livello aziendale per la gestione degli accessi?
- I revisori possono verificare l'operatività dei controlli?

## <a name="identity-lifecycle"></a>Ciclo di vita delle identità

Identity Governance consente alle organizzazioni trovare un equilibrio tra *produttività* (la velocità con cui una persona può accedere alle risorse necessarie, ad esempio quando inizia a collaborare con un'azienda) e *sicurezza* (in che modo dovrebbe cambiare nel tempo l'accesso degli utenti, ad esempio a causa di cambiamenti nello status lavorativo).  La gestione del ciclo di vita delle identità è la base per Identity Governance e per una governance efficace su larga scala occorre modernizzare l'infrastruttura di gestione del ciclo di vita delle identità per le applicazioni.

![Ciclo di vita delle identità](./media/identity-governance-overview/identity-lifecycle.png)

Per molte organizzazioni, il ciclo di vita delle identità dei dipendenti è associato alla rappresentazione dell'utente in un sistema di gestione delle risorse umane (HCM).  Azure AD Premium gestisce automaticamente le identità degli utenti rappresentati in Workday sia in Active Directory che in Azure Active Directory, come descritto nell'esercitazione [Configurare Workday per il provisioning utenti automatico](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium include inoltre [Microsoft Identity Manager](/microsoft-identity-manager/), che può importare record dai sistemi HCM locali, come SAP, Oracle eBusiness e Oracle PeopleSoft.

Sempre più spesso, gli scenari aziendali richiedono la collaborazione con utenti esterni. Le funzionalità di [Collaborazione B2B di Azure AD](/azure/active-directory/b2b/) consentono di condividere in modo sicuro applicazioni e servizi con utenti guest e partner esterni provenienti da qualsiasi organizzazione, mantenendo al tempo stesso il controllo sui dati aziendali.  La funzionalità di [gestione entitlement di Azure AD](entitlement-management-overview.md) consente di selezionare gli utenti dell'organizzazione autorizzati a richiedere l'accesso e l'aggiunta come utenti guest B2B alla directory dell'organizzazione e assicura che tali utenti guest vengano rimossi quando non necessitano più dell'accesso.

## <a name="access-lifecycle"></a>Ciclo di vita degli accessi

Le organizzazioni hanno bisogno di un processo per gestire l'accesso oltre il provisioning iniziale eseguito al momento della creazione dell'identità di un utente.  Inoltre, le organizzazioni aziendali devono disporre di scalabilità efficiente per poter sviluppare e applicare criteri e controlli di accesso in modo continuativo.

![Ciclo di vita degli accessi](./media/identity-governance-overview/access-lifecycle.png)

In genere, l'IT delega le decisioni in merito all'approvazione dell'accesso ai decision maker aziendali.  Inoltre, l'IT può comprendere gli utenti stessi.  Ad esempio, gli utenti che accedono a dati riservati dei clienti nell'applicazione di marketing di un'azienda in Europa devono conoscere i criteri dell'azienda. Gli utenti guest potrebbero non essere a conoscenza dei requisiti di gestione dei dati in un'organizzazione in cui sono stati invitati.

Le organizzazioni possono automatizzare il processo di gestione del ciclo di vita degli accessi grazie a tecnologie come i [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md), associati al provisioning degli utenti in [app SaaS](../saas-apps/tutorial-list.md) o [app integrate con SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Possono anche controllare quali [utenti guest hanno accesso alle applicazioni locali](../b2b/hybrid-cloud-to-on-premises.md).  I diritti di accesso possono quindi essere riesaminati regolarmente usando le [verifiche di accesso ricorrenti di Azure AD](access-reviews-overview.md).   La funzionalità di [gestione entitlement di Azure AD](entitlement-management-overview.md) consente inoltre di definire il modo in cui gli utenti richiedono l'accesso tra pacchetti di appartenenza a gruppi e team, ruoli applicazione e ruoli di SharePoint Online.

Quando un utente prova ad accedere alle applicazioni, Azure AD applica criteri di [accesso condizionale](/azure/active-directory/conditional-access/). I criteri di accesso condizionale possono includere ad esempio la visualizzazione di [condizioni per l'utilizzo](../conditional-access/terms-of-use.md) e la [verifica che l'utente le abbia accettate](../conditional-access/require-tou.md) prima di poter accedere a un'applicazione.

## <a name="privileged-access-lifecycle"></a>Ciclo di vita dell'accesso con privilegi

In passato l'accesso con privilegi è stato descritto da altri fornitori come una funzionalità separata rispetto a Identity Governance. Microsoft, tuttavia, ritiene che la regolamentazione dell'accesso con privilegi sia una parte essenziale di Identity Governance, in particolare considerando i potenziali danni che potrebbe causare a un'organizzazione l'uso improprio associato ai diritti dell'amministratore. I dipendenti, fornitori e terzisti che acquisiscono diritti amministrativi devono essere controllati.

![Ciclo di vita dell'accesso con privilegi](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) fornisce controlli aggiuntivi mirati alla protezione dei diritti di accesso alle risorse in Azure AD, Azure e altri Microsoft Online Services.  Le funzionalità di accesso just-in-time e avviso di modifica del ruolo fornite da Azure AD PIM, sommate all'autenticazione a più fattori e all'accesso condizionale, forniscono un set completo di controlli di governance per la protezione delle risorse aziendali, come directory, Office 365 e ruoli delle risorse di Azure. Come con altre forme di accesso, le organizzazioni possono usare le verifiche di accesso per configurare la ricertificazione ricorrente dell'accesso per tutti gli utenti con ruoli di amministratore.

## <a name="getting-started"></a>Introduzione

Per iniziare a usare le funzionalità di gestione entitlement, verifiche di accesso, Privileged Identity Management e condizioni per l'utilizzo, vedere la scheda Introduzione di **Identity Governance** nel portale di Azure.

![Introduzione a Identity Governance](./media/identity-governance-overview/getting-started.png)


Per inviare feedback sulle funzionalità di Identity Governance, fare clic su **Sono disponibili commenti?** nel portale di Azure. Il team esamina regolarmente il feedback.

Sebbene non esista una soluzione o una raccomandazione perfetta per ogni cliente, le guide alla configurazione seguenti forniscono i criteri di base che Microsoft consiglia di seguire per migliorare la sicurezza e la produttività della forza lavoro.

- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protezione dell'accesso con privilegi](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Appendice - Ruoli con privilegi minimi per la gestione nelle funzionalità di Identity Governance

Come procedura consigliata, usare il ruolo con privilegi minimi per eseguire attività amministrative in Identity Governance. È consigliabile usare Azure AD Privileged Identity Management per attivare un ruolo in base alle esigenze per eseguire queste attività. Di seguito sono riportati i ruoli della directory con privilegi minimi per configurare le funzionalità di Identity Governance:

| Funzionalità | Ruolo con privilegi minimi |
| ------- | --------------------- |
| Gestione entitlement | Amministratore utenti (ad eccezione dell'aggiunta di siti o cataloghi di SharePoint Online, che richiede l'amministratore globale) |
| Verifiche di accesso | Amministratore utenti (ad eccezione delle verifiche di accesso di Azure o dei ruoli di Azure AD, che richiedono l'amministratore ruolo con privilegi) |
|Privileged Identity Management | Amministratore dei ruoli con privilegi |
| Condizioni per l'utilizzo | Amministratore della sicurezza o amministratore dell'accesso condizionale |

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Gestione entitlement di Azure AD](entitlement-management-overview.md)
- [Informazioni sulle verifiche di accesso di Azure AD](access-reviews-overview.md)
- [Che cos'è Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Operazioni supportate da Condizioni per l'utilizzo](active-directory-tou.md)


