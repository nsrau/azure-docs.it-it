---
title: Aggiungere attestazioni e personalizzare l'input dell'utente nei criteri personalizzatiAdd claims and customize user input in custom policies
titleSuffix: Azure AD B2C
description: Informazioni su come personalizzare l'input dell'esperienza utente e aggiungere attestazioni al percorso di accesso o di registrazione in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473677"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Aggiungere attestazioni e input di personalizzazione dell'esperienza utente tramite criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In questo articolo viene raccolto un nuovo attributo durante il percorso di iscrizione in Azure Active Directory B2C (Azure AD B2C). Otterrai la città degli utenti, la configurerai come menu a discesa e definirai se è necessario fornirla.

> [!NOTE]
> In questo esempio viene utilizzata l'attestazione incorporata "città". È invece possibile scegliere uno degli [attributi predefiniti di Azure AD B2C](user-profile-attributes.md) supportati o un attributo personalizzato. Per utilizzare un attributo personalizzato, [abilitare gli attributi personalizzati nel criterio.](custom-policy-custom-attributes.md) Per utilizzare un attributo predefinito o personalizzato diverso, sostituire 'city' con l'attributo di propria scelta, ad esempio l'attributo predefinito *jobTitle* o un attributo personalizzato come *extension_loyaltyId*.  

È possibile raccogliere dati iniziali dagli utenti utilizzando il percorso utente di iscrizione o di accesso. In un secondo momento è possibile raccogliere attestazioni aggiuntive usando il percorso utente di modifica del profilo. Ogni volta che Azure AD B2C raccoglie le informazioni direttamente dall'utente in modo interattivo, Identity Experience Framework usa il proprio [profilo tecnico auto-asserito.](self-asserted-technical-profile.md) In questo esempio:In this sample, you:

1. Definire un reclamo "città". 
1. Chiedere all'utente la propria città.
1. Rendere persistente la città per il profilo utente nella directory B2C di Azure AD.
1. Leggere l'attestazione della città dalla directory B2C di Azure AD a ogni accesso.
1. Restituire la città all'applicazione relying party dopo l'accesso o l'iscrizione.  

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md). È necessario disporre di criteri personalizzati di lavoro per l'iscrizione e l'accesso con account social e locali.

## <a name="define-a-claim"></a>Definire un reclamoDefine a claim

Un'attestazione fornisce un archivio temporaneo dei dati durante l'esecuzione di criteri B2C di Azure AD. Lo [schema delle attestazioni](claimsschema.md) è la posizione in cui si dichiarano le attestazioni. Vengono usati gli elementi seguenti per definire l'attestazione:

- **DisplayName**: una stringa che definisce l'etichetta destinata all'utente.
- [DataType](claimsschema.md#datatype) : il tipo dell'attestazione.
- **UserHelpText**: consente all'utente di identificare i requisiti.
- [UserInputType:](claimsschema.md#userinputtype) tipo di controllo di input, ad esempio casella di testo, selezione radio, elenco a discesa o selezioni multiple.

Aprire il file delle estensioni del criterio. Ad esempio, <em> `SocialAndLocalAccounts/` </em>.

1. Cercare l'elemento [BuildingBlocks](buildingblocks.md). Se l'elemento non esiste, aggiungerlo.
1. Individuare l'elemento [ClaimsSchema.](claimsschema.md) Se l'elemento non esiste, aggiungerlo.
1. Aggiungere l'attestazione city all'elemento **ClaimsSchema.**  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Aggiungere un'attestazione all'interfaccia utente

I seguenti profili tecnici sono [auto-asseriti,](self-asserted-technical-profile.md)richiamati quando si prevede che un utente fornisca l'input:

- **LocalAccountSignUpWithLogonEmail** - Flusso di iscrizione all'account locale.
- **SelfAsserted-Social** - Account federato per la prima volta accesso utente.
- **SelfAsserted-ProfileUpdate** - Modifica flusso del profilo.

Per raccogliere il reclamo della città durante l'iscrizione, `LocalAccountSignUpWithLogonEmail` deve essere aggiunto come attestazione di uscita al profilo tecnico. Eseguire l'override di questo profilo tecnico nel file di estensione. Specificare l'intero elenco di attestazioni di output per controllare l'ordine in cui le attestazioni vengono presentate sullo schermo. Trovare l'elemento **ClaimsProviders**. Aggiungere un nuovo ClaimsProviders come segue:Add a new ClaimsProviders as follows:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Per raccogliere l'attestazione della città dopo l'accesso iniziale con un `SelfAsserted-Social` account federato, è necessario aggiungerla come attestazione di output al profilo tecnico. Affinché gli utenti dell'account locale e federato siano in grado `SelfAsserted-ProfileUpdate` di modificare i dati del profilo in un secondo momento, aggiungere l'attestazione di output al profilo tecnico. Eseguire l'override di questi profili tecnici nel file di estensione. Specificare l'intero elenco delle attestazioni di output per controllare l'ordine in cui le attestazioni vengono presentate sullo schermo. Trovare l'elemento **ClaimsProviders**. Aggiungere un nuovo ClaimsProviders come segue:Add a new ClaimsProviders as follows:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Leggere e scrivere un'attestazioneRead and write a claim

I profili tecnici seguenti sono [profili tecnici di Active Directory,](active-directory-technical-profile.md)che leggono e scrivono dati in Azure Active Directory.  
Utilizzare `PersistedClaims` per scrivere i dati `OutputClaims` nel profilo utente e leggere i dati dal profilo utente all'interno dei rispettivi profili tecnici di Active Directory.

Eseguire l'override di questi profili tecnici nel file di estensione. Trovare l'elemento **ClaimsProviders**.  Aggiungere un nuovo ClaimsProviders come segue:Add a new ClaimsProviders as follows:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Includere un'attestazione nel tokenInclude a claim in the token 

Per restituire l'attestazione della città all'applicazione relying <em> `SocialAndLocalAccounts/` </em> party, aggiungere un'attestazione di output al file. L'attestazione di output verrà aggiunta al token dopo un percorso utente riuscito e verrà inviata all'applicazione. Modificare l'elemento del profilo tecnico all'interno della sezione relying party per aggiungere la città come attestazione di output.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **Directory e sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant di Azure AD.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Selezionare **Identity Experience Framework**.
5. Selezionare **Carica criteri personalizzati**e caricare i file dei due criteri modificati.
2. Selezionare il criterio di iscrizione o di accesso che è stato caricato e fare clic sul pulsante **Esegui adesso**.
3. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

La schermata di iscrizione dovrebbe essere simile alla schermata seguente:The sign-up screen should look similar to the following screenshot:

![Screenshot dell'opzione di iscrizione modificata](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Il token inviato all'applicazione include l'attestazione `city`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sull'elemento [ClaimsSchema](claimsschema.md) sono riportate nel riferimento IEF.
- Informazioni su come utilizzare gli attributi personalizzati in un criterio di [modifica del profilo personalizzato.](custom-policy-custom-attributes.md)
