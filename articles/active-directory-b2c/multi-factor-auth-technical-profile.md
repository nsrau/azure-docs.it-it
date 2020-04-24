---
title: Profili tecnici dell'autenticazione a più fattori di Azure nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni di riferimento sui criteri personalizzati per i profili tecnici di Azure Multi-Factor Authentication (AMF) in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332812"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definire un profilo tecnico dell'autenticazione a più fattori di Azure in un Azure AD B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per la verifica di un numero di telefono tramite Azure Multi-Factor Authentication (multi-factor authentication). Usare questo profilo tecnico per generare e inviare un codice a un numero di telefono e quindi verificare il codice. Il profilo tecnico dell'autenticazione a più fattori di Azure può anche restituire un messaggio di errore.  Il profilo tecnico di convalida consente la convalida i dati specificati dall'utente prima che il percorso utente proceda. Con il profilo tecnico di convalida, viene visualizzato un messaggio di errore in una pagina autocertificata.

Questo profilo tecnico:

- Non fornisce un'interfaccia per interagire con l'utente. Al contrario, l'interfaccia utente viene chiamata da un profilo tecnico [autocertificato](self-asserted-technical-profile.md) o da un [controllo di visualizzazione](display-controls.md) come [profilo tecnico di convalida](validation-technical-profile.md).
- Usa il Servizio autenticazione a più fattori di Azure per generare e inviare un codice a un numero di telefono e quindi verifica il codice.  
- Convalida un numero di telefono tramite SMS.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **handler** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure ad B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

L'esempio seguente illustra un profilo tecnico dell'autenticazione a più fattori di Azure:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Invia SMS

La prima modalità di questo profilo tecnico consiste nel generare un codice e inviarlo. Per questa modalità è possibile configurare le opzioni seguenti.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare all'autenticazione a più fattori di Azure. È anche possibile mappare il nome dell'attestazione al nome definito nel profilo tecnico dell'autenticazione a più fattori.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| userPrincipalName | Sì | Identificatore dell'utente a cui appartiene il numero di telefono. |
| phoneNumber | Sì | Numero di telefono a cui inviare un codice SMS. |
| companyName | No |Nome della società in SMS. Se non viene specificato, viene usato il nome dell'applicazione. |
| locale | No | Impostazioni locali dell'SMS. Se non viene specificato, vengono usate le impostazioni locali del browser dell'utente. |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuovi prima dell'invio al servizio Azure multi-factor authentication.

### <a name="output-claims"></a>Attestazioni di output

Il provider del protocollo di autenticazione a più fattori di Azure non restituisce alcun **OutputClaims**, pertanto non è necessario specificare le attestazioni di output. È tuttavia possibile includere le attestazioni che non vengono restituite dal provider di identità di autenticazione a più fattori di `DefaultValue` Azure, purché si imposti l'attributo.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | Deve essere **OneWaySMS**.  |

#### <a name="ui-elements"></a>Elementi dell'interfaccia utente

I metadati seguenti possono essere utilizzati per configurare i messaggi di errore visualizzati durante l'invio di errori SMS. I metadati devono essere configurati nel profilo tecnico [autocertificato](self-asserted-technical-profile.md) . È possibile [localizzare](localization-string-ids.md#azure-mfa-error-messages)i messaggi di errore.

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | No | Messaggio di errore dell'utente se il numero di telefono specificato non accetta SMS. |
| UserMessageIfInvalidFormat | No | Messaggio di errore dell'utente se il numero di telefono specificato non è un numero di telefono valido. |
| UserMessageIfServerError | No | Messaggio di errore dell'utente se si è verificato un errore interno del server. |
| UserMessageIfThrottled| No | Messaggio di errore dell'utente se una richiesta è stata limitata.|

### <a name="example-send-an-sms"></a>Esempio: inviare un SMS

L'esempio seguente illustra un profilo tecnico dell'autenticazione a più fattori di Azure usato per inviare un codice tramite SMS.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verificare il codice

La seconda modalità di questo profilo tecnico consiste nel verificare un codice. Per questa modalità è possibile configurare le opzioni seguenti.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare all'autenticazione a più fattori di Azure. È anche possibile mappare il nome dell'attestazione al nome definito nel profilo tecnico dell'autenticazione a più fattori.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Sì | Lo stesso numero di telefono usato in precedenza per inviare un codice. Viene anche usato per individuare una sessione di verifica telefonica. |
| verificationCode  | Sì | Codice di verifica fornito dall'utente da verificare |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuovi prima di chiamare il servizio Azure multi-factor authentication.

### <a name="output-claims"></a>Attestazioni di output

Il provider del protocollo di autenticazione a più fattori di Azure non restituisce alcun **OutputClaims**, pertanto non è necessario specificare le attestazioni di output. È tuttavia possibile includere le attestazioni che non vengono restituite dal provider di identità di autenticazione a più fattori di `DefaultValue` Azure, purché si imposti l'attributo.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | Deve essere **Verify** |

#### <a name="ui-elements"></a>Elementi dell'interfaccia utente

I metadati seguenti possono essere utilizzati per configurare i messaggi di errore visualizzati quando si verifica un errore di verifica del codice. I metadati devono essere configurati nel profilo tecnico [autocertificato](self-asserted-technical-profile.md) . È possibile [localizzare](localization-string-ids.md#azure-mfa-error-messages)i messaggi di errore.

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| No | Messaggio di errore dell'utente se l'utente ha tentato un numero eccessivo di volte un codice di verifica. |
| UserMessageIfServerError | No | Messaggio di errore dell'utente se si è verificato un errore interno del server. |
| UserMessageIfThrottled| No | Messaggio di errore dell'utente se la richiesta è limitata.|
| UserMessageIfWrongCodeEntered| No| Messaggio di errore dell'utente se il codice immesso per la verifica è errato.|

### <a name="example-verify-a-code"></a>Esempio: verifica di un codice

L'esempio seguente illustra un profilo tecnico dell'autenticazione a più fattori di Azure usato per verificare il codice.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
