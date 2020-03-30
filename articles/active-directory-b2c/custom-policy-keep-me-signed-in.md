---
title: Mantieni l'accesso in Azure Active Directory B2C
description: Informazioni su come configurare Mantieni l'accesso (KMSI) in Active Directory B2C di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0de94cdce1d7f0e9da9d2844b300956ad6f6970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330847"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Abilitare "Mantieni l'accesso (KMSI)" in Active Directory B2C di Azure

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

È possibile abilitare la funzionalità Mantieni accesso a utente (KMSI) per gli utenti delle applicazioni Web e native che dispongono di account locali nella directory B2C di Azure Active Directory (Azure AD B2C). Questa funzionalità consente l'accesso agli utenti che tornano all'applicazione senza chiedere loro di immettere nuovamente il nome utente e la password. Quando l'utente si scollega, l'accesso viene revocato.

Gli utenti dovrebbero evitare di abilitare questa funzione su un computer pubblico.

![Esempio di pagina di iscrizione con una casella di controllo Mantieni l'accesso](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Prerequisiti

- Un tenant B2C di Azure AD configurato per consentire l'accesso all'account locale. KMSI non è supportato per gli account del provider di identità esterno.
- Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Configurare l'identificatore di pagina

Per abilitare KMSI, `DataUri` impostare l'elemento di definizione del contenuto su [identificatore](contentdefinitions.md#datauri) `unifiedssp` di pagina e [versione pagina](page-layout.md) *1.1.0* o successiva.

1. Aprire il file di estensione dei criteri, Ad esempio, <em> `SocialAndLocalAccounts/` </em>. Questo file di estensione è uno dei file di criteri inclusi nello starter pack dei criteri personalizzati, che si dovrebbe avere ottenuto nel prerequisito, Introduzione ai [criteri personalizzati.](custom-policy-get-started.md)
1. Cercare l'elemento **BuildingBlocks**. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere l'elemento **ContentDefinitions** all'elemento **BuildingBlocks** del criterio.

    I criteri personalizzati dovrebbero essere simili al frammento di codice seguente:Your custom policy should look like the following code snippet:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Aggiungere i metadati al profilo tecnico auto-asserito

Per aggiungere la casella di controllo KMSI alla pagina `setting.enableRememberMe` di iscrizione e accesso, impostare i metadati su false. Eseguire l'override dei profili tecnici SelfAsserted-LocalAccountSignin-Email nel file di estensione.

1. Trovare l'elemento ClaimsProviders. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere il provider di attestazioni seguente all'elemento ClaimsProviders:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Salvare il file delle estensioni.

## <a name="configure-a-relying-party-file"></a>Configurare un file della relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Aprire il file dei criteri personalizzati. Ad esempio, *SignUpOrSignin.xml*.
1. Se non esiste già, aggiungere `<UserJourneyBehaviors>` un nodo `<RelyingParty>` figlio al nodo. Deve trovarsi immediatamente `<DefaultUserJourney ReferenceId="User journey Id" />`dopo, `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`ad esempio: .
1. Aggiungere il nodo seguente come figlio dell'elemento `<UserJourneyBehaviors>`.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - Indica come la sessione viene `SessionExpiryInSeconds` estesa `KeepAliveInDays`in base al tempo specificato in e . Il `Rolling` valore (predefinito) indica che la sessione viene estesa ogni volta che l'utente esegue l'autenticazione. Il `Absolute` valore indica che l'utente è costretto a eseguire nuovamente l'autenticazione dopo il periodo di tempo specificato.

    - **SessionExpiryInSeconds** - La durata dei cookie di sessione quando *l'accesso è* non abilitata o se un utente non seleziona *Mantieni l'accesso*. La sessione scade `SessionExpiryInSeconds` dopo essere trascorsa o il browser è stato chiuso.

    - **KeepAliveInDays** - La durata dei cookie di sessione quando *l'accesso è* abilitata e l'utente seleziona *Mantieni l'accesso*.  Il valore `KeepAliveInDays` di ha `SessionExpiryInSeconds` la precedenza sul valore e determina il tempo di scadenza della sessione. Se un utente chiude il browser e lo riapre in un secondo momento, può comunque accedere automaticamente finché rientra nel periodo di tempo KeepAliveInDays.

    Per ulteriori informazioni, consultate [Comportamenti del percorso utente.](relyingparty.md#userjourneybehaviors)

È consigliabile impostare il valore di SessionExpiryInSeconds su un periodo breve (1200 secondi), mentre il valore di KeepAliveInDays può essere impostato su un periodo relativamente lungo (30 giorni), come illustrato nell'esempio seguente:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>Verificare i criteri

1. Salvare le modifiche e caricare il file.
1. Per testare i criteri personalizzati caricati, nel portale di Azure passare alla pagina dei criteri e quindi selezionare **Esegui ora.**
1. Digitare il **nome utente** e la **password**, selezionare **Mantieni l'accesso**, quindi fare clic su **Accedi**.
1. Tornare al portale di Azure. Passare alla pagina dei criteri e quindi selezionare **Copia** per copiare l'URL di accesso.
1. Nella barra degli indirizzi `&prompt=login` del browser, rimuovere il parametro della stringa di query, che impone all'utente di immettere le proprie credenziali nella richiesta.
1. Nel browser fare clic su **Vai**. Ora Azure AD B2C genererà un token di accesso senza richiedere nuovamente l'accesso. 

## <a name="next-steps"></a>Passaggi successivi

Trova il criterio di esempio [qui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
