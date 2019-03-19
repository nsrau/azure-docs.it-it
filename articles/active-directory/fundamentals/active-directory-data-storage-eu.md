---
title: Archivi dati relativi all'identità per i clienti europei - Azure Active Directory | Microsoft Docs
description: Informazioni su dove Azure Active Directory archivia i dati relativi all'identità per i clienti europei.
services: active-directory
author: eross-msft
manager: daveba
ms.author: lizross
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b21f82dc0a1eb8edf571da13e0d34fecae5f401b
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337686"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Archivi dati relativi all'identità per i clienti europei - Azure Active Directory
Azure Active Directory aiuta a gestire le identità degli utenti e a creare criteri di accesso basati su intelligence che consentono di proteggere le risorse dell'organizzazione. I dati di identità vengono archiviati in un percorso basato sull'indirizzo dell'organizzazione fornito quando è stato sottoscritto il servizio, ad esempio quando è stata effettuata la sottoscrizione di Office 365 o Azure. Per informazioni specifiche su dove vengono archiviati i dati di identità, è possibile usare la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.

Mentre Azure più dati correlati AD European identità rimangono nei data center europeo, esistono alcuni dati operativi, specifici del servizio che ha richiesto per normale funzionamento di Azure AD, che vengono archiviati negli Stati Uniti e non include tutti i dati personali.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>Dati archiviati al di fuori dei data center europei per i clienti europei

La maggior parte dei dati di identità europei relativi ad Azure AD, per le organizzazioni con sede in Europa, rimane in data center europei. I dati di Azure AD archiviati in data center europei e replicati anche in data center degli Stati Uniti includono:

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
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli:
- [Informazioni su Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Reimpostazione self-service delle password di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Informazioni su Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Che cos'è Azure AD B2B Collaboration?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
