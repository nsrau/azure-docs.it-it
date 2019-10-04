---
title: Archivi dati relativi all'identità per i clienti europei - Azure Active Directory | Microsoft Docs
description: Informazioni su dove Azure Active Directory archivia i dati relativi all'identità per i clienti europei.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/04/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 178f81cf42e5c57be4a0b69ada6560d46951a3a3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942853"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Archivi dati relativi all'identità per i clienti europei - Azure Active Directory
I dati di identità vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione quando si sottoscrive un servizio online Microsoft, ad esempio Office 365 e Azure. Per informazioni sul percorso di archiviazione dei dati di identità, è possibile usare la sezione [dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) di Microsoft Trust Center.

Per i clienti che hanno fornito un indirizzo in Europa, Azure AD mantiene la maggior parte dei dati di identità nei data center europei. In questo documento vengono fornite informazioni sui dati archiviati al di fuori dell'Europa da Azure AD Services.

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multi-factor authentication
    
- Tutte le autenticazioni a due fattori che usano chiamate telefoniche o SMS hanno origine da Data Center statunitensi e vengono instradate anche dai provider globali.
- Le notifiche push che usano l'app Microsoft Authenticator provengono dai data center statunitensi. Inoltre, è possibile che anche i servizi specifici del fornitore del dispositivo entrino in gioco e questi servizi siano all'esterno dell'Europa.
- I codici OATH vengono sempre convalidati negli Stati Uniti. 

Per altre informazioni sulle informazioni sugli utenti raccolte dal server Azure multi-factor authentication (server di autenticazione a più fattori) e dall'autenticazione a più fattori di Azure basata sul cloud, vedere [raccolta di dati utente di Azure multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting-datacollection).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

I dati di configurazione dei criteri di Azure AD B2C e i contenitori di chiavi vengono archiviati nei data center statunitensi. Non contengono dati personali dell'utente. Per altre informazioni sulle configurazioni dei criteri, vedere l'articolo [Azure Active Directory B2C: criteri predefiniti](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B archivia gli inviti con il collegamento di riscatto e le informazioni sull'URL di reindirizzamento nei data center degli Stati Uniti. Inoltre, l'indirizzo di posta elettronica degli utenti che annullano la sottoscrizione alla ricezione degli inviti B2B vengono archiviati anche nei data center statunitensi.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Servizi di dominio Microsoft Azure Active Directory (Azure AD DS)

Azure Active Directory Domain Services archivia i dati utente nella stessa posizione della rete virtuale di Azure selezionata dal cliente. Se quindi la rete non è in Europa, i dati vengono replicati e archiviati al di fuori dell'Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federazione in Microsoft Exchange Server 2013
    
- ID applicazione (AppID): numero univoco generato dal sistema di autenticazione Azure Active Directory per identificare le organizzazioni di Exchange.
- Elenco di domini federati approvati per l'applicazione
- Chiave pubblica per la firma di token dell'applicazione 

Per ulteriori informazioni sulla Federazione in Microsoft Exchange Server, vedere [Federazione: Articolo della Guida](https://docs.microsoft.com/exchange/federation-exchange-2013-help) di Exchange 2013.


## <a name="other-considerations"></a>Altre considerazioni

I servizi e le applicazioni che si integrano con Azure AD hanno accesso ai dati di identità. Valutare ogni servizio e applicazione usato per determinare come vengono elaborati i dati di identità da tale servizio e applicazione specifica e se soddisfano i requisiti di archiviazione dei dati dell'azienda.

Per altre informazioni sulla residenza dei dati dei servizi Microsoft, vedere la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli:
- [Informazioni su Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)

- [Reimpostazione self-service delle password di Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-overview)

- [Informazioni su Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)

- [Che cos'è Azure AD B2B Collaboration?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

- [Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)
