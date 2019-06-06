---
title: Definire un profilo tecnico di convalida in un criterio personalizzato di Azure Active Directory B2C | Microsoft Docs
description: Definire un profilo tecnico di Azure Active Directory in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f20c3c6d6821b5a8bbdb74101095431f6f7f18f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511918"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico di convalida in un criterio personalizzato di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un profilo tecnico di convalida è un profilo tecnico standard di un qualsiasi protocollo, ad esempio [Azure Active Directory](active-directory-technical-profile.md) o un'[API REST](restful-technical-profile.md). Il profilo tecnico di convalida restituisce attestazioni di output oppure un messaggio di errore HTTP 409 (codice di stato della risposta al conflitto) con i dati seguenti:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Le attestazioni restituite da un profilo tecnico di convalida vengono aggiunte di nuovo all'elenco delle attestazioni. È possibile usare tali attestazioni nei profili tecnici di convalida successivi.

I profili tecnici di convalida vengono eseguiti nella sequenza in cui compaiono nell'elemento **ValidationTechnicalProfiles**. In un profilo tecnico di convalida è possibile configurare se l'esecuzione di qualsiasi profilo tecnico di convalida successivo dovrà continuare nel caso in cui il profilo generi un errore o abbia esito positivo.

Un profilo tecnico di convalida può essere eseguito in modo condizionale in base alle precondizioni definite nell'elemento **ValidationTechnicalProfile**. È ad esempio possibile verificare se una specifica attestazione esiste oppure se un'attestazione corrisponde o meno al valore specificato.

Un profilo tecnico autocertificato può stabilire che un profilo tecnico venga usato per la convalida di alcune o di tutte le attestazioni di output. Tutte le attestazioni di input del profilo tecnico a cui si fa riferimento devono essere visualizzate nelle attestazioni di output del profilo tecnico di convalida di riferimento.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

L'elemento **ValidationTechnicalProfiles** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Profilo tecnico da usare per la convalida di alcune o di tutte le attestazioni di output del profilo tecnico di riferimento. |

L'elemento **ValidationTechnicalProfile** contiene gli attributi seguenti:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Identificatore di un profilo tecnico già definito nei criteri o nei criteri padre. |
|ContinueOnError|No| Che indica se la convalida di tutti i profili tecnici convalida successiva deve continuare se questo profilo tecnico convalida genera un errore. Valori possibili: `true` o `false` (valore predefinito, l'elaborazione di ulteriori profili di convalida verrà arrestata e verrà restituito un errore). |
|ContinueOnSuccess | No | Indica se la convalida di tutti i profili tecnici successivi deve continuare nel caso in cui il profilo tecnico di convalida corrente abbia esito positivo. I valori possibili sono: `true` o `false`. Il valore predefinito è `true`, che significa che continuerà l'elaborazione di ulteriori profili di convalida. |

L'elemento **ValidationTechnicalProfile** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Preconditions | 0:1 | Elenco di precondizioni che devono essere soddisfatte per consentire l'esecuzione del profilo tecnico di convalida. |

L'elemento **Precondition** contiene gli attributi seguenti:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| `Type` | Yes | Tipo di controllo o query da eseguire per la precondizione. Il valore specificato può essere `ClaimsExist`, a indicare che le azioni devono essere eseguite se le attestazioni specificate esistono nel set di attestazioni corrente dell'utente, oppure `ClaimEquals`, a indicare che le azioni devono essere eseguite se l'attestazione specificata esiste e il relativo valore corrisponde al valore specificato. |
| `ExecuteActionsIf` | Yes | Indica se le azioni incluse nella precondizione devono essere eseguite nel caso in cui il test sia true o false. |

L'elemento **Precondition** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Value | 1:n | Dati usati dal controllo. Se il controllo è di tipo `ClaimsExist`, in questo campo viene specificato un valore di ClaimTypeReferenceId per il quale eseguire query. Se il controllo è di tipo `ClaimEquals`, in questo campo viene specificato un valore di ClaimTypeReferenceId per il quale eseguire query, mentre un altro elemento Value contiene il valore da controllare.|
| Azione | 1:1 | Azione da eseguire se il controllo della precondizione all'interno di un passaggio di orchestrazione è true. Il valore di **Action** è `SkipThisValidationTechnicalProfile`. Specifica che il profilo tecnico convalida associato non deve essere eseguito. |

### <a name="example"></a>Esempio

Questo esempio usa i profili tecnici di convalida seguenti:

1. Il primo profilo tecnico di convalida controlla le credenziali utente e non continua se si verifica un errore, ad esempio viene rilevato un nome utente non valido o una password errata.
2. Il profilo tecnico di convalida successivo non viene eseguito se l'attestazione userType non esiste o se il valore di userType è `Partner`. Il profilo tecnico di convalida prova a leggere il profilo utente dal database clienti interno e continua se si verifica un errore, ad esempio se il servizio API REST non è disponibile o in caso di errore interno.
3. L'ultimo profilo tecnico di convalida non viene eseguito se l'attestazione userType non esiste o se il valore di userType è `Customer`. Il profilo tecnico di convalida prova a leggere il profilo utente dal database dei clienti interno e continua se si verifica un errore, ad esempio se il servizio API REST non è disponibile o in caso di errore interno.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
