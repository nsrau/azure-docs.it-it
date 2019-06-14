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
ms.openlocfilehash: 93ac5ef5f03f800a8f90259db3e382b3bc5c5e2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235171"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Archivi dati relativi all'identità per i clienti europei - Azure Active Directory
Dati di identità vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione per la sottoscrizione per un servizio Online Microsoft come Office 365 e Azure. Per informazioni su dove vengono archiviati i dati di identità, è possibile usare la [dove sono i dati che si trova?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) sezione di Microsoft Trust Center.

Per i clienti che hanno fornito un indirizzo in Europa, Azure AD mantiene la maggior parte dei dati di identità nei data center europeo. Questo documento vengono fornite informazioni su tutti i dati vengono archiviati all'esterno di Europa per servizi di Azure AD.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticazione di Microsoft Azure a più fattori (MFA)
    
- L'autenticazione di tutto a due fattori tramite chiamate telefoniche o SMS provengono dai Data Center degli Stati Uniti e viene instradato anche dai provider globale.
- Push di notifiche con Microsoft Authenticator app provenienti da Microsoft Data Center. Inoltre, servizi specifici del fornitore dispositivo anche possono entrare in gioco e questi servizi probabilmente di fuori di Europa.
- I codici OATH vengono sempre convalidati negli Stati Uniti. 

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

I dati di configurazione dei criteri di Active Directory B2C di Azure e contenitori di chiavi vengono archiviati in Data Center degli Stati Uniti. Questi non contengono alcun dati personali degli utenti. Per altre informazioni sulle configurazioni dei criteri, vedere l'articolo [Azure Active Directory B2C: criteri predefiniti](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B archivi inviti con riscatto collegano e reindirizzare le informazioni sull'URL in Data Center degli Stati Uniti. Inoltre, indirizzo di posta elettronica di utenti a cui annullare la sottoscrizione a inviti B2B vengono anche archiviati in Data Center degli Stati Uniti.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure Active Directory Domain Services archivia i dati utente nella stessa posizione della rete virtuale di Azure selezionata dal cliente. Se quindi la rete non è in Europa, i dati vengono replicati e archiviati al di fuori dell'Europa.

## <a name="other-considerations"></a>Altre considerazioni

Servizi e applicazioni che si integrano con Azure AD hanno accesso ai dati di identità. Valutare ogni servizio e l'applicazione in uso per determinare la modalità di elaborazione dati sull'identità da tale servizio specifico e applicazione, e il fatto che soddisfino i requisiti di archiviazione dei dati dell'azienda.

Per altre informazioni sulla residenza dei dati dei servizi Microsoft, vedere la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli:
- [Informazioni su Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Reimpostazione self-service delle password di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Informazioni su Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Che cos'è Azure AD B2B Collaboration?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
