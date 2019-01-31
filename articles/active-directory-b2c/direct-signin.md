---
title: Configurare l'accesso diretto tramite Active Directory B2C | Microsoft Docs
description: Informazioni su come precompilare il nome di accesso o eseguire l'accesso diretto a un provider di identità di social networking.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c66fc9ecb608d211c839bab720e8f3f37b96fd5d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55169230"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Configurare l'accesso diretto tramite Active Directory B2C

Quando si configura l'accesso per l'applicazione tramite Azure Active Directory (AD) B2C, è possibile precompilare il nome di accesso o eseguire l'accesso diretto a un provider di identità di social networking specifico, ad esempio Facebook, LinkedIn o un account Microsoft. 

## <a name="prepopulate-the-sign-in-name"></a>Precompilare il nome di accesso

Durante una procedura di accesso utente, un'applicazione relying party può avere come destinazione un nome utente o un nome di dominio specifico. Quando ha un utente come destinazione, un'applicazione può specificare, nella richiesta di autorizzazione, il parametro di query `login_hint` con il nome di accesso utente. Azure AD B2C inserisce automaticamente il nome di accesso, mentre l'utente deve solo immettere la password.

![Uso di hint di accesso](./media/direct-signin/login-hint.png) 

L'utente può modificare il valore nella casella di testo di accesso.

Se si usa un criterio personalizzato, eseguire l'override del profilo tecnico `SelfAsserted-LocalAccountSignin-Email`. Nella sezione `<InputClaims>` impostare il valore DefaultValue dell'attestazione signInName su `{OIDC:LoginHint}`. La variabile `{OIDC:LoginHint}` contiene il valore del parametro `login_hint`. Azure AD B2C legge il valore dell'attestazione signInName e precompila la casella di testo signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Reindirizzare l'accesso a un provider di social networking

Se la procedura di accesso per l'applicazione è stata configurata per includere gli account di social networking, ad esempio Facebook, LinkedIn o Google, è possibile specificare il parametro `domain_hint`. Questo parametro di query fornisce un hint ad Azure AD B2C sul provider di identità di social networking che deve essere usato per l'accesso. Se ad esempio l'applicazione specifica `domain_hint=facebook.com`, l'accesso rimanda direttamente alla pagina di accesso di Facebook.

![Uso di hint di dominio](./media/direct-signin/domain-hint.png) 

Se si usa un criterio personalizzato, è possibile configurare il nome di dominio usando l'elemento XML `<Domain>domain name</Domain>` di qualsiasi `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


