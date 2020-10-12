---
title: Configurare l'accesso diretto tramite Active Directory B2C | Microsoft Docs
description: Informazioni su come precompilare il nome di accesso o eseguire l'accesso diretto a un provider di identità di social networking.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9e7c537e85039675f27fa3e276b6b964ce1679b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388596"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Configurare l'accesso diretto tramite Active Directory B2C

Quando si configura l'accesso per l'applicazione tramite Azure Active Directory (AD) B2C, è possibile precompilare il nome di accesso o eseguire l'accesso diretto a un provider di identità di social networking specifico, ad esempio Facebook, LinkedIn o un account Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Precompilare il nome di accesso

Durante una procedura di accesso utente, un'applicazione relying party può avere come destinazione un nome utente o un nome di dominio specifico. Quando ha un utente come destinazione, un'applicazione può specificare, nella richiesta di autorizzazione, il parametro di query `login_hint` con il nome di accesso utente. Azure AD B2C inserisce automaticamente il nome di accesso, mentre l'utente deve solo immettere la password.

![Pagina di accesso all'iscrizione con login_hint parametro di query evidenziato nell'URL](./media/direct-signin/login-hint.png)

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

![Pagina di accesso all'iscrizione con domain_hint parametro di query evidenziato nell'URL](./media/direct-signin/domain-hint.png)

Se si usa un criterio personalizzato, è possibile configurare il nome di dominio usando l'elemento XML `<Domain>domain name</Domain>` di qualsiasi `<ClaimsProvider>`.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


