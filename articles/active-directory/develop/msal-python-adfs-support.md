---
title: Azure AD FS support (MSAL Python)
titleSuffix: Microsoft identity platform
description: Informazioni sul supporto di Active Directory Federation Services (ADFS) nella libreria di autenticazione Microsoft per PythonLearn about Active Directory Federation Services (AD FS) support in Microsoft Authentication Library for Python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699547"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Supporto di Active Directory Federation Services in MSAL per Python

Active Directory Federation Services (ADFS) in Windows Server consente di aggiungere l'autenticazione e l'autorizzazione basate su OpenID Connect e OAuth 2.0 alle app usando Microsoft Authentication Library (MSAL) per Python. Usando la libreria MSAL per Python, l'app può autenticare gli utenti direttamente in AD FS. Per ulteriori informazioni sugli scenari, vedere Scenari di [ADFS per gli sviluppatori](/windows-server/identity/ad-fs/ad-fs-development).

Esistono in genere due modi per eseguire l'autenticazione in ADFS:There are usually two ways of authenticating against AD FS:

- MSAL Python comunica con Azure Active Directory, che a sua volta è federato con altri provider di identità. La federazione avviene tramite ADFS. MSAL Python si connette ad Azure AD, che accede agli utenti gestiti in Azure AD (utenti gestiti) o agli utenti gestiti da un altro provider di identità, ad esempio AD FS (utenti federati). MSAL Python non sa che un utente è federato. Viene semplice comunicato con Azure AD. [L'autorità](msal-client-application-configuration.md#authority) utilizzata in questo caso è l'autorità usuale (nome host dell'autorità, tenant, comune o organizzazioni).
- MSAL Python comunica direttamente con un'autorità ADFS. Questa operazione è supportata solo da AD FS 2019 e versioni successive.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Connettersi ad Active Directory federata con ADFSConnect to Active Directory federated with AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Acquisire un token in modo interattivo per un utente federatoAcquire a token interactively for a federated user

Quanto segue si applica se ci si connette direttamente ad Active Directory Federation Services (ADFS) o tramite Active Directory.

Quando si `acquire_token_by_authorization_code` `acquire_token_by_device_flow`chiama o , l'esperienza utente è in genere la seguente:

1. L'utente immette l'ID account.
2. Azure AD visualizza brevemente il messaggio "Passare alla pagina dell'organizzazione" e l'utente viene reindirizzato alla pagina di accesso del provider di identità. La pagina di accesso viene in genere personalizzata con il logo dell'organizzazione.

Le versioni di ADFS supportate in questo scenario federato sono:The supported AD FS versions in this federated scenario are:
- ADFS di Active Directory Federation Services v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (ADFS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Acquisire un token tramite nome utente e password

Quanto segue si applica se ci si connette direttamente ad Active Directory Federation Services (ADFS) o tramite Active Directory.

Quando si acquisisce `acquire_token_by_username_password`un token utilizzando , MSAL Python ottiene il provider di identità da contattare in base al nome utente. MSAL Python ottiene un [token SAML 1.1](reference-saml-tokens.md) dal provider di identità, che fornisce quindi ad Azure AD che restituisce il token JSON Web (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Connessione diretta ad ADFSConnecting directly to AD FS

Quando si connette la directory ad ADFS, l'autorità che si desidera utilizzare per compilare l'applicazione sarà qualcosa di simile`https://somesite.contoso.com/adfs/`

MSAL Python supporta ADFS 2019.

Non supporta una connessione diretta ad ADFS 2016 o ADFS v2. Se è necessario supportare scenari che richiedono una connessione diretta ad ADFS 2016, utilizzare la versione più recente di ADAL Python. Dopo aver aggiornato il sistema locale ad ADFS 2019, è possibile utilizzare MSAL Python.

## <a name="next-steps"></a>Passaggi successivi

- Per il caso federato, vedere Configurare il comportamento di accesso di [Azure Active Directory per un'applicazione usando un criterio di individuazione dell'area](../manage-apps/configure-authentication-for-federated-users-portal.md) di autenticazione principaleFor the federated case, see Configure Azure Active Directory sign in behavior for an application by using a Home Realm Discovery policy