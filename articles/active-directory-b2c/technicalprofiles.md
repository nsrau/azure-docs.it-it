---
title: TechnicalProfiles | Microsoft Docs
description: Specificare l'elemento Technicalprofiles di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c17159e1fa901e8219d1727769d234719c4321c6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856554"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un elemento **TechnicalProfiles** contiene un set di profili tecnici supportati dal provider di attestazione. Ogni provider di attestazioni deve disporre di uno o più profili tecnici che determinano gli endpoint e i protocolli necessari per comunicare con il provider stesso. Un provider di attestazioni può avere più profili tecnici.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

L'elemento **TechnicalProfile** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
|---------|---------|---------|
| ID | Yes | Un identificatore univoco del profilo tecnico. È possibile fare riferimento al profilo tecnico usando questo identificatore dagli altri elementi nel file dei criteri. Ad esempio, **OrchestrationSteps** e **ValidationTechnicalProfile**. |

L'elemento **TechnicalProfile** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| Domain | 0:1 | Il nome di dominio per il profilo tecnico. Ad esempio, se il profilo tecnico specifica il provider di identità Facebook, il nome di dominio è Facebook.com. |
| DisplayName | 0:1 | Il nome del profilo tecnico che può essere visualizzato dagli utenti. |
| DESCRIZIONE | 0:1 | La descrizione del profilo tecnico che può essere visualizzato dagli utenti. |
| Protocollo | 0:1 | Il protocollo usato per la comunicazione con altre entità. |
| Metadata | 0:1 | Una raccolta di coppie di chiavi/valori che vengono usate dal protocollo per comunicare con l'endpoint durante la transazione. |
| InputTokenFormat | 0:1 | Il formato del token di input. I valori possibili sono: `JSON`, `JWT`, `SAML11` o `SAML2`. Il valore `JWT` rappresenta un Token JSON Web in base alla specifica IETF. Il valore `SAML11` rappresenta un token di sicurezza SAML 1.1 in base alla specifica di OASIS.  Il valore `SAML2` rappresenta un token di sicurezza SAML 2.0 in base alla specifica di OASIS. |
| OutputTokenFormat | 0:1 | Il formato del token di output. I valori possibili sono: `JSON`, `JWT`, `SAML11` o `SAML2`. |
| CryptographicKeys | 0:1 | Un elenco di chiavi di crittografia usate nel profilo tecnico. |
| InputClaimsTransformations | 0:1 | Un elenco di riferimenti definiti in precedenza per le trasformazioni di attestazioni che devono essere eseguiti prima che le attestazioni siano inviate al provider di attestazioni o relying party. |
| InputClaims | 0:1 | Un elenco dei riferimenti definiti in precedenza per i tipi di attestazione eseguiti come input nel profilo tecnico. |
| PersistedClaims | 0:1 | Un elenco dei riferimenti definiti in precedenza per i tipi di attestazione mantenuti dal provider di attestazioni che si riferisce al profilo tecnico. |
| OutputClaims | 0:1 | Un elenco dei riferimenti definiti in precedenza per i tipi di attestazione eseguiti come output nel profilo tecnico. |
| OutputClaimsTransformations | 0:1 | Un elenco di riferimenti definiti in precedenza per le trasformazioni di attestazioni che devono essere eseguiti prima che le attestazioni siano ricevute dal provider di attestazioni. |
| ValidationTechnicalProfiles | 0:n | Un elenco di riferimenti ad altri profili tecnici che il profilo tecnico usa ai fini della convalida. Per altre informazioni, vedere il [profilo tecnico di convalida](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Controlla la produzione del nome del soggetto nei token in cui il nome del soggetto è specificato separatamente dalle attestazioni. Ad esempio OAuth o SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | Un identificatore del profilo tecnico da cui si desidera aggiungere tutte le attestazioni di input e output al profilo tecnico. Il profilo tecnico a cui si fa riferimento deve essere definito nello stesso file di criteri. | 
| IncludeTechnicalProfile |0:1 | Un identificatore del profilo tecnico da cui si desidera aggiungere tutti i dati al profilo tecnico. Il profilo tecnico a cui si fa riferimento deve esistere nello stesso file di criteri. |
| UseTechnicalProfileForSessionManagement | 0:1 | Un profilo tecnico diverso da usare per la gestione della sessione. |
|EnabledForUserJourneys| 0:1 |Controlla se il profilo tecnico viene eseguito in un percorso utente.  |

### <a name="protocol"></a>Protocollo

L'elemento **Protocollo** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| NOME | Yes | Nome di un protocollo valido supportato da Azure AD B2C usato come parte del profilo tecnico. I valori possibili sono: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `WsFed`, `WsTrust`, `Proprietary`, `session management`, `self-asserted` o `None`. |
| Gestore | No  | Quando il nome del protocollo è impostato su `Proprietary`, specificare il nome completo dell'assembly usato da Azure AD B2C per determinare il gestore di protocollo. |

### <a name="metadata"></a>Metadata

L'elemento **Metadata** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| Elemento | 0:n | I metadati che riguardano il profilo tecnico. Ogni tipo di profilo tecnico dispone di un diverso set di elementi di metadati. Per altre informazioni, vedere la sezione dedicata ai tipi di profilo tecnico. |

#### <a name="item"></a>Elemento

L'elemento **Elemento** di **Metadati** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| Chiave | Yes | La chiave dei metadati. Per l'elenco degli elementi di metadati, vedere ogni tipo di profilo tecnico. |

### <a name="cryptographickeys"></a>CryptographicKeys

L'elemento **CryptographicKeys** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| Chiave | 1:n | Una chiave di crittografia usata in questo profilo tecnico. |

#### <a name="key"></a>Chiave

L'elemento **Chiave** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ID | No  | Un identificatore univoco di una coppia di chiavi specifica a cui fanno riferimento altri elementi nel file dei criteri. |
| StorageReferenceId | Yes | Un identificatore di un contenitore di chiavi di archiviazione a cui fanno riferimento altri elementi nel file dei criteri. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

L'elemento **InputClaimsTransformations** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | L'identificatore di una trasformazione di attestazioni che deve essere eseguita prima che le attestazioni siano inviate al provider di attestazioni o relying party. Una trasformazione delle attestazioni può essere usata per modificare le attestazioni ClaimsSchema esistenti o generarne di nuove. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

L'elemento **InputClaimsTransformation** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Un identificatore di trasformazione delle attestazioni già definito nel file dei criteri o nel file dei criteri padre. |

### <a name="inputclaims"></a>InputClaims

L'elemento **InputClaims** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Un tipo di attestazione di input previsto. |

#### <a name="inputclaim"></a>InputClaim 

L'elemento **InputClaim** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | L'identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file dei criteri o del file dei criteri padre. |
| DefaultValue | No  | Un valore predefinito da usare per creare un'attestazione se l'attestazione indicata da ClaimTypeReferenceId non esiste, in modo che l'attestazione che ne deriva possa essere usata come InputClaim dal profilo tecnico. |
| PartnerClaimType | No  | L'identificatore del tipo di attestazione del partner esterno verso cui il tipo di attestazione di criterio specificato esegue il mapping. Se l'attributo PartnerClaimType non è specificato, viene eseguito il mapping del tipo di attestazione di criteri specificato al tipo di attestazione partner con lo stesso nome. Utilizzare questa proprietà quando il nome del tipo di attestazione è diverso da altre entità. Ad esempio, il primo nome dell'attestazione è 'givenName', mentre il partner usa un'attestazione denominata 'first_name'. |

### <a name="persistedclaims"></a>PersistedClaims

L'elemento **PersistedClaims** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | Il tipo di attestazione da mantenere. |

#### <a name="persistedclaim"></a>PersistedClaim 

L'elemento **PersistedClaim** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | L'identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file dei criteri o del file dei criteri padre. |
| DefaultValue | No  | Un valore predefinito da usare per creare un'attestazione se l'attestazione indicata da ClaimTypeReferenceId non esiste, in modo che l'attestazione che ne deriva possa essere usata come InputClaim dal profilo tecnico. |
| PartnerClaimType | No  | L'identificatore del tipo di attestazione del partner esterno verso cui il tipo di attestazione di criterio specificato esegue il mapping. Se l'attributo PartnerClaimType non è specificato, viene eseguito il mapping del tipo di attestazione di criteri specificato al tipo di attestazione partner con lo stesso nome. Utilizzare questa proprietà quando il nome del tipo di attestazione è diverso da altre entità. Ad esempio, il primo nome dell'attestazione è 'givenName', mentre il partner usa un'attestazione denominata 'first_name'. |

### <a name="outputclaims"></a>OutputClaims

L'elemento **OutputClaims** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Un tipo di attestazione di output previsto. |

#### <a name="outputclaim"></a>OutputClaim 

L'elemento **OutputClaim** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | L'identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file dei criteri o del file dei criteri padre. |
| DefaultValue | No  | Un valore predefinito da usare per creare un'attestazione se l'attestazione indicata da ClaimTypeReferenceId non esiste, in modo che l'attestazione che ne deriva possa essere usata come InputClaim dal profilo tecnico. |
|AlwaysUseDefaultValue |No  |Forzare l'uso del valore predefinito.  |
| PartnerClaimType | No  | L'identificatore del tipo di attestazione del partner esterno verso cui il tipo di attestazione di criterio specificato esegue il mapping. Se l'attributo PartnerClaimType non è specificato, viene eseguito il mapping del tipo di attestazione di criteri specificato al tipo di attestazione partner con lo stesso nome. Utilizzare questa proprietà quando il nome del tipo di attestazione è diverso da altre entità. Ad esempio, il primo nome dell'attestazione è 'givenName', mentre il partner usa un'attestazione denominata 'first_name'. |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

L'elemento **OutputClaimsTransformations** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Gli identificatori delle trasformazione di attestazioni che devono essere eseguiti prima che le attestazioni siano inviate al provider di attestazioni o relying party. Una trasformazione delle attestazioni può essere usata per modificare le attestazioni ClaimsSchema esistenti o generarne di nuove. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

L'elemento **OutputClaimsTransformation** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Un identificatore di trasformazione delle attestazioni già definito nel file dei criteri o nel file dei criteri padre. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

L'elemento **ValidationTechnicalProfiles** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Gli identificatori dei profili tecnici che consentono di convalidare alcune o tutte le attestazioni di output del profilo tecnico di riferimento. Tutte le attestazioni di input del profilo tecnico a cui si fa riferimento devono essere visualizzate nelle attestazioni di output del profilo tecnico di riferimento. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

L'elemento **ValidationTechnicalProfile** contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Un identificatore del profilo tecnico già definito nel file dei criteri o nel file dei criteri padre. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

Il **SubjectNamingInfo** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ClaimType | Yes | Un identificatore di un tipo di attestazione già definito nella sezione ClaimsSchema del file dei criteri. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

L'elemento **IncludeTechnicalProfile** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Un identificatore del profilo tecnico già definito nel file dei criteri o nel file dei criteri padre. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

L'elemento **UseTechnicalProfileForSessionManagement** contiene l'attributo seguente:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Un identificatore del profilo tecnico già definito nel file dei criteri o nel file dei criteri padre. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
L'elemento **ClaimsProviderSelections** in un percorso utente definisce l'elenco di selezioni di provider di attestazioni e il relativo ordine. Con l'elemento **EnabledForUserJourneys** è possibile filtrare i provider di attestazioni disponibili per l'utente. L'elemento **EnabledForUserJourneys** contiene uno dei valori seguenti:

- **Sempre**, eseguire il profilo tecnico.
- **Mai**, ignorare il profilo tecnico. 
- **OnClaimsExistence**, eseguire solo quando esiste una determinata attestazione, specificata nel profilo tecnico. 
- **OnItemExistenceInStringCollectionClaim**, eseguire solo quando un elemento esiste in un'attestazione di raccolta di stringhe. 
- **OnItemExistenceInStringCollectionClaim**, eseguire solo quando un elemento non esiste in un'attestazione di raccolta di stringhe.

L'uso di **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** o **OnItemAbsenceInStringCollectionClaim** richiede l'indicazione dei metadati seguenti: **ClaimTypeOnWhichToEnable** specifica il tipo dell'attestazione che deve essere valutata. **ClaimValueOnWhichToEnable** specifica il valore da confrontare.

Il seguente profilo tecnico viene eseguito solo se la raccolta di stringhe **identityProviders** contiene il valore di `facebook.com`:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
        <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
        <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>        
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>  
```












