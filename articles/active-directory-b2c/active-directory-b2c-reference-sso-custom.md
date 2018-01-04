---
title: Gestione della sessione SSO tramite criteri personalizzati - Azure AD B2C | Microsoft Docs
description: Informazioni sulla gestione delle sessioni SSO tramite criteri personalizzati in Azure AD B2C.
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 809f6000-2e52-43e4-995d-089d85747e1f
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: parja
ms.openlocfilehash: 676b277ae3fbf4554838eee70c5d3e2d8e12c33d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-b2c-single-sign-on-sso-session-management"></a>Azure AD B2C: gestione della sessione Single Sign-On (SSO)

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C consente a un amministratore di controllare come Azure AD B2C interagisce con l'utente dopo l'autenticazione. A tale scopo, viene usata la gestione della sessione SSO. L'amministratore può ad esempio controllare se verrà visualizzata la selezione dei provider di identità o se sarà necessario immettere nuovamente i dettagli dell'account locale. In questo articolo viene descritto come configurare le impostazioni di SSO per Azure AD B2C.

## <a name="overview"></a>Panoramica

La gestione della sessione SSO è costituita da due parti. La prima riguarda le interazioni dell'utente direttamente con Azure AD B2C, mentre l'altra riguarda le interazioni dell'utente con parti esterne, ad esempio Facebook. Azure AD B2C non esegue l'override né ignora le sessioni SSO che possono essere tenute da parti esterne. Il percorso attraverso Azure AD B2C per arrivare alla parte esterna viene invece "memorizzato", evitando così di chiedere di nuovo all'utente di selezionare i provider di identità basati su social network o aziendali. La decisione SSO finale spetta alla parte esterna.

## <a name="how-does-it-work"></a>Come funziona?

La gestione della sessione SSO usa la stessa semantica di qualsiasi altro profilo tecnico nei criteri personalizzati. Quando si esegue un passaggio di orchestrazione, nel profilo tecnico associato al passaggio viene eseguita la query di un riferimento a `UseTechnicalProfileForSessionManagement`. Se ne esiste uno, il provider della sessione SSO a cui si fa riferimento viene controllato per verificare se l'utente sia un partecipante alla sessione. In caso affermativo, il provider della sessione SSO viene usato per ripopolare la sessione. In modo analogo, al termine dell'esecuzione di un passaggio di orchestrazione, il provider viene usato per archiviare le informazioni nella sessione, se è stato specificato un provider di sessione SSO.

In Azure AD B2C sono stati definiti alcuni provider di sessione SSO che è possibile usare:

* NoopSSOSessionProvider
* DefaultSSOSessionProvider
* ExternalLoginSSOSessionProvider
* SamlSSOSessionProvider

Le classi di gestione SSO vengono specificate usando l'elemento `<UseTechnicalProfileForSessionManagement ReferenceId=“{ID}" />` di un profilo tecnico.

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Come indica il nome, questo provider non esegue alcuna operazione. Può essere usato per eliminare il comportamento SSO per un profilo tecnico specifico.

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

È possibile usare questo provider per archiviare le attestazioni in una sessione. Un riferimento a questo provider è in genere incluso in un profilo tecnico usato per gestire account locali. 

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

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Questo provider viene usato per eliminare la schermata di selezione del provider di identità. Un riferimento a questo provider è in genere incluso in un profilo tecnico configurato per un provider di identità esterno, ad esempio Facebook. 

```XML
<TechnicalProfile Id="SM-SocialLogin">
    <DisplayName>Session Mananagement Provider</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

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

Quando si usa il provider per archiviare la sessione di un provider di identità SAML, gli elementi sopra indicati devono avere entrambi il valore false. Quando si usa il provider per archiviare la sessione SAML B2C, gli elementi sopra indicati devono avere il valore true oppure devono essere omessi poiché i valori predefiniti sono true.

>[!NOTE]
> Per completare la disconnessione dalla sessione SAML sono necessari `SessionIndex` e `NameID`.

## <a name="next-steps"></a>Passaggi successivi

Sono graditi commenti e suggerimenti. In caso di difficoltà relative a questo argomento, inserire un post in Stack Overflow usando il tag ["azure-ad-b2c"](https://stackoverflow.com/questions/tagged/azure-ad-b2c). Per le richieste di funzionalità, votare nel [Forum dei commenti](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

