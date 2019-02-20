---
title: Gestire la personalizzazione di token e SSO con criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come gestire la personalizzazione di token e SSO con criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c0f5be7fd77ae195b66f8a8fb052ab8573d48171
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856360"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gestire la personalizzazione di token e SSO con criteri personalizzati in Azure Active Directory B2C

Questo articolo contiene informazioni su come è possibile gestire le configurazioni dei token, delle sessioni e dell'accesso Single Sign-On (SSO) usando [criteri personalizzati](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Configurazione delle attestazioni e delle durate dei token

Per modificare le impostazioni per le durate dei token, si aggiunge un elemento [ClaimsProviders](claimsproviders.md) nel file di relying party dei criteri su cui si vuole intervenire.  **ClaimsProviders** è un elemento figlio dell'elemento [TrustFrameworkPolicy](trustframeworkpolicy.md). Sarà necessario inserirvi le informazioni che influiscono sulle durate dei token. L'XML è simile all'esempio seguente:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

Nell'esempio precedente vengono impostati i valori seguenti.

- **Durate dei token di accesso**: il valore di durata dei token viene impostato con l'elemento dei metadati **token_lifetime_secs**. Il valore predefinito è 3600 secondi (60 minuti).
- **Durata del token ID**: il valore di durata del token ID viene impostato con l'elemento dei metadati **id_token_lifetime_secs**. Il valore predefinito è 3600 secondi (60 minuti).
- **Durata del token di aggiornamento**: il valore di durata del token di aggiornamento viene impostato con l'elemento dei metadati **refresh_token_lifetime_secs**. Il valore predefinito è 1209600 secondi (14 giorni).
- **Durata della finestra temporale scorrevole del token di aggiornamento**: per impostare una durata della finestra temporale scorrevole per il token di aggiornamento, impostare il valore dell'elemento dei metadati **rolling_refresh_token_lifetime_secs**. Il valore predefinito è 7776000 giorni (90 giorni). Se non si vuole applicare una durata della finestra temporale scorrevole, sostituire l'elemento con `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Attestazione autorità di certificazione (iss)**: l'attestazione dell'autorità di certificazione (iss) viene impostata con l'elemento dei metadati **IssuanceClaimPattern**. I valori applicabili sono `AuthorityAndTenantGuid` e `AuthorityWithTfp`.
- **Impostazione dell'attestazione che rappresenta l'ID criteri**: per impostare questo valore sono disponibili le opzioni `TFP` (criteri del framework attendibilità) e `ACR` (riferimento al contesto di autenticazione). Il valore consigliato è `TFP`. Impostare **AuthenticationContextReferenceClaimPattern** con il valore `None`. 

    Nell'elemento **ClaimsSchema**, aggiungere l'elemento seguente: 
    
    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```
    
    Nell'elemento **OutputClaims** aggiungere questo elemento:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Per ACR, rimuovere l'elemento **AuthenticationContextReferenceClaimPattern**.

- **Attestazione soggetto (sub)**: l'impostazione predefinita di questa opzione è ObjectID. Per modificare l'impostazione in `Not Supported`, sostituire questa riga: 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    con la riga seguente:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Comportamento della sessione e SSO

Per modificare le configurazioni del comportamento della sessione e dell'accesso SSO, è necessario aggiungere un elemento **UserJourneyBehaviors** all'interno dell'elemento [RelyingParty](relyingparty.md).  L'elemento **UserJourneyBehaviors** deve essere immediatamente successivo a **DefaultUserJourney**. Il contenuto dell'elemento **UserJourneyBehaviors** avrà un aspetto simile a questo esempio:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Nell'esempio precedente vengono configurati i valori seguenti.

- **Single Sign-On (SSO)**: l'accesso Single Sign-On viene configurato con **SingleSignOn**. I valori applicabili sono `Tenant`, `Application` `Policy` e `Suppressed`. 
- **Durata della sessione dell'app Web (minuti)**: la durata della sessione dell'app Web viene impostata con l'elemento **SessionExpiryInSeconds**. Il valore predefinito è 86400 secondi (1440 minuti).
- **Timeout della sessione dell'app Web**: la durata della sessione dell'app Web viene impostata con l'elemento **SessionExpiryType**. I valori applicabili sono `Absolute` e `Rolling`.
