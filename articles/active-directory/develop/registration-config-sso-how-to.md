---
title: Configurare l'accesso Single Sign-on dell'applicazione | Microsoft Docs
description: Come configurare l'accesso Single Sign-on per un'applicazione personalizzata che si sta sviluppando e registrando con Azure AD.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc9f20903d65fbde161d26ed3e194e63d341ccb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320948"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Come configurare l'accesso Single Sign-on per un'applicazione

L'abilitazione dell'accesso Single Sign-on (SSO) federato nell'applicazione avviene automaticamente quando si esegue la federazione tramite Azure AD per OpenID Connect, SAML 2.0 o WS-Fed. Se gli utenti finali devono eseguire l'accesso nonostante dispongano già di una sessione esistente con Azure AD, è probabile che l'applicazione non sia configurata correttamente.

* Se si usa ADAL/MSAL, accertarsi di avere impostato **PromptBehavior** su **Auto** anziché su **Sempre**.

* Se si sta creando un'applicazione per dispositivi mobili, potrebbe essere necessaria della configurazione aggiuntiva per abilitare l'accesso Single Sign-On negoziato o non negoziato.

Per Android, vedere [Abilitare l'accesso Single Sign-On tra app in Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Per iOS, vedere [Abilitare l'accesso Single Sign-On tra app in iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Passaggi successivi

[Accesso Single Sign-On AD Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Abilitare l'accesso Single Sign-On tra app in Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Abilitare l'accesso Single Sign-On tra app in iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Consent and Permissioning for AzureAD v2.0 converged Apps](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes) (Consenso e concessione delle autorizzazioni per le app con convergenza di Azure Active Directory v2.0)<br>

[Azure AD in Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
