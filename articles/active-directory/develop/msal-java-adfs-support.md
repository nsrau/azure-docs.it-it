---
title: Supporto AD FS in Microsoft Authentication Library per Java
titleSuffix: Microsoft identity platform
description: Informazioni sul supporto di Active Directory Federation Services (AD FS) in Microsoft Authentication Library per Java (MSAL4j).
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c286ed7467560c90f9cf9594e75af06d6cdcc5d1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482083"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Supporto Active Directory Federation Services in MSAL per Java

Active Directory Federation Services (AD FS) in Windows Server consente di aggiungere l'autenticazione e l'autorizzazione basate su OpenID Connect e OAuth 2,0 all'app Microsoft Authentication Library per Java (MSAL per Java). Una volta integrato, l'app è in grado di autenticare gli utenti in AD FS, federati tramite Azure AD. Per ulteriori informazioni sugli scenari, vedere [ad FS scenari per gli sviluppatori](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers).

Un'app che usa MSAL per Java comunicherà con Azure Active Directory (Azure AD), che quindi federa a AD FS.

MSAL per Java si connette a Azure AD, che consente di accedere agli utenti gestiti in Azure AD (utenti gestiti) o a utenti gestiti da un altro provider di identità, ad esempio AD FS (utenti federati). MSAL per Java non sa che un utente è federato. Semplicemente comunica con Azure AD.

L' [autorità](msal-client-application-configuration.md#authority) utilizzata in questo caso è l'autorità consueta (nome host autorità + tenant, comune o organizzazioni).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Acquisire un token in modo interattivo per un utente federato

Quando si chiama `ConfidentialClientApplication.AcquireToken()` o `PublicClientApplication.AcquireToken()` con `AuthorizationCodeParameters` o `DeviceCodeParameters`, l'esperienza utente è in genere:

1. L'utente immette il proprio ID account.
2. Azure AD Visualizza brevemente "accesso alla pagina dell'organizzazione" e l'utente viene reindirizzato alla pagina di accesso del provider di identità. La pagina di accesso viene in genere personalizzata con il logo dell'organizzazione.

Le versioni di AD FS supportate in questo scenario federato sono:
- Active Directory Federation Services FS V2
- Active Directory Federation Services V3 (Windows Server 2012 R2)
- Active Directory Federation Services V4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Acquisire un token tramite nome utente e password

Quando si acquisisce un token usando `ConfidentialClientApplication.AcquireToken()` o `PublicClientApplication.AcquireToken()` con `IntegratedWindowsAuthenticationParameters` o `UsernamePasswordParameters`, MSAL per Java ottiene il provider di identità da contattare in base al nome utente. MSAL per Java ottiene un token [token SAML 1,1](reference-saml-tokens.md) dal provider di identità, che fornisce quindi a Azure ad che restituisce il token JSON Web (JWT).

## <a name="next-steps"></a>Passaggi successivi

Per il caso federato, vedere [configurare il comportamento di accesso Azure Active Directory per un'applicazione usando un criterio di individuazione dell'area di autenticazione principale](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)