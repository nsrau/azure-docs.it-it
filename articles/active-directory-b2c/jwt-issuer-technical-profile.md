---
title: Definire un profilo tecnico per un'autorità di certificazione del token JWT nei criteri personalizzati di Azure Active Directory B2C | Microsoft Docs
description: Definire un profilo tecnico per un'autorità di certificazione del token JWT nei criteri personalizzati di Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 33dce27b69d080c57b925562ba83db0046b77ca9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683790"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico per un'autorità di certificazione del token JWT nei criteri personalizzati di Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C rilascia diversi tipi di token di sicurezza durante l'elaborazione di ogni flusso di autenticazione. Un profilo tecnico per un'autorità di certificazione del token JWT emette un token JWT che viene restituito all'applicazione basata su attestazioni. In genere questo profilo tecnico è l'ultimo passaggio di orchestrazione nel percorso utente.

## <a name="protocol"></a>Protocol

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `None`. Impostare l'elemento **OutputTokenFormat** su `JWT`.

Nell'esempio seguente viene illustrato un profilo tecnico per `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>Input, output e attestazioni persistenti

Gli elementi **InputClaims**, **OutputClaims** e **PersistClaims** sono vuoti o assenti. Anche gli elementi **InutputClaimsTransformations** e **OutputClaimsTransformations** sono assenti.

## <a name="metadata"></a>Metadata

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Sì | L'attestazione da usare come attestazione dell'identità dell'utente all'interno dei codici di autorizzazione OAuth2 e dei token di aggiornamento. Per impostazione predefinita, è necessario impostare su `objectId`, a meno che non si specifichi un tipo di attestazione SubjectNamingInfo diverso. | 
| SendTokenResponseBodyWithJsonNumbers | No  | Sempre impostato su `true`. Per il formato legacy in cui i valori numerici vengono forniti sotto forma di stringhe anziché di numeri JSON, impostare su `false`. Questo attributo è necessario per i client che hanno acquisito una dipendenza su un'implementazione precedente che ha restituito tali proprietà sotto forma di stringhe. | 
| token_lifetime_secs | No  | Durata del token di accesso. La durata del token di connessione OAuth 2.0 usato per ottenere l'accesso a una risorsa protetta. Il valore predefinito è 3.600 secondi (1 ora). Il valore minimo (inclusivo) è 300 secondi (5 minuti). Il massimo valore (inclusivo) è 86.400 secondi (24 ore). | 
| id_token_lifetime_secs | No  | Durate del token ID. Il valore predefinito è 3.600 secondi (1 ora). Il valore minimo (inclusivo) è 300 secondi (5 minuti). Il massimo valore (inclusivo) è 86.400 secondi (24 ore). | 
| refresh_token_lifetime_secs | No  | Durata del token di aggiornamento. Il periodo di tempo massimo prima del quale un token di aggiornamento può essere usato per acquisire un nuovo token di accesso, nel caso in cui all'applicazione fosse stato concesso l'ambito offline_access. Il valore predefinito è 120.9600 secondi (14 giorni). Il massimo valore (inclusivo) è 86.400 secondi (24 ore). Il massimo valore (inclusivo) è 7.776.000 secondi (90 giorni). | 
| rolling_refresh_token_lifetime_secs | No  | Durata della finestra temporale scorrevole del token di aggiornamento. Allo scadere di questo periodo di tempo, l'utente deve ripetere l'autenticazione, indipendentemente dal periodo di validità del token di aggiornamento più recente acquisito dall'applicazione. Se non si desidera applicare una durata di una finestra temporale scorrevole, impostare il valore di allow_infinite_rolling_refresh_token su `true`. Il valore predefinito è 7.776.000 secondi (90 giorni). Il massimo valore (inclusivo) è 86.400 secondi (24 ore). Il massimo valore (inclusivo) è 31.536.000 secondi (365 giorni). | 
| allow_infinite_rolling_refresh_token | No  | Se impostato su `true`, la durata della finestra temporale scorrevole del token di aggiornamento non scade mai. |
| IssuanceClaimPattern | Sì | Controlla l'attestazione dell'autorità di certificazione (iss). Uno dei valori:<ul><li>AuthorityAndTenantGuid - l'attestazione iss include il nome di dominio, ad esempio `login.microsoftonline` o `tenant-name.b2clogin.com` e l'identificatore del tenant https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - l'attestazione iss include il nome di dominio, ad esempio `login.microsoftonline` o `tenant-name.b2clogin.com`, l'identificatore del tenant e il nome dei criteri relying party. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | No  | Controllare il valore di attestazione `acr`.<ul><li>Nessuno - Azure AD B2C non rilascia l'attestazione acr (record di controllo di accesso)</li><li>PolicyId: l'attestazione `acr` contiene il nome del criterio</li></ul>Le opzioni per impostare questo valore sono TFP (criteri del framework attendibilità) e ACR (riferimento al contesto di autenticazione). È consigliabile impostare questo valore su TFP, per impostare il valore, assicurarsi che `<Item>` con `Key="AuthenticationContextReferenceClaimPattern"` esista e il valore sia `None`. Nei criteri relying party, aggiungere l'elemento `<OutputClaims>`, aggiungere questo elemento `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Inoltre assicurarsi che i criteri contengano il tipo di attestazione `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` | 

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento CryptographicKeys contiene gli attributi seguenti:

| Attributo | Obbligatoria | DESCRIZIONE |
| --------- | -------- | ----------- |
| issuer_secret | Sì | Il certificato X509 (set di chiavi RSA) da usare per firmare il token JWT. Questa è la chiave `B2C_1A_TokenSigningKeyContainer` configurata in [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md). | 
| issuer_refresh_token_key | Sì | Il certificato X509 (set di chiavi RSA) da usare per crittografare il token di aggiornamento. È stata configurata la chiave `B2C_1A_TokenEncryptionKeyContainer` in [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md) |














