---

title: Confrontare Collaborazione B2B e B2C di Azure Active Directory | Microsoft Docs
description: Differenze tra Collaborazione B2B e B2C di Azure Active Directory.
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/08/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 72e7e57185d882a5f3243e2705aed54ee8347798
ms.openlocfilehash: 5d157a936317bbf66cfc3634d4d08ea3483d137b


---

# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Confrontare Collaborazione B2B e B2C di Azure Active Directory

Sia Collaborazione B2B che B2C di Azure Active Directory consentono di lavorare con utenti esterni in Azure AD. Di seguito è riportato un confronto tra le diverse caratteristiche.


Funzionalità di Collaborazione B2B |     Offerta autonoma di B2C di Azure AD
-------- | --------
Pensate per organizzazioni che desiderano offrire accesso a dati aziendali, risorse e applicazioni per gli utenti di altre organizzazioni, che possono usare qualsiasi identità di loro scelta. | Pensata per app per dispositivi mobili e Web destinate ai clienti (singoli utenti, cittadini e clienti aziendali o istituzionali, ma non dipendenti o collaboratori esterni), che possono usare qualsiasi identità di loro scelta.
Identità supportate: dipendenti con account aziendale o dell'istituto di istruzione, partner con account aziendale o dell'istituto di istruzione oppure qualsiasi indirizzo email. A breve sarà disponibile il supporto per la federazione diretta.  | Identità supportate: utenti consumer con account di applicazioni locali (qualsiasi indirizzo email o nome utente) o qualsiasi identità social supportata con federazione diretta.
Directory degli utenti partner: gli utenti partner dell'organizzazione esterna sono gestiti nella stessa directory dei dipendenti ma annotati in modo speciale. Questi utenti esterni possono essere gestiti allo stesso modo dei dipendenti, possono essere aggiunti agli stessi gruppi e così via  | Directory delle entità degli utenti clienti: directory dell'applicazione. Le entità sono gestite separatamente dalla directory dei dipendenti e dall'eventuale directory dei partner dell'organizzazione.
È supportato il Single Sign-On (SSO) a tutte le app connesse di Azure AD, (incluse le app locali), ad esempio Office 365, e ad altre app SaaS Microsoft e non Microsoft (ad esempio Salesforce, Box, Workday e così via).  |  È supportato il SSO ad app di proprietà del cliente all'interno di tenant B2C di Azure AD. Non è supportato il SSO a Office 365 o ad altre app SaaS Microsoft e non Microsoft.
Ciclo di vita per i partner: gestito dall'organizzazione host (o che manda l'invito).  | Ciclo di vita per i clienti: in modalità self-service o gestito dall'applicazione.
Criteri di sicurezza e conformità: gestiti dall'organizzazione host (o che manda l'invito).  | Criteri di sicurezza e conformità: gestiti dall'applicazione.
Personalizzazione: viene usato il marchio dell'organizzazione host (o che manda l'invito).  |    Personalizzazione: gestita dall'applicazione. In genere viene eseguita una personalizzazione in base al prodotto, con l'organizzazione sullo sfondo.
Per altre informazioni: [post di blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentazione](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Per altre informazioni: [pagina del prodotto](https://azure.microsoft.com/en-us/services/active-directory-b2c/), [documentazione](https://docs.microsoft.com/en-us/azure/active-directory-b2c/)


### <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente in Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)
* [Getting support for B2B collaboration](active-directory-b2b-support.md) (Ricevere supporto per Collaborazione B2B)



<!--HONumber=Feb17_HO2-->


