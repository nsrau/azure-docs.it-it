---
title: Supporto di Azure AD FS in Microsoft Authentication Library per Python
titleSuffix: Microsoft identity platform
description: Informazioni sul supporto di Active Directory Federation Services (AD FS) in Microsoft Authentication Library per Python
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0aada339ab68eeb7f29eeb815611a8e434e6a998
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803649"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Supporto Active Directory Federation Services in MSAL per Python

Active Directory Federation Services (AD FS) in Windows Server consente di aggiungere l'autenticazione e l'autorizzazione basate su OpenID Connect e OAuth 2,0 alle app usando Microsoft Authentication Library (MSAL) per Python. Usando la libreria MSAL per Python, l'app è in grado di autenticare gli utenti direttamente con AD FS. Per ulteriori informazioni sugli scenari, vedere [ad FS scenari per gli sviluppatori](/windows-server/identity/ad-fs/ad-fs-development).

Esistono in genere due modi per eseguire l'autenticazione rispetto a AD FS:

- MSAL Python comunica con Azure Active Directory, che a sua volta è federato con altri provider di identità. La Federazione viene eseguita tramite AD FS. MSAL Python si connette a Azure AD, che consente di accedere agli utenti gestiti in Azure AD (utenti gestiti) o a utenti gestiti da un altro provider di identità, ad esempio AD FS (utenti federati). MSAL Python non sa che un utente è federato. Semplicemente comunica con Azure AD. L' [autorità](msal-client-application-configuration.md#authority) utilizzata in questo caso è l'autorità consueta (nome host autorità + tenant, comune o organizzazioni).
- MSAL Python parla direttamente con un'autorità AD FS. Questa operazione è supportata solo da AD FS 2019 e versioni successive.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Connettersi a Active Directory federato con AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Acquisire un token in modo interattivo per un utente federato

Di seguito viene illustrato se connettersi direttamente a Active Directory Federation Services (AD FS) o tramite Active Directory.

Quando si chiama `acquire_token_by_authorization_code` o `acquire_token_by_device_flow`, l'esperienza utente è in genere la seguente:

1. L'utente immette il proprio ID account.
2. Azure AD Visualizza brevemente il messaggio "accesso alla pagina dell'organizzazione" e l'utente viene reindirizzato alla pagina di accesso del provider di identità. La pagina di accesso viene in genere personalizzata con il logo dell'organizzazione.

Le versioni di AD FS supportate in questo scenario federato sono:
- Active Directory Federation Services FS V2
- Active Directory Federation Services V3 (Windows Server 2012 R2)
- Active Directory Federation Services V4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Acquisire un token tramite nome utente e password

Di seguito viene illustrato se connettersi direttamente a Active Directory Federation Services (AD FS) o tramite Active Directory.

Quando si acquisisce un token usando `acquire_token_by_username_password`, MSAL Python ottiene il provider di identità da contattare in base al nome utente. MSAL Python ottiene un [token SAML 1,1](reference-saml-tokens.md) dal provider di identità, che fornisce quindi a Azure ad che restituisce il token JSON Web (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Connessione diretta a AD FS

Quando si connette la directory alla AD FS, l'autorità che si vuole usare per compilare l'applicazione sarà simile `https://somesite.contoso.com/adfs/`

MSAL Python supporta ADFS 2019.

Non supporta una connessione diretta ad ADFS 2016 o ADFS v2. Se è necessario supportare scenari che richiedono una connessione diretta ad ADFS 2016, usare la versione più recente di ADAL Python. Dopo aver aggiornato il sistema locale ad ADFS 2019, è possibile usare MSAL Python.

## <a name="next-steps"></a>Passaggi successivi

- Per il caso federato, vedere [configurare il comportamento di accesso Azure Active Directory per un'applicazione usando un criterio di individuazione dell'area di autenticazione principale](../manage-apps/configure-authentication-for-federated-users-portal.md)