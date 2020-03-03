---
title: Mantieni l'accesso in Azure Active Directory B2C
description: Informazioni su come configurare Mantieni l'accesso (KMSI) in Active Directory B2C di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a27487fa69888b02883c3d9a2151887f41afc45
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189379"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Abilitare "Mantieni l'accesso (KMSI)" in Active Directory B2C di Azure

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

È possibile abilitare la funzionalità Mantieni l'accesso (KMSI) per gli utenti delle applicazioni Web e native che dispongono di account locali nella directory Azure Active Directory B2C (Azure AD B2C). Questa funzionalità concede l'accesso agli utenti che tornano all'applicazione senza chiedere di immettere nuovamente il nome utente e la password. Quando l'utente si scollega, l'accesso viene revocato.

Gli utenti dovrebbero evitare di abilitare questa funzione su un computer pubblico.

![Pagina di accesso di esempio che mostra una casella di controllo Mantieni l'accesso](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>Prerequisiti

- Un tenant di Azure AD B2C configurato per consentire l'accesso all'account locale. KMSI non è supportato per gli account del provider di identità esterno.
- Completare la procedura descritta in [Introduzione ai criteri personalizzati](custom-policy-get-started.md).

## <a name="configure-the-page-identifier"></a>Configurare l'identificatore di pagina

Per abilitare KMSI, impostare la definizione del contenuto `DataUri` elemento su [identificatore di pagina](contentdefinitions.md#datauri) `unifiedssp` e la [pagina](page-layout.md) *1.1.0* o successiva.

1. Aprire il file di estensione dei criteri, Ad esempio, <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em>  . Questo file di estensione è uno dei file di criteri inclusi nello Starter Pack del criterio personalizzato, che è necessario ottenere nel prerequisito, [Introduzione ai criteri personalizzati](custom-policy-get-started.md).
1. Cercare l'elemento **BuildingBlocks**. Se l'elemento non esiste, aggiungerlo.
1. Aggiungere l'elemento **ContentDefinitions** all'elemento **BuildingBlocks** del criterio.

    Il criterio personalizzato dovrebbe essere simile al frammento di codice seguente:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Salvare il file delle estensioni.



## <a name="configure-a-relying-party-file"></a>Configurare un file di relying party

Aggiornare il file della relying party (RP) che avvierà il percorso utente appena creato.

1. Aprire il file dei criteri personalizzati. Ad esempio, *SignUpOrSignin.xml*.
1. Se non esiste già, aggiungere un `<UserJourneyBehaviors>` nodo figlio al nodo `<RelyingParty>`. Deve trovarsi immediatamente dopo `<DefaultUserJourney ReferenceId="User journey Id" />`, ad esempio: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`.
1. Aggiungere il nodo seguente come figlio dell'elemento `<UserJourneyBehaviors>`.

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** : indica il modo in cui la sessione viene estesa in base al tempo specificato in `SessionExpiryInSeconds` e `KeepAliveInDays`. Il valore `Rolling` (impostazione predefinita) indica che la sessione viene estesa ogni volta che l'utente esegue l'autenticazione. Il valore `Absolute` indica che l'utente è obbligato a eseguire nuovamente l'autenticazione dopo il periodo di tempo specificato.

    - **SessionExpiryInSeconds** : la durata dei cookie di sessione quando l'opzione *Mantieni l'accesso* non è abilitata o se un utente non seleziona *Mantieni l'accesso*. La sessione scade dopo che `SessionExpiryInSeconds` è stata superata oppure il browser è chiuso.

    - **KeepAliveInDays** : la durata dei cookie di sessione quando l'opzione *Mantieni l'accesso* è abilitata e l'utente seleziona *Mantieni l'accesso*.  Il valore di `KeepAliveInDays` ha la precedenza sul valore `SessionExpiryInSeconds` e determina l'ora di scadenza della sessione. Se un utente chiude il browser e lo riapre in un secondo momento, potrà comunque accedere automaticamente, purché sia entro il periodo di KeepAliveInDays.

    Per altre informazioni, vedere [comportamenti dei percorsi utente](relyingparty.md#userjourneybehaviors).

È consigliabile impostare il valore di SessionExpiryInSeconds su un periodo breve (1200 secondi), mentre il valore di KeepAliveInDays può essere impostato su un periodo di tempo relativamente lungo (30 giorni), come illustrato nell'esempio seguente:

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

4. Salvare le modifiche e caricare il file.
5. Per testare i criteri personalizzati caricati, nel portale di Azure passare alla pagina dei criteri e quindi selezionare **Esegui ora**.

È possibile trovare i criteri di esempio [qui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
