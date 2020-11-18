---
title: Verifica in due passaggi Azure AD multi-factor authentication e ADFS-Azure Active Directory
description: Questo è il Azure AD Multi-Factor Authentication pagina che descrive come iniziare a usare Azure AD multi-factor authentication e AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247ebfa70de3e916894cccfc9374c4ddd4e761c2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837941"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Introduzione a Azure AD Multi-Factor Authentication e Active Directory Federation Services

<center>

![Introduzione a AMF e ADFS Azure AD](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se l'organizzazione ha federato la Active Directory locale con Azure Active Directory tramite AD FS, sono disponibili due opzioni per l'uso di Azure AD Multi-Factor Authentication.

* Proteggere le risorse cloud usando Azure AD Multi-Factor Authentication o Active Directory Federation Services
* Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication

Nella tabella seguente viene riepilogata l'esperienza di verifica tra la protezione delle risorse con Azure AD Multi-Factor Authentication e AD FS

| Esperienza di verifica - App basate su browser | Esperienza di verifica - App non basate su browser |
|:--- |:--- |
| Protezione delle risorse di Azure AD tramite Azure AD Multi-Factor Authentication |<li>Il primo passaggio di verifica viene eseguito in locale usando AD FS.</li> <li>Il secondo passaggio è un metodo basato su telefono eseguito mediante l'autenticazione cloud.</li> |
| Proteggere le risorse di Azure AD con Active Directory Federation Services |<li>Il primo passaggio di verifica viene eseguito in locale usando AD FS.</li><li>Il secondo passaggio viene eseguito in locale rispettando l'attestazione.</li> |

Avvertenze per le password dell’app rivolte agli utenti federati:

* Le password dell'app vengono verificate mediante l'autenticazione cloud e di conseguenza ignorano la federazione. La federazione viene usata attivamente solo durante l'impostazione della password dell'app.
* Le impostazioni locali di Controllo di accesso client non vengono rispettate dalle password dell'app.
* Si perde la funzionalità di registrazione dell'autenticazione locale per le password dell'app.
* La disabilitazione/eliminazione dell'account può richiedere fino a tre ore per la sincronizzazione della directory, ritardando la disabilitazione/eliminazione delle password dell'app nell'identità cloud.

Per informazioni sulla configurazione di Azure AD Multi-Factor Authentication o Azure server Multi-Factor Authentication con AD FS, vedere gli articoli seguenti:

* [Proteggere le risorse cloud usando Azure AD Multi-Factor Authentication e AD FS](howto-mfa-adfs.md)
* [Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con ADFS Server Windows 2012 R2](howto-mfaserver-adfs-2012.md)
* [Proteggere le risorse del cloud e locali mediante Server Azure Multi-Factor Authentication con AD FS 2.0](howto-mfaserver-adfs-2.md)
