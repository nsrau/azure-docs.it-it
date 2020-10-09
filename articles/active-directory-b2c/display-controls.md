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
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 131ecd010cba55f08199f713654792c0844a47e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202297"
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
| InputClaims | 0:1 | **InputClaims** vengono utilizzati per prepopolare il valore delle attestazioni da raccogliere dall'utente. |
| DisplayClaims | 0:1 | **DisplayClaims** vengono utilizzati per rappresentare le attestazioni che devono essere raccolte dall'utente. |
| OutputClaims | 0:1 | **OutputClaims** vengono utilizzati per rappresentare le attestazioni da salvare temporaneamente per questo **DisplayControl**. |
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

Nell'esempio seguente viene inviato un codice in un messaggio di posta elettronica o in un SMS in base alla selezione dell'attestazione **mfaType** da parte dell'utente.

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
