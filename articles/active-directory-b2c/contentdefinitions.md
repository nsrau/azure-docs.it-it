---
title: ContentDefinitions - Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento ContentDefinitions di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82785a0f833afb6a9c675fc7022ed19e96c7fc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511323"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

È possibile personalizzare l'aspetto di qualsiasi [profilo tecnico autocertificato](self-asserted-technical-profile.md). Azure Active Directory (Azure AD) B2C esegue il codice nel browser del cliente e usa un approccio moderno denominato condivisione di risorse tra le origini (CORS). 

Per personalizzare l'interfaccia utente, si specifica un URL nell'elemento **ContentDefinition** con contenuto HTML personalizzato. Nel profilo tecnico autocertificato oppure **OrchestrationStep**, si punta a tale identificatore di definizione del contenuto. La definizione del contenuto può contenere un elemento **LocalizedResourcesReferences** che specifica un elenco di risorse localizzate da caricare. Azure AD B2C unisce elementi dell'interfaccia utente con il contenuto HTML caricato dall'URL e quindi mostra la pagina all'utente.

L'elemento **ContentDefinitions** include gli URL per i template HTML5 che possono essere usati in un percorso utente. L'URI della pagina HTML5 viene usato per un passaggio dell'interfaccia utente specificato. Ad esempio, le pagine di accesso o iscrizione, reimpostazione della password o di errore. Nei criteri di estensione è possibile modificare l'aspetto eseguendo l'override del LoadUri per il file HTML5. È possibile creare nuove definizioni del contenuto in base alle esigenze. Questo elemento può contenere un riferimento alle risorse localizzate, all'identificatore della localizzazione specificato nell'elemento [Localizzazione](localization.md).

L'esempio seguente mostra l'identificatore di definizione del contenuto e la definizione di risorse localizzate:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LoalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

I metadati del profilo tecnico autocertificato **LocalAccountSignUpWithLogonEmail** contengono l'identificatore di definizione del contenuto **ContentDefinitionReferenceId** impostato su `api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```


## <a name="contentdefinition"></a>ContentDefinition

L'elemento **ContentDefinition** contiene gli attributi seguenti:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| ID | Yes | Un identificatore per una definizione del contenuto. Il valore è quello specificato nella sezione **ID di definizione del contenuto** più avanti in questa pagina. |

L'elemento **ContentDefinition** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Una stringa che contiene l'URL della pagina HTML5 per la definizione del contenuto. |
| RecoveryUri | 0:1 | Una stringa che contiene l'URL della pagina HTML per la visualizzazione di un errore relativo alla definizione del contenuto. | 
| DataUri | 1:1 | Una stringa che contiene l'URL relativo di un file HTML che fornisce l'esperienza utente da richiamare per il passaggio. |  
| Metadata | 1:1 | Una raccolta di coppie chiave/valore che contiene i metadati usati per la definizione del contenuto. | 
| LocalizedResourcesReferences | 0:1 | Una raccolta di riferimenti di risorse localizzate. Usare questo elemento per personalizzare la localizzazione di un'interfaccia utente e di un attributo delle attestazioni. |

### <a name="datauri"></a>DataUri

L'elemento **DataUri** viene usato per specificare l'identificatore della pagina. Azure AD B2C usa l'identificatore di pagina per caricare e avviare gli elementi dell'interfaccia utente e JavaScript lato client. Il formato del valore è `urn:com:microsoft:aad:b2c:elements:page-name:version`.  I seguenti elenchi tabella degli identificatori di pagina che è possibile usare.

| Value |   Descrizione |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Viene visualizzata una pagina di errore quando viene rilevata un'eccezione o un errore. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Elenca i provider di identità tra cui gli utenti possono scegliere durante la procedura di accesso. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Viene visualizzato un modulo per eseguire l'accesso con un account locale basato su un indirizzo di posta elettronica o un nome utente. Questo valore fornisce anche la funzionalità "mantieni l'accesso" e "Password dimenticata?" collegamento. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Viene visualizzato un modulo per eseguire l'accesso con un account locale basato su un indirizzo di posta elettronica o un nome utente. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Vengono verificati numeri di telefono usando SMS o chiamata vocale durante la procedura di iscrizione o di accesso. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Visualizza un modulo che consente agli utenti di creare o aggiornare il profilo. | 


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

L'elemento **LocalizedResources** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Un elenco di riferimenti di risorse localizzate per la definizione del contenuto. | 

L'elemento **LocalizedResources** contiene gli attributi seguenti:

| Attributo | Obbligatorio | Descrizione |
| --------- | -------- | ----------- |
| Linguaggio | Yes | Una stringa che contiene una lingua supportata per i criteri per RFC 5646 - Tag per identificare le lingue. |
| LocalizedResourcesReferenceId | Yes | L'identificatore dell'elemento **LocalizedResources**. |

Nell'esempio seguente viene mostrata una definizione del contenuto di iscrizione o accesso:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

Nell'esempio seguente viene illustrata una definizione del contenuto di iscrizione o accesso con un riferimento alla localizzazione per inglese, francese e spagnolo:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Per informazioni su come aggiungere il supporto di localizzazione per le definizioni del contenuto, vedere [Localizzazione](localization.md).

## <a name="content-definition-ids"></a>ID definizione del contenuto

L'attributo ID dell'elemento **ContentDefinition** specifica il tipo di pagina correlato alla definizione del contenuto. L'elemento definisce il contesto a cui un modello personalizzato HTML5/CSS viene applicato. La tabella seguente descrive il set degli ID definizione del contenuto riconosciuti dal framework di gestione dell'identità e i tipi di pagina correlati. È possibile creare le definizioni del contenuto con un ID arbitrario.

| ID | Modello predefinito | Descrizione | 
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Pagina di errore**: Viene visualizzata una pagina di errore quando viene rilevata un'eccezione o un errore. |
| **api.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Pagina di selezione del provider di identità** - Elenca i provider di identità tra cui gli utenti possono scegliere durante l'iscrizione. Si tratta in genere di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Pagina di selezione del provider di identità**: Elenca i provider di identità tra cui gli utenti possono scegliere durante l'iscrizione. Si tratta in genere di provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook e Google+, o account locali. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di password dimenticata**: mostra un modulo che gli utenti devono completare per avviare la reimpostazione della password. |
| **api.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di accesso all'account locale**: mostra un modulo per l'accesso con un account locale basato su un indirizzo e-mail o un nome utente. Il modulo può contenere una casella di input di testo e una casella di immissione della password. |
| **api.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina d'iscrizione all'account locale**:mostra un modulo per l'iscrizione con un account locale basato su un indirizzo e-mail o un nome utente. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo e una per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina di autenticazione a più fattori**: vengono verificati numeri di telefono usando SMS o vocali, durante l'iscrizione o l'accesso. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di iscrizione a un account social**: mostra un modulo che gli utenti devono completare quando effettuano l'iscrizione usando un account esistente da un provider di identità social. Questa pagina è simile alla pagina di iscrizione dell'account di social network descritta in precedenza, ad eccezione dei campi di immissione della password. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina di aggiornamento profilo**: mostra un modulo a cui gli utenti possono accedere per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social network, ad eccezione dei campi di immissione della password. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Pagina unificata per l'iscrizione o accesso**: viene gestito il processo di iscrizione e accesso dell'utente. Gli utenti possono usare provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook o Google +, o account locali. |
 
