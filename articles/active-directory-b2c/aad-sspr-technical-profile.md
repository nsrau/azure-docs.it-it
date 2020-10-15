---
title: Azure AD profili tecnici SSPR nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Riferimento ai criteri personalizzati per Azure AD profili tecnici SSPR in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85383598"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definire un profilo tecnico Azure AD SSPR in un criterio personalizzato Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) fornisce il supporto per la verifica di un indirizzo di posta elettronica per la reimpostazione della password self-service (SSPR). Usare il profilo tecnico Azure AD SSPR per generare e inviare un codice a un indirizzo di posta elettronica e quindi verificare il codice. Il profilo tecnico Azure AD SSPR può inoltre restituire un messaggio di errore. Il profilo tecnico di convalida consente la convalida i dati specificati dall'utente prima che il percorso utente proceda. Con il profilo tecnico di convalida, viene visualizzato un messaggio di errore in una pagina autocertificata.

Questo profilo tecnico:

- Non fornisce un'interfaccia per interagire con l'utente. Al contrario, l'interfaccia utente viene chiamata da un profilo tecnico [autocertificato](self-asserted-technical-profile.md) o da un [controllo di visualizzazione](display-controls.md) come [profilo tecnico di convalida](validation-technical-profile.md).
- Usa il servizio Azure AD SSPR per generare e inviare un codice a un indirizzo di posta elettronica e quindi verifica il codice.  
- Convalida un indirizzo di posta elettronica tramite un codice di verifica.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **handler** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure ad B2C:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Nell'esempio seguente viene illustrato un profilo tecnico Azure AD SSPR:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>Invio di un messaggio di posta elettronica

La prima modalità di questo profilo tecnico consiste nel generare un codice e inviarlo. Per questa modalità è possibile configurare le opzioni seguenti.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare a Azure ad sspr. È anche possibile mappare il nome dell'attestazione al nome definito nel profilo tecnico SSPR.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| emailAddress | Sì | Identificatore dell'utente a cui appartiene l'indirizzo di posta elettronica. La `PartnerClaimType` proprietà dell'attestazione di input deve essere impostata su `emailAddress` . |


L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuovi prima dell'invio al servizio Azure ad sspr.

### <a name="output-claims"></a>Attestazioni di output

Il provider del protocollo Azure AD SSPR non restituisce alcun **OutputClaims**, pertanto non è necessario specificare le attestazioni di output. È tuttavia possibile includere le attestazioni che non vengono restituite dal provider del protocollo Azure AD SSPR, purché si imposti l' `DefaultValue` attributo.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | Deve essere **SendCode**.  |

#### <a name="ui-elements"></a>Elementi dell'interfaccia utente

I metadati seguenti possono essere utilizzati per configurare i messaggi di errore visualizzati durante l'invio di errori SMS. I metadati devono essere configurati nel profilo tecnico [autocertificato](self-asserted-technical-profile.md) . I messaggi di errore possono essere [localizzati](localization-string-ids.md#azure-ad-sspr).

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | No | Messaggio di errore dell'utente se si è verificato un errore interno del server. |
| UserMessageIfThrottled| No | Messaggio di errore dell'utente se una richiesta è stata limitata.|


### <a name="example-send-an-email"></a>Esempio: inviare un messaggio di posta elettronica

Nell'esempio seguente viene illustrato un profilo tecnico Azure AD SSPR usato per inviare un codice tramite posta elettronica.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Verifica il codice

La seconda modalità di questo profilo tecnico consiste nel verificare un codice. Per questa modalità è possibile configurare le opzioni seguenti.

### <a name="input-claims"></a>Attestazioni di input

L'elemento **InputClaims** contiene un elenco di attestazioni da inviare a Azure ad sspr. È anche possibile mappare il nome dell'attestazione al nome definito nel profilo tecnico SSPR.

| ClaimReferenceId | Obbligatoria | Descrizione |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Sì | Stesso indirizzo di posta elettronica usato in precedenza per inviare un codice. Viene anche usato per individuare una sessione di verifica tramite posta elettronica. La `PartnerClaimType` proprietà dell'attestazione di input deve essere impostata su `emailAddress` .|
| verificationCode  | Sì | Codice di verifica fornito dall'utente da verificare. La `PartnerClaimType` proprietà dell'attestazione di input deve essere impostata su `verificationCode` . |

L'elemento **InputClaimsTransformations** può contenere una raccolta di elementi **InputClaimsTransformation** usati per modificare le attestazioni di input o generarne di nuovi prima di chiamare il servizio di Azure ad sspr.

### <a name="output-claims"></a>Attestazioni di output

Il provider del protocollo Azure AD SSPR non restituisce alcun **OutputClaims**, pertanto non è necessario specificare le attestazioni di output. È tuttavia possibile includere le attestazioni che non vengono restituite dal provider del protocollo Azure AD SSPR, purché si imposti l' `DefaultValue` attributo.

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Operazione | Sì | Deve essere **VerifyCode** |

#### <a name="ui-elements"></a>Elementi dell'interfaccia utente

I metadati seguenti possono essere utilizzati per configurare i messaggi di errore visualizzati quando si verifica un errore di verifica del codice. I metadati devono essere configurati nel profilo tecnico [autocertificato](self-asserted-technical-profile.md) . I messaggi di errore possono essere [localizzati](localization-string-ids.md#azure-ad-sspr).

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | Messaggio da visualizzare all'utente se la sessione di verifica del codice è scaduta. Il codice è scaduto o il codice non è mai stato generato per un identificatore specificato.|
|UserMessageIfInternalError | Messaggio di errore dell'utente se si è verificato un errore interno del server.|
|UserMessageIfThrottled | Messaggio di errore dell'utente se una richiesta è stata limitata.|
|UserMessageIfVerificationFailedNoRetry | Messaggio da visualizzare all'utente se è stato fornito un codice non valido e l'utente non è autorizzato a fornire il codice corretto.|
|UserMessageIfVerificationFailedRetryAllowed | Messaggio da visualizzare all'utente se è stato fornito un codice non valido e l'utente è autorizzato a fornire il codice corretto.|

### <a name="example-verify-a-code"></a>Esempio: verifica di un codice

Nell'esempio seguente viene illustrato un profilo tecnico Azure AD SSPR usato per verificare il codice.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```