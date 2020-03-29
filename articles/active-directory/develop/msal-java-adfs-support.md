---
title: Supporto di ADFS (MSAL per Java)
titleSuffix: Microsoft identity platform
description: Informazioni sul supporto di Active Directory Federation Services (ADFS) in Microsoft Authentication Library per Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696215"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Supporto di Active Directory Federation Services in MSAL per Java

Active Directory Federation Services (ADFS) in Windows Server consente di aggiungere l'autenticazione e l'autorizzazione basate su OpenID Connect e OAuth 2.0 all'app Microsoft Authentication Library for Java (MSAL for Java). Una volta integrata, l'app può autenticare gli utenti in ADFS, federati tramite Azure AD. Per ulteriori informazioni sugli scenari, vedere Scenari di [ADFS per gli sviluppatori](/windows-server/identity/ad-fs/ad-fs-development).

Un'app che usa MSAL per Java rivolgerà Azure Active Directory (Azure AD), che quindi esegue la federazione ad AD FS.

MSAL per Java si connette ad Azure AD, che accede agli utenti gestiti in Azure AD (utenti gestiti) o agli utenti gestiti da un altro provider di identità, ad esempio AD FS (utenti federati). MSAL per Java non sa che un utente è federato. Viene semplice comunicato con Azure AD.

[L'autorità](msal-client-application-configuration.md#authority) utilizzata in questo caso è l'autorità usuale (nome host dell'autorità, tenant, comune o organizzazioni).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Acquisire un token in modo interattivo per un utente federatoAcquire a token interactively for a federated user

Quando si `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` chiama `AuthorizationCodeParameters` `DeviceCodeParameters`o con o , l'esperienza utente è in genere:

1. L'utente immette l'ID account.
2. Azure AD visualizza brevemente "Passare alla pagina dell'organizzazione" e l'utente viene reindirizzato alla pagina di accesso del provider di identità. La pagina di accesso viene in genere personalizzata con il logo dell'organizzazione.

Le versioni di ADFS supportate in questo scenario federato sono:The supported AD FS versions in this federated scenario are:
- ADFS di Active Directory Federation Services v2
- Active Directory Federation Services v3 (Windows Server 2012 R2)
- Active Directory Federation Services v4 (ADFS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Acquisire un token tramite nome utente e password

Quando si acquisisce `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` un `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters`token utilizzando o con o , MSAL per Java ottiene il provider di identità da contattare in base al nome utente. MSAL per Java ottiene un token [SAML 1.1](reference-saml-tokens.md) dal provider di identità, che fornisce quindi ad Azure AD che restituisce il token JSON Web (JWT).

## <a name="next-steps"></a>Passaggi successivi

Per il caso federato, vedere Configurare il comportamento di accesso di [Azure Active Directory per un'applicazione usando un criterio di individuazione dell'area](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) di autenticazione principaleFor the federated case, see Configure Azure Active Directory sign in behavior for an application by using a Home Realm Discovery policy