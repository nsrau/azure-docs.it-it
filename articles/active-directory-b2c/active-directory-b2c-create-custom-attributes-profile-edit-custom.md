---
title: Aggiungere propri attributi ai criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Procedura dettagliata che illustra come usare proprietà di estensione e attributi personalizzati e includerli nell'interfaccia utente.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 41c3db1c9a7295d939aa34a36f86c0dfa9fecd91
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891447"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Usare gli attributi personalizzati in un criterio di modifica del profilo personalizzato

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In questo articolo si apprenderà come creare un attributo personalizzato nella directory di Azure Active Directory (Azure AD) B2C. Si userà il nuovo attributo come attestazione personalizzata nel percorso utente di modifica del profilo.

## <a name="prerequisites"></a>Prerequisiti

Eseguire la procedura descritta nell'articolo [Azure Active Directory B2C: Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Usare attributi personalizzati per raccogliere informazioni sui clienti in Azure AD B2C usando criteri personalizzati
La directory di Azure AD B2C include un set predefinito di attributi. Alcuni esempi: **nome**, **cognome**, **città**, **codice postale** e **userPrincipalName**. È spesso necessario creare attributi personalizzati quali i seguenti:
* Un'applicazione per i clienti deve rendere permanente un attributo, ad esempio **LoyaltyNumber.**
* Un provider di identità ha un identificatore utente univoco che deve essere salvato, ad esempio **uniqueUserGUID**.
* Un percorso utente personalizzato deve rendere permanente lo stato dell'utente, ad esempio **migrationStatus**.

Con Azure AD B2C è possibile estendere il set di attributi archiviati in ogni account utente. Questi attributi possono anche essere scritti e letti usando l' [API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

Le proprietà di estensione estendono lo schema degli oggetti utente nella directory. I termini *proprietà di estensione*, *attributo personalizzato* e *attestazione personalizzata* fanno riferimento allo stesso concetto nel contesto di questo articolo. Il nome varia in base al contesto, ad esempio applicazione, oggetto o criteri.

Le proprietà di estensione possono essere registrate solo su un oggetto applicazione anche se possono contenere dati per un utente. La proprietà è collegata all'applicazione. All'oggetto applicazione deve essere concesso l'accesso in scrittura per registrare una proprietà di estensione. In un oggetto possono essere scritte cento proprietà di estensione per tutti i tipi e tutte le applicazioni. Le proprietà di estensione vengono aggiunte al tipo di directory di destinazione e diventano immediatamente accessibili nel tenant della directory di Azure AD B2C.
Se l'applicazione viene eliminata, vengono rimosse anche le proprietà di estensione insieme a tutti i dati contenuti per tutti gli utenti. Se una proprietà di estensione viene eliminata dall'applicazione, viene rimossa dagli oggetti della directory di destinazione e i valori vengono eliminati.

Le proprietà di estensione esistono solo nel contesto di un'applicazione registrata nel tenant. L'ID oggetto dell'applicazione deve essere incluso nell'elemento **TechnicalProfile** che usa l'applicazione.

>[!NOTE]
>La directory di Azure AD B2C include in genere un'app Web denominata `b2c-extensions-app`. Questa applicazione viene usata principalmente dai criteri B2C predefiniti per le attestazioni personalizzate create tramite il portale di Azure. L'uso di questa applicazione per registrare estensioni per i criteri personalizzati B2C è consigliata solo agli utenti esperti.  
Le istruzioni sono comprese nella sezione **Passaggi successivi** di questo articolo.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Creazione una nuova applicazione per archiviare le proprietà di estensione

1. Aprire una sessione del browser e passare al [portale di Azure](https://portal.azure.com). Accedere con le credenziali amministrative della directory B2C che si desidera configurare.
2. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory**. Potrebbe essere necessario selezionare **Altri servizi** per trovarlo.
3. Selezionare **Registrazioni per l'app**. Selezionare **Registrazione nuova applicazione**.
4. Aggiungere le voci seguenti:
    * Un nome per l'applicazione Web: **WebApp-GraphAPI-DirectoryExtensions**.
    * Il tipo di applicazione: **App/API Web**.
    * URL di accesso: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Selezionare **Create**.
6. Selezionare l'applicazione Web appena creata.
7. Selezionare **Impostazioni** > **Autorizzazioni necessarie**.
8. Selezionare l'API **Microsoft Azure Active Directory**.
9. Immettere un segno di spunta in Autorizzazioni dell'applicazione: **Lettura e scrittura dati directory**. Selezionare quindi **Salva**.
10. Selezionare **Concedere le autorizzazioni** e quindi fare clic su **Sì** per confermare.
11. Copiare negli Appunti e salvare gli identificatori seguenti:
    * **ID applicazione**. Esempio: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **ID oggetto**. Esempio: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Modifica dei criteri personalizzati per l'aggiunta di **ApplicationObjectId**

Quando è stata eseguita la procedura descritta in [Azure Active Directory B2C: Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md), sono stati scaricati e modificati [file di esempio](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) denominati **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml** e **PasswordReset.xml**. In questo passaggio, vengono apportate altre modifiche a tali file.

* Aprire il file **TrustFrameworkBase.xml** e aggiungere la sezione `Metadata` come illustrato nell'esempio seguente. Inserire l'ID oggetto registrato in precedenza per il valore `ApplicationObjectId` e l'ID applicazione registrato per il valore `ClientId`: 

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

> [!NOTE]
> Quando **TechnicalProfile** scrive per la prima volta nella proprietà di estensione appena creata, può verificarsi un errore occasionale. La proprietà di estensione viene creata la prima volta che viene usata.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Usare la nuova proprietà di estensione o del nuovo attributo personalizzato in un percorso utente

1. Aprire il file **ProfileEdit.xml**.
2. Aggiungere un'attestazione personalizzata `loyaltyId`. Includendo l'attestazione personalizzata nell'elemento `<RelyingParty>`, la si include anche nel token per l'applicazione.
    
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Aprire il file **TrustFrameworkExtensions.xml** e aggiungere l'elemento`<ClaimsSchema>` e i relativi elementi figlio all'elemento `BuildingBlocks`:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Aggiungere la stessa definizione `ClaimType` a **TrustFrameworkBase.xml**. Non è necessario aggiungere una definizione `ClaimType` nei file di base e di estensione. Tuttavia, con i passaggi successivi si aggiunge il `extension_loyaltyId` all'elemento **TechnicalProfile** nel file di base. Pertanto la convalida dei criteri rifiuterà il caricamento del file di base senza di esso. Può essere utile tenere traccia dell'esecuzione del percorso utente denominato **ProfileEdit** nel file **TrustFrameworkBase.xml**. Cercare il percorso utente con lo stesso nome nell'editor. Osservare che il passaggio di orchestrazione 5 richiama **TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate**. Cercare ed esaminare questo elemento **TechnicalProfile** per acquisire familiarità con il flusso.

5. Aprire il file **TrustFrameworkBase.xml** e aggiungere `loyaltyId` come attestazione di input e di output in **SelfAsserted-ProfileUpdate in TechnicalProfile**:

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
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. Nel file **trustframeworkbase. XML**, aggiungere l'attestazione `loyaltyId` nell'elemento **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Tale aggiunta rende permanente il valore dell'attestazione nella proprietà di estensione nella directory per l'utente corrente:

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
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. Nel file **TrustFrameworkBase.xml** aggiungere l'attestazione `loyaltyId` nell'elemento **AAD-UserReadUsingObjectId in TechnicalProfile** per leggere il valore dell'attributo di estensione ogni volta che l'utente esegue l'accesso. Fino ad ora gli elementi **TechnicalProfile** sono stati modificati solo nel flusso degli account locali. Per inserire il nuovo attributo nel flusso di un account di social networking/federato è necessario modificare un set diverso di elementi **TechnicalProfile**. Vedere la sezione **Passaggi successivi**.

    ```xml
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
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. Aprire il pannello Azure AD B2C e passare a **Framework dell'esperienza di gestione delle identità** > **Criteri personalizzati**.
1. Selezionare i criteri personalizzati caricati. Selezionare **Esegui adesso**.
1. Iscriversi usando un indirizzo di posta elettronica.

Il token ID inviato all'applicazione include la nuova proprietà di estensione come attestazione personalizzata, preceduta da **extension_loyaltyId**. Vedere l'esempio seguente:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

1. Aggiungere la nuova attestazione ai flussi per gli accessi con account di social networking modificando gli elementi **TechnicalProfile** elencati. Gli account di social networking e federati usano questi due elementi **TechnicalProfile** per l'accesso. Scrivono e leggono i dati utente usando **alternativeSecurityId** come localizzatore dell'oggetto utente.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. È possibile usare gli stessi attributi di estensione tra i criteri predefiniti e personalizzati. Quando si aggiungono attributi di estensione, o personalizzati, tramite il portale, gli attributi vengono registrati usando la **b2c-extensions-app** presente in ogni tenant B2C. Per usare questi attributi di estensione nei criteri personalizzati, seguire la seguente procedura:

   a. All'interno del tenant B2C in portal.azure.com, passare a **Azure Active Directory** e selezionare **Registrazioni dell'app**.  
   b. Trovare **b2c-extensions-app** e selezionarlo.  
   c. Nel campo **Informazioni di base** immettere l'**ID applicazione** e l'**ID oggetto**.  
   d. Includerli nei metadati dell'elemento **AAD-Common** in TechnicalProfile:  

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

3. Per mantenere la coerenza con l'esperienza del portale, Creare questi attributi tramite l'interfaccia utente del portale prima di usarli nei criteri personalizzati. Quando si crea un attributo **ActivationStatus** nel portale, è necessario farvi riferimento, come indicato di seguito:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Riferimenti

Per una descrizione completa delle proprietà di estensione, vedere l'articolo [Estensioni dello schema della directory | Concetti relativi all'API Graph](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * Un **TechnicalProfile** è un tipo di elemento, o una funzione, che definisce il nome di un endpoint, metadati e protocollo. L'elemento **TechnicalProfile** definisce i dettagli dello scambio di attestazioni che esegue il Framework dell'esperienza di gestione delle identità. Quando questa funzione viene chiamata in un passaggio di orchestrazione o da un altro elemento **TechnicalProfile**, gli elementi **InputClaims** e **OutputClaims** vengono forniti come parametri dal chiamante.  
> * Gli attributi di estensione nell'API Graph vengono denominati usando la convenzione `extension_ApplicationObjectID_attributename`.  
> * I criteri personalizzati fanno riferimento agli attributi di estensione, ad esempio **extension_attributename**. Con questo riferimento viene omesso **ApplicationObjectId** in XML.
