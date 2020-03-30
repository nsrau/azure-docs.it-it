---
title: Abilitare la verifica della password mono(one-time)
titleSuffix: Azure AD B2C
description: Informazioni su come configurare uno scenario OTP (One-Time Password) usando i criteri personalizzati di Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332774"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definire un profilo tecnico con password monovista in un criterio personalizzato B2C di Azure ADDefine a one-time password technical profile in an Azure AD B2C custom policy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce supporto per la gestione della generazione e della verifica di una password monocucosa. Utilizzare un profilo tecnico per generare un codice e quindi verificare che il codice in un secondo momento.

Il profilo tecnico con password monouso può anche restituire un messaggio di errore durante la verifica del codice. Progettare l'integrazione con la password monouso utilizzando un **profilo tecnico di convalida**. Un profilo tecnico di convalida chiama il profilo tecnico password monouso per verificare un codice. Il profilo tecnico di convalida consente la convalida i dati specificati dall'utente prima che il percorso utente proceda. Con il profilo tecnico di convalida, viene visualizzato un messaggio di errore in una pagina auto-asserita.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **del gestore** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

L'esempio seguente mostra un profilo tecnico con password monovista:The following example shows a one-time password technical profile:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generare il codice

La prima modalità di questo profilo tecnico consiste nel generare un codice. Di seguito sono riportate le opzioni che possono essere configurate per questa modalità.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni necessarie per l'invio al provider di protocollo password monogruppo. È inoltre possibile mappare il nome della richiesta al nome definito di seguito.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| identificatore | Sì | Identificatore per identificare l'utente che deve verificare il codice in un secondo momento. Viene comunemente utilizzato come identificatore della destinazione a cui viene recapitato il codice, ad esempio l'indirizzo di posta elettronica o il numero di telefono. |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuove prima dell'invio al provider di protocollo password monouso.

### <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni generate dal provider di protocollo password monodata. È inoltre possibile mappare il nome della richiesta al nome definito di seguito.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| otpGenerato | Sì | Il codice generato la cui sessione è gestita da Azure AD B2C. |

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

Le impostazioni seguenti possono essere utilizzate per configurare la modalità di generazione del codice:The following settings can be used to configure code generation mode:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| CodeExpirationInSecondi | No | Tempo in secondi fino alla scadenza del codice. Minimo: `60`; Massimo: `1200`; Valore `600`predefinito: . |
| CodeLength | No | Lunghezza del codice. Il valore predefinito è `6`. |
| CharacterSet | No | Set di caratteri per il codice, formattato per l'utilizzo in un'espressione regolare. Ad esempio: `a-z0-9A-Z`. Il valore predefinito è `0-9`. Il set di caratteri deve includere un minimo di 10 caratteri diversi nel set specificato. |
| NumRetryTenta | No | Il numero di tentativi di verifica prima che il codice venga considerato non valido. Il valore predefinito è `5`. |
| Operazione | Sì | L'operazione da eseguire. Valore possibile: `GenerateCode`. |
| ReuseSameCode | No | Se deve essere specificato un codice duplicato anziché generare un nuovo codice quando il codice specificato non è scaduto ed è ancora valido. Il valore predefinito è `false`. |

### <a name="example"></a>Esempio

L'esempio `TechnicalProfile` seguente viene utilizzato per generare un codice:The following example is used for generating a code:

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificare il codice

La seconda modalità di questo profilo tecnico consiste nel verificare un codice. Di seguito sono riportate le opzioni che possono essere configurate per questa modalità.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni necessarie per l'invio al provider di protocollo password monogruppo. È inoltre possibile mappare il nome della richiesta al nome definito di seguito.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| identificatore | Sì | Identificatore per identificare l'utente che in precedenza ha generato un codice. Viene comunemente utilizzato come identificatore della destinazione a cui viene recapitato il codice, ad esempio l'indirizzo di posta elettronica o il numero di telefono. |
| otpToVerify (informazioni in base alle impostazioni in questo stato instato) | Sì | Il codice di verifica fornito dall'utente. |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuove prima dell'invio al provider di protocollo password monouso.

### <a name="output-claims"></a>Attestazioni di output

Non vengono fornite attestazioni di output durante la verifica del codice di questo provider di protocollo.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

Le seguenti impostazioni possono essere utilizzate per codificare la modalità di verifica:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | L'operazione da eseguire. Valore possibile: `VerifyCode`. |


### <a name="ui-elements"></a>Elementi dell'interfaccia utente

I metadati seguenti possono essere utilizzati per configurare i messaggi di errore visualizzati in caso di errore di verifica del codice. I metadati devono essere configurati nel profilo tecnico [auto-asserito.](self-asserted-technical-profile.md) I messaggi di errore possono essere [localizzati.](localization-string-ids.md#one-time-password-error-messages)

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | No | Messaggio da visualizzare all'utente se la sessione di verifica del codice è scaduta. È il codice scaduto o il codice non è mai stato generato per un determinato identificatore. |
| UserMessageIfMaxRetryAttempted | No | Messaggio da visualizzare all'utente se ha superato il numero massimo di tentativi di verifica consentiti. |
| UserMessageIfInvalidCode | No | Messaggio da visualizzare all'utente se ha fornito un codice non valido. |
|UserMessageIfSessionConflict|No| Messaggio da visualizzare all'utente se il codice non può essere verificato.|

### <a name="example"></a>Esempio

L'esempio `TechnicalProfile` seguente viene utilizzato per la verifica di un codice:The following example is used for verifying a code:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Passaggi successivi

Vedere il seguente articolo per esempio di utilizzo del profilo tecnico password monouso con la verifica e-mail personalizzata:

- [Verifica della posta elettronica personalizzata in Azure Active Directory B2CCustom email verification in Azure Active Directory B2C](custom-email.md)

