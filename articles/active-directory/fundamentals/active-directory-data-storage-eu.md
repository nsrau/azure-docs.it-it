---
title: Percorso di archiviazione dei dati di identità per i clienti europei di Azure AD | Microsoft Docs
description: Informazioni su dove Microsoft Azure Active Directory archivia i dati relativi all'identità per i clienti europei.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 6aa2307123d62983f7afde3d871e8aa96e0abb5d
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2018
ms.locfileid: "51976894"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Percorso di archiviazione dei dati di identità per i clienti europei di Microsoft Azure Active Directory (Azure AD)
Azure AD aiuta a gestire le identità degli utenti e a creare criteri di accesso basati su intelligence che consentono di proteggere le risorse dell'organizzazione. I dati di identità vengono archiviati in un percorso basato sull'indirizzo dell'organizzazione fornito quando è stato sottoscritto il servizio, ad esempio quando è stata effettuata la sottoscrizione di Office 365 o Azure. Per informazioni specifiche su dove vengono archiviati i dati di identità, è possibile usare la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.

Anche se la maggior parte dei dati di identità europei relativi ad Azure AD rimane nei data center europei, tuttavia cinque attributi relativi agli utenti vengono in genere archiviati nei data center degli Stati Uniti. Questi attributi sono GivenName, Surname, userPrincipalName, Domain e PasswordHash. L'attributo PasswordHash può fare eccezione e non essere archiviato negli Stati Uniti se un utente usa un metodo di autenticazione federata locale che impedisce la sincronizzazione del valore di PasswordHash con Azure AD. Esistono anche alcuni dati operativi specifici del servizio, necessari per il normale funzionamento di Azure AD, che vengono archiviati negli Stati Uniti e non includono dati personali.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Dati archiviati al di fuori dei data center europei per i clienti europei

La maggior parte dei dati di identità europei relativi ad Azure AD, per le organizzazioni con sede in Europa, rimane in data center europei. I dati di Azure AD archiviati in data center europei e replicati anche in data center degli Stati Uniti includono:

- **Attributi relativi all'identità**

    Gli attributi relativi all'identità seguenti verranno replicati negli Stati Uniti:

    - GivenName
    - Surname
    - userPrincipalName
    - Domain
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **Microsoft Azure Multi-Factor Authentication (MFA) e reimpostazione password self-service (SSPR) di Azure AD**
    
    MFA archivia tutti i dati utente inattivi nei data center europei. Alcuni dati specifici del servizio MFA vengono tuttavia archiviati negli Stati Uniti, tra cui:
    
    - L'autenticazione a due fattori e i dati personali correlati possono essere archiviati negli Stati Uniti se si usa MFA o SSPR.
        - Tutte le operazioni di autenticazione a due fattori che usa chiamate telefoniche o SMS potrebbero essere completate da gestori telefonici degli Stati Uniti.
        - Le notifiche push che usano l'app Microsoft Authenticator richiedono notifiche dal servizio di notifica del produttore (Apple o Google), che potrebbe essere al di fuori dell'Europa.
        - I codici OATH vengono sempre convalidati negli Stati Uniti. 
    - Alcuni log MFA e SSPR vengono archiviati negli Stati Uniti per 30 giorni, indipendentemente dal tipo di autenticazione.

- **Microsoft Azure Active Directory B2C (Azure AD B2C)**

    Active Directory B2C archivia tutti i dati utente inattivi nei data center europei. I log operativi (con i dati personali rimossi) rimangono tuttavia nella posizione da cui la persona accede ai servizi. Se ad esempio un utente B2C accede al servizio negli Stati Uniti, i log operativi rimangono negli Stati Uniti. Inoltre, tutti i dati di configurazione dei criteri non contenenti dati personali vengono archiviati solo negli Stati Uniti. Per altre informazioni sulle configurazioni dei criteri, vedere l'articolo [Azure Active Directory B2C: criteri predefiniti](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

- **Microsoft Azure Active Directory B2B (Azure AD B2B)** 
    
    Active Directory B2B archivia tutti i dati utente inattivi nei data center europei. B2B archivia tuttavia i metadati non personali in tabelle nei data center degli Stati Uniti. Questa tabella include campi quali redeemUrl, invitationTicket, resource tenant Id, InviteRedirectUrl e InviterAppId.

- **Microsoft Azure Active Directory Domain Services (Azure AD DS)**

    Azure Active Directory Domain Services archivia i dati utente nella stessa posizione della rete virtuale di Azure selezionata dal cliente. Se quindi la rete non è in Europa, i dati vengono replicati e archiviati al di fuori dell'Europa.

- **Servizi e app integrati con Azure AD**

    I servizi e le app che si integrano con Azure AD hanno accesso ai dati di identità. Valutare ogni servizio e app per determinare come i dati di identità vengono elaborati da tale specifico servizio e app e se soddisfano i requisiti di archiviazione dei dati della società.

    Per altre informazioni sulla residenza dei dati dei servizi Microsoft, vedere la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli.
- [Informazioni su Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
- [Reimpostazione self-service delle password di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)
- [Informazioni su Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Che cos'è Azure AD B2B Collaboration?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
