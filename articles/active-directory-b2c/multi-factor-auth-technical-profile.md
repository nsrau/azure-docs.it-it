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
ms.date: 12/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05851dba9de06b5dfba2da4f455fbaf5e9376d08
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184282"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definire un profilo tecnico dell'autenticazione a più fattori di Azure in un Azure AD B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per la verifica di un numero di telefono tramite Azure Multi-Factor Authentication (multi-factor authentication). Usare questo profilo tecnico per generare e inviare un codice a un numero di telefono e quindi verificare il codice.

Il profilo tecnico dell'autenticazione a più fattori di Azure può anche restituire un messaggio di errore. È possibile progettare l'integrazione con l'autenticazione a più fattori di Azure usando un **profilo tecnico di convalida**. Un profilo tecnico di convalida chiama il servizio multi-factor authentication di Azure. Il profilo tecnico di convalida consente la convalida i dati specificati dall'utente prima che il percorso utente proceda. Con il profilo tecnico di convalida, viene visualizzato un messaggio di errore in una pagina autocertificata.

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

Il provider del protocollo di autenticazione a più fattori di Azure non restituisce alcun **OutputClaims**, pertanto non è necessario specificare le attestazioni di output. È tuttavia possibile includere le attestazioni che non vengono restituite dal provider di identità di autenticazione a più fattori di Azure, purché si imposti l'attributo `DefaultValue`.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | Deve essere **OneWaySMS**.  |
| UserMessageIfInvalidFormat | No | Messaggio di errore personalizzato se il numero di telefono specificato non è un numero di telefono valido |
| UserMessageIfCouldntSendSms | No | Messaggio di errore personalizzato se il numero di telefono specificato non accetta SMS |
| UserMessageIfServerError | No | Messaggio di errore personalizzato se si è verificato un errore interno del server |

### <a name="return-an-error-message"></a>Restituisce un messaggio di errore

Come descritto in [metadati](#metadata), è possibile personalizzare il messaggio di errore visualizzato all'utente in caso di errori diversi. È possibile localizzare ulteriormente tali messaggi anteponendo le impostazioni locali. Ad esempio:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

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

Il provider del protocollo di autenticazione a più fattori di Azure non restituisce alcun **OutputClaims**, pertanto non è necessario specificare le attestazioni di output. È tuttavia possibile includere le attestazioni che non vengono restituite dal provider di identità di autenticazione a più fattori di Azure, purché si imposti l'attributo `DefaultValue`.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | Deve essere **Verify** |
| UserMessageIfInvalidFormat | No | Messaggio di errore personalizzato se il numero di telefono specificato non è un numero di telefono valido |
| UserMessageIfWrongCodeEntered | No | Messaggio di errore personalizzato se il codice immesso per la verifica non è corretto |
| UserMessageIfMaxAllowedCodeRetryReached | No | Messaggio di errore personalizzato se l'utente ha tentato troppe volte un codice di verifica |
| UserMessageIfThrottled | No | Messaggio di errore personalizzato se l'utente è limitato |
| UserMessageIfServerError | No | Messaggio di errore personalizzato se si è verificato un errore interno del server |

### <a name="return-an-error-message"></a>Restituisce un messaggio di errore

Come descritto in [metadati](#metadata), è possibile personalizzare il messaggio di errore visualizzato all'utente in caso di errori diversi. È possibile localizzare ulteriormente tali messaggi anteponendo le impostazioni locali. Ad esempio:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

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
