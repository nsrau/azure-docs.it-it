---
title: Riferimento del controllo di visualizzazione
titleSuffix: Azure AD B2C
description: Riferimento per i controlli di visualizzazione Di Azure AD B2C. Utilizzare i controlli di visualizzazione per personalizzare i percorsi utente definiti nei criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188733"
---
# <a name="display-controls"></a>Controlli di visualizzazione

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un **controllo di visualizzazione** è un elemento dell'interfaccia utente con funzionalità speciali e interagisce con il servizio back-end Azure Active Directory B2C (Azure AD B2C). Consente all'utente di eseguire azioni nella pagina che richiamano un [profilo tecnico](validation-technical-profile.md) di convalida nel back-end. I controlli di visualizzazione vengono visualizzati nella pagina e fanno riferimento a un [profilo tecnico auto-asserito.](self-asserted-technical-profile.md)

L'immagine seguente illustra una pagina di iscrizione con asserizione automatica con due controlli di visualizzazione che convalidano un indirizzo di posta elettronica primario e secondario.

![Esempio di controllo di visualizzazione sottoposto a rendering](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

 Nella sezione [Metadati](self-asserted-technical-profile.md#metadata) di un [profilo tecnico auto-asserito](self-asserted-technical-profile.md) `DataUri` , il [ContentDefinition](contentdefinitions.md) a cui si fa riferimento deve essere impostato sulla versione 2.0.0 del contratto di pagina. Ad esempio:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definizione dei controlli di visualizzazione

L'elemento **DisplayControl** contiene i seguenti attributi:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ID | Sì | Identificatore utilizzato per il controllo di visualizzazione. È possibile [farvi riferimento](#referencing-display-controls). |
| UserInterfaceControlType (Tipo utenteInterfaceControlType) | Sì | Tipo del controllo di visualizzazione. Attualmente supportato è [VerificationControl](display-control-verification.md) |

L'elemento **DisplayControl** contiene i seguenti elementi:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** vengono usati per prepopolare il valore delle attestazioni da raccogliere dall'utente. |
| DisplayClaims | 0:1 | **DisplayClaims** vengono utilizzati per rappresentare le attestazioni da raccogliere dall'utente. |
| OutputClaims | 0:1 | **OutputClaims** vengono utilizzati per rappresentare le attestazioni da salvare temporaneamente per questo **DisplayControl**. |
| Azioni | 0:1 | **Le azioni** vengono utilizzate per elencare i profili tecnici di convalida da richiamare per le azioni dell'utente che si verificano nel front-end. |

### <a name="input-claims"></a>Attestazioni di input

In un controllo di visualizzazione, è possibile usare **InputClaims** elementi per prepopolare il valore delle attestazioni da raccogliere dall'utente nella pagina. Qualsiasi **InputClaimsTransformations** può essere definito nel profilo tecnico auto-asserito che fa riferimento a questo controllo di visualizzazione.

Nell'esempio seguente viene prepopolato l'indirizzo di posta elettronica da verificare con l'indirizzo già presente.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Visualizza attestazioni

Ogni tipo di controllo di visualizzazione richiede un diverso set di attestazioni di visualizzazione, [attestazioni](#output-claims)di output e [azioni](#display-control-actions) da eseguire.

Analogamente alle **attestazioni** di visualizzazione definite in un [profilo tecnico auto-asserito,](self-asserted-technical-profile.md#display-claims)le attestazioni di visualizzazione rappresentano le attestazioni da raccogliere dall'utente all'interno del controllo di visualizzazione. L'elemento **ClaimType** a cui si fa riferimento deve specificare l'elemento **UserInputType** per un tipo di input utente supportato da Azure AD B2C, ad `TextBox` esempio o `DropdownSingleSelect`. Se un valore dell'attestazione di **Required** visualizzazione è `true` richiesto da **un'azione**, impostare l'attributo Required su per imporre all'utente di fornire un valore per l'attestazione di visualizzazione specifica.

Alcune attestazioni di visualizzazione sono necessarie per determinati tipi di controllo di visualizzazione. Ad esempio, **VerificationCode** è necessario per il controllo di visualizzazione del tipo **VerificationControl**. Utilizzare l'attributo **ControlClaimType** per specificare quale DisplayClaim è designato per l'attestazione richiesta. Ad esempio:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Attestazioni di output

Le **attestazioni** di output di un controllo di visualizzazione non vengono inviate al passaggio successivo dell'orchestrazione. Vengono salvati temporaneamente solo per la sessione di controllo di visualizzazione corrente. Queste attestazioni temporanee possono essere condivise tra le diverse azioni dello stesso controllo di visualizzazione.

Per eseguire il bubbling delle attestazioni di output al passaggio di orchestrazione successivo, usare **OutputClaims** del profilo tecnico auto-asserito effettivo che fa riferimento a questo controllo di visualizzazione.

### <a name="display-control-actions"></a>Azioni di controllo del display

Le **azioni** di un controllo di visualizzazione sono procedure che si verificano nel back-end B2C di Azure AD quando un utente esegue una determinata azione sul lato client (il browser). Ad esempio, le convalide da eseguire quando l'utente seleziona un pulsante nella pagina.

Un'azione definisce un elenco di profili tecnici di **convalida.** Vengono utilizzati per la convalida di alcune o tutte le attestazioni di visualizzazione del controllo di visualizzazione. Il profilo tecnico di convalida convalida l'input dell'utente e può restituire un errore all'utente. È possibile utilizzare **ContinueOnError**, **ContinueOnSuccess**e **Precondizioni** nell'azione del controllo di visualizzazione in modo simile al modo in cui vengono utilizzate nella [convalida dei profili tecnici](validation-technical-profile.md) in un profilo tecnico autoasse.

Nell'esempio seguente viene inviato un codice tramite posta elettronica o SMS in base alla selezione dell'attestazione **mfaType** da parte dell'utente.

```XML
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

Ai controlli di visualizzazione viene fatto riferimento nelle [rivendicazioni](self-asserted-technical-profile.md#display-claims) di visualizzazione del [profilo tecnico auto-asserito.](self-asserted-technical-profile.md)

Ad esempio:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
