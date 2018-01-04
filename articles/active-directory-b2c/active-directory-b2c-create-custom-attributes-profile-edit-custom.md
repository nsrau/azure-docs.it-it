---
title: 'Azure Active Directory B2C: Aggiungere attributi a criteri personalizzati e usarli nella modifica del profilo | Microsoft Docs'
description: "Procedura dettagliata che illustra come usare proprietà di estensione e attributi personalizzati e includerli nell'interfaccia utente"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 0d4ee064c15c914eea7353900c6bb5a77b3e3b3b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Creazione e utilizzo di attributi personalizzati in criteri personalizzati di modifica del profilo

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In questo articolo si crea un attributo personalizzato nella directory di Azure AD B2C e si usa il nuovo attributo come attestazione personalizzata nel percorso utente di modifica del profilo.

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Usare attributi personalizzati per raccogliere informazioni sui clienti in Azure Active Directory B2C usando criteri personalizzati
La directory di Azure Active Directory (Azure AD) B2C viene fornita con un set predefinito di attributi: nome, cognome, città, codice postale, userPrincipalName e così via.  È spesso necessario creare attributi personalizzati.  Ad esempio: 
* Un'applicazione per i clienti deve rendere persistente un attributo, ad esempio "LoyaltyNumber".
* Un provider di identità ha un identificatore utente univoco che deve essere salvato, ad esempio "uniqueUserGUID".
* Un percorso utente personalizzato deve rendere persistente lo stato dell'utente, ad esempio "migrationStatus".

Con Azure AD B2C è possibile estendere il set di attributi archiviati in ogni account utente. Questi attributi possono anche essere scritti e letti usando l' [API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

Le proprietà di estensione estendono lo schema degli oggetti utente nella directory.  I termini proprietà di estensione, attributo personalizzato e attestazione personalizzata fanno riferimento allo stesso concetto nel contesto di questo articolo e il nome varia a seconda del contesto (applicazione, oggetto, criteri).

Le proprietà di estensione possono essere registrate solo su un oggetto applicazione anche se possono contenere dati per un utente. La proprietà è collegata all'applicazione. All'oggetto applicazione deve essere concesso l'accesso in scrittura per registrare una proprietà di estensione. In un oggetto possono essere scritte 100 proprietà di estensione per TUTTI i tipi e TUTTE le applicazioni. Le proprietà di estensione vengono aggiunte al tipo di directory di destinazione e diventano immediatamente accessibili nel tenant della directory di Azure AD B2C.
Se l'applicazione viene eliminata, vengono rimosse anche le proprietà di estensione insieme a tutti i dati contenuti per tutti gli utenti. Se una proprietà di estensione viene eliminata dall'applicazione, viene rimossa dagli oggetti della directory di destinazione e i valori vengono eliminati.

Le proprietà di estensione esistono solo nel contesto di un'applicazione registrata nel tenant. L'ID oggetto dell'applicazione deve essere incluso nell'elemento TechnicalProfile che usa l'applicazione.

>[!NOTE]
>La directory di Azure AD B2C include in genere un'app Web denominata `b2c-extensions-app`.  Questa applicazione viene usata principalmente dai criteri B2C predefiniti per le attestazioni personalizzate create tramite il portale di Azure.  L'uso di questa applicazione per registrare estensioni per i criteri personalizzati B2C è consigliata solo agli utenti esperti.  Le istruzioni per questa operazione sono comprese nella sezione Passaggi successivi di questo articolo.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Creazione di una nuova applicazione per archiviare le proprietà di estensione

1. Aprire una sessione di esplorazione, passare al [portale di Azure](https://portal.azure.com) e accedere con le credenziali amministrative della directory B2C che si vuole configurare.
1. Nel menu di spostamento sinistro fare clic su **Azure Active Directory**. Potrebbe essere necessario selezionare Altri servizi> per trovarlo.
1. Selezionare **Registrazioni per l'app** e fare clic su **Registrazione nuova applicazione**
1. Specificare gli elementi seguenti consigliati:
  * Specificare un nome per l'applicazione web: **WebApp-GraphAPI-DirectoryExtensions**
  * Tipo di applicazione: app Web/API
  * URL di accesso: https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Selezionare **Crea. Il completamento dell'operazione viene visualizzato nella sezione **notifiche**
1. Selezionare l'applicazione web appena creata: **WebApp-GraphAPI-DirectoryExtensions**
1. Selezionare Impostazioni: **Autorizzazioni necessarie**
1. Selezionare l'API **Windows Azure Active Directory**
1. Inserire un segno di spunta in Autorizzazioni per l'applicazione: **Legge e scrive i dati della directory**, quindi **Salva**
1. Selezionare **Concedere le autorizzazioni** e quindi fare clic su **Sì** per confermare.
1. Copiare negli Appunti e salvare gli identificatori seguenti da WebApp-GraphAPI-DirectoryExtensions>Impostazioni>Proprietà>
*  **ID applicazione**. Esempio: `103ee0e6-f92d-4183-b576-8c3739027780`
* **ID oggetto**. Esempio: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modifica dei criteri personalizzati per l'aggiunta di ApplicationObjectId

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item>
                <Item Key="ClientId">insert appId here</Item>
              </Metadata>
            <!-- End of changes -->
              <CryptographicKeys>
                <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
              </CryptographicKeys>
              <IncludeInSso>false</IncludeInSso>
              <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
            </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
```

>[!NOTE]
><TechnicalProfile Id="AAD-Common"> viene definito "common" perché i relativi elementi vengono inclusi e riusati in tutti i TechnicalProfile di Azure Active Directory usando l'elemento: `<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>Quando TechnicalProfile scrive per la prima volta nella proprietà di estensione appena creata, può verificarsi un errore occasionale.  La proprietà di estensione viene creata la prima volta che viene usata.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Uso della nuova proprietà di estensione o del nuovo attributo personalizzato in un percorso utente


1. Aprire il file Relying Party che descrive il percorso utente di modifica del criterio.  Se si è appena iniziato, può essere consigliabile scaricare la versione del file RP-PolicyEdit già configurata direttamente dalla sezione dei criteri personalizzati di Azure B2C nel portale di Azure.  In alternativa, aprire il file XML dalla cartella di archiviazione.
2. Aggiungere un'attestazione personalizzata `loyaltyId`.  Includendo l'attestazione personalizzata nell'elemento `<RelyingParty>`, l'attestazione viene passata come parametro agli elementi UserJourney TechnicalProfile e inclusa nel token per l'applicazione.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Aggiungere una definizione di attestazione al file dei criteri di estensione `TrustFrameworkExtensions.xml` all'interno dell'elemento `<ClaimsSchema>`, come illustrato di seguito.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Aggiungere la stessa definizione di attestazione al file dei criteri di base `TrustFrameworkBase.xml`.  
>L'aggiunta di una definizione `ClaimType` al file di base e delle estensioni non è generalmente necessaria. Dato che i passaggi successivi aggiungeranno tuttavia extension_loyaltyId agli elementi TechnicalProfile nel file di base, la convalida dei criteri rifiuterà il caricamento del file di base in assenza della definizione.
>Può essere utile tenere traccia dell'esecuzione del percorso utente denominato "ProfileEdit" nel file TrustFrameworkBase.xml.  Cercare il percorso utente con lo stesso nome nell'editor e osservare che il passaggio di orchestrazione 5 richiama TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Cercare ed esaminare questo elemento TechnicalProfile per acquisire familiarità con il flusso.
5. Aggiungere loyaltyId come attestazione di input e output nell'elemento TechnicalProfile "SelfAsserted-ProfileUpdate"
```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
          <DisplayName>User ID signup</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>

            <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
            <InputClaim ClaimTypeReferenceId="userPrincipalName" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Aggiungere l'attestazione nell'elemento TechnicalProfile "AAD-UserWriteProfileUsingObjectId" per rendere persistente il valore dell'attestazione nella proprietà di estensione per l'utente corrente nella directory.
```xml
<TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
          <Metadata>
            <Item Key="Operation">Write</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <IncludeInSso>false</IncludeInSso>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <PersistedClaims>
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Aggiungere l'attestazione nell'elemento TechnicalProfile "AAD-UserReadUsingObjectId" per leggere il valore dell'attributo di estensione ogni volta che l'utente esegue l'accesso. Fino ad ora gli elementi TechnicalProfile sono stati modificati solo nel flusso degli account locali.  Per inserire il nuovo attributo nel flusso di un account di social networking/federato è necessario modificare un set diverso di elementi TechnicalProfile. Vedere i passaggi successivi.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
     <TechnicalProfile Id="AAD-UserReadUsingObjectId">
       <Metadata>
         <Item Key="Operation">Read</Item>
         <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
       </Metadata>
       <IncludeInSso>false</IncludeInSso>
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
       </InputClaims>
       <OutputClaims>
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>L'elemento IncludeTechnicalProfile aggiunge tutti gli elementi di AAD-Common a questo TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Testare i criteri personalizzati tramite "Esegui adesso"
1. Aprire il pannello **Azure AD B2C** e passare a **Framework dell'esperienza di gestione delle identità > Criteri personalizzati**.
1. Selezionare il criterio personalizzato che è stato caricato e fare clic sul pulsante **Esegui adesso**.
1. Dovrebbe essere possibile iscriversi usando un indirizzo di posta elettronica.

Il token ID inviato all'applicazione include la nuova proprietà di estensione come attestazione personalizzata, preceduta da extension_loyaltyId. Vedere l'esempio.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Aggiungere la nuova attestazione ai flussi per gli accessi con account di social networking modificando gli elementi TechnicalProfile elencati di seguito. Questi due elementi TechnicalProfile vengono usati per gli accessi con account di social networking/federati per scrivere e leggere i dati utente usando alternativeSecurityId come localizzatore dell'oggetto utente.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

È possibile usare gli stessi attributi di estensione tra i criteri predefiniti e personalizzati.
Quando si aggiungono attributi di estensione (noti anche come attributi personalizzati) tramite il portale, gli attributi vengono registrati usando la **b2c-extensions-app presente in ogni tenant b2c.  Per usare questi attributi di estensione nei criteri personalizzati:
1. All'interno del tenant b2c in portal.azure.com, passare a **Azure Active Directory** e selezionare **Registrazioni dell'app**
2. Trovare **b2c-extensions-app** e selezionarlo
3. Nella riga "Essentials" registrare l'**ID applicazione** e l'**ID oggetto**
4. Includerli nei metadati del profilo tecnico AAD-Common come indicato di seguito:

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
              </Metadata>
```

Per mantenere la coerenza con l'esperienza del portale, creare questi attributi tramite l'interfaccia utente del portale *prima* di usarli nei criteri personalizzati.  Quando si crea un attributo "ActivationStatus" nel portale, è necessario farvi riferimento, come indicato di seguito:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Riferimenti

* Un **profilo tecnico** è un tipo di elemento che può essere considerato una *funzione* che definisce il nome di un endpoint, i relativi metadati, il protocollo e i dettagli dello scambio di attestazioni che deve essere eseguito dal Framework dell'esperienza di gestione delle identità.  Quando questa *funzione* viene chiamata in un passaggio di orchestrazione o da un altro TechnicalProfile, gli elementi InputClaims e OutputClaims vengono forniti come parametri dal chiamante.


* Per una descrizione completa delle proprietà di estensione, vedere l'articolo [ESTENSIONI DELLO SCHEMA DELLA DIRECTORY | CONCETTI RELATIVI ALL'API GRAPH](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Gli attributi di estensione nell'API Graph vengono denominati usando la convenzione `extension_ApplicationObjectID_attributename`. I criteri personalizzati fanno riferimento agli attributi di estensione come extension_attributename, omettendo quindi ApplicationObjectId nel file XML
