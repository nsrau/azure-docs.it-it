---
title: Archiviazione dei dati Identity per i clienti australiani-Azure AD
description: Informazioni su dove Azure Active Directory archivia i dati relativi all'identità per i clienti australiani.
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
ms.openlocfilehash: cbe4befa824990c2f8b2627e7709913f564c0486
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751419"
---
# <a name="identity-data-storage-for-australian-customers-in-azure-active-directory"></a>Archiviazione dei dati Identity per i clienti australiani in Azure Active Directory

I dati di identità vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione quando si sottoscrive un servizio online Microsoft, ad esempio Office 365 e Azure. Per informazioni sulla posizione in cui vengono archiviati i dati dei clienti di identità, è possibile usare la sezione [dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) di Microsoft Trust Center.

> [!NOTE]
> I servizi e le applicazioni che si integrano con Azure AD hanno accesso ai dati dei clienti Identity. Valutare ogni servizio e applicazione usato per determinare il modo in cui i dati dei clienti Identity vengono elaborati da tale servizio e applicazione e se soddisfano i requisiti di archiviazione dei dati aziendali. Per ulteriori informazioni sulla residenza dei dati dei servizi Microsoft, vedere la posizione in cui si trovano i dati. sezione del Centro protezione Microsoft.

Per i clienti che hanno fornito un indirizzo in Australia, Azure AD mantiene i dati di identità per questi servizi nei data center australiani: 
- Gestione di Azure AD directory 
- Autenticazione

Tutti gli altri servizi Azure AD archiviano i dati dei clienti nei data center globali. Per trovare il Data Center per un servizio, vedere [Azure Active Directory: dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multi-factor authentication

L'autenticazione a più fattori archivia i dati dei clienti Identity nei data center globali. Per ulteriori informazioni sulle informazioni sugli utenti raccolte e archiviate da Azure multi-factor authentication e dal server di autenticazione a più fattori di Azure, vedere la pagina relativa alla [raccolta di dati utente multi-factor authentication Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli:
- [Informazioni su Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
