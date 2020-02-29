---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Specificare l'elemento ContentDefinitions di un criterio personalizzato di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b55199ec2684ab7b95ce4e4988b19814c27b2cc3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189583"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

È possibile personalizzare l'aspetto di qualsiasi [profilo tecnico autocertificato](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) esegue il codice nel browser del cliente e usa un approccio moderno denominato condivisione risorse tra le origini (CORS).

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
  <LocalizedResourcesReferences MergeBehavior="Prepend">
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

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| ID | Sì | Un identificatore per una definizione del contenuto. Il valore è quello specificato nella sezione **ID di definizione del contenuto** più avanti in questa pagina. |

L'elemento **ContentDefinition** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Una stringa che contiene l'URL della pagina HTML5 per la definizione del contenuto. |
| RecoveryUri | 1:1 | Una stringa che contiene l'URL della pagina HTML per la visualizzazione di un errore relativo alla definizione del contenuto. |
| DataUri | 1:1 | Una stringa che contiene l'URL relativo di un file HTML che fornisce l'esperienza utente da richiamare per il passaggio. |
| Metadati | 0:1 | Una raccolta di coppie chiave/valore che contiene i metadati usati per la definizione del contenuto. |
| LocalizedResourcesReferences | 0:1 | Una raccolta di riferimenti di risorse localizzate. Usare questo elemento per personalizzare la localizzazione di un'interfaccia utente e di un attributo delle attestazioni. |

### <a name="datauri"></a>DataUri

L'elemento **DataUri** viene usato per specificare l'identificatore della pagina. Azure AD B2C usa l'identificatore di pagina per caricare e avviare gli elementi dell'interfaccia utente e JavaScript lato client. Il formato del valore è `urn:com:microsoft:aad:b2c:elements:page-name:version`. Nella tabella seguente sono elencati gli identificatori di pagina che è possibile utilizzare.

| Identificatore di pagina | Descrizione |
| ----- | ----------- |
| `globalexception` | Viene visualizzata una pagina di errore quando viene rilevata un'eccezione o un errore. |
| `providerselection`, `idpselection` | Elenca i provider di identità tra cui gli utenti possono scegliere durante la procedura di accesso.  |
| `unifiedssp` | Viene visualizzato un modulo per eseguire l'accesso con un account locale basato su un indirizzo di posta elettronica o un nome utente. Questo valore fornisce anche la funzionalità "mantieni l'accesso" e "Password dimenticata?" collegamento. |
| `unifiedssd` | Viene visualizzato un modulo per eseguire l'accesso con un account locale basato su un indirizzo di posta elettronica o un nome utente. |
| `multifactor` | Vengono verificati numeri di telefono usando SMS o chiamata vocale durante la procedura di iscrizione o di accesso. |
| `selfasserted` | Visualizza un modulo per la raccolta di dati da un utente. Ad esempio, consente agli utenti di creare o aggiornare il proprio profilo. |

### <a name="select-a-page-layout"></a>Selezionare un layout di pagina

È possibile abilitare il [codice lato client JavaScript](javascript-samples.md) inserendo `contract` tra `elements` e il tipo di pagina. Ad esempio: `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

La [versione](page-layout.md) della `DataUri` specifica il pacchetto di contenuto contenente HTML, CSS e JavaScript per gli elementi dell'interfaccia utente nei criteri. Se si intende abilitare il codice JavaScript lato client, gli elementi su cui si basa il codice JavaScript non devono essere modificabili. Se non sono immutabili, le eventuali modifiche potrebbero provocare comportamenti imprevisti nelle pagine utente. Per evitare questi problemi, imporre l'utilizzo di un layout di pagina e specificare una versione del layout di pagina. In questo modo si garantisce che tutte le definizioni di contenuto su cui si basa JavaScript siano non modificabili. Anche se non si intende abilitare JavaScript, è comunque necessario specificare la versione del layout di pagina per le pagine.

Nell'esempio seguente viene illustrato il **DataUri** di `1.2.0``selfasserted` versione:

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migrazione al layout di pagina

Il formato del valore deve contenere la parola `contract`: _urn: com: Microsoft: AAD: B2C: Elements:**Contract**:p Age-Name: Version_. Per specificare un layout di pagina nei criteri personalizzati che usano un valore **DataUri** precedente, usare la tabella seguente per eseguire la migrazione al nuovo formato.

| Valore di DataUri precedente | Nuovo valore di DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |


### <a name="metadata"></a>Metadati

L'elemento **Metadata** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Elemento | 0:n | Metadati che fanno riferimento alla definizione del contenuto. |

L'elemento **Elemento** di **Metadati** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Chiave | Sì | La chiave dei metadati.  |

#### <a name="metadata-keys"></a>Chiavi di metadati

La definizione del contenuto supporta gli elementi di metadati seguenti:

| Chiave | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| DisplayName | No | Stringa che contiene il nome della definizione del contenuto. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

L'elemento **LocalizedResources** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | Un elenco di riferimenti di risorse localizzate per la definizione del contenuto. |

L'elemento **LocalizedResourcesReference** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Linguaggio | Sì | Una stringa che contiene una lingua supportata per i criteri per RFC 5646 - Tag per identificare le lingue. |
| LocalizedResourcesReferenceId | Sì | L'identificatore dell'elemento **LocalizedResources**. |

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
| **api.localaccountpasswordreset** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di password dimenticata**: mostra un modulo che gli utenti devono completare per avviare la reimpostazione della password. |
| **api.localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di accesso all'account locale**: mostra un modulo per l'accesso con un account locale basato su un indirizzo e-mail o un nome utente. Il modulo può contenere una casella di input di testo e una casella di immissione della password. |
| **api.localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina d'iscrizione all'account locale**:mostra un modulo per l'iscrizione con un account locale basato su un indirizzo e-mail o un nome utente. Il modulo può contenere diversi controlli di input, ad esempio una casella per l'immissione di testo e una per l'immissione della password, un pulsante di opzione, caselle a discesa a selezione singola e caselle di controllo con selezione multipla. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Pagina di autenticazione a più fattori**: vengono verificati numeri di telefono usando SMS o vocali, durante l'iscrizione o l'accesso. |
| **api.selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Pagina di iscrizione a un account social**: mostra un modulo che gli utenti devono completare quando effettuano l'iscrizione usando un account esistente da un provider di identità social. Questa pagina è simile alla pagina di iscrizione dell'account di social network descritta in precedenza, ad eccezione dei campi di immissione della password. |
| **api.selfasserted.profileupdate** | [updateprofile in. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Pagina di aggiornamento profilo**: mostra un modulo a cui gli utenti possono accedere per aggiornare il profilo. Questa pagina è simile alla pagina di iscrizione dell'account di social network, ad eccezione dei campi di immissione della password. |
| **api.signuporsignin** | [Unified. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Pagina unificata per l'iscrizione o accesso**: viene gestito il processo di iscrizione e accesso dell'utente. Gli utenti possono usare provider di identità aziendali, provider di identità basati su social network, ad esempio Facebook o Google +, o account locali. |

## <a name="next-steps"></a>Passaggi successivi

Per un esempio di personalizzazione dell'interfaccia utente usando le definizioni di contenuto, vedere:

[Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato](custom-policy-ui-customization.md)
