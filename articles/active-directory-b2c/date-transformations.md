---
title: Esempi di trasformazione delle attestazioni di data per lo schema del framework di gestione delle identità di Azure Active Directory B2C | Microsoft Docs
description: Esempi di trasformazione delle attestazioni di data per lo schema del framework di gestione delle identità di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 35c5731d260fb63dece3d2b8ae4f4a4522fb91b8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153405"
---
# <a name="date-claims-transformations"></a>Trasformazioni delle attestazioni di data

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo offre esempi per l'uso delle trasformazioni delle attestazioni di data dello schema del framework di gestione delle identità di Azure Active Directory (Azure AD) B2C. Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Verifica che un'attestazione di data e ora (tipo di dati stringa) sia successiva a una seconda attestazione di data e ora (tipo di dati stringa) e genera un'eccezione.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | leftOperand | stringa | Tipo della prima attestazione, che deve essere successiva alla seconda attestazione. |
| inputClaim | rightOperand | stringa | Tipo della seconda attestazione, che deve essere precedente alla prima attestazione. |
| InputParameter | AssertIfEqualTo | boolean | Specifica se questa asserzione debba passare se l'operando sinistro è uguale all'operando destro. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Specifica se questa asserzione debba passare se l'operando destro è assente. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Specifica il numero di millisecondi che devono trascorrere tra le due date e ore affinché le ore vengano considerate uguali (ad esempio, per tenere conto di un eventuale sfasamento di orario). |

La trasformazione dell'asserzione **AssertDateTimeIsGreaterThan** viene sempre eseguita da un [profilo tecnico di convalida](validation-technical-profile.md) che viene chiamato da un [ profilo tecnico autocertificato](self-asserted-technical-profile.md). I metadati del profilo tecnico autocertificato **DateTimeGreaterThan** controllano il messaggio di errore che il profilo tecnico presenta all'utente.

![Esecuzione di AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

Nell'esempio seguente viene confrontata l'attestazione `currentDateTime` con l'attestazione `approvedDateTime`. Se `currentDateTime` è successivo ad `approvedDateTime`, viene generato un errore. La trasformazione considera uguali i valori se rientrano in una differenza di 5 minuti (30000 millisecondi).

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Il profilo tecnico di convalida `login-NonInteractive` chiama la trasformazione delle attestazioni `AssertApprovedDateTimeLaterThanCurrentDateTime`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Il profilo tecnico autocertificato chiama il profilo tecnico **login-NonInteractive** di convalida.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Risultato: errore generato


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Converte un ClaimType **Date** in un ClaimType **DateTime**. La trasformazione delle attestazioni converte il formato dell'ora e aggiunge 12:00:00 AM alla data.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | ClaimType da convertire. |
| OutputClaim | outputClaim | dateTime | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. |

L'esempio seguente illustra la conversione dell'attestazione `dateOfBirth` (tipo di dati date) in un'altra attestazione `dateOfBirthWithTime` (tipo di dati dateTime).

```XML
<ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **inputClaim**: 01/06/2019
- Attestazioni di output:
    - **outputClaim**: 1559347200 (1° giugno 2019, mezzanotte)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Ottiene la data e ora UTC correnti e aggiunge il valore a un ClaimType.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

* Attestazioni di output:
    * **currentDateTime**: 1534418820 (16 agosto 2018, 11:27)

## <a name="datetimecomparison"></a>DateTimeComparison

Determinare se un valore dateTime è successivo, precedente o uguale a un altro. Il risultato è un valore booleano ClaimType nuovo con un valore `true` o `false`.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | Il primo valore dateTime da confrontare per verificare se è precedente o successivo al secondo valore dateTime. I valori Null generano un'eccezione. |
| InputClaim | secondDateTime | dateTime | Il secondo valore dateTime da confrontare per verificare se è precedente o successivo al primo valore dateTime. Il valore Null viene considerato come il valore dateTime corrente. |
| InputParameter | operator | stringa | Uno dei seguenti valori: uguale, successiva a o precedente a. |
| InputParameter | timeSpanInSeconds | int | Aggiungere l'intervallo di tempo al primo valore datetime. |
| OutputClaim | risultato | boolean | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. |

Usare questa trasformazione di attestazione per determinare se due valori ClaimType sono uguali oppure se uno è successivo o precedente all'altro. Ad esempio, è possibile archiviare l'ultima volta che un utente ha accettato le condizioni d'uso. Dopo 3 mesi, è possibile chiedere all'utente di accedere nuovamente alle condizioni d'uso.
Per eseguire la trasformazione delle attestazioni, è prima necessario ottenere il valore dateTime corrente e l'ultimo orario in cui l'utente ha accettato le condizioni d'uso.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>      
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Parametri di input:
    - **operatore**: later than
    - **timeSpanInSeconds**: 7776000 (90 giorni)
- Attestazioni di output: 
    - **risultato**: true

