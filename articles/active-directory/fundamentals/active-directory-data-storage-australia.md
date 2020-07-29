---
title: Archiviazione dei dati Identity per i clienti australiani e neozelandesi-Azure AD
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
ms.openlocfilehash: 850298719d5636e964b0c338d7a2a4cc9bb8aece
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77370302"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Archiviazione dei dati Identity per i clienti australiani e neozelandesi in Azure Active Directory

I dati di identità vengono archiviati da Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione quando si sottoscrive un servizio online Microsoft, ad esempio Office 365 e Azure. Per informazioni sulla posizione in cui vengono archiviati i dati dei clienti di identità, è possibile usare la sezione [dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) di Microsoft Trust Center.

> [!NOTE]
> I servizi e le applicazioni che si integrano con Azure AD hanno accesso ai dati dei clienti Identity. Valutare ogni servizio e applicazione usato per determinare il modo in cui i dati dei clienti Identity vengono elaborati da tale servizio e applicazione e se soddisfano i requisiti di archiviazione dei dati aziendali. Per altre informazioni sulla residenza dei dati dei servizi Microsoft, vedere la sezione Where is your data located? (Percorso di archiviazione dei dati) di Microsoft Trust Center.

Per i clienti che hanno fornito un indirizzo in Australia o Nuova Zelanda, Azure AD mantiene i dati di identità per questi servizi nei data center australiani: 
- Gestione di Azure AD directory 
- Authentication

Tutti gli altri servizi Azure AD archiviano i dati dei clienti nei data center globali. Per trovare il Data Center per un servizio, vedere [Azure Active Directory: dove si trovano i dati?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

## <a name="microsoft-azure-multi-factor-authentication-mfa"></a>Microsoft Azure multi-factor authentication

L'autenticazione a più fattori archivia i dati dei clienti Identity nei data center globali. Per ulteriori informazioni sulle informazioni sugli utenti raccolte e archiviate da Azure multi-factor authentication e dal server di autenticazione a più fattori di Azure, vedere la pagina relativa alla [raccolta di dati utente multi-factor authentication Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-data-residency).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli:
- [Informazioni su Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)
