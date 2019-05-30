---
title: 'Governance delle identità: Azure Active Directory | Microsoft Docs'
description: Azure Governance di Active Directory Identity consente di bilanciare la necessità della propria organizzazione per la sicurezza e produttività dei dipendenti con i processi a destra e la visibilità.
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
ms.date: 04/29/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1be6fc2b7d6da85778524cb8986f399c341370c1
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307031"
---
# <a name="what-is-azure-ad-identity-governance"></a>Che cos'è Azure AD Identity Governance?

Governance delle identità di Azure Active Directory (Azure AD) consente di bilanciare la necessità della propria organizzazione per la sicurezza e produttività dei dipendenti con i processi a destra e la visibilità. Fornisce le funzionalità necessarie a garantire che gli utenti giusti abbiano i diritti di accesso appropriati per le risorse corrette e consente di proteggere, monitorare e controllare l'accesso agli asset critici, assicurando allo stesso tempo la produttività dei dipendenti.  

Governance delle identità offrono alle organizzazioni la possibilità di eseguire le attività seguenti tra i dipendenti, partner aziendali e fornitori e servizi e applicazioni:

- Governance del ciclo di vita delle identità
- Governance del ciclo di vita degli accessi
- Amministrazione sicura

In particolare, consente alle organizzazioni di rispondere a queste quattro domande principali:

- Quali utenti hanno accesso a quali risorse?
- In che modo questi utenti usano l'accesso?
- Esistono controlli efficaci a livello aziendale per la gestione degli accessi?
- I revisori possono verificare l'operatività dei controlli?

## <a name="identity-lifecycle"></a>Ciclo di vita delle identità

Governance delle identità consente alle organizzazioni di raggiungere un equilibrio tra *produttività* -rapidità con cui una persona può avere accesso alle risorse necessarie, ad esempio quando questi vengono aggiunti la mia organizzazione? e *sicurezza* (in che modo dovrebbe cambiare nel tempo l'accesso degli utenti, ad esempio a causa di cambiamenti nello status lavorativo).  Gestione del ciclo di vita delle identità è la base per la governance delle identità e governance efficace su larga scala richiede modernizzando l'infrastruttura di gestione del ciclo di vita delle identità per le applicazioni.

![Ciclo di vita delle identità](./media/identity-governance-overview/identity-lifecycle.png)

Per molte organizzazioni, il ciclo di vita delle identità dei dipendenti è associato alla rappresentazione dell'utente in un sistema di gestione delle risorse umane (HCM).  Azure AD Premium gestisce automaticamente le identità degli utenti rappresentati in Workday sia in Active Directory che in Azure Active Directory, come descritto nell'esercitazione [Configurare Workday per il provisioning utenti automatico (anteprima)](../saas-apps/workday-inbound-tutorial.md).  Azure AD Premium include inoltre [Microsoft Identity Manager](/microsoft-identity-manager/), che può importare record dai sistemi HCM locali, come SAP, Oracle eBusiness e Oracle PeopleSoft.

Sempre più spesso, gli scenari aziendali richiedono la collaborazione con utenti esterni. Le funzionalità di [Collaborazione B2B di Azure AD](/azure/active-directory/b2b/) consentono di condividere in modo sicuro applicazioni e servizi con utenti guest e partner esterni provenienti da qualsiasi organizzazione, mantenendo al tempo stesso il controllo sui dati aziendali.

## <a name="access-lifecycle"></a>Ciclo di vita degli accessi

Le organizzazioni hanno bisogno di un processo per gestire l'accesso oltre il provisioning iniziale eseguito al momento della creazione dell'identità di un utente.  Inoltre, le organizzazioni aziendali devono disporre di scalabilità efficiente per poter sviluppare e applicare criteri e controlli di accesso in modo continuativo.

![Ciclo di vita degli accessi](./media/identity-governance-overview/access-lifecycle.png)

In genere, l'IT delega le decisioni in merito all'approvazione dell'accesso ai decision maker aziendali.  Inoltre, l'IT può comprendere gli utenti stessi.  Ad esempio, gli utenti che accedono a dati riservati dei clienti nell'applicazione di marketing di un'azienda in Europa devono conoscere i criteri dell'azienda. Gli utenti guest potrebbero non essere a conoscenza dei requisiti di gestione dei dati in un'organizzazione in cui sono stati invitati.

Le organizzazioni possono automatizzare il processo di gestione del ciclo di vita degli accessi grazie a tecnologie come i [gruppi dinamici](../users-groups-roles/groups-dynamic-membership.md), associati al provisioning degli utenti in [app SaaS](../saas-apps/tutorial-list.md) o [app integrate con SCIM](../manage-apps/use-scim-to-provision-users-and-groups.md).  Possono anche controllare quali [utenti guest hanno accesso alle applicazioni locali](../b2b/hybrid-cloud-to-on-premises.md).  I diritti di accesso possono quindi essere riesaminati regolarmente usando le [verifiche di accesso ricorrenti di Azure AD](access-reviews-overview.md).

Quando un utente tenta di accedere alle applicazioni, Azure AD applica criteri di [accesso condizionale](/azure/active-directory/conditional-access/). Ad esempio, i criteri di accesso condizionale possono includere la visualizzazione di [condizioni per l'utilizzo](../conditional-access/terms-of-use.md) e [garantire che l'utente abbia accettato le condizioni](../conditional-access/require-tou.md) prima di poter accedere a un'applicazione.

## <a name="privileged-access-lifecycle"></a>Ciclo di vita dell'accesso con privilegi

In passato, l'accesso con privilegi è stato descritto da altri fornitori come funzionalità separata dalla governance delle identità. Tuttavia, Microsoft si pensa che regolano l'accesso con privilegi è una parte fondamentale della governance delle identità, soprattutto considerando la possibilità per un uso improprio associato a tali amministratore diritti possono causare un'organizzazione. I dipendenti, fornitori e terzisti che acquisiscono diritti amministrativi devono essere controllati.

![Ciclo di vita dell'accesso con privilegi](./media/identity-governance-overview/privileged-access-lifecycle.png)

Azure AD Privileged Identity Management (PIM) fornisce controlli aggiuntivi mirati alla protezione dei diritti di accesso alle risorse in Azure AD, Azure e altri Microsoft Online Services.  Le funzionalità di accesso just-in-time e avviso di modifica del ruolo fornite da Azure AD Privileged Identity Management, sommate all'autenticazione a più fattori e all'accesso condizionale, forniscono un set completo di controlli di governance per la protezione delle risorse aziendali, compresi directory, Office 365 e ruoli delle risorse di Azure. Come con altre forme di accesso, le organizzazioni possono usare le verifiche di accesso per configurare la ricertificazione ricorrente dell'accesso per tutti gli utenti con ruoli di amministratore.

## <a name="getting-started"></a>Introduzione

Se non è presente alcuna raccomandazione per ogni cliente o soluzione perfetta, le configurazioni seguenti forniscono le istruzioni per ciò che i criteri di base di che Microsoft consiglia seguono per garantire una forza lavoro mobile più sicura e produttiva.

- [Configurazioni di identità e accesso dei dispositivi](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Protezione dell'accesso con privilegi](../users-groups-roles/directory-admin-roles-secure.md)

È anche possibile consultare la scheda Guida introduttiva del **governance delle identità** nel portale di Azure per iniziare a usare Gestione dei diritti di accesso recensioni, Privileged Identity Management e le condizioni d'uso.

![Governance delle identità introduttiva](./media/identity-governance-overview/getting-started.png)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è la gestione dei diritti di Azure AD? (anteprima)](entitlement-management-overview.md)
- [Informazioni sulle verifiche di accesso di Azure AD](access-reviews-overview.md)
- [Che cos'è Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Operazioni supportate da Condizioni per l'utilizzo](active-directory-tou.md)
