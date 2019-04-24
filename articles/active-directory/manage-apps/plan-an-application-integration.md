---
title: Introduzione all'integrazione di Azure AD con le app | Documentazione Microsoft
description: Questo articolo è una guida introduttiva per l'integrazione di Azure Active Directory (AD) con applicazioni locali e applicazioni cloud.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1223c96bffdf125da0c47fc932d46cfa84fa45c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291263"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guida introduttiva all'integrazione di Azure Active Directory con le applicazioni

Questo argomento riepiloga il processo per l'integrazione di applicazioni con Azure Active Directory (AD). Ciascuna delle sezioni seguenti contiene un breve riepilogo di un argomento più dettagliato, per consentire di identificare le parti di questa guida introduttiva rilevanti per la situazione specifica.

Per scaricare i piani di distribuzione dettagliati, vedere [Passaggi successivi](#next-steps).

## <a name="take-inventory"></a>Inventario
Prima di integrare applicazioni con Azure AD, è importante avere un quadro preciso della situazione attuale e degli obiettivi desiderati.  Le domande seguenti sono utili per valutare i diversi aspetti del progetto di integrazione delle applicazioni con Azure AD.

### <a name="application-inventory"></a>Inventario delle applicazioni
* Dove si trovano tutte le applicazioni? Quali sono i proprietari?
* Che tipo di autenticazione richiedono le applicazioni?
* Chi deve accedere a quali applicazioni?
* Si vuole distribuire una nuova applicazione?
  * L'applicazione verrà compilata internamente e distribuita in un'istanza di calcolo di Azure?
  * Si userà un'applicazione disponibile nella raccolta di applicazioni di Azure?

### <a name="user-and-group-inventory"></a>Inventario di utenti e gruppi
* Dove si trovano gli account utente?
  * Active Directory locale
  * Azure AD
  * In un database dell'applicazione separato di proprietà dell'utente
  * In applicazioni non approvate
  * Tutte le precedenti
* Quali autorizzazioni e assegnazioni di ruoli hanno attualmente i singoli utenti? È necessario verificarne l'accesso o si è sicuri che gli accessi utente e le assegnazioni dei ruoli siano appropriati per la situazione attuale?
* Sono già stati definiti gruppi in Active Directory locale?
  * In che modo sono organizzati i gruppi?
  * Chi sono i membri dei gruppi?
  * Quali autorizzazioni/assegnazioni di ruolo hanno attualmente i gruppi?
* Sarà necessario pulire i database di utenti/gruppi prima dell'integrazione?  Questa è una domanda molto importante. La qualità del risultato dipende dalla qualità dei dati di partenza.

### <a name="access-management-inventory"></a>Inventario della gestione degli accessi
* In che modo si gestisce attualmente l'accesso utente alle applicazioni? È necessario modificarlo?  Sono stati valutati altri modi per gestire l'accesso, ad esempio [RBAC](../../role-based-access-control/role-assignments-portal.md) ?
* Chi deve accedere a cosa?

È possibile che non siano immediatamente disponibili le risposte a tutte queste domande.  Questa guida è utile per rispondere ad alcune domande e prendere decisioni informate.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Ricercare applicazioni cloud non approvate con Cloud Discovery

Come indicato in precedenza, è possibile che alcune applicazioni non siano state gestite dall'organizzazione fino a oggi.  Come parte del processo di inventario, è possibile individuare applicazioni cloud non approvate. Vedere [Configurare Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrazione di applicazioni con Azure AD
Gli articoli seguenti illustrano i diversi modi in cui le applicazioni si integrano con Azure AD e forniscono alcune indicazioni.

* [Scelta del tipo di Active Directory da usare](../fundamentals/active-directory-administer.md)
* [Uso delle applicazioni della raccolta di applicazioni di Azure](what-is-single-sign-on.md)
* [Elenco delle esercitazioni sull'integrazione di applicazioni SaaS](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Tipi di autenticazione
È possibile che ogni applicazione abbia requisiti di autenticazione diversi. Con Azure AD la firma dei certificati può essere usata con applicazioni che usano i protocolli di connessione SAML 2.0, WS-Federation oppure OpenID, oltre a Password Single Sign-On. Per altre informazioni sui tipi di autenticazione per le applicazioni che possono essere usati con Azure AD, vedere [Gestione di certificati per Single Sign-On federato in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) e [Accesso Single Sign-On basato su password](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Abilitazione di SSO con il proxy dell'app di Azure AD
Con il proxy di applicazione di Microsoft Azure AD è possibile garantire l'accesso ad applicazioni che si trovano all'interno della rete privata in modo sicuro da qualsiasi posizione e su qualsiasi dispositivo. Dopo aver installato un connettore del proxy di applicazione all'interno dell'ambiente, è possibile configurarlo facilmente con Azure AD.

### <a name="integrating-custom-applications"></a>Integrazione di applicazioni personalizzate
Se si sviluppa una nuova applicazione e si vuole aiutare gli sviluppatori a sfruttare i vantaggi di Azure AD, vedere [Guida per gli sviluppatori](../active-directory-applications-guiding-developers-for-lob-applications.md).

Per aggiungere un'applicazione personalizzata alla raccolta di applicazioni di Azure, vedere il post di blog su ["Bring your own app" con la configurazione SAML Self-Service di Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Gestione dell'accesso alle applicazioni
Gli articoli seguenti illustrano i modi in cui è possibile gestire l'accesso alle applicazioni dopo l'integrazione con Azure AD mediante i connettori di Azure AD e Azure AD.

* [Gestione dell'accesso alle app tramite Azure AD](what-is-access-management.md)
* [Automazione con i connettori di Azure AD](user-provisioning.md)
* [Assegnazione di utenti a un'applicazione](../active-directory-applications-guiding-developers-assigning-users.md)
* [Assegnazione di gruppi a un'applicazione](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Condivisione di account](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate, è possibile scaricare i piani di distribuzione di Azure Active Directory da [GitHub](https://aka.ms/deploymentplans). Per le applicazioni della raccolta, è possibile scaricare i piani di distribuzione per l'accesso single sign-on, l'accesso condizionale e utente, il provisioning tramite il [portale di Azure](https://portal.azure.com). 

Per scaricare un piano di distribuzione dal portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Applicazioni aziendali** | **Seleleziona un'app** | **Piano di distribuzione**.

Fornire commenti e suggerimenti sui piani di distribuzione grazie all'uso di [Sondaggio piano di distribuzione](https://aka.ms/DeploymentPlanFeedback).
