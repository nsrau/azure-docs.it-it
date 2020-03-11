---
title: Definire un profilo tecnico per un'autorità emittente SAML in un criterio personalizzato
titleSuffix: Azure AD B2C
description: Definire un profilo tecnico per un emittente SAML (Security Assertion Markup Language token) in un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967261"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definire un profilo tecnico per un emittente di token SAML in un Azure Active Directory B2C criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) genera diversi tipi di token di sicurezza durante l'elaborazione di ogni flusso di autenticazione. Un profilo tecnico per un'autorità emittente del token SAML emette un token SAML restituito all'applicazione relying party (provider di servizi). In genere questo profilo tecnico è l'ultimo passaggio di orchestrazione nel percorso utente.

## <a name="protocol"></a>Protocollo

L'attributo **Nome** dell'elemento **Protocollo** deve essere impostato su `None`. Impostare l'elemento **OutputTokenFormat** su `SAML2`.

Nell'esempio seguente viene illustrato un profilo tecnico per `Saml2AssertionIssuer`:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Input, output e attestazioni persistenti

Gli elementi **InputClaims**, **OutputClaims** e **PersistClaims** sono vuoti o assenti. Anche gli elementi **InutputClaimsTransformations** e **OutputClaimsTransformations** sono assenti.

## <a name="metadata"></a>Metadati

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| IssuerUri | No | Nome dell'autorità emittente visualizzato nella risposta SAML. Il valore deve corrispondere al nome configurato nell'applicazione relying party. |

## <a name="cryptographic-keys"></a>Chiavi crittografiche

L'elemento CryptographicKeys contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| MetadataSigning | Sì | Certificato X509 (set di chiavi RSA) da usare per firmare i metadati SAML. Azure AD B2C usa questa chiave per firmare i metadati. |
| SamlMessageSigning| Sì| Specificare il certificato X509 (set di chiavi RSA) da usare per firmare i messaggi SAML. Azure AD B2C usa questa chiave per firmare la risposta `<samlp:Response>` inviare al relying party.|

## <a name="session-management"></a>Gestione delle sessioni

Per configurare le sessioni SAML Azure AD B2C tra un'applicazione relying party, l'attributo dell'elemento `UseTechnicalProfileForSessionManagement`, fare riferimento alla sessione SSO [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) .

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo seguente per esempio di uso di un profilo tecnico dell'emittente SAML:

- [Registrare un'applicazione SAML in Azure AD B2C](connect-with-saml-service-providers.md)












