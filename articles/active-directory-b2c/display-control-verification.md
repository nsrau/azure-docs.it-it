---
title: Verificare le attestazioni con i controlli di visualizzazioneVerify claims with display controls
titleSuffix: Azure AD B2C
description: Informazioni su come usare i controlli di visualizzazione di Azure AD B2C per verificare le attestazioni nei percorsi utente forniti dai criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188784"
---
# <a name="verification-display-control"></a>Controllo del display di verifica

Usa un [controllo di visualizzazione](display-controls.md) della verifica per verificare un reclamo, ad esempio un indirizzo email o un numero di telefono, con un codice di verifica inviato all'utente.

## <a name="verificationcontrol-actions"></a>Azioni VerificationControl

Il controllo del display di verifica è costituito da due passaggi (azioni):

1. Richiedere una destinazione all'utente, ad esempio un indirizzo di posta elettronica o un numero di telefono, a cui deve essere inviato il codice di verifica. Quando l'utente seleziona il pulsante **Invia codice,** viene eseguita **l'azione SendCode** del controllo di visualizzazione della verifica. **L'azione Inviacodice** genera un codice, costruisce il contenuto da inviare e lo invia all'utente. Il valore dell'indirizzo può essere prepopolato e fungere da autenticazione a due fattori.

    ![Pagina di esempio per l'azione di invio del codiceExample page for send code action](media/display-control-verification/display-control-verification-email-action-01.png)

1. Dopo l'invio del codice, l'utente legge il messaggio, immette il codice di verifica nel controllo fornito dal controllo di visualizzazione e seleziona **Verifica codice**. Selezionando **Verifica codice**, viene eseguita **l'azione VerifyCode** per verificare il codice associato all'indirizzo. Se l'utente seleziona **Invia nuovo codice**, viene eseguita nuovamente la prima azione.

    ![Pagina di esempio per verificare l'azione del codiceExample page for verify code action](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>Elementi richiesti VerificationControl

**VerificationControl** deve contenere gli elementi seguenti:

- Il tipo `DisplayControl` di `VerificationControl`is.
- `DisplayClaims`
  - **Invia a:** una o più attestazioni che specificano dove inviare il codice di verifica. Ad esempio, *e-mail* o *il codice paese* e il numero di *telefono*.
  - **Codice di verifica** - Il codice di verifica indica che l'utente fornisce dopo l'invio del codice. Questa attestazione deve essere impostata come obbligatoria e deve `ControlClaimType` essere impostata su `VerificationCode`.
- Attestazione di output (facoltativa) da restituire alla pagina auto-asserita dopo che l'utente ha completato il processo di verifica. Ad esempio, *e-mail* o *il codice paese* e il numero di *telefono*. Il profilo tecnico auto-asserito utilizza le attestazioni per rendere persistenti i dati o eseguire il bubboloing delle attestazioni di output al passaggio di orchestrazione successivo.
- Due `Action`s con i seguenti nomi:
  - **SendCode** - Invia un codice all'utente. Questa azione contiene in genere due profili tecnici di convalida, per generare un codice e inviarlo.
  - **VerifyCode** - Verifica il codice. Questa azione contiene in genere un singolo profilo tecnico di convalida.

Nell'esempio seguente, nella pagina viene visualizzata una casella di testo di **posta elettronica.** Quando l'utente immette il proprio indirizzo di posta elettronica e seleziona **SendCode**, l'azione **SendCode** viene attivata nel back-end B2C di Azure AD.

Quindi, l'utente immette il **verificationCode** e seleziona **VerifyCode** per attivare l'azione **VerifyCode** nel back-end. Se tutte le convalide vengono superate, **VerificationControl** viene considerato completo e l'utente può continuare con il passaggio successivo.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
