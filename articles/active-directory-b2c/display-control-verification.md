---
title: Verificare le attestazioni con i controlli di visualizzazione
titleSuffix: Azure AD B2C
description: Informazioni su come usare i controlli di visualizzazione Azure AD B2C per verificare le attestazioni nei percorsi utente forniti dai criteri personalizzati.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4d7deb02e28734eb2b4c42bad566fdfd0d380682
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75479085"
---
# <a name="verification-display-control"></a>Controllo visualizzazione Verifica

Utilizzare un [controllo di visualizzazione](display-controls.md) verifica per verificare un'attestazione, ad esempio un indirizzo di posta elettronica o un numero di telefono, con un codice di verifica inviato all'utente.

## <a name="verificationcontrol-actions"></a>Azioni VerificationControl

Il controllo di visualizzazione verifica è costituito da due passaggi (azioni):

1. Richiedere una destinazione all'utente, ad esempio un indirizzo di posta elettronica o un numero di telefono, a cui deve essere inviato il codice di verifica. Quando l'utente seleziona il pulsante **Invia codice** , viene eseguita l' **azione SendCode** del controllo di visualizzazione della verifica. L' **azione SendCode** genera un codice, costruisce il contenuto da inviare e lo invia all'utente. Il valore dell'indirizzo può essere pre-popolato e fungere da autenticazione a due fattori.

    ![Pagina di esempio per l'azione Invia codice](media/display-control-verification/display-control-verification-email-action-01.png)

1. Una volta inviato il codice, l'utente legge il messaggio, immette il codice di verifica nel controllo fornito dal controllo di visualizzazione e seleziona **Verifica codice**. Selezionando **Verifica codice**, viene eseguita l' **azione VerifyCode** per verificare il codice associato all'indirizzo. Se l'utente seleziona **Invia nuovo codice**, la prima azione viene eseguita di nuovo.

    ![Pagina di esempio per l'azione verifica codice](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>Elementi obbligatori VerificationControl

**VerificationControl** deve contenere gli elementi seguenti:

- Il tipo di `DisplayControl` è `VerificationControl`.
- `DisplayClaims`
  - **Invia a** -una o più attestazioni che specificano la posizione a cui inviare il codice di verifica. Ad esempio, *indirizzo di posta elettronica* o *codice paese* e *numero di telefono*.
  - **Codice di verifica** : il codice di verifica attestazione fornito dall'utente dopo l'invio del codice. Questa attestazione deve essere impostata come obbligatoria e il `ControlClaimType` deve essere impostato su `VerificationCode`.
- Attestazione di output (facoltativo) da restituire alla pagina autocertificata dopo che l'utente ha completato il processo di verifica. Ad esempio, *indirizzo di posta elettronica* o *codice paese* e *numero di telefono*. Il profilo tecnico autocertificato utilizza le attestazioni per salvare in modo permanente i dati o eseguire il bubbling delle attestazioni di output nel passaggio di orchestrazione successivo.
- Due `Action`s con i nomi seguenti:
  - **SendCode** : Invia un codice all'utente. Questa azione contiene in genere due profili tecnici di convalida, per generare un codice e per inviarlo.
  - **VerifyCode** : verifica il codice. Questa azione contiene in genere un singolo profilo tecnico di convalida.

Nell'esempio seguente viene visualizzata una casella di testo di **posta elettronica** nella pagina. Quando l'utente immette il proprio indirizzo di posta elettronica e seleziona **SendCode**, viene attivata l'azione **SendCode** nel back-end Azure ad B2C.

Quindi, l'utente immette il **verificationCode** e seleziona **VerifyCode** per attivare l'azione **VerifyCode** nel back-end. Se tutte le convalide passano, il **VerificationControl** viene considerato completo e l'utente può continuare con il passaggio successivo.

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
