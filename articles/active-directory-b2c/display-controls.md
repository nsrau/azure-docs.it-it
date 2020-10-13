---
title: Visualizza riferimento al controllo
titleSuffix: Azure AD B2C
description: Riferimento per Azure AD B2C controlli di visualizzazione. Usare i controlli di visualizzazione per personalizzare i percorsi utente definiti nei criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 49626d418f90f8b4bc7288a6d2f7d195cd906f7a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961358"
---
# <a name="display-controls"></a>Controlli per la visualizzazione

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un **controllo display** è un elemento dell'interfaccia utente che dispone di funzionalità speciali e interagisce con il servizio back-end Azure Active Directory B2C (Azure ad B2C). Consente all'utente di eseguire azioni nella pagina che richiama un [profilo tecnico di convalida](validation-technical-profile.md) nel back-end. I controlli di visualizzazione vengono visualizzati nella pagina e a cui viene fatto riferimento da un [profilo tecnico autocertificato](self-asserted-technical-profile.md).

Nell'immagine seguente viene illustrata una pagina di iscrizione autocertificata con due controlli di visualizzazione che convalidano un indirizzo di posta elettronica primario e secondario.

![Esempio di controllo di visualizzazione visualizzato](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

 Nella sezione dei [metadati](self-asserted-technical-profile.md#metadata) di un [profilo tecnico autocertificato](self-asserted-technical-profile.md), il [ContentDefinition](contentdefinitions.md) a cui si fa riferimento deve essere `DataUri` impostato su page Contract versione 2.0.0 o successiva. Ad esempio:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definizione di controlli di visualizzazione

L'elemento **DisplayControl** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ID | Sì | Identificatore usato per il controllo di visualizzazione. È possibile [farvi riferimento](#referencing-display-controls). |
| UserInterfaceControlType | Sì | Tipo del controllo di visualizzazione. Attualmente supportato è [VerificationControl](display-control-verification.md) |

L'elemento **DisplayControl** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** vengono utilizzati per prepopolare il valore delle attestazioni da raccogliere dall'utente. Per altre informazioni, vedere elemento [InputClaims](technicalprofiles.md#inputclaims) . |
| DisplayClaims | 0:1 | **DisplayClaims** vengono utilizzati per rappresentare le attestazioni che devono essere raccolte dall'utente. Per altre informazioni, vedere elemento [DisplayClaim](technicalprofiles.md#displayclaim) .|
| OutputClaims | 0:1 | **OutputClaims** vengono utilizzati per rappresentare le attestazioni da salvare temporaneamente per questo **DisplayControl**. Per altre informazioni, vedere elemento [OutputClaims](technicalprofiles.md#outputclaims) .|
| Azioni | 0:1 | Le **azioni** vengono usate per elencare i profili tecnici di convalida da richiamare per le azioni dell'utente che si verificano nel front-end. |

### <a name="input-claims"></a>Attestazioni di input

In un controllo di visualizzazione è possibile usare gli elementi **InputClaims** per prepopolare il valore delle attestazioni da raccogliere dall'utente nella pagina. Qualsiasi **InputClaimsTransformations** può essere definito nel profilo tecnico autocertificato che fa riferimento a questo controllo di visualizzazione.

Nell'esempio seguente viene prepopolato l'indirizzo di posta elettronica da verificare con l'indirizzo già presente.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Visualizza attestazioni

Ogni tipo di controllo di visualizzazione richiede un set diverso di attestazioni di visualizzazione, [attestazioni di output](#output-claims)e [azioni](#display-control-actions) da eseguire.

Analogamente alle **attestazioni di visualizzazione** definite in un [profilo tecnico autocertificato](self-asserted-technical-profile.md#display-claims), le attestazioni di visualizzazione rappresentano le attestazioni che devono essere raccolte dall'utente all'interno del controllo di visualizzazione. L'elemento **ClaimType** a cui si fa riferimento deve specificare l'elemento **tipo** per un tipo di input utente supportato da Azure ad B2C, ad esempio `TextBox` o `DropdownSingleSelect` . Se per un' **azione**è richiesto un valore di attestazione di visualizzazione, impostare l'attributo **obbligatorio** su `true` per forzare l'utente a fornire un valore per l'attestazione di visualizzazione specifica.

Alcune attestazioni di visualizzazione sono necessarie per determinati tipi di controllo di visualizzazione. Ad esempio, **VerificationCode** è obbligatorio per il controllo di visualizzazione di tipo **VerificationControl**. Usare l'attributo **ControlClaimType** per specificare quale DisplayClaim è designato per l'attestazione richiesta. Ad esempio:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Attestazioni di output

Le **attestazioni di output** di un controllo di visualizzazione non vengono inviate al passaggio di orchestrazione successivo. Vengono salvati temporaneamente solo per la sessione di controllo dello schermo corrente. Queste attestazioni temporanee possono essere condivise tra le diverse azioni dello stesso controllo di visualizzazione.

Per eseguire il propagazione delle attestazioni di output al passaggio successivo dell'orchestrazione, usare il **OutputClaims** del profilo tecnico autocertificato effettivo che fa riferimento a questo controllo di visualizzazione.

### <a name="display-control-actions"></a>Visualizzare le azioni di controllo

Le **azioni** di un controllo di visualizzazione sono procedure che si verificano nel Azure ad B2C back-end quando un utente esegue una determinata azione sul lato client (il browser). Ad esempio, le convalide da eseguire quando l'utente seleziona un pulsante nella pagina.

Un'azione definisce un elenco di **profili tecnici di convalida**. Vengono utilizzati per la convalida di alcune o tutte le attestazioni di visualizzazione del controllo di visualizzazione. Il profilo tecnico di convalida convalida l'input dell'utente e può restituire un errore all'utente. È possibile usare **ContinueOnError**, **ContinueOnSuccess**e **precondizioni** nell'azione di controllo dello schermo Analogamente al modo in cui vengono usati nei [profili tecnici di convalida](validation-technical-profile.md) in un profilo tecnico autocertificato.

#### <a name="actions"></a>Azioni

L'elemento **Actions** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Azione | 1:n | Elenco di azioni da eseguire. |

#### <a name="action"></a>Azione

L'elemento **Action** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ID | Sì | Tipo di operazione. I valori possibili sono: `SendCode` o `VerifyCode`. Il `SendCode` valore Invia un codice all'utente. Questa azione può contenere due profili tecnici di convalida: uno per generare un codice e uno per inviarlo. Il `VerifyCode` valore verifica il codice digitato dall'utente nella casella di testo di input. |

L'elemento **Action** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | Identificatori dei profili tecnici utilizzati per convalidare alcune o tutte le attestazioni di visualizzazione del profilo tecnico di riferimento. Tutte le attestazioni di input del profilo tecnico a cui viene fatto riferimento devono essere visualizzate nelle attestazioni di visualizzazione del profilo tecnico di riferimento. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

L'elemento **ValidationClaimsExchange** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Profilo tecnico da utilizzare per la convalida di alcune o tutte le attestazioni di visualizzazione del profilo tecnico di riferimento. |

L'elemento **tecnico** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ReferenceId | Sì | Identificatore di un profilo tecnico già definito nei criteri o nei criteri padre. |
|ContinueOnError|No| Indica se la convalida di tutti i profili tecnici di convalida successivi deve continuare se il profilo tecnico di convalida genera un errore. Possibili valori: `true` o `false` (impostazione predefinita, l'elaborazione di ulteriori profili di convalida verrà arrestata e verrà restituito un errore). |
|ContinueOnSuccess | No | Indica se la convalida di tutti i profili di convalida successivi deve continuare se il profilo tecnico di convalida ha esito positivo. I valori possibili sono: `true` o `false`. Il valore predefinito è `true`, che significa che continuerà l'elaborazione di ulteriori profili di convalida. |

L'elemento **ValidationTechnicalProfile** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | Elenco di precondizioni che devono essere soddisfatte per consentire l'esecuzione del profilo tecnico di convalida. |

L'elemento **precondition** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| `Type` | Sì | Tipo di controllo o query da eseguire per la precondizione. I valori possibili sono: `ClaimsExist` o `ClaimEquals`. `ClaimsExist` Specifica che le azioni devono essere eseguite se le attestazioni specificate sono presenti nel set di attestazioni corrente dell'utente. `ClaimEquals` Specifica che le azioni devono essere eseguite se l'attestazione specificata esiste e se il relativo valore è uguale al valore specificato. |
| `ExecuteActionsIf` | Sì | Indica se le azioni incluse nella precondizione devono essere eseguite nel caso in cui il test sia true o false. |

L'elemento **Precondition** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| valore | 1:n | Dati usati dal controllo. Se il controllo è di tipo `ClaimsExist`, in questo campo viene specificato un valore di ClaimTypeReferenceId per il quale eseguire query. Se il controllo è di tipo `ClaimEquals`, in questo campo viene specificato un valore di ClaimTypeReferenceId per il quale eseguire query, Consente di specificare il valore da archiviare in un altro elemento valore.|
| Azione | 1:1 | Azione da eseguire se il controllo della precondizione all'interno di un passaggio di orchestrazione è true. Il valore dell' **azione** è impostato su `SkipThisValidationTechnicalProfile` , che specifica che il profilo tecnico di convalida associato non deve essere eseguito. |

Nell'esempio seguente viene inviato e verificato l'indirizzo di posta elettronica utilizzando [Azure ad profilo tecnico SSPR](aad-sspr-technical-profile.md).

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

Nell'esempio seguente viene inviato un codice in un messaggio di posta elettronica o in un SMS in base alla selezione dell'attestazione **mfaType** da parte dell'utente con le precondizioni.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Riferimento ai controlli di visualizzazione

Ai controlli di visualizzazione viene fatto riferimento nelle [attestazioni di visualizzazione](self-asserted-technical-profile.md#display-claims) del [profilo tecnico autocertificato](self-asserted-technical-profile.md).

Ad esempio:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Passaggi successivi

Per esempi sull'uso del controllo di visualizzazione, vedere: 

- [Verifica della posta elettronica personalizzata con Mailjet](custom-email-mailjet.md)
- [Verifica della posta elettronica personalizzata con SendGrid](custom-email-sendgrid.md)
