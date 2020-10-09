---
title: Aggiungere attributi personalizzati ai criteri personalizzati
titleSuffix: Azure AD B2C
description: Procedura dettagliata che illustra come usare proprietà di estensione e attributi personalizzati e includerli nell'interfaccia utente.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dbeb2540084fad2cfab3ce360dd15b60a75e5e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389327"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: abilitare gli attributi personalizzati in un criterio del profilo personalizzato

Nell'articolo [aggiungere attestazioni e personalizzare l'input dell'utente tramite criteri personalizzati](custom-policy-configure-user-input.md) viene illustrato come usare [gli attributi predefiniti del profilo utente](user-profile-attributes.md). In questo articolo viene abilitato un attributo personalizzato nella directory Azure Active Directory B2C (Azure AD B2C). Successivamente, è possibile usare il nuovo attributo come attestazione personalizzata in [flussi utente](user-flow-overview.md) o [criteri personalizzati](custom-policy-get-started.md) simultaneamente.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Prerequisiti

Eseguire la procedura descritta nell'articolo [Azure Active Directory B2C: Introduzione ai criteri personalizzati](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Usare attributi personalizzati per raccogliere informazioni sui clienti 

La directory Azure AD B2C viene fornita con un [set predefinito di attributi](user-profile-attributes.md). Tuttavia, è spesso necessario creare attributi personalizzati per gestire uno scenario specifico, ad esempio quando:

* Un'applicazione per il cliente deve salvare in modo permanente un attributo **LoyaltyId** .
* Un provider di identità ha un identificatore utente univoco, **uniqueUserGUID**, che deve essere reso permanente.
* Un percorso utente personalizzato deve salvare in modo permanente lo stato dell'utente, **migrationStatus**, per l'uso di altre logiche.

Azure AD B2C consente di estendere il set di attributi archiviati in ogni account utente. Questi attributi possono anche essere scritti e letti usando l'[API Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>App Azure AD B2C Extensions

Gli attributi di estensione possono essere registrati solo in un oggetto applicazione, anche se possono contenere dati per un utente. L'attributo di estensione è associato all'applicazione denominata b2c-extensions-app. Non modificare questa applicazione, perché viene usata da Azure AD B2C per archiviare i dati utente. Questa applicazione è reperibile in Azure AD B2C, registrazioni per l'app.

I termini *proprietà di estensione*, *attributo personalizzato* e *attestazione personalizzata* fanno riferimento allo stesso concetto nel contesto di questo articolo. Il nome varia in base al contesto, ad esempio applicazione, oggetto o criteri.

## <a name="get-the-application-properties"></a>Ottenere le proprietà dell'applicazione

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **registrazioni app**, quindi selezionare **tutte le applicazioni**.
1. Selezionare l'applicazione `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Copiare negli Appunti e salvare gli identificatori seguenti:
    * **ID applicazione**. Esempio: `11111111-1111-1111-1111-111111111111`.
    * **ID oggetto**. Esempio: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Modificare i criteri personalizzati

Per abilitare gli attributi personalizzati nei criteri, fornire l'ID **applicazione** e l' **ID oggetto** applicazione nei metadati del profilo tecnico AAD-Common. Il profilo tecnico *comune di AAD* si trova nel profilo tecnico di base [Azure Active Directory](active-directory-technical-profile.md) e fornisce il supporto per la gestione Azure ad utenti. Altri profili tecnici Azure AD includono i AAD-Common per sfruttare la configurazione. Eseguire l'override del profilo tecnico AAD-Common nel file di estensione.

1. Aprire il file di estensioni dei criteri, ad esempio <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Trovare l'elemento ClaimsProviders. Aggiungere un nuovo ClaimsProvider all'elemento ClaimsProviders.
1. Sostituire `ApplicationObjectId` con l'ID oggetto registrato in precedenza. Quindi sostituire `ClientId` con l'ID applicazione registrato in precedenza nel frammento di codice seguente.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Caricare i criteri personalizzati

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Fare clic su **Framework dell'esperienza di gestione delle identità**.
5. Selezionare **carica criteri personalizzati**, quindi caricare i file di criteri TrustFrameworkExtensions.xml modificati.

> [!NOTE]
> La prima volta che il Azure AD profilo tecnico rende persistente l'attestazione nella directory, verifica se l'attributo personalizzato esiste. In caso contrario, crea l'attributo personalizzato.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Creazione di un attributo personalizzato tramite portale di Azure

Gli stessi attributi di estensione sono condivisi tra i criteri predefiniti e quelli personalizzati. Quando si aggiungono attributi personalizzati tramite l'esperienza del portale, questi attributi vengono registrati usando **B2C-Extensions-app** presente in ogni tenant B2C.

È possibile creare questi attributi usando l'interfaccia utente del portale prima o dopo averli usati nei criteri personalizzati. Seguire le istruzioni per [definire gli attributi personalizzati in Azure Active Directory B2C](user-flow-custom-attributes.md). Quando si crea un attributo **loyaltyId** nel portale, è necessario farvi riferimento come indicato di seguito:

|Nome     |Campo di utilizzo |
|---------|---------|
|`extension_loyaltyId`  | Criteri personalizzati|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [API Microsoft Graph](manage-user-accounts-graph-api.md)|

Nell'esempio seguente viene illustrato l'utilizzo di attributi personalizzati in un Azure AD B2C definizione di attestazione dei criteri personalizzata.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

Nell'esempio seguente viene illustrato l'utilizzo di un attributo personalizzato in Azure AD B2C criteri personalizzati in un profilo tecnico, input, output e attestazioni rese permanente.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Usare un attributo personalizzato in un criterio

Seguire le istruzioni per [aggiungere attestazioni e personalizzare l'input utente usando criteri personalizzati](custom-policy-configure-user-input.md). Questo esempio usa un'attestazione predefinita "City". Per usare un attributo personalizzato, sostituire ' City ' con gli attributi personalizzati.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:

- [Attributi del profilo utente Azure AD B2C](user-profile-attributes.md)
- [Definizione degli attributi di estensione](user-profile-attributes.md#extension-attributes)
- [Gestire gli account utente Azure AD B2C con Microsoft Graph](manage-user-accounts-graph-api.md)
