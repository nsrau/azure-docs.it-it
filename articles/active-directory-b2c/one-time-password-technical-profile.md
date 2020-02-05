---
title: Abilitare la verifica della password monouso (OTP)
titleSuffix: Azure AD B2C
description: Informazioni su come configurare uno scenario OTP (One-time password) usando Azure AD B2C criteri personalizzati.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dab35fbcd221af9f4eb587b8c98a8ff85aeef59f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982790"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definire un profilo tecnico monouso per la password in un Azure AD B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per la gestione della generazione e della verifica di una password monouso. Usare un profilo tecnico per generare un codice e quindi verificare tale codice in un secondo momento.

Il profilo tecnico della password monouso può inoltre restituire un messaggio di errore durante la verifica del codice. Progettare l'integrazione con la password monouso usando un **profilo tecnico di convalida**. Un profilo tecnico di convalida chiama il profilo tecnico della password monouso per verificare un codice. Il profilo tecnico di convalida consente la convalida i dati specificati dall'utente prima che il percorso utente proceda. Con il profilo tecnico di convalida, viene visualizzato un messaggio di errore in una pagina autocertificata.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **handler** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure ad B2C:

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Nell'esempio seguente viene illustrato un profilo tecnico per la password monouso:

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Generare il codice

La prima modalità di questo profilo tecnico consiste nel generare un codice. Di seguito sono elencate le opzioni che possono essere configurate per questa modalità.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni necessarie per l'invio al provider del protocollo password monouso. È anche possibile mappare il nome dell'attestazione al nome definito di seguito.

| ClaimReferenceId | Obbligatorio | Description |
| --------- | -------- | ----------- |
| identificatore | Sì | Identificatore per identificare l'utente che deve verificare il codice in un secondo momento. Viene comunemente usato come identificatore della destinazione in cui viene recapitato il codice, ad esempio indirizzo di posta elettronica o numero di telefono. |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuovi prima dell'invio al provider del protocollo password monouso.

### <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni generate dal provider del protocollo password monouso. È anche possibile mappare il nome dell'attestazione al nome definito di seguito.

| ClaimReferenceId | Obbligatorio | Description |
| --------- | -------- | ----------- |
| otpGenerated | Sì | Il codice generato la cui sessione viene gestita da Azure AD B2C. |

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

Per configurare la generazione e la manutenzione del codice, è possibile usare le impostazioni seguenti:

| Attributo | Obbligatorio | Description |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | No | Tempo in secondi per la scadenza del codice. Valore minimo: `60`; Massimo: `1200`; Impostazione predefinita: `600`. |
| CodeLength | No | Lunghezza del codice. Il valore predefinito è `6`. |
| CharacterSet | No | Set di caratteri per il codice, formattato per essere utilizzato in un'espressione regolare. Ad esempio: `a-z0-9A-Z`. Il valore predefinito è `0-9`. Il set di caratteri deve includere almeno 10 caratteri diversi nel set specificato. |
| NumRetryAttempts | No | Il numero di tentativi di verifica prima che il codice venga considerato non valido. Il valore predefinito è `5`. |
| Operazione | Sì | L'operazione da eseguire. Valori possibili: `GenerateCode`o `VerifyCode`. |
| ReuseSameCode | No | Indica se deve essere fornito un codice duplicato anziché generare un nuovo codice quando il codice specificato non è scaduto ed è ancora valido. Il valore predefinito è `false`. |

### <a name="returning-error-message"></a>Restituzione messaggio di errore

Nessun messaggio di errore restituito per la modalità di generazione del codice.

### <a name="example"></a>Esempio

Il `TechnicalProfile` di esempio seguente viene usato per generare un codice:

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

La seconda modalità di questo profilo tecnico consiste nel verificare un codice. Di seguito sono elencate le opzioni che possono essere configurate per questa modalità.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni necessarie per l'invio al provider del protocollo password monouso. È anche possibile mappare il nome dell'attestazione al nome definito di seguito.

| ClaimReferenceId | Obbligatorio | Description |
| --------- | -------- | ----------- |
| identificatore | Sì | Identificatore per identificare l'utente che ha generato in precedenza un codice. Viene comunemente usato come identificatore della destinazione in cui viene recapitato il codice, ad esempio indirizzo di posta elettronica o numero di telefono. |
| otpToVerify | Sì | Codice di verifica fornito dall'utente. |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuovi prima dell'invio al provider del protocollo password monouso.

### <a name="output-claims"></a>Attestazioni di output

Nessuna attestazione di output fornita durante la verifica del codice del provider del protocollo.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

È possibile utilizzare le impostazioni seguenti per configurare il messaggio di errore visualizzato al momento dell'errore di verifica del codice:

| Attributo | Obbligatorio | Description |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | No | Messaggio da visualizzare all'utente se la sessione di verifica del codice è scaduta. Il codice è scaduto o il codice non è mai stato generato per un identificatore specificato. |
| UserMessageIfMaxRetryAttempted | No | Messaggio da visualizzare all'utente se è stato superato il numero massimo di tentativi di verifica consentiti. |
| UserMessageIfInvalidCode | No | Messaggio da visualizzare all'utente se è stato fornito un codice non valido. |

### <a name="returning-error-message"></a>Restituzione messaggio di errore

Come descritto in [metadati](#metadata), è possibile personalizzare il messaggio di errore visualizzato all'utente in caso di errori diversi. È possibile localizzare ulteriormente i messaggi anteponendo le impostazioni locali, ad esempio:

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Esempio

Il `TechnicalProfile` di esempio seguente viene usato per la verifica di un codice:

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```
