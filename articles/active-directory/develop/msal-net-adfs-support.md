---
title: Supporto AD FS in MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sul supporto di Active Directory Federation Services (AD FS) in Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b5124712e4002c9a3a6740986cafc18c12dbb98
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921947"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Supporto Active Directory Federation Services in MSAL.NET
Active Directory Federation Services (AD FS) in Windows Server consente di aggiungere l'autenticazione e l'autorizzazione basate su OpenID Connect e OAuth 2,0 alle applicazioni in fase di sviluppo. Tali applicazioni possono quindi autenticare gli utenti direttamente in AD FS. Per ulteriori informazioni, vedere [ad FS scenari per gli sviluppatori](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Microsoft Authentication Library per .NET (MSAL.NET) supporta due scenari per l'autenticazione rispetto a AD FS:

- MSAL.NET comunica con Azure Active Directory, che a sua volta è *federato* con ad FS.
- MSAL.NET comunica **direttamente** con un'autorità ADFS. Questa operazione è supportata solo da AD FS 2019 e versioni successive. Uno degli scenari più importanti è [Azure stack](https://azure.microsoft.com/overview/azure-stack/) supporto


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL si connette a Azure AD, che è federato con AD FS
MSAL.NET supporta la connessione a Azure AD, che consente di accedere a utenti gestiti (utenti gestiti in Azure AD) o a utenti federati (utenti gestiti da un altro provider di identità, ad esempio AD FS). MSAL.NET non è a conoscenza del fatto che gli utenti sono federati. Per quanto concerne, si parla di Azure AD.

L' [autorità](msal-client-application-configuration.md#authority) utilizzata in questo caso è l'autorità consueta (nome host autorità + tenant, comune o organizzazioni).

### <a name="acquiring-a-token-interactively"></a>Acquisizione di un token in modo interattivo
Quando si chiama il metodo `AcquireTokenInteractive`, l'esperienza utente è in genere:

1. L'utente immette il proprio ID account.
2. Azure AD Visualizza brevemente il messaggio "portarsi alla pagina dell'organizzazione".
3. L'utente viene reindirizzato alla pagina di accesso del provider di identità. La pagina di accesso viene in genere personalizzata con il logo dell'organizzazione.

Le versioni di AD FS supportate in questo scenario federato sono AD FS V2, AD FS V3 (Windows Server 2012 R2) e AD FS V4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Acquisizione di un token tramite AcquireTokenByIntegratedAuthentication o AcquireTokenByUsernamePassword
Quando si acquisisce un token usando i metodi `AcquireTokenByIntegratedAuthentication` o `AcquireTokenByUsernamePassword`, MSAL.NET ottiene il provider di identità da contattare in base al nome utente.  MSAL.NET riceve un [token SAML 1,1](reference-saml-tokens.md) dopo aver contattato il provider di identità.  MSAL.NET fornisce quindi il token SAML per Azure AD come asserzione utente (simile al [flusso per conto di](msal-authentication-flows.md#on-behalf-of)) per ottenere un JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL si connette direttamente a AD FS
MSAL.NET supporta la connessione a AD FS 2019, che è conforme a Open ID Connect e che comprende PKCE e gli ambiti. Questo supporto richiede che un Service Pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) venga applicato a Windows Server. Quando ci si connette direttamente a AD FS, l'autorità che si vuole usare per compilare l'applicazione è simile a `https://mysite.contoso.com/adfs/`.

Attualmente non sono previsti piani per supportare una connessione diretta a:

- AD FS 16, perché non supporta PKCE e usa ancora le risorse, non l'ambito
- AD FS V2, che non è conforme a OIDC.

 Se è necessario supportare scenari che richiedono una connessione diretta a AD FS 2016, usare la versione più recente di [Azure Active Directory libreria di autenticazione](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Dopo aver aggiornato il sistema locale a AD FS 2019, sarà possibile usare MSAL.NET.

## <a name="next-steps"></a>Passaggi successivi

Per il caso federato, vedere [configurare il comportamento di accesso Azure Active Directory per un'applicazione usando un criterio di individuazione dell'area di autenticazione principale](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)
