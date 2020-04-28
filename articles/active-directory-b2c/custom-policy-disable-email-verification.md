---
title: Disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente con un criterio personalizzato
titleSuffix: Azure AD B2C
description: Informazioni su come disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente in Azure Active Directory B2C.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136143"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Disabilitare la verifica tramite posta elettronica durante l'iscrizione del cliente usando un criterio personalizzato in Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario avere un criterio personalizzato funzionante per l'iscrizione e l'accesso con account social e locali.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Aggiungere i metadati al profilo tecnico autocertificato

Il profilo tecnico **LocalAccountSignUpWithLogonEmail** è un [autocertificato](self-asserted-technical-profile.md), che viene richiamato durante il flusso di iscrizione. Per disabilitare la verifica tramite posta elettronica, `EnforceEmailVerification` impostare i metadati su false. Eseguire l'override dei profili tecnici LocalAccountSignUpWithLogonEmail nel file di estensione. 

1. Aprire il file delle estensioni dei criteri. Ad esempio, <em> `SocialAndLocalAccounts/` </em>.
1. Trovare l'elemento `ClaimsProviders`. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere il provider di attestazioni seguente `ClaimsProviders` all'elemento:

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

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Selezionare **Framework esperienza di identità**.
5. Selezionare **Carica criteri personalizzati**e caricare i file dei due criteri modificati.
2. Selezionare il criterio di iscrizione o di accesso che è stato caricato e fare clic sul pulsante **Esegui adesso**.
3. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica senza la convalida.


## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sul [profilo tecnico autocertificato](self-asserted-technical-profile.md) nella Guida di riferimento a Framework dell'esperienza.
