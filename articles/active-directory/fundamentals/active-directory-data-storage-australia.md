---
title: Archiviazione dei dati di identità per i clienti australiani e neozelandesi - Azure ADIdentity data storage for Australian and New zel zel and New zel and New zel
description: Informazioni sulla posizione in cui Azure Active Directory archivia i dati relativi all'identità per i clienti australiani.
services: active-directory
author: msaburnley
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370302"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Archiviazione dei dati di identità per i clienti australiani e neozelandesi in Azure Active Directory

I dati di identità vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione durante la sottoscrizione a un servizio Microsoft Online, ad esempio Office 365 e Azure.Identity data is stored by Azure AD in a geographical location based on the address provided by your organization when sottocribing for a Microsoft Online service such as Office 365 and Azure. Per informazioni sulla posizione in cui sono archiviati i dati dei clienti Identity, è possibile utilizzare la sezione [Dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) del Centro protezione Microsoft.

> [!NOTE]
> I servizi e le applicazioni che si integrano con Azure AD hanno accesso ai dati dei clienti Identity.Services and applications that integrate with Azure AD have access to Identity Customer Data. Valutare ogni servizio e applicazione utilizzata per determinare il modo in cui i dati dei clienti di identità vengono elaborati da tale servizio e applicazione specifici e se soddisfano i requisiti di archiviazione dei dati dell'azienda. Per altre informazioni sulla residenza dei dati dei servizi Microsoft, vedere la sezione Where is your data located? (Percorso di archiviazione dei dati) di Microsoft Trust Center.

Per i clienti che hanno fornito un indirizzo in Australia o in Nuova èelanda, Azure AD mantiene i dati di identità per questi servizi all'interno dei data center australiani:For customers who provided an address in Australia or New eealand, Azure AD keeps identity data for these services within Australian datacenters: 
- Azure AD Directory Management 
- Authentication

Tutti gli altri servizi di Azure AD archiviano i dati dei clienti nei data center globali. Per individuare il data center per un servizio, vedere [Azure Active Directory : Dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Autenticazione a più fattori di Microsoft Azure (MFA)Microsoft Azure multi-factor authentication (MFA)

L'autenticazione a più fattori archivia i dati dei clienti identity nei data center globali. Per altre informazioni sulle informazioni utente raccolte e archiviate da Azure MFA e Azure MFA Server basato su cloud, vedere Raccolta dati utente di Azure Multi-Factor Authentication.To learn more about the user information collected and stored by cloud-based Azure MFA and Azure MFA Server, see [Azure Multi-Factor Authentication user data collection.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli:
- [Informazioni su Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
