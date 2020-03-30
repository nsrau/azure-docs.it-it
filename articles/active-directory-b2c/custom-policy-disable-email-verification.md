---
title: Disabilitare la verifica della posta elettronica durante l'iscrizione del cliente con un criterio personalizzato
titleSuffix: Azure AD B2C
description: Informazioni su come disabilitare la verifica della posta elettronica durante l'iscrizione dei clienti in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136143"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Disabilitare la verifica della posta elettronica durante l'iscrizione del cliente usando un criterio personalizzato in Azure Active Directory B2CDisable email verification during customer sign-up using a custom policy in Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario disporre di criteri personalizzati di lavoro per l'iscrizione e l'accesso con account social e locali.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Aggiungere i metadati al profilo tecnico auto-asserito

Il profilo tecnico **LocalAccountSignUpWithLogonEmail** è un profilo tecnico [auto-asserito](self-asserted-technical-profile.md), che viene richiamato durante il flusso di iscrizione. Per disabilitare la verifica `EnforceEmailVerification` della posta elettronica, impostare i metadati su false. Eseguire l'override dei profili tecnici LocalAccountSignUpWithLogonEmail nel file di estensione. 

1. Aprire il file delle estensioni del criterio. Ad esempio, <em> `SocialAndLocalAccounts/` </em>.
1. Trovare l'elemento `ClaimsProviders`. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere il provider di `ClaimsProviders` attestazioni seguente all'elemento:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **Directory e sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant di Azure AD.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Selezionare **Identity Experience Framework**.
5. Selezionare **Carica criteri personalizzati**e caricare i file dei due criteri modificati.
2. Selezionare il criterio di iscrizione o di accesso che è stato caricato e fare clic sul pulsante **Esegui adesso**.
3. Dovresti essere in grado di registrarti utilizzando un indirizzo email senza la convalida.


## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul [profilo tecnico auto-asserito](self-asserted-technical-profile.md) sono nel riferimento IEF.
