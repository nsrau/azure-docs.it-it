---
title: Definire un profilo tecnico autocertificato in un criterio personalizzato
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico autocertificato nei criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 817267414555ea0641e8fb8a8392976a4789c780
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096216"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico autocertificato nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tutte le interazioni in Azure Active Directory B2C (Azure AD B2C) in cui l'utente deve fornire l'input sono profili tecnici autocertificati. ad esempio una pagina di registrazione, una pagina di accesso o una pagina di reimpostazione della password.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `Proprietary`. L'attributo **handler** deve contenere il nome completo dell'assembly del gestore di protocollo usato da Azure AD B2C per l'autocertificazione: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

L'esempio seguente illustra un profilo tecnico autocertificato per l'iscrizione alla posta elettronica:

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Attestazioni di input

In un profilo tecnico autocertificato, è possibile usare gli elementi **InputClaims** e **InputClaimsTransformations** per prepopolare il valore delle attestazioni visualizzate nella pagina autocertificata (visualizzare le attestazioni). Nei criteri del profilo di modifica ad esempio il percorso utente legge prima il profilo utente dal servizio directory di Azure AD B2C, quindi il profilo tecnico autocertificato imposta le attestazioni di input con i dati utente archiviati nel profilo utente. Queste attestazioni vengono raccolte dal profilo utente e successivamente presentate all'utente che può quindi modificare i dati esistenti.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Visualizza attestazioni

La funzionalità di visualizzazione delle attestazioni è attualmente in **Anteprima**.

L'elemento **DisplayClaims** contiene un elenco di attestazioni da presentare sullo schermo per la raccolta di dati da parte dell'utente. Per prepopolare i valori delle attestazioni di visualizzazione, usare le attestazioni di input descritte in precedenza. L'elemento può contenere anche un valore predefinito.

L'ordine delle attestazioni in **DisplayClaims** specifica l'ordine in cui Azure ad B2C esegue il rendering delle attestazioni sullo schermo. Per forzare l'utente a fornire un valore per un'attestazione specifica, impostare l'attributo **obbligatorio** dell'elemento **DisplayClaim** su `true` .

L'elemento **ClaimType** nella raccolta **DisplayClaims** deve impostare l'elemento **tipo** su qualsiasi tipo di input utente supportato da Azure ad B2C. Ad esempio, `TextBox` o `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Aggiungere un riferimento a un DisplayControl

Nella raccolta di attestazioni di visualizzazione è possibile includere un riferimento a un [DisplayControl](display-controls.md) creato. Un controllo display è un elemento dell'interfaccia utente che dispone di funzionalità speciali e interagisce con il servizio back-end Azure AD B2C. Consente all'utente di eseguire azioni nella pagina che richiama un profilo tecnico di convalida nel back-end. Ad esempio, verificando un indirizzo di posta elettronica, un numero di telefono o un numero di fedeltà del cliente.

Nell'esempio seguente `TechnicalProfile` viene illustrato l'utilizzo di attestazioni di visualizzazione con i controlli di visualizzazione.

* La prima attestazione di visualizzazione fa riferimento al `emailVerificationControl` controllo display, che raccoglie e verifica l'indirizzo di posta elettronica.
* La Quinta attestazione di visualizzazione fa riferimento al `phoneVerificationControl` controllo display, che raccoglie e verifica un numero di telefono.
* Le altre attestazioni di visualizzazione sono ClaimTypes da raccogliere dall'utente.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Come indicato in precedenza, un'attestazione di visualizzazione con un riferimento a un controllo di visualizzazione può eseguire la propria convalida, ad esempio la verifica dell'indirizzo di posta elettronica. Inoltre, la pagina autocertificata supporta l'utilizzo di un profilo tecnico di convalida per convalidare l'intera pagina, inclusi eventuali input utente (tipi di attestazione o controlli di visualizzazione), prima di passare al passaggio successivo dell'orchestrazione.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Combinare attentamente l'utilizzo delle attestazioni di visualizzazione e delle attestazioni di output

Se si specificano uno o più elementi **DisplayClaim** in un profilo tecnico autocertificato, è necessario usare un DisplayClaim per *ogni* attestazione che si desidera visualizzare sullo schermo e raccogliere dall'utente. Nessuna attestazione di output viene visualizzata da un profilo tecnico autocertificato che contiene almeno un'attestazione di visualizzazione.

Si consideri l'esempio seguente in cui un' `age` attestazione viene definita come attestazione di **output** in un criterio di base. Prima di aggiungere attestazioni di visualizzazione al profilo tecnico autocertificato, l' `age` attestazione viene visualizzata sullo schermo per la raccolta di dati dall'utente:

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Se un criterio foglia che eredita tale base successivamente specifica `officeNumber` come attestazione di **visualizzazione** :

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

L' `age` attestazione nei criteri di base non viene più visualizzata sullo schermo per l'utente, perché è effettivamente "nascosta". Per visualizzare l' `age` attestazione e raccogliere il valore Age dall'utente, è necessario aggiungere un `age` **DisplayClaim**.

## <a name="output-claims"></a>Attestazioni di output

L'elemento **OutputClaims** contiene un elenco di attestazioni da restituire al passaggio di orchestrazione successivo. L'attributo **DefaultValue** viene applicato solo se l'attestazione non è mai stata impostata. Se è stato impostato in un passaggio di orchestrazione precedente, il valore predefinito non diventa effettivo anche se l'utente lascia vuoto il valore. Per forzare l'utilizzo di un valore predefinito, impostare l'attributo **AlwaysUseDefaultValue** su `true`.

Per motivi di sicurezza, un valore di attestazione password ( `UserInputType` impostato su `Password` ) è disponibile solo per i profili tecnici di convalida del profilo tecnico autocertificato. Non è possibile usare l'attestazione password nei passaggi successivi dell'orchestrazione. 

> [!NOTE]
> Nelle versioni precedenti di Identity Experience Framework (Framework dell'esperienza), le attestazioni di output sono state usate per raccogliere dati dall'utente. Per raccogliere dati dall'utente, usare invece una raccolta **DisplayClaims** .

L'elemento **OutputClaimsTransformations** può contenere una raccolta di elementi **OutputClaimsTransformation** che vengono usati per modificare le attestazioni di output o per generarne di nuove.

### <a name="when-you-should-use-output-claims"></a>Quando si usano le attestazioni di output

In un profilo tecnico autocertificato, la raccolta di attestazioni di output restituisce le attestazioni al passaggio di orchestrazione successivo.

USA attestazioni di output nei casi seguenti:

- Le **attestazioni vengono restituite dalla trasformazione delle attestazioni di output**.
- **Impostazione di un valore predefinito in un'attestazione di output** senza raccogliere dati dall'utente o restituire i dati dal profilo tecnico di convalida. Il profilo tecnico autocertificato `LocalAccountSignUpWithLogonEmail` imposta l'attestazione **executed-SelfAsserted-Input** su `true`.
- **Un profilo tecnico di convalida restituisce le attestazioni di output** - Il profilo tecnico può chiamare un profilo tecnico di convalida che restituisce alcune attestazioni. È possibile che si desideri sviluppare le attestazioni e restituirle ai successivi passaggi dell'orchestrazione nel percorso utente. Quando si accede con un account locale, ad esempio, il profilo tecnico autocertificato denominato `SelfAsserted-LocalAccountSignin-Email` chiama il profilo tecnico di convalida denominato `login-NonInteractive`. Questo profilo tecnico convalida le credenziali utente e restituisce il profilo utente, ad esempio 'userPrincipalName', 'displayName', 'givenName' e 'surName'.
- **Un controllo di visualizzazione restituisce le attestazioni di output** . il profilo tecnico può avere un riferimento a un [controllo di visualizzazione](display-controls.md). Il controllo di visualizzazione restituisce alcune attestazioni, ad esempio l'indirizzo di posta elettronica verificato. È possibile che si desideri sviluppare le attestazioni e restituirle ai successivi passaggi dell'orchestrazione nel percorso utente. La funzionalità di controllo di visualizzazione è attualmente in **Anteprima**.

Nell'esempio seguente viene illustrato l'utilizzo di un profilo tecnico autocertificato che utilizza sia le attestazioni di visualizzazione sia le attestazioni di output.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Rendere persistenti le attestazioni

L'elemento PersistedClaims non viene utilizzato. Il profilo tecnico autocertificato non rende persistenti i dati Azure AD B2C. Viene invece eseguita una chiamata a un profilo tecnico di convalida che è responsabile della persistenza dei dati. I criteri di accesso ad esempio usano il profilo tecnico autocertificato `LocalAccountSignUpWithLogonEmail` per raccogliere il nuovo profilo utente. Il profilo tecnico `LocalAccountSignUpWithLogonEmail` chiama il profilo tecnico di convalida per creare l'account in Azure AD B2C.

## <a name="validation-technical-profiles"></a>Profili tecnici di convalida

Il profilo tecnico di convalida viene usato per convalidare alcune o tutte le attestazioni di output del profilo tecnico di riferimento. Le attestazioni di input del profilo tecnico di convalida devono apparire nelle attestazioni di output del profilo tecnico autocertificato. Il profilo tecnico di convalida esegue la convalida dell'input utente e può restituire un errore all'utente.

Il profilo tecnico di convalida può essere un qualsiasi profilo tecnico nei criteri, ad esempio un profilo tecnico di [Azure Active Directory](active-directory-technical-profile.md) o di un'[API REST](restful-technical-profile.md). Nell'esempio precedente il profilo tecnico `LocalAccountSignUpWithLogonEmail` convalida che l'attributo signinName non esiste nella directory. In caso contrario, il profilo tecnico di convalida crea un account locale e restituisce il valore objectId, authenticationSource, newUser. Il profilo tecnico `SelfAsserted-LocalAccountSignin-Email` chiama il profilo tecnico di convalida `login-NonInteractive` per convalidare le credenziali utente.

È possibile anche chiamare un profilo tecnico di API REST usando la logica di business che si preferisce, sovrascrivere le attestazioni di input o arricchire i dati utente integrando ulteriormente con applicazioni line-of-business aziendali. Per altre informazioni, vedere [Validation technical profile](validation-technical-profile.md) (Profilo tecnico di convalida)

## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Description |
| --------- | -------- | ----------- |
| impostazione. operatingMode <sup>1</sup>| No | Per una pagina di accesso, questa proprietà controlla il comportamento del campo del nome utente, ad esempio i messaggi di errore e di convalida di input. I valori previsti sono: `Username` o `Email`.  |
| AllowGenerationOfClaimsWithNullValues| No| Consente a di generare un'attestazione con valore null. Ad esempio, in un caso l'utente non seleziona una casella di controllo.|
| ContentDefinitionReferenceId | Sì | Identificatore della [definizione di contenuto](contentdefinitions.md) associata a questo profilo tecnico. |
| EnforceEmailVerification | No | Per la registrazione o la modifica del profilo, applica la verifica tramite posta elettronica. I valori possibili sono: `true` (impostazione predefinita) o `false`. |
| setting.retryLimit | No | Controlla il numero di volte in cui un utente può provare a fornire i dati verificati rispetto a un profilo tecnico di convalida. Un utente tenta ad esempio di iscriversi con un account che esiste già e insiste fino a quando non raggiunge il limite.
| SignUpTarget <sup>1</sup>| No | Identificatore di scambio di destinazione dell'iscrizione. Quando l'utente fa clic sul pulsante di iscrizione, Azure AD B2C esegue l'identificatore di scambio specificato. |
| setting.showCancelButton | No | Visualizza il pulsante Annulla. I valori possibili sono: `true` (impostazione predefinita) o `false` |
| setting.showContinueButton | No | Visualizza il pulsante Continua. I valori possibili sono: `true` (impostazione predefinita) o `false` |
| impostazione. showSignupLink <sup>2</sup>| No | Visualizza il pulsante di iscrizione. I valori possibili sono: `true` (impostazione predefinita) o `false` |
| impostazione. forgotPasswordLinkLocation <sup>2</sup>| No| Visualizza il collegamento password dimenticata. Valori possibili: `AfterInput` (impostazione predefinita) il collegamento viene visualizzato nella parte inferiore della pagina o `None` rimuove il collegamento password dimenticata.|
| impostazione. enableRememberMe <sup>2</sup>| No| Visualizza la casella [di controllo Mantieni l'accesso](custom-policy-keep-me-signed-in.md) . Valori possibili: `true` , o `false` (impostazione predefinita). |
| impostazione. inputVerificationDelayTimeInMilliseconds <sup>3</sup>| No| Consente di migliorare l'esperienza utente, attendendo che l'utente interrompa la digitazione, quindi convalidare il valore. Valore predefinito 2000 millisecondi. |
| IncludeClaimResolvingInClaimsHandling  | No | Per le attestazioni di input e output, specifica se la [risoluzione delle attestazioni](claim-resolver-overview.md) è inclusa nel profilo tecnico. Valori possibili: `true` , o `false`   (impostazione predefinita). Se si desidera utilizzare un resolver di attestazioni nel profilo tecnico, impostare questa impostazione su `true` . |

Note:
1. Disponibile per la definizione del contenuto [DataUri](contentdefinitions.md#datauri) tipo di `unifiedssp` o `unifiedssd` .
1. Disponibile per la definizione del contenuto [DataUri](contentdefinitions.md#datauri) tipo di `unifiedssp` o `unifiedssd` . [Layout di pagina](page-layout.md) 1.1.0 e versioni successive.
1. Disponibile per il [layout di pagina](page-layout.md) 1.2.0 e versioni successive.

## <a name="cryptographic-keys"></a>Chiavi di crittografia

L'elemento **CryptographicKeys** non viene usato.
