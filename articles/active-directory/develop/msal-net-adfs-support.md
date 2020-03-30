---
title: Supporto di AD FS in MSAL.NET Azure
titleSuffix: Microsoft identity platform
description: Informazioni sul supporto di Active Directory Federation Services (ADFS) in Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160761"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Supporto di Active Directory Federation Services in MSAL.NET
Active Directory Federation Services (ADFS) in Windows Server consente di aggiungere l'autenticazione e l'autorizzazione basate su OpenID Connect e OAuth 2.0 alle applicazioni in uso. Tali applicazioni possono, quindi, autenticare gli utenti direttamente in ADFS. Per altre informazioni, leggere [Scenari di ADFS per gli sviluppatori](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Microsoft Authentication Library for .NET (MSAL.NET) supporta due scenari per l'autenticazione in ADFS:

- MSAL.NET viene comunicato con Azure Active Directory, che a sua volta è *federato* con ADFS.
- MSAL.NET parla **direttamente** con un'autorità ADFS. Questa operazione è supportata solo da AD FS 2019 e versioni successive. Uno degli scenari evidenziati è il supporto di [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL si connette ad Azure AD, federato con AD FS
MSAL.NET supporta la connessione ad Azure AD, che accede agli utenti gestiti (utenti gestiti in Azure AD) o agli utenti federati (utenti gestiti da un altro provider di identità, ad esempio ADFS). MSAL.NET non sa del fatto che gli utenti sono federati. Per quanto riguarda, comunica con Azure AD.

[L'autorità](msal-client-application-configuration.md#authority) utilizzata in questo caso è l'autorità usuale (nome host dell'autorità, tenant, comune o organizzazioni).

### <a name="acquiring-a-token-interactively"></a>Acquisizione interattiva di un token
Quando si `AcquireTokenInteractive` chiama il metodo, l'esperienza utente è in genere:When you call the method, the user experience is typically:

1. L'utente immette l'ID account.
2. Azure AD visualizza brevemente il messaggio "Passare alla pagina dell'organizzazione".
3. L'utente viene reindirizzato alla pagina di accesso del provider di identità. La pagina di accesso viene in genere personalizzata con il logo dell'organizzazione.

Le versioni di ADFS supportate in questo scenario federato sono AD FS v2, AD FS v3 (Windows Server 2012 R2) e AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Acquisizione di un token tramite AcquireTokenByIntegratedAuthentication o AcquireTokenByUsernamePasswordAcquiring a token using AcquireTokenByIntegratedAuthentication or AcquireTokenByUsernamePassword
Quando si acquisisce `AcquireTokenByIntegratedAuthentication` un `AcquireTokenByUsernamePassword` token utilizzando i metodi or, MSAL.NET ottiene il provider di identità da contattare in base al nome utente.  MSAL.NET riceve un [token SAML 1.1](reference-saml-tokens.md) dopo aver contattato il provider di identità.  MSAL.NET fornisce quindi il token SAML ad Azure AD come asserzione utente (simile al [flusso per conto di](msal-authentication-flows.md#on-behalf-of)) per ottenere un token JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL si connette direttamente ad ADFS
MSAL.NET supporta la connessione ad AD FS 2019, che è conforme a Open ID Connect e comprende gli ambiti e PKCE. Questo supporto richiede l'applicazione di un service pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) a Windows Server. Quando ci si connette direttamente ad ADFS, l'autorità che si `https://mysite.contoso.com/adfs/`desidera utilizzare per compilare l'applicazione è simile a .

Attualmente, non è prevista una connessione diretta a:

- AD FS 16, in quanto non supporta PKCE e utilizza ancora le risorse, non l'ambito
- AD FS v2, che non è conforme a OIDC.

 Se è necessario supportare scenari che richiedono una connessione diretta ad AD FS 2016, usare la versione più recente della libreria di [autenticazione di Azure Active Directory.](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries) Dopo aver aggiornato il sistema locale ad AD FS 2019, sarà possibile usare MSAL.NET.

## <a name="next-steps"></a>Passaggi successivi

Per il caso federato, vedere Configurare il comportamento di accesso di [Azure Active Directory per un'applicazione usando un criterio di individuazione dell'area](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) di autenticazione principaleFor the federated case, see Configure Azure Active Directory sign in behavior for an application by using a Home Realm Discovery policy
