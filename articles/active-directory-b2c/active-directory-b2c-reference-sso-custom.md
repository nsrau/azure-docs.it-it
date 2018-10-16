---
title: Gestione delle sessioni Single Sign-On tramite criteri personalizzati in Azure Active Directory B2C | Microsoft Docs
description: Informazioni sulla gestione delle sessioni SSO tramite criteri personalizzati in Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bd41ce5ba0cc738c1fd0d61d080e63753706f975
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377413"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Gestione delle sessioni Single Sign-On in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

La gestione delle sessioni Single sign-on (SSO) in Azure Active Directory (Azure AD) B2C consente agli amministratori di controllare le interazioni con un utente dopo che questi si è autenticato. L'amministratore può ad esempio controllare se verrà visualizzata la selezione dei provider di identità o se sarà necessario immettere nuovamente i dettagli dell'account locale. In questo articolo viene descritto come configurare le impostazioni di SSO per Azure AD B2C.

La gestione della sessione SSO è costituita da due parti. La prima riguarda le interazioni dell'utente direttamente con Azure AD B2C, mentre l'altra riguarda le interazioni dell'utente con parti esterne, ad esempio Facebook. Azure AD B2C non esegue l'override né ignora le sessioni SSO che possono essere tenute da parti esterne. Il percorso attraverso Azure AD B2C per arrivare alla parte esterna viene invece "memorizzato", evitando così di chiedere di nuovo all'utente di selezionare i provider di identità basati su social network o aziendali. La decisione SSO finale spetta alla parte esterna.

La gestione della sessione SSO usa la stessa semantica di qualsiasi altro profilo tecnico nei criteri personalizzati. Quando si esegue un passaggio di orchestrazione, nel profilo tecnico associato al passaggio viene eseguita la query di un riferimento a `UseTechnicalProfileForSessionManagement`. Se ne esiste uno, il provider della sessione SSO a cui si fa riferimento viene controllato per verificare se l'utente sia un partecipante alla sessione. In caso affermativo, il provider della sessione SSO viene usato per ripopolare la sessione. In modo analogo, al termine dell'esecuzione di un passaggio di orchestrazione, il provider viene usato per archiviare le informazioni nella sessione, se è stato specificato un provider di sessione SSO.

In Azure AD B2C sono stati definiti alcuni provider di sessione SSO che è possibile usare:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Le classi di gestione SSO vengono specificate usando l'elemento `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` di un profilo tecnico.

## <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Come indica il nome, questo provider non esegue alcuna operazione. Può essere usato per eliminare il comportamento SSO per un profilo tecnico specifico.

## <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

È possibile usare questo provider per archiviare le attestazioni in una sessione. Un riferimento a questo provider è in genere incluso in un profilo tecnico usato per gestire account locali. Quando si usa DefaultSSOSessionProvider per archiviare attestazioni in una sessione, è necessario assicurarsi che qualsiasi attestazione che deve essere restituita all'applicazione o usata da pre-condizioni in passaggi successivi sia archiviata nella sessione o aumentata da una lettura dal profilo utente nella directory. Ciò garantisce che l'autenticazione non abbia esito negativo per mancanza di attestazioni.

```XML
<TechnicalProfile Id="SM-AAD">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="newUser" />
        <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
    </PersistedClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true" />
    </OutputClaims>
</TechnicalProfile>
```

Per aggiungere attestazioni alla sessione, usare l'elemento `<PersistedClaims>` del profilo tecnico. Quando il provider viene usato per ripopolare la sessione, le attestazioni persistenti vengono aggiunte all'elenco delle attestazioni. `<OutputClaims>` viene usato per recuperare le attestazioni dalla sessione.

## <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Questo provider viene usato per eliminare la schermata di selezione del provider di identità. Un riferimento a questo provider è in genere incluso in un profilo tecnico configurato per un provider di identità esterno, ad esempio Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Questo provider viene usato per gestire le sessioni SAML di Azure AD B2C tra le app, nonché i provider di identità SAML esterni.

```XML
<TechnicalProfile Id="SM-Reflector-SAML">
    <DisplayName>Session Management Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
    </Metadata>
</TechnicalProfile>
```

Il profilo tecnico presenta due elementi di metadati:

| Elemento | Default Value | Valori possibili | DESCRIZIONE
| --- | --- | --- | --- |
| IncludeSessionIndex | true | true/false | Indica al provider che l'indice della sessione deve essere archiviato. |
| RegisterServiceProviders | true | true/false | Indica che il provider deve registrare tutti i provider di servizi SAML a cui sia stata rilasciata un'asserzione. |

Quando si usa il provider per archiviare la sessione di un provider di identità SAML, gli elementi sopra indicati devono avere entrambi il valore false. Quando si usa il provider per archiviare la sessione SAML B2C, gli elementi sopra indicati devono avere il valore true oppure devono essere omessi poiché i valori predefiniti sono true. Per completare la disconnessione dalla sessione SAML sono necessari `SessionIndex` e `NameID`.

