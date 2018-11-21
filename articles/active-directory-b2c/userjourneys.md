---
title: UserJourneys | Microsoft Docs
description: Specificare l'elemento UserJourneys di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 231a3e87692e47ec33f8a613832acf5102257c96
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567061"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I percorsi utente specificano percorsi espliciti attraverso cui un criterio consente a un'applicazione relying party di ottenere le attestazioni desiderate per un utente. L'utente segue questi percorsi per recuperare le attestazioni che devono essere presentate alla relying party. In altre parole, i percorsi utente definiscono la logica di business di ciò a cui accede un utente finale mentre il framework dell'esperienza di gestione delle identità di Azure AD B2C elabora la richiesta.

Questi percorsi utente possono essere considerati come modelli disponibili per soddisfare le esigenze di base delle varie relying parti della community di interesse. I percorsi utente facilitano la definizione della parte relying party di un criterio. Un criterio può definire più percorsi utente. Ogni percorso utente è una sequenza di passaggi di orchestrazione.

Per definire i percorsi utente supportati dai criteri, viene aggiunto un elemento **UserJourneys** nell'elemento di primo livello del file dei criteri. 

L'elemento **UserJourneys** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| UserJourney | 1:n | Percorso utente che definisce tutti i costrutti necessari per un flusso utente completo. | 

L'elemento **UserJourney** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ID | Yes | Identificatore di un percorso utente che può essere usato in modo che altri elementi nei criteri possano farvi riferimento. L'elemento **DefaultUserJourney** dei [criteri della relying party](relyingparty.md) punta a questo attributo. |

L'elemento **UserJourney** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1:n | Sequenza di orchestrazione da seguire per garantire l'esito positivo di una transazione. Ogni percorso utente è costituito da un elenco ordinato di passaggi di orchestrazione eseguiti in sequenza. Se un passaggio non riesce, la transazione ha esito negativo. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Un percorso utente è costituito da una sequenza di orchestrazione da seguire per garantire l'esito positivo di una transazione. Se un passaggio non riesce, la transazione ha esito negativo. Questi passaggi di orchestrazione referenziano i blocchi predefiniti e i provider di attestazioni consentiti nel file dei criteri. Qualsiasi passaggio di orchestrazione finalizzato a mostrare o eseguire il rendering di un'esperienza utente ha anche un riferimento all'identificatore della definizione del contenuto corrispondente.

I passaggi di orchestrazione possono essere eseguiti in modo condizionale sulla base di precondizioni definite nell'elemento del passaggio di orchestrazione. È ad esempio possibile verificare di eseguire un passaggio di orchestrazione solo se esiste una specifica attestazione o se una di queste è uguale o meno al valore specificato. 

Per specificare l'elenco ordinato dei passaggi di orchestrazione, viene aggiunto un elemento **OrchestrationSteps** come parte dei criteri. Questo elemento è obbligatorio.

L'elemento **OrchestrationSteps** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1:n | Passaggio di orchestrazione ordinato. | 

L'elemento **OrchestrationStep** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| Ordine | Yes | Ordine dei passaggi di orchestrazione. | 
| type | Yes | Tipo del passaggio di orchestrazione. Valori possibili: <ul><li>**ClaimsProviderSelection**: indica che il passaggio di orchestrazione mostra diversi provider di attestazioni all'utente per selezionarne uno.</li><li>**CombinedSignInAndSignUp**: indica che il passaggio di orchestrazione mostra una pagina combinata di accesso a provider di social network e iscrizione ad account locali.</li><li>**ClaimsExchange**: indica che il passaggio di orchestrazione scambia attestazioni con un provider di attestazioni.</li><li>**SendClaims**: indica che il passaggio di orchestrazione invia le attestazioni alla relying party con un token emesso da un'autorità di certificazione delle attestazioni.</li></ul> | 
| ContentDefinitionReferenceId | No  | Identificatore della [definizione del contenuto](contentdefinitions.md) associata a questo passaggio di orchestrazione. In genere l'identificatore di riferimento della definizione del contenuto viene definito nel profilo tecnico autocertificato. In alcuni casi, tuttavia, Azure AD B2C deve visualizzare un elemento senza un profilo tecnico. Esistono due esempi, se il tipo di passaggio di orchestrazione è uno dei seguenti: `ClaimsProviderSelection` o `CombinedSignInAndSignUp`. Azure AD B2C deve visualizzare la selezione del provider di identità senza avere un profilo tecnico. | 
| CpimIssuerTechnicalProfileReferenceId | No  | Il tipo del passaggio di orchestrazione è `SendClaims`. Questa proprietà definisce l'identificatore del profilo tecnico del provider di attestazioni che emette il token per la relying party.  Se assente, non viene creato alcun token di relying party. |


L'elemento **OrchestrationStep** può contenere gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- | 
| Preconditions | 0:n | Elenco di precondizioni che devono essere soddisfatte per l'esecuzione del passaggio di orchestrazione. | 
| ClaimsProviderSelections | 0:n | Elenco di selezioni di provider di attestazioni per il passaggio di orchestrazione. | 
| ClaimsExchanges | 0:n | Elenco di scambi di attestazioni per il passaggio di orchestrazione. | 

#### <a name="preconditions"></a>Preconditions

L'elemento **Preconditions** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- | 
| Precondition | 0:n | In base al profilo tecnico in uso, reindirizza il client in base alla selezione del provider di attestazioni o esegue una chiamata al server per lo scambio di attestazioni. | 


##### <a name="precondition"></a>Precondition

L'elemento **Precondition** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| type | Yes | Tipo di controllo o query da eseguire per questa precondizione. Il valore può essere **ClaimsExist**, a indicare che le azioni devono essere eseguite se le attestazioni specificate esistono nel set di attestazioni corrente dell'utente, oppure **ClaimEquals**, a indicare che le azioni devono essere eseguite se l'attestazione specificata esiste e il relativo valore è uguale al valore specificato. |
| ExecuteActionsIf | Yes | Usare un test true o false per decidere se eseguire le azioni nella precondizione. | 

L'elemento **Precondition** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| Valore | 1:n | ClaimTypeReferenceId per cui eseguire una query. Un altro elemento value contiene il valore da controllare.</li></ul>|
| Azione | 1:1 | Azione da eseguire se il controllo della precondizione all'interno di un passaggio di orchestrazione è true. Se il valore di `Action` è impostato su `SkipThisOrchestrationStep`, l'elemento `OrchestrationStep` associato non deve essere eseguito. | 

### <a name="preconditions-examples"></a>Esempi di precondizioni

Le precondizioni seguenti determinano l'esistenza del valore objectId dell'utente. Nel percorso utente l'utente ha scelto di eseguire l'accesso con l'account locale. Se il valore objectId è presente, ignorare questo passaggio di orchestrazione.

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Le precondizioni seguenti determinano se l'utente ha eseguito l'accesso con un account di social network. Viene effettuato un tentativo per trovare l'account utente nella directory. Se l'utente accede o si iscrive con un account locale, ignorare questo passaggio di orchestrazione.

```XML
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Le precondizioni possono verificare più precondizioni. L'esempio seguente determina se è presente 'objectId' o 'email'. Se la prima condizione è true, il percorso procede al passaggio successivo dell'orchestrazione.

```XML
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>      
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsproviderselection"></a>ClaimsProviderSelection

Un passaggio di orchestrazione di tipo `ClaimsProviderSelection` o `CombinedSignInAndSignUp` può contenere un elenco di provider di attestazioni con cui un utente può eseguire l'accesso. L'ordine degli elementi all'interno degli elementi `ClaimsProviderSelections` determina l'ordine dei provider di identità visualizzati.

L'elemento **ClaimsProviderSelection** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 0:n | Fornisce l'elenco dei provider di attestazioni che possono essere selezionati.|

L'elemento **ClaimsProviderSelection** contiene gli attributi seguenti: 

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | No  | Identificatore dello scambio di attestazioni, eseguito nel passaggio successivo di selezione dei provider di attestazioni. È necessario specificare questo attributo o l'attributo ValidationClaimsExchangeId, ma non entrambi. | 
| ValidationClaimsExchangeId | No  | Identificatore dello scambio di attestazioni, eseguito nel passaggio corrente di convalida della selezione dei provider di attestazioni. È necessario specificare questo attributo o l'attributo TargetClaimsExchangeId, ma non entrambi. |

### <a name="claimsproviderselection-example"></a>Esempio di ClaimsProviderSelection

Nel passaggio di orchestrazione seguente l'utente può scegliere eseguire l'accesso con Facebook, LinkedIn, Twitter, Google o un account locale. Se l'utente sceglie uno dei provider di identità di social networking, il secondo passaggio di orchestrazione viene eseguito con lo scambio di attestazioni selezionato specificato nell'attributo `TargetClaimsExchangeId`. Il secondo passaggio di orchestrazione reindirizza l'utente al provider di identità di social network per completare il processo di accesso. Se l'utente sceglie di eseguire l'accesso con l'account locale, Azure AD B2C rimane nello stesso passaggio di orchestrazione (la stessa pagina di iscrizione o accesso) e ignora il secondo passaggio.

```XML
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
    <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
    </ClaimsProviderSelections>
    <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange" 
                    TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
    </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

L'elemento **ClaimsExchanges** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| ClaimsExchange | 0:n | In base al profilo tecnico in uso, reindirizza il client in base al valore ClaimsProviderSelection selezionato o esegue una chiamata al server per lo scambio di attestazioni. | 

L'elemento **ClaimsExchange** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ID | Yes | Identificatore del passaggio di scambio di attestazioni. L'identificatore viene usato per referenziare lo scambio di attestazioni da un passaggio di selezione dei provider di attestazioni nei criteri. | 
| TechnicalProfileReferenceId | Yes | Identificatore del profilo tecnico da eseguire. |
 
















