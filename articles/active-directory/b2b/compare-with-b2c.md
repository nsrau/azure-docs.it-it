---
title: Confrontare Collaborazione B2B e B2C di Azure Active Directory | Microsoft Docs
description: Differenze tra Collaborazione B2B e B2C di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 01/30/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 4ec9a891777cc8d33b1256a8eb68ea814563b8cc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471097"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Confrontare Collaborazione B2B e B2C di Azure Active Directory

Sia Collaborazione B2B che B2C di Azure Active Directory consentono di lavorare con utenti esterni in Azure AD. Di seguito è riportato un confronto tra le diverse caratteristiche.

**Azure AD B2B** è destinato alle aziende che vogliono condividere file e risorse in modo sicuro con utenti esterni per consentire la collaborazione. Un amministratore di Azure imposta B2B nel portale di Azure e Azure AD si occupa della federazione tra l'azienda e i partner esterni. Per accedere alle risorse condivise, gli utenti usano un semplice processo di invito e riscatto con il proprio account aziendale o dell'istituto di istruzione o con un account di posta elettronica qualsiasi.
 
**Azure AD B2C** è destinato principalmente alle aziende e agli sviluppatori che creano app usate direttamente dai clienti. Con Azure AD B2C, gli sviluppatori possono usare Azure AD come sistema di identità completo per la propria applicazione, consentendo al contempo ai clienti di accedere con un'identità già disponibile (ad esempio Facebook o Gmail).

La tabella seguente offre un confronto dettagliato.


Funzionalità di Collaborazione B2B |     Offerta autonoma di B2C di Azure AD
-------- | --------
Destinatari: organizzazioni che vogliono poter autenticare gli utenti di un'organizzazione partner, indipendentemente dal provider di identità. | Destinatari: invitare i clienti di app Web e per dispositivi mobili, sia individui, clienti istituzionali oppure organizzazioni, in Azure AD.
Identità supportate: dipendenti con account aziendale o dell'istituto di istruzione, partner con account aziendale o dell'istituto di istruzione oppure qualsiasi indirizzo email. A breve sarà disponibile il supporto per la federazione diretta.  | Identità supportate: utenti consumer con account di applicazioni locali (qualsiasi indirizzo email o nome utente) o qualsiasi identità social supportata con federazione diretta.
Gli utenti esterni vengono gestiti nella stessa directory dei dipendenti, ma con annotazioni speciali. Possono essere gestiti in modo analogo ai dipendenti, possono essere aggiunti agli stessi gruppi e così via  | Gli utenti esterni vengono gestiti nella directory dell'applicazione. Vengono gestiti separatamente rispetto alla directory di dipendenti e partner dell'organizzazione (se disponibile).
La funzione Single Sign-On (SSO) per tutte le app connesse ad Azure AD è supportata. È possibile ad esempio concedere l'accesso a Office 365 oppure ad app locali e ad altre app SaaS, ad esempio Salesforce o Workday.  |  È supportato il SSO ad app di proprietà del cliente all'interno di tenant B2C di Azure AD. Non è supportato il SSO a Office 365 o ad altre app SaaS Microsoft e non Microsoft.
Ciclo di vita per i partner: gestito dall'organizzazione host (o che manda l'invito).  | Ciclo di vita per i clienti: in modalità self-service o gestito dall'applicazione.
Criteri di sicurezza e conformità: gestiti dall'organizzazione host (o che manda l'invito), ad esempio con [criteri di accesso condizionale](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access).  | Criteri di sicurezza e conformità: gestito dall'applicazione.
Personalizzazione: viene usato il marchio dell'organizzazione host (o che manda l'invito).  |    Personalizzazione: gestito dall'applicazione. In genere viene eseguita una personalizzazione in base al prodotto, con l'organizzazione sullo sfondo.
Per altre informazioni, vedere: [Post di blog](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [documentazione](what-is-b2b.md)  | Per altre informazioni, vedere: [pagina del prodotto](https://azure.microsoft.com/services/active-directory-b2c/), [documentazione](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Proprietà dell'utente di Collaborazione B2B](user-properties.md)

