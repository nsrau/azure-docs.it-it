---
title: 'Azure Active Directory B2C: Aggiungere attributi a criteri personalizzati e usarli nella modifica del profilo | Microsoft Docs'
description: "Procedura dettagliata che illustra come usare proprietà di estensione e attributi personalizzati e includerli nell&quot;interfaccia utente"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/29/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 83748140c7b92b95a648ae3ecf78f22e2393780b
ms.contentlocale: it-it
ms.lasthandoff: 05/08/2017

---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Creazione e uso di attributi personalizzati in un criterio personalizzato di modifica del profilo.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In questo articolo si creerà un attributo personalizzato nella directory di Azure AD B2C e si userà il nuovo attributo come attestazione personalizzata nel percorso utente di modifica del profilo.

## <a name="prerequisites"></a>Prerequisiti

Completare la procedura descritta nell'articolo [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Usare attributi personalizzati per raccogliere informazioni sui clienti in Azure Active Directory B2C usando criteri personalizzati
La directory di Azure Active Directory (Azure AD) B2C viene fornita con un set predefinito di attributi: nome, cognome, città, codice postale, userPrincipalName e così via.  È spesso necessario creare attributi personalizzati.  ad esempio:
* Un'applicazione per i clienti deve rendere persistente un attributo, ad esempio "LoyaltyNumber".
* Un provider di identità ha un identificatore utente univoco che deve essere salvato, ad esempio "uniqueUserGUID".
* Un percorso utente personalizzato deve rendere persistente lo stato dell'utente, ad esempio "migrationStatus".

Con Azure AD B2C è possibile estendere il set di attributi archiviati in ogni account utente. Questi attributi possono anche essere scritti e letti usando l' [API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md).

[!NOTE]
Gli attributi personalizzati o le proprietà di estensione sono funzionalità della directory di Azure AD B2C.  Le proprietà di estensione estendono lo schema degli oggetti utente nella directory.  Per usare un attributo personalizzato come attestazione personalizzata in un criterio, definirlo nel criterio in `ClaimsSchema`.

[!NOTE]
Le proprietà di estensione possono essere registrate solo su un oggetto applicazione anche se possono contenere dati per un utente. La proprietà è collegata all'applicazione. All'oggetto applicazione deve essere concesso l'accesso in scrittura per registrare una proprietà di estensione. In un oggetto possono essere scritte 100 proprietà di estensione per TUTTI i tipi e TUTTE le applicazioni. Le proprietà di estensione vengono aggiunte al tipo di directory di destinazione e diventano immediatamente accessibili nel tenant della directory di Azure AD B2C.
Se l'applicazione viene eliminata, vengono rimosse anche le proprietà di estensione insieme a tutti i dati contenuti per tutti gli utenti. Se una proprietà di estensione viene eliminata dall'applicazione, viene rimossa nell'oggetto della directory di destinazione e vengono rimossi anche eventuali dati in essa contenuti.

[!NOTE]
Le proprietà di estensione esistono solo nel contesto di un'applicazione registrata nel tenant. L'ID oggetto dell'applicazione deve essere incluso nell'elemento TechnicalProfile che usa l'applicazione

[!NOTE]
La directory di Azure AD B2C include in genere un'app per le API Web denominata `b2c-extensions-app`.  Questa applicazione viene usata principalmente dai criteri B2C predefiniti per le attestazioni personalizzate create tramite il portale di Azure.  L'uso di questa applicazione per registrare estensioni per i criteri personalizzati B2C è consigliata solo agli utenti esperti.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Creazione di una nuova applicazione per archiviare le proprietà di estensione

1. Aprire una sessione di esplorazione, passare al [portale di Azure](https://portal.azure.com) e accedere con le credenziali amministrative della directory B2C che si vuole configurare.
1. Fare clic su `Azure Active Directory` nel riquadro di spostamento sinistro. Potrebbe essere necessario selezionare Altri servizi> per trovarlo.
1. Selezionare `App registrations` e fare clic su `New application registration`
1. Specificare gli elementi seguenti consigliati:
  * Specificare un nome per l'applicazione Web: `WebApp-GraphAPI-DirectoryExtensions`
  * Tipo di applicazione: app Web/API
  * URL di accesso: https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. Selezionare `Create`. Il completamento dell'operazione viene visualizzato in `notifications`
1. Selezionare l'applicazione Web appena creata: `WebApp-GraphAPI-DirectoryExtensions`
1. Selezionare Impostazioni: `Required permissions`
1. Selezionare l'API `Windows Active Directory`
1. Inserire un segno di spunta in Autorizzazioni applicazione: `Read and write directory data` e `Save`
1. Scegliere `Grant permissions` e confermare con `Yes`.
1. Copiare negli Appunti e salvare gli identificatori seguenti da WebApp-GraphAPI-DirectoryExtensions>Impostazioni>Proprietà>
*  `Application ID`. Esempio: `103ee0e6-f92d-4183-b576-8c3739027780`
* `Object ID`. Esempio: `80d8296a-da0a-49ee-b6ab-fd232aa45201`

## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Modifica del criterio personalizzato per l'aggiunta di `ApplicationObjectId`

Per ogni TechnicalProfile che leggerà o scriverà attributi di estensione è necessario aggiungere un elemento `<Metadata>` con le due voci ApplicationObjectId e ClientId ottenute nei passaggi precedenti.

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
[!NOTE]
<TechnicalProfile Id="AAD-Common"> è "common" perché i relativi elementi vengono riutilizzati in tutti i TechnicalProfile di Azure Active Directory usando l'elemento:
```
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
```
[!NOTE]
Quando TechnicalProfile scrive per la prima volta nella proprietà di estensione appena creata, potrebbe verificarsi un errore una sola volta, perché la proprietà verrà creata se non viene trovata.  .*  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Uso della nuova proprietà di estensione o del nuovo attributo personalizzato in un percorso utente


1. Aprire il file Relying Party che descrive il percorso utente di modifica del criterio.  Se si è appena iniziato, può essere consigliabile scaricare la versione del file RP-PolicyEdit già configurata direttamente dalla sezione dei criteri personalizzati di Azure B2C nel portale di Azure.  In alternativa, aprire il file XML dalla cartella di archiviazione.
2. Aggiungere un'attestazione personalizzata `loyaltyId`.  Includendo l'attestazione personalizzata nell'elemento `<RelyingParty>`, l'attestazione verrà passata come parametro agli elementi TechnicalProfile UserJourney e inclusa nel token per l'applicazione.
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
3. Aggiungere una definizione di attestazione al file dei criteri di estensione `TrustFrameworkExtensions.xml` all'interno dell'elemento ``<ClaimsSchema>`` come illustrato di seguito.
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
NOTA: l'aggiunta di una definizione `ClaimType` al file di base e delle estensioni non è generalmente necessaria. Dato che i passaggi successivi aggiungeranno tuttavia extension_loyaltyId agli elementi TechnicalProfile nel file di base, la convalida dei criteri rifiuterà il caricamento del file di base in assenza della definizione.

NOTA: può essere utile tenere traccia dell'esecuzione del percorso utente denominato "ProfileEdit" nel file TrustFrameworkBase.xml.  Cercare il percorso utente con lo stesso nome nell'editor e osservare che il passaggio di orchestrazione 5 richiama TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate".  Cercare ed esaminare questo elemento TechnicalProfile per acquisire familiarità con il flusso.

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
7. Aggiungere l'attestazione nell'elemento TechnicalProfile "AAD-UserReadUsingObjectId" per leggere il valore dell'attributo di estensione ogni volta che l'utente esegue l'accesso.
NOTA: finora gli elementi TechnicalProfile sono stati modificati solo nel flusso degli account locali.  Per inserire il nuovo attributo nel flusso di un account di social networking/federato è necessario modificare un set diverso di elementi TechnicalProfile. Vedere i passaggi successivi.

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

[!IMPORTANT]
L'elemento IncludeTechnicalProfile indicato in precedenza aggiunge tutti gli elementi di AAD-Common a questo TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Testare i criteri personalizzati tramite "Esegui adesso"

     1. Open the **Azure AD B2C Blade** and navigate to **Identity Experience Framework > Custom policies**.
     2. Select the custom policy that you uploaded, and click the **Run now** button.
     3. You should be able to sign up using an email address.

Il token ID inviato all'applicazione includerà la nuova proprietà di estensione come attestazione personalizzata preceduta da extension_loyaltyId. Vedere l'esempio.

```
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

Aggiungere la nuova attestazione ai flussi per gli accessi con account di social networking modificando gli elementi TechnicalProfile elencati di seguito. Questi due elementi TechnicalProfile vengono usati per gli accessi con account di social networking/federati per scrivere e leggere i dati utente usando alternativeSecurityId come localizzatore dell'oggetto utente.
```
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```


## <a name="reference"></a>Riferimento

* Un **profilo tecnico** è un tipo di elemento che può essere considerato una *funzione* che definisce il nome di un endpoint, i relativi metadati, il protocollo e i dettagli dello scambio di attestazioni che deve essere eseguito dal Framework dell'esperienza di gestione delle identità.  Quando questa *funzione* viene chiamata in un passaggio di orchestrazione o da un altro TechnicalProfile, gli elementi InputClaims e OutputClaims vengono forniti come parametri dal chiamante.


* Per una descrizione completa delle proprietà di estensione, vedere l'articolo [ESTENSIONI DELLO SCHEMA DELLA DIRECTORY | CONCETTI RELATIVI ALL'API GRAPH](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)
[!NOTE]
Gli attributi di estensione nell'API Graph vengono denominati usando la convenzione `extension_ApplicationObjectID_attributename`. I criteri personalizzati fanno riferimento agli attributi di estensione come extension_attributename, omettendo quindi ApplicationObjectId nel file XML

