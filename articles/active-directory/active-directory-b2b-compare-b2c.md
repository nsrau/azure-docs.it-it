---
title: Confrontare Collaborazione B2B e B2C di Azure Active Directory | Microsoft Docs
description: Differenze tra Collaborazione B2B e B2C di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0e8781fcf2710b00ba352bff29a370cbb73e0648
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33927884"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Confrontare Collaborazione B2B e B2C di Azure Active Directory

Sia Collaborazione B2B che B2C di Azure Active Directory consentono di lavorare con utenti esterni in Azure AD. Di seguito è riportato un confronto tra le diverse caratteristiche.


Funzionalità di Collaborazione B2B |     Offerta autonoma di B2C di Azure AD
-------- | --------
Destinatari: organizzazioni che desiderano essere in grado di autenticare gli utenti di un'organizzazione partner, indipendentemente dal provider di identità. | Scopo: invitare i clienti di app Web e per dispositivi mobili, sia individui, clienti istituzionali oppure organizzazioni, in Azure AD.
Identità supportate: dipendenti con account aziendale o dell'istituto di istruzione, partner con account aziendale o dell'istituto di istruzione oppure qualsiasi indirizzo email. A breve sarà disponibile il supporto per la federazione diretta.  | Identità supportate: utenti consumer con account di applicazioni locali (qualsiasi indirizzo email o nome utente) o qualsiasi identità social supportata con federazione diretta.
Directory degli utenti partner: gli utenti partner dell'organizzazione esterna sono gestiti nella stessa directory dei dipendenti ma annotati in modo speciale. Tali utenti possono essere gestiti in modo analogo ai dipendenti, possono essere aggiunti agli stessi gruppi e così via  | Directory delle entità degli utenti clienti: directory dell'applicazione. Le entità sono gestite separatamente dalla directory dei dipendenti e dall'eventuale directory dei partner dell'organizzazione.
La funzione Single Sign-On (SSO) per tutte le app connesse ad Azure AD è supportata. È possibile ad esempio concedere l'accesso a Office 365 oppure ad app locali e ad altre app SaaS, ad esempio Salesforce o Workday.  |  È supportato il SSO ad app di proprietà del cliente all'interno di tenant B2C di Azure AD. Non è supportato il SSO a Office 365 o ad altre app SaaS Microsoft e non Microsoft.
Ciclo di vita per i partner: gestito dall'organizzazione host (o che manda l'invito).  | Ciclo di vita per i clienti: in modalità self-service o gestito dall'applicazione.
Criteri di sicurezza e conformità: gestiti dall'organizzazione host (o che manda l'invito).  | Criteri di sicurezza e conformità: gestiti dall'applicazione.
Personalizzazione: viene usato il marchio dell'organizzazione host (o che manda l'invito).  |    Personalizzazione: gestita dall'applicazione. In genere viene eseguita una personalizzazione in base al prodotto, con l'organizzazione sullo sfondo.
Per altre informazioni: [post di blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentazione](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | Per altre informazioni: [pagina del prodotto](https://azure.microsoft.com/services/active-directory-b2c/), [documentazione](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)

