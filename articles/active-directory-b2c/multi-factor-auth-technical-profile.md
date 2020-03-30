---
title: Profili tecnici di Azure MFA nei criteri personalizzatiAzure MFA technical profiles in custom policies
titleSuffix: Azure AD B2C
description: Informazioni di riferimento sui criteri personalizzati per i profili tecnici di Azure Multi-Factor Authentication (MFA) in Azure AD B2C.
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
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definire un profilo tecnico di Azure MFA in un criterio personalizzato di Azure AD B2CDefine an Azure MFA technical profile in an Azure AD B2C custom policy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce supporto per la verifica di un numero di telefono tramite Azure Multi-Factor Authentication (MFA). Utilizzare questo profilo tecnico per generare e inviare un codice a un numero di telefono, quindi verificare il codice. Il profilo tecnico di Azure MFA può anche restituire un messaggio di errore.  Il profilo tecnico di convalida consente la convalida i dati specificati dall'utente prima che il percorso utente proceda. Con il profilo tecnico di convalida, viene visualizzato un messaggio di errore in una pagina auto-asserita.

Questo profilo tecnico:

- Non fornisce un'interfaccia per interagire con l'utente. Al contrario, l'interfaccia utente viene chiamata da un profilo tecnico [auto-asserito](self-asserted-technical-profile.md) o un [controllo di visualizzazione](display-controls.md) come profilo tecnico di [convalida](validation-technical-profile.md).
- Usa il servizio Azure MFA per generare e inviare un codice a un numero di telefono, quindi verifica il codice.  
- Convalida un numero di telefono tramite messaggi di testo.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **del gestore** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

L'esempio seguente mostra un profilo tecnico di Azure MFA:The following example shows an Azure MFA technical profile:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Invia SMS

La prima modalità di questo profilo tecnico consiste nel generare un codice e inviarlo. Per questa modalità è possibile configurare le seguenti opzioni.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare ad Azure MFA. È inoltre possibile mappare il nome della richiesta al nome definito nel profilo tecnico MFA.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| userPrincipalName | Sì | Identificatore dell'utente proprietario del numero di telefono. |
| phoneNumber | Sì | Numero di telefono a cui inviare un codice SMS. |
| companyName | No |Il nome della società nell'SMS. Se non viene specificato, viene utilizzato il nome dell'applicazione. |
| locale | No | Impostazioni locali dell'SMS. Se non viene specificato, vengono utilizzate le impostazioni locali del browser dell'utente. |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi InputClaimsTransformation usati per modificare le attestazioni di input o generarne di nuove prima dell'invio al servizio MFA di Azure.The InputClaimsTransformations element may contain a collection of **InputClaimsTransformation** elements that are used to modify the input claims or generate new ones before sending to the Azure MFA service.

### <a name="output-claims"></a>Attestazioni di output

Il provider di protocollo Azure MFA non restituisce alcun **OutputClaims**, pertanto non è necessario specificare attestazioni di output. È tuttavia possibile includere attestazioni non restituite dal provider di identità `DefaultValue` di Azure MFA, purché si imposti l'attributo.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | Deve essere **OneWaySMS**.  |

#### <a name="ui-elements"></a>Elementi dell'interfaccia utente

I seguenti metadati possono essere utilizzati per configurare i messaggi di errore visualizzati al momento dell'invio di un errore SMS. I metadati devono essere configurati nel profilo tecnico [auto-asserito.](self-asserted-technical-profile.md) I messaggi di errore possono essere [localizzati.](localization-string-ids.md#azure-mfa-error-messages)

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| UserMessageIfAndtSendSmsUserMessageIfAndtSendSms | No | Messaggio di errore dell'utente se il numero di telefono fornito non accetta SMS. |
| UserMessageIfInvalidFormat | No | Messaggio di errore dell'utente se il numero di telefono fornito non è un numero di telefono valido. |
| UserMessageIfServerError (Messaggio utente) | No | Messaggio di errore dell'utente se il server ha rilevato un errore interno. |
| UserMessageIfThrottled| No | Messaggio di errore dell'utente se una richiesta è stata limitata.|

### <a name="example-send-an-sms"></a>Esempio: invio di un SMS

L'esempio seguente mostra un profilo tecnico di Azure MFA usato per inviare un codice tramite SMS.

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

La seconda modalità di questo profilo tecnico consiste nel verificare un codice. Per questa modalità è possibile configurare le seguenti opzioni.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare ad Azure MFA. È inoltre possibile mappare il nome della richiesta al nome definito nel profilo tecnico MFA.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Sì | Stesso numero di telefono utilizzato in precedenza per inviare un codice. E 'utilizzato anche per individuare una sessione di verifica telefonica. |
| verificationCode (codice di verifica)  | Sì | Il codice di verifica fornito dall'utente da verificare |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi InputClaimsTransformation usati per modificare le attestazioni di input o generarne di nuove prima di chiamare il servizio MFA di Azure.The InputClaimsTransformations element may contain a collection of **InputClaimsTransformation** elements that are used to modify the input claims or generate new ones before calling the Azure MFA service.

### <a name="output-claims"></a>Attestazioni di output

Il provider di protocollo Azure MFA non restituisce alcun **OutputClaims**, pertanto non è necessario specificare attestazioni di output. È tuttavia possibile includere attestazioni non restituite dal provider di identità `DefaultValue` di Azure MFA, purché si imposti l'attributo.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | Deve essere **Verifica** |

#### <a name="ui-elements"></a>Elementi dell'interfaccia utente

I metadati seguenti possono essere utilizzati per configurare i messaggi di errore visualizzati in caso di errore di verifica del codice. I metadati devono essere configurati nel profilo tecnico [auto-asserito.](self-asserted-technical-profile.md) I messaggi di errore possono essere [localizzati.](localization-string-ids.md#azure-mfa-error-messages)

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| No | Messaggio di errore dell'utente se l'utente ha tentato un codice di verifica troppe volte. |
| UserMessageIfServerError (Messaggio utente) | No | Messaggio di errore dell'utente se il server ha rilevato un errore interno. |
| UserMessageIfThrottled| No | Messaggio di errore dell'utente se la richiesta è limitata.|
| UserMessageIfWrongCodeEntered| No| Messaggio di errore dell'utente se il codice immesso per la verifica è errato.|

### <a name="example-verify-a-code"></a>Esempio: verifica di un codice

Nell'esempio seguente viene illustrato un profilo tecnico di Azure MFA usato per verificare il codice.

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
