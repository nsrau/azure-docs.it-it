---
title: Azure AD Identity Governance| Microsoft Docs
description: Azure AD Identity Governance consente di bilanciare le esigenze dell'organizzazione in termini di sicurezza e produttività dei dipendenti con la visibilità e i processi adeguati.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.openlocfilehash: 887c0bd4246c7c4215cb4d97dfd96db5d2aba6cb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170019"
---
# <a name="what-is-azure-ad-identity-governance"></a>Informazioni su Azure AD Identity Governance

Azure Active Directory (Azure AD) Identity Governance consente di bilanciare le esigenze dell'organizzazione in termini di sicurezza e produttività dei dipendenti con la visibilità e i processi adeguati. Fornisce le funzionalità necessarie a garantire che gli utenti giusti abbiano i diritti di accesso appropriati per le risorse corrette e consente di proteggere, monitorare e controllare l'accesso agli asset critici, assicurando allo stesso tempo la produttività dei dipendenti.  

La governance delle identità offre alle organizzazioni le capacità seguenti per dipendenti, fornitori, partner commerciali, servizi e applicazioni:

- Governance del ciclo di vita delle identità
- Governance del ciclo di vita degli accessi
- Amministrazione sicura

In particolare, consente alle organizzazioni di rispondere a queste quattro domande principali:

- Quali utenti hanno accesso a quali risorse?
- In che modo questi utenti usano l'accesso?
- Esistono controlli efficaci a livello aziendale per la gestione degli accessi?
- I revisori possono verificare l'operatività dei controlli?

## <a name="identity-lifecycle"></a>Ciclo di vita delle identità

La governance delle identità consente alle organizzazioni di raggiungere un equilibrio tra *produttività* (la velocità con cui una persona può accedere alle risorse necessarie, ad esempio quando inizia a collaborare con un'azienda) e *sicurezza* (in che modo dovrebbe cambiare nel tempo l'accesso degli utenti, ad esempio a causa di cambiamenti nello status lavorativo).  La gestione del ciclo di vita delle identità è la base per la governance delle identità e per una governance efficace su larga scala occorre modernizzare l'infrastruttura di gestione del ciclo di vita delle identità per le applicazioni.

Per molte organizzazioni, il ciclo di vita delle identità dei dipendenti è associato alla rappresentazione dell'utente in un sistema di gestione delle risorse umane (HCM).  Azure AD Premium gestisce automaticamente le identità degli utenti rappresentati in Workday sia in Active Directory che in Azure Active Directory, come descritto nell'esercitazione [Configurare Workday per il provisioning utenti automatico (anteprima)](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium include inoltre [Microsoft Identity Manager](/microsoft-identity-manager/), che può importare record dai sistemi HCM locali, come SAP, Oracle eBusiness e Oracle PeopleSoft.

Sempre più spesso, gli scenari aziendali richiedono la collaborazione con utenti esterni. Le funzionalità di [Collaborazione B2B di Azure AD](/azure/active-directory/b2b/) consentono di condividere in modo sicuro applicazioni e servizi con utenti guest e partner esterni provenienti da qualsiasi organizzazione, mantenendo al tempo stesso il controllo sui dati aziendali.

## <a name="access-lifecycle"></a>Ciclo di vita degli accessi

Le organizzazioni hanno bisogno di un processo per gestire l'accesso oltre il provisioning iniziale eseguito al momento della creazione dell'identità di un utente.  Inoltre, le organizzazioni aziendali devono disporre di scalabilità efficiente per poter sviluppare e applicare criteri e controlli di accesso in modo continuativo.

In genere, l'IT delega le decisioni in merito all'approvazione dell'accesso ai decision maker aziendali.  Inoltre, l'IT può comprendere gli utenti stessi.  Ad esempio, gli utenti che accedono a dati riservati dei clienti nell'applicazione di marketing di un'azienda in Europa devono conoscere i criteri dell'azienda. Gli utenti guest potrebbero non essere a conoscenza dei requisiti di gestione dei dati in un'organizzazione in cui sono stati invitati.

Le organizzazioni possono automatizzare il processo di gestione del ciclo di vita degli accessi grazie a tecnologie come i [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md), associati al provisioning degli utenti in [app SaaS](../saas-apps/tutorial-list.md) o [app integrate con SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  Possono anche controllare quali [utenti guest hanno accesso alle applicazioni locali](../b2b/hybrid-cloud-to-on-premises.md).  I diritti di accesso possono quindi essere riesaminati regolarmente usando le [verifiche di accesso ricorrenti di Azure AD](access-reviews-overview.md).

Quando un utente tenta di accedere alle applicazioni, Azure AD applica criteri di [accesso condizionale](/azure/active-directory/conditional-access/). Ad esempio, i criteri di accesso condizionale possono includere la visualizzazione di [condizioni per l'utilizzo](active-directory-tou.md) e [garantire che l'utente abbia accettato le condizioni](../conditional-access/require-tou.md) prima di poter accedere a un'applicazione.

## <a name="privileged-access-lifecycle"></a>Ciclo di vita dell'accesso con privilegi

In passato l'accesso con privilegi è stato descritto da altri fornitori come una funzionalità separata rispetto alla governance delle identità. Microsoft, tuttavia, ritiene la gestione dell'accesso con privilegi una parte essenziale della governance delle identità, in particolare per l'entità dei potenziali danni a un'organizzazione conseguenti all'uso improprio dei diritti di amministratore. I dipendenti, fornitori e terzisti che acquisiscono diritti amministrativi devono essere controllati.

Azure AD Privileged Identity Management (PIM) fornisce controlli aggiuntivi mirati alla protezione dei diritti di accesso alle risorse in Azure AD, Azure e altri Microsoft Online Services.  Le funzionalità di accesso just-in-time e avviso di modifica del ruolo fornite da Azure AD Privileged Identity Management, sommate all'autenticazione a più fattori e all'accesso condizionale, forniscono un set completo di controlli di governance per la protezione delle risorse aziendali, compresi directory, Office 365 e ruoli delle risorse di Azure. Come con altre forme di accesso, le organizzazioni possono usare le verifiche di accesso per configurare la ricertificazione ricorrente dell'accesso per tutti gli utenti con ruoli di amministratore.

## <a name="getting-started"></a>Introduzione

Sebbene non esista una soluzione o una raccomandazione perfetta per ogni cliente, le configurazioni seguenti rappresentano una guida sui criteri di base consigliati da Microsoft per migliorare la sicurezza e la produttività della forza lavoro.

- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protezione dell'accesso con privilegi](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>Verifiche di accesso

- [Informazioni sulle verifiche di accesso](access-reviews-overview.md)
- [Gestire l'accesso utente con le verifiche di accesso](manage-user-access-with-access-reviews.md)
- [Gestire l'accesso guest con le verifiche di accesso](manage-guest-access-with-access-reviews.md)
- [Avviare una verifica di accesso per i ruoli della directory](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

- [Operazioni supportate da Condizioni per l'utilizzo](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged Identity Management

- [Informazioni su Azure AD PIM](../privileged-identity-management/pim-configure.md)
