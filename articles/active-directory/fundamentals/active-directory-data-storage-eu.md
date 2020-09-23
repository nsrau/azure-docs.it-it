---
title: Archiviazione dei dati Identity per i clienti europei-Azure AD
description: Informazioni su dove Azure Active Directory archivia i dati relativi all'identità per i clienti Europai.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dc229b366a7816c861da665c73d6421e67545c6
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985916"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Archivi dati relativi all'identità per i clienti Europai - Azure Active Directory
I dati di identità vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione quando si sottoscrive un servizio online Microsoft, ad esempio Microsoft 365 e Azure. Per informazioni sul percorso di archiviazione dei dati di identità, è possibile usare la sezione [dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) di Microsoft Trust Center.

Per i clienti che hanno fornito un indirizzo in Europa, Azure AD mantiene la maggior parte dei dati di identità nei data center europei. In questo documento vengono fornite informazioni sui dati archiviati al di fuori dell'Europa da Azure AD Services.

## <a name="microsoft-azure-multi-factor-authentication"></a>Microsoft Azure Multi-Factor Authentication

Per la Multi-Factor Authentication di Azure basata sul cloud, l'autenticazione è completa nel data center più vicino all'utente. I Data Center per Azure Multi-Factor Authentication esistono in America del Nord, Europa e Asia Pacifico.

* L'autenticazione a più fattori tramite chiamate telefoniche ha origine da Data Center statunitensi e viene instradata dai provider globali.
* La funzionalità autenticazione a più fattori tramite SMS viene instradata dai provider globali.
* Le richieste di autenticazione a più fattori che usano le notifiche push dell'app Microsoft Authenticator che provengono dai data center dell'UE vengono elaborate nei data center dell'Unione europea.
    * I servizi specifici del fornitore di dispositivi, ad esempio le notifiche push Apple, possono trovarsi all'esterno dell'Europa.
* Le richieste di autenticazione a più fattori mediante codici di GIURAmento che provengono dai data center dell'UE vengono convalidate nell'Unione europea.

Per altre informazioni sulle informazioni sugli utenti raccolte da Azure server Multi-Factor Authentication (server di autenticazione a più fattori) e dall'autenticazione a più fattori di Azure basata sul cloud, vedere [raccolta di dati utente multi-factor authentication di Azure](../authentication/howto-mfa-reporting-datacollection.md).

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

I dati di configurazione dei criteri di Azure AD B2C e i contenitori di chiavi vengono archiviati nei data center statunitensi. Non contengono dati personali dell'utente. Per altre informazioni sulle configurazioni dei criteri, vedere l'articolo [Azure Active Directory B2C: criteri predefiniti](../../active-directory-b2c/user-flow-overview.md).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B archivia gli inviti con il collegamento di riscatto e le informazioni sull'URL di reindirizzamento nei data center degli Stati Uniti. Inoltre, l'indirizzo di posta elettronica degli utenti che annullano la sottoscrizione alla ricezione degli inviti B2B vengono archiviati anche nei data center statunitensi.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure Active Directory Domain Services archivia i dati utente nella stessa posizione della rete virtuale di Azure selezionata dal cliente. Se quindi la rete non è in Europa, i dati vengono replicati e archiviati al di fuori dell'Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federazione in Microsoft Exchange Server 2013
    
- ID applicazione (AppID): numero univoco generato dal sistema di autenticazione Azure Active Directory per identificare le organizzazioni di Exchange.
- Elenco di domini federati approvati per l'applicazione
- Chiave pubblica per la firma di token dell'applicazione 

Per ulteriori informazioni sulla Federazione in Microsoft Exchange Server, vedere l'articolo della Guida relativa alla [Federazione: Exchange 2013](/exchange/federation-exchange-2013-help) .


## <a name="other-considerations"></a>Altre considerazioni

I servizi e le applicazioni che si integrano con Azure AD hanno accesso ai dati di identità. Valutare ogni servizio e applicazione usato per determinare come vengono elaborati i dati di identità da tale servizio e applicazione specifica e se soddisfano i requisiti di archiviazione dei dati dell'azienda.

Per altre informazioni sulla residenza dei dati dei servizi Microsoft, vedere la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Percorso di archiviazione dei dati) di Microsoft Trust Center.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli:
- [Informazioni su Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

- [Azure AD la reimpostazione della password self-service](../authentication/concept-sspr-howitworks.md)

- [Informazioni su Azure Active Directory B2C](../../active-directory-b2c/overview.md)

- [Che cos'è Azure AD B2B Collaboration?](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)