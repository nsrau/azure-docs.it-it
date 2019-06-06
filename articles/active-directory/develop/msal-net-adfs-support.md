---
title: Supporto di AD FS in Microsoft Authentication Library per .NET | Azure
description: Informazioni sul supporto di Active Directory Federation Services (ADFS) in Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676729"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Supporto di Active Directory Federation Services in MSAL.NET
Active Directory Federation Services (ADFS) in Windows Server consente di aggiungere OpenID Connect e l'autenticazione basata su OAuth 2.0 e autorizzazione alle applicazioni si sviluppa e si dispone di tali applicazioni autenticare gli utenti direttamente in ADFS. Per altre informazioni, leggere [AD FS scenari per gli sviluppatori](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Microsoft Authentication Library per .NET (MSAL.NET) supporta due scenari per l'autenticazione in AD FS:

- MSAL.NET comunica con Azure Active Directory, che a sua volta viene *federated* con AD FS.
- Interventi MSAL.NET *direttamente* presso un'autorità di AD FS, dove la versione di AD FS è la conformità a OpenID Connect (a partire in AD FS 2019). La connessione diretta a AD FS consente MSAL.NET per l'autenticazione con le App in esecuzione [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL si connette ad Azure AD, che è federato con AD FS
MSAL.NET supporta la connessione ad Azure AD, che esegue l'accesso gestito-users (utenti gestiti in Azure AD) o federated users (utenti gestiti da un altro provider di identità, ad esempio AD FS). MSAL.NET non conosce del fatto che gli utenti federati. Per quanto riguarda lo è, comunica con Azure AD.

Il [autorità](msal-client-application-configuration.md#authority) è utilizzare in questo caso è l'autorità consueto (nome host dell'autorità + tenant, comuni o organizzazioni).

### <a name="acquiring-a-token-interactively"></a>Acquisire un token in modo interattivo
Quando si chiama il `AcquireTokenInteractive` metodo, l'esperienza utente è in genere:

1. L'utente immette le ID dell'account.
2. Azure AD Visualizza brevemente il messaggio "Reindirizzamento alla pagina dell'organizzazione".
3. L'utente viene reindirizzato alla pagina di accesso del provider di identità. La pagina di accesso viene in genere personalizzata con il logo dell'organizzazione.

Versioni supportate di AD FS in questo scenario federato sono ADFS v2 e v3 di AD FS (Windows Server 2012 R2) e v4 di AD FS (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Acquisire un token mediante AcquireTokenByIntegratedAuthentication o AcquireTokenByUsernamePassword
Quando l'acquisizione di un token utilizzando il `AcquireTokenByIntegratedAuthentication` o `AcquireTokenByUsernamePassword` metodi, MSAL.NET ottiene di contattare il provider di identità basato sul nome utente.  MSAL.NET riceve un [token SAML 1.1](reference-saml-tokens.md) dopo aver contattato il provider di identità.  MSAL.NET fornisce quindi il token SAML con Azure AD come un'asserzione di utente (simili per il [flusso on-behalf-of](msal-authentication-flows.md#on-behalf-of)) per ottenere un token JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL si connette direttamente al servizio AD FS
MSAL.NET supporta la connessione ad AD FS 2019, ovvero Open ID Connect conformi. Quando ci si connette direttamente al servizio AD FS, l'autorità è opportuno utilizzare per compilare l'applicazione è simile a `https://mysite.contoso.com/adfs/`.

Non esistono attualmente, si prevede di supportare una connessione diretta a AD FS 2016 o ADFS v2 (che non sono conformità a OpenID Connect). Se è necessario supportare gli scenari che richiedono una connessione diretta a AD FS 2016, usare la versione più recente di [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md#microsoft-supported-client-libraries). Dopo aver aggiornato il sistema di un'istanza locale di AD FS 2019, sarà possibile usare MSAL.NET.
