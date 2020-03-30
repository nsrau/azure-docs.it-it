---
title: Sviluppare un endpoint SCIM per il provisioning degli utenti nelle app di Azure ADDevelop a SCIM endpoint for user provisioning to apps from Azure AD
description: System for Cross-domain Identity Management (SCIM) standardizza il provisioning automatico degli utenti. Informazioni su come sviluppare un endpoint SCIM, integrare l'API SCIM con Azure Active Directory e iniziare ad automatizzare il provisioning di utenti e gruppi nelle applicazioni cloud.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297669"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Creare un endpoint SCIM e configurare il provisioning degli utenti con Azure Active Directory (Azure AD)Build a SCIM endpoint and configure user provisioning with Azure Active Directory (Azure AD)

Gli sviluppatori di applicazioni possono usare l'API di gestione degli utenti SCIM (System for Cross-Domain Identity Management) per abilitare il provisioning automatico di utenti e gruppi tra l'applicazione e Azure AD. Questo articolo descrive come creare un endpoint SCIM e integrarsi con il servizio di provisioning di Azure AD. La specifica SCIM fornisce uno schema utente comune per il provisioning. Se utilizzato in combinazione con standard federativi come SAML o OpenID Connect, SCIM offre agli amministratori una soluzione end-to-end basata su standard per la gestione degli accessi.

SCIM è una definizione standardizzata di due endpoint: un endpoint /Users e un endpoint /Groups. Usa verbi REST comuni per creare, aggiornare ed eliminare oggetti e uno schema predefinito per attributi comuni come nome del gruppo, nome utente, nome, cognome e posta elettronica. Le app che offrono un'API REST SCIM 2.0 possono ridurre o eliminare il problema di lavorare con un'API proprietaria di gestione degli utenti. Ad esempio, qualsiasi client SCIM conforme sa come creare un HTTP POST di un oggetto JSON per l'endpoint /Users per creare una nuova voce utente. Invece di aver bisogno di un'API leggermente diversa per le stesse azioni di base, le app conformi allo standard SCIM possono sfruttare istantaneamente i client, gli strumenti e il codice preesistenti. 

![Provisioning da Azure AD a un'app con SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Lo schema dell'oggetto utente standard e le API rest per la gestione definite in SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) consentono ai provider di identità e alle app di integrarsi più facilmente tra loro. Gli sviluppatori di applicazioni che creano un endpoint SCIM possono integrarsi con qualsiasi client conforme a SCIM senza dover eseguire operazioni personalizzate.

L'automazione del provisioning in un'applicazione richiede la compilazione e l'integrazione di un endpoint SCIM con il client SCIM di Azure AD. Eseguire la procedura seguente per avviare il provisioning di utenti e gruppi nell'applicazione. 
    
  * **[Passaggio 1: Progettare lo schema di utenti e gruppi.](#step-1-design-your-user-and-group-schema)** Identificare gli oggetti e gli attributi necessari all'applicazione e determinare la modalità di mapping allo schema di utenti e gruppi supportato dall'implementazione SCIM di Azure AD.

  * **[Passaggio 2: Comprendere l'implementazione SCIM di Azure AD.](#step-2-understand-the-azure-ad-scim-implementation)** Comprendere come viene implementato il client SCIM di Azure AD e modellare la gestione e le risposte delle richieste del protocollo SCIM.

  * **[Passaggio 3: Creare un endpoint SCIM.Step 3: Build a SCIM endpoint.](#step-3-build-a-scim-endpoint)** Un endpoint deve essere compatibile con SCIM 2.0 per l'integrazione con il servizio di provisioning di Azure AD. Come opzione, è possibile usare librerie ed esempi di codice di Microsoft Common Language Infrastructure (CLI) per compilare l'endpoint. Questi esempi sono solo di riferimento e di test; Ti consigliamo di non codificare l'app di produzione per dipenderne.

  * **[Passaggio 4: Integrare l'endpoint SCIM con il client SCIM di Azure AD.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Se l'organizzazione usa un'applicazione di terze parti che implementa il profilo di SCIM 2.0 supportato da Azure AD, è possibile iniziare subito a automatizzare il provisioning e il deprovisioning di utenti e gruppi.

  * **[Passaggio 5: Pubblicare l'applicazione nella raccolta di applicazioni di Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Semplifica l'individuazione dell'applicazione da parte dei clienti e la facilità di configurazione del provisioning. 

![Passaggi per l'integrazione di un endpoint SCIM con Azure ADSteps for integrating a SCIM endpoint with Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Passaggio 1: Progettare lo schema di utenti e gruppiStep 1: Design your user and group schema

Ogni applicazione richiede attributi diversi per creare un utente o un gruppo. Avviare l'integrazione identificando gli oggetti (utenti, gruppi) e gli attributi (nome, responsabile, posizione e così via) necessari per l'applicazione. Lo standard SCIM definisce uno schema per la gestione di utenti e gruppi. Lo schema utente principale richiede solo tre attributi: **id** (identificatore definito dal provider di servizi), **externalId** (identificatore definito dal client) e **meta** (metadati di sola lettura gestiti dal provider di servizi). Tutti gli altri attributi sono facoltativi. Oltre allo schema utente principale, lo standard SCIM definisce un'estensione utente enterprise e un modello per estendere lo schema utente in base alle esigenze dell'applicazione. Se, ad esempio, l'applicazione richiede il responsabile di un utente, è possibile utilizzare lo schema dell'utente aziendale per raccogliere il responsabile dell'utente e lo schema principale per raccogliere il messaggio di posta elettronica dell'utente. Per progettare lo schema, attenersi alla seguente procedura:
  1. Elencare gli attributi necessari per l'applicazione. Può essere utile suddividere le esigenze negli attributi necessari per l'autenticazione (ad esempio loginName ed email), negli attributi necessari per gestire il ciclo di vita dell'utente (ad esempio status /active) e altri attributi necessari per il funzionamento della particolare applicazione (ad esempio manager, tag).
  2. Verificare se tali attributi sono già definiti nello schema utente principale o nello schema utente aziendale. Se gli attributi necessari e non sono trattati negli schemi utente principale o aziendale, è necessario definire un'estensione per lo schema utente che copra gli attributi necessari. Nell'esempio seguente, abbiamo aggiunto un'estensione all'utente per consentire il provisioning di un "tag" su un utente. È consigliabile iniziare con solo gli schemi utente principale e aziendale ed espandersi a schemi personalizzati aggiuntivi in un secondo momento.  
  3. Eseguire il mapping degli attributi SCIM agli attributi utente in Azure AD. Se uno degli attributi definiti nell'endpoint SCIM non dispone di una controparte chiara nello schema utente di Azure AD, è probabile che i dati non vengano archiviati nell'oggetto utente nella maggior parte dei tenant. Valutare se questo attributo può essere facoltativo per la creazione di un utente. Se l'attributo è fondamentale per il funzionamento dell'applicazione, guidare l'amministratore tenant a estendere lo schema o usare un attributo di estensione come illustrato di seguito per la proprietà "tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabella 1: Delineare gli attributi necessari 
| Passaggio 1: Determinare gli attributi necessari per l'appStep 1: Determine attributes your app requires| Passaggio 2: mappare i requisiti dell'app allo standard SCIMStep 2: Map app requirements to SCIM standard| Passaggio 3: Eseguire il mapping degli attributi SCIM per gli attributi di Azure ADStep 3: Map SCIM attributes to the Azure AD attributes|
|--|--|--|
|Loginname|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|nome.cognomeNome|lastName|
|workMail|Email[tipo eq "lavoro"].valore|Posta elettronica|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|active|isSoftDeleted (valore calcolato non memorizzato nell'utente)|

Lo schema definito in precedenza verrà rappresentato utilizzando il payload Json riportato di seguito. Si noti che oltre agli attributi necessari per l'applicazione, la rappresentazione JSON include gli attributi "id", "externalId" e "meta" obbligatori.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabella 2: Mapping degli attributi utente predefiniti
È quindi possibile usare la tabella seguente per comprendere in che modo gli attributi necessari per l'applicazione potrebbero eseguire il mapping a un attributo in Azure AD e nella RFC SCIM. È possibile [personalizzare](customize-application-attributes.md) la modalità di mapping degli attributi tra Azure AD e l'endpoint SCIM. Si noti che non è necessario supportare sia gli utenti che i gruppi o tutti gli attributi illustrati di seguito. Sono un riferimento per il modo in cui gli attributi in Azure AD vengono spesso mappati alle proprietà nel protocollo SCIM. 

| Utente Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabella 3: Mapping di attributi di gruppo predefinito

| Gruppo di Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Gruppo |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| Membri di |Membri di |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

Esistono diversi endpoint definiti nella RFC SCIM. È possibile iniziare con l'endpoint /User e quindi espandersi da lì. L'endpoint /Schemas è utile quando si usano attributi personalizzati o se lo schema viene modificato di frequente. Consente a un client di recuperare automaticamente lo schema più aggiornato. L'endpoint /Bulk è particolarmente utile per il supporto dei gruppi. Nella tabella seguente vengono descritti i vari endpoint definiti nello standard SCIM. L'endpoint /Schemas è utile quando si usano attributi personalizzati o se lo schema viene modificato di frequente. Consente a un client di recuperare automaticamente lo schema più aggiornato. L'endpoint /Bulk è particolarmente utile per il supporto dei gruppi. Nella tabella seguente vengono descritti i vari endpoint definiti nello standard SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabella 4: Determinare gli endpoint che si desidera sviluppare
|ENDPOINT|DESCRIZIONE|
|--|--|
|/Utente|Eseguire operazioni CRUD su un oggetto utente.|
|/Group|Eseguire operazioni CRUD su un oggetto gruppo.|
|/ServiceProviderConfig (informazioni in inglese)|Vengono fornite informazioni dettagliate sulle funzionalità dello standard SCIM supportate, ad esempio le risorse supportate e il metodo di autenticazione.|
|/ResourceTipi|Specifica i metadati su ogni risorsa|
|/Schemas (schema)|Il set di attributi supportati da ogni client e provider di servizi può variare. Mentre un provider di servizi potrebbe includere "nome", "titolo" e "e-mail", mentre un altro provider di servizi utilizza "nome", "titolo" e "phoneNumbers". L'endpoint degli schemi consente l'individuazione degli attributi supportati.|
|/Bulk|Le operazioni in blocco consentono di eseguire operazioni su una raccolta di grandi dimensioni di oggetti risorsa in un'unica operazione(ad esempio, aggiornare le appartenenze per un gruppo di grandi dimensioni).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Passaggio 2: Comprendere l'implementazione sCIM di Azure ADStep 2: Understand the Azure AD SCIM implementation
> [!IMPORTANT]
> L'ultimo aggiornamento del comportamento dell'implementazione SCIM di Azure AD è stato eseguito il 18 dicembre 2018. Per informazioni sulle modifiche, vedere [Conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Se si sta creando un'applicazione che supporta un'API di gestione utenti SCIM 2.0, in questa sezione viene descritto in dettaglio come viene implementato il client SCIM di Azure AD. Viene inoltre illustrato come modellare la gestione e le risposte delle richieste del protocollo SCIM. Dopo aver implementato l'endpoint SCIM, è possibile testarlo seguendo la procedura descritta nella sezione precedente.

Nell'ambito della specifica del [protocollo SCIM 2.0](http://www.simplecloud.info/#Specification), l'applicazione deve soddisfare i requisiti seguenti:

* Supporta la creazione di utenti e, facoltativamente, anche di gruppi, in base alla sezione [3.3 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Supporta la modifica di utenti o gruppi con richieste PATCH, in base [alla sezione 3.5.2 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Supporta il recupero di una risorsa nota per un utente o un gruppo creato in precedenza, come indicato nella [sezione 3.4.1 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Supporta l'esecuzione di query su utenti o gruppi, in base alla sezione [3.4.2 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Per impostazione predefinita, `id` gli utenti vengono `username` recuperati `externalid`da loro e sottoposti a query da e , e i gruppi vengono interrogati da `displayName`.  
* Supporta l'esecuzione di query utente in base all'ID e al responsabile, in base alla sezione 3.4.2 del protocollo SCIM.  
* Supporta l'esecuzione di query sui gruppi in base all'ID e al membro, in base alla sezione 3.4.2 del protocollo SCIM.  
* Accetta un singolo token di connessione per l'autenticazione e l'autorizzazione di Azure AD all'applicazione.

Seguire queste linee guida generali quando si implementa un endpoint SCIM per garantire la compatibilità con Azure AD:Follow these general guidelines when implementing a SCIM endpoint to ensure compatibility with Azure AD:

* `id`è una proprietà obbligatoria per tutte le risorse. Ogni risposta che restituisce una risorsa deve garantire `ListResponse` che ogni risorsa disponga di questa proprietà, ad eccezione di zero membri.
* La risposta a una richiesta di `ListResponse`query/filtro deve essere sempre un file .
* I gruppi sono facoltativi, ma sono supportati solo se l'implementazione SCIM supporta le richieste PATCH.
* Non è necessario includere l'intera risorsa nella risposta PATCH.
* Microsoft Azure AD utilizza solo gli operatori seguenti:  
    - `eq`
    - `and`
* Non richiedere una corrispondenza con distinzione tra maiuscole e `op` minuscole negli elementi https://tools.ietf.org/html/rfc7644#section-3.5.2strutturali in SCIM, in particolare i valori dell'operazione PATCH, come definito in . Azure AD genera i valori di `Add` `Replace`'op' come , e `Remove`.
* Microsoft Azure AD effettua richieste per recuperare un utente e un gruppo casuali per garantire che l'endpoint e le credenziali siano validi. Questa operazione viene eseguita anche come parte del flusso di connessione di prova nel portale di Azure.It's also done as a part of **Test Connection** flow in the Azure [portal](https://portal.azure.com). 
* L'attributo su cui è possibile eseguire query sulle risorse deve essere impostato come attributo corrispondente nell'applicazione nel portale di [Azure.](https://portal.azure.com) Per ulteriori informazioni, vedere Personalizzazione dei mapping degli attributi di provisioning degli [utentiFor more information, see Customizing User Provisioning Attribute Mappings](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Provisioning e deprovisioning degli utenti

Nella figura seguente vengono illustrati i messaggi inviati da Azure Active Directory a un servizio SCIM per gestire il ciclo di vita di un utente nell'archivio identità dell'applicazione.  

![Mostra la sequenza di provisioning e deprovisioning degli utenti](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sequenza di provisioning e deprovisioning degli utenti*

### <a name="group-provisioning-and-deprovisioning"></a>Provisioning e deprovisioning dei gruppi

Il provisioning di gruppo e il deprovisioning sono facoltativi. Quando implementato e abilitato, la figura seguente mostra i messaggi inviati da Azure AD a un servizio SCIM per gestire il ciclo di vita di un gruppo nell'archivio identità dell'applicazione.  Tali messaggi differiscono dai messaggi sugli utenti in due modi:

* Le richieste di recupero dei gruppi specificano che l'attributo members deve essere escluso da qualsiasi risorsa fornita in risposta alla richiesta.  
* Le richieste per determinare se un attributo reference ha un determinato valore sono richieste relative all'attributo members.  

![Mostra la sequenza di provisioning e deprovisioning del gruppo](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sequenza di provisioning e deprovisioning dei gruppi*

### <a name="scim-protocol-requests-and-responses"></a>Richieste e risposte del protocollo SCIM
In questa sezione vengono fornite richieste SCIM di esempio generate dal client SCIM di Azure AD e risposte previste di esempio. Per ottenere risultati ottimali, devi codificare l'app per gestire queste richieste in questo formato e generare le risposte previste.

> [!IMPORTANT]
> Per informazioni su come e quando il servizio di provisioning utenti di Azure AD genera le operazioni descritte di seguito, vedere la sezione Cicli di [provisioning: iniziale e incrementale](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Funzionamento del provisioning.](how-provisioning-works.md)

[Operazioni utente](#user-operations)
  - [Crea utente](#create-user) ([Richiesta](#request) / [risposta](#response))
  - [Ottenere l'utente](#get-user) ([Richiesta di risposta](#response-1)[richiesta](#request-1) / )
  - [Ottenere l'utente tramite query](#get-user-by-query) ([Richiesta](#request-2) / [risposta](#response-2)richiesta )
  - [Ottenere utente per query - zero risultati](#get-user-by-query---zero-results) ( Richiesta[risposta](#request-3)
/ [Response](#response-3))
  - [Aggiorna utente [proprietà multivalore]](#update-user-multi-valued-properties) ([Richiesta](#request-4) /  [risposta](#response-4))
  - [Aggiorna utente [proprietà](#update-user-single-valued-properties) a valore singolo] ([Richiesta](#request-5)
/ [risposta](#response-5)) 
  - [Disabilita utente](#disable-user) ([Richiesta](#request-14) / 
[risposta](#response-14))
  - [Elimina utente](#delete-user) ([Richiesta](#request-6) / 
[risposta](#response-6))


[Operazioni di gruppo](#group-operations)
  - [Crea gruppo](#create-group) ( [Richiedi](#request-7) / [risposta](#response-7))
  - [Ottieni gruppo](#get-group) ( [Richiesta](#request-8) / [risposta](#response-8))
  - [Ottieni raggruppamento per displayName](#get-group-by-displayname) ([Richiesta](#request-9) / [risposta](#response-9))
  - [Aggiorna gruppo [Attributi non membri]](#update-group-non-member-attributes) ([Richiesta](#request-10) /
  [risposta](#response-10))
  - [Aggiorna gruppo [Aggiungi membri]](#update-group-add-members) ( [Richiedi](#request-11) /
[risposta](#response-11))
  - [Aggiorna gruppo [Rimuovi membri]](#update-group-remove-members) ( [Richiedi](#request-12) /
[risposta](#response-12))
  - [Elimina gruppo](#delete-group) ([Richiedi](#request-13) /
[risposta](#response-13))

### <a name="user-operations"></a>Operazioni utente

* Gli utenti possono essere `userName` `email[type eq "work"]` interrogati da o attributi.  

#### <a name="create-user"></a>Crea utente

###### <a name="request"></a>Richiesta

*POST /Utenti*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Risposta

*HTTP/1.1 201 Creato*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Ottieni utente

###### <a name="request"></a><a name="request-1"></a>Richiesta
*GET /Utenti/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Risposta (trovato dall'utente)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Richiesta
*GET /Utenti/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Risposta (Utente non trovato. Si noti che il dettaglio non è obbligatorio, ma solo lo stato.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Ottieni utente per query

##### <a name="request"></a><a name="request-2"></a>Richiesta

*GET /Utenti?filtro-nomeutente eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Ottenere utente per query - zero risultatiGet User by query - zero results

##### <a name="request"></a><a name="request-3"></a>Richiesta

*OTTIENI /Utenti?filtro:nomeutente eq "utente inesistente"*

##### <a name="response"></a><a name="response-3"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Aggiorna utente [proprietà multivalore]

##### <a name="request"></a><a name="request-4"></a>Richiesta

*PATCH /Utenti/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Aggiorna utente [proprietà a valore singolo]

##### <a name="request"></a><a name="request-5"></a>Richiesta

*PATCH /Utenti/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Disabilita utente

##### <a name="request"></a><a name="request-14"></a>Richiesta

*PATCH /Utenti/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Response

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Eliminazione di un utente.

##### <a name="request"></a><a name="request-6"></a>Richiesta

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Response

*HTTP/1.1 204 Nessun contenuto*

### <a name="group-operations"></a>Operazioni di gruppo

* I gruppi devono sempre essere creati con un elenco di membri vuoto.
* I gruppi possono essere `displayName` sottoposti a query dall'attributo.
* L'aggiornamento alla richiesta PATCH di gruppo deve restituire un *HTTP 204 Nessun contenuto* nella risposta. Restituire un corpo con un elenco di tutti i membri non è consigliabile.
* Non è necessario supportare la restituzione di tutti i membri del gruppo.

#### <a name="create-group"></a>Creare un gruppo

##### <a name="request"></a><a name="request-7"></a>Richiesta

*POST /Gruppi HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Response

*HTTP/1.1 201 Creato*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Ottenere un gruppo

##### <a name="request"></a><a name="request-8"></a>Richiesta

*GET /Gruppi/40734ae655284ad3abcc?excludedAttributes*

##### <a name="response"></a><a name="response-8"></a>Response
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Ottieni gruppo per displayNameGet Group by displayName

##### <a name="request"></a><a name="request-9"></a>Richiesta
*GET /Groups?excludedAttributes: membri&filter-displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Response

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Aggiorna gruppo [Attributi non membri]

##### <a name="request"></a><a name="request-10"></a>Richiesta

*PATCH /Gruppi/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Response

*HTTP/1.1 204 Nessun contenuto*

### <a name="update-group-add-members"></a>Aggiorna gruppo [Aggiungi membri]

##### <a name="request"></a><a name="request-11"></a>Richiesta

*PATCH /Gruppi/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Response

*HTTP/1.1 204 Nessun contenuto*

#### <a name="update-group-remove-members"></a>Aggiorna gruppo [Rimuovi membri]

##### <a name="request"></a><a name="request-12"></a>Richiesta

*PATCH /Gruppi/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Response

*HTTP/1.1 204 Nessun contenuto*

#### <a name="delete-group"></a>Eliminare un gruppo

##### <a name="request"></a><a name="request-13"></a>Richiesta

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Response

*HTTP/1.1 204 Nessun contenuto*

### <a name="security-requirements"></a>Requisiti di protezione
**Versioni del protocollo TLS**

Le uniche versioni del protocollo TLS accettabili sono TLS 1.2 e TLS 1.3. Non sono consentite altre versioni di TLS. Non è consentita alcuna versione di SSL. 
- Le chiavi RSA devono essere di almeno 2.048 bit.
- Le chiavi ECC devono essere di almeno 256 bit, generate utilizzando una curva ellittica approvata


**Lunghezze chiave**

Tutti i servizi devono utilizzare certificati X.509 generati utilizzando chiavi crittografiche di lunghezza sufficiente, il che significa:

**Suite di cifratura**

Tutti i servizi devono essere configurati per utilizzare i seguenti pacchetti di crittografia, nell'ordine esatto specificato di seguito. Si noti che se si dispone solo di un certificato RSA, le suite di crittografia ECDSA installate non hanno alcun effetto. </br>

Barra minima DI TLS 1.2 Cipher Suites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Passaggio 3: Creare un endpoint SCIMStep 3: Build a SCIM endpoint

Dopo aver progettato lo schema e compreso l'implementazione SCIM di Azure AD, è possibile iniziare a sviluppare l'endpoint SCIM. Invece di partire da zero e costruire l'implementazione completamente da soli, puoi contare su una serie di librerie SCIM open source pubblicate dalla comunità SCIM.

Il codice di [riferimento](https://aka.ms/SCIMReferenceCode) .NET Core open source pubblicato dal team di provisioning di Azure AD è una di queste risorse che può avviare lo sviluppo. Dopo aver creato l'endpoint SCIM, è consigliabile testarlo. È possibile utilizzare la raccolta di [test postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) forniti come parte del codice di riferimento o eseguire le richieste/risposte di esempio fornite [in precedenza.](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)  

   > [!Note]
   > Il codice di riferimento ha lo scopo di iniziare a creare l'endpoint SCIM e viene fornito "AS IS". I contributi della community sono i benvenuti per aiutare a costruire e mantenere il codice.

La soluzione è composta da due progetti, _Microsoft.SCIM_ e _Microsoft.SCIM.WebHostSample_.

Il progetto _Microsoft.SCIM_ è la libreria che definisce i componenti del servizio Web conformi alla specifica SCIM. Dichiara l'interfaccia _Microsoft.SCIM.IProvider_, le richieste vengono convertite in chiamate ai metodi del provider, che verrebbero programmate per operare su un archivio identità.

![Ripartizione: una richiesta convertita in chiamate ai metodi del provider](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Il progetto _Microsoft.SCIM.WebHostSample_ è un'applicazione Web di Visual Studio ASP.NET Core, basata sul modello _vuoto._ In questo modo il codice di esempio può essere distribuito come autonomo, ospitato in contenitori o all'interno di Internet Information Services. Implementa inoltre l'interfaccia _Microsoft.SCIM.IProvider_ mantenendo le classi in memoria come archivio identità di esempio.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Creazione di un endpoint SCIM personalizzatoBuilding a custom SCIM endpoint

Il servizio SCIM deve disporre di un indirizzo HTTP e di un certificato di autenticazione server di cui l'autorità di certificazione radice è uno dei nomi seguenti:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK include un certificato di sviluppo HTTPS che può essere utilizzato durante lo sviluppo, il certificato viene installato come parte dell'esperienza di prima esecuzione. A seconda della modalità di esecuzione del ASP.NET applicazione Web di base, verrà ascoltata una porta diversa:

* Microsoft.SCIM.WebHostSample:https://localhost:5001
* Espresso IIS:https://localhost:44359/

Per altre informazioni su HTTPS in ASP.NET Core, usare il seguente collegamento: [Applicare HTTPS in ASP.NET CoreFor](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl) more information on HTTPS in ASP.NET Core use the following link: Enforce HTTPS in ASP.NET Core

### <a name="handling-endpoint-authentication"></a>Gestione dell'autenticazione dell'endpoint

Le richieste da Azure Active Directory includono un token di connessione OAuth 2.0. Qualsiasi servizio che riceve la richiesta deve autenticare l'autorità emittente come Azure Active Directory per il tenant di Azure Active Directory previsto.

Nel token, l'autorità emittente è identificata `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`da un'attestazione iss, ad esempio . In questo esempio, l'indirizzo di `https://sts.windows.net`base del valore dell'attestazione, , identifica Azure Active Directory come autorità emittente, mentre il segmento di indirizzo relativo, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, è un identificatore univoco del tenant di Azure Active Directory per il quale è stato emesso il token.

Il gruppo di destinatari per il token sarà l'ID del modello di applicazione per l'applicazione nella raccolta, ognuna delle applicazioni registrate in un singolo tenant può ricevere la stessa `iss` attestazione con richieste SCIM. L'ID del modello di applicazione per ogni [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) applicazione nella raccolta varia, contattare per domande relative all'ID del modello di applicazione per un'applicazione raccolta. L'ID del modello di applicazione per tutte le app personalizzate è _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_.

Nel codice di esempio, le richieste vengono autenticate utilizzando il pacchetto Microsoft.AspNetCore.Authentication.JwtBearer. Il codice seguente impone che le richieste a uno degli endpoint del servizio vengano autenticate utilizzando il token di connessione rilasciato da Azure Active Directory per un tenant specificato:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Un token di connessione è inoltre necessario per utilizzare i [test del postino](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) forniti ed eseguire il debug locale utilizzando localhost. Il codice di esempio usa ASP.NET ambienti core per modificare le opzioni di autenticazione durante la fase di sviluppo e abilitare l'uso di un token autofirmato.

Per altre informazioni su più ambienti in ASP.NET Core usare il collegamento seguente: Usare più ambienti in ASP.NET CoreFor more information on multiple environments in ASP.NET Core use the following [link: Use multiple environments in ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

Il codice seguente impone che le richieste a uno degli endpoint del servizio vengano autenticate usando un token di connessione firmato con una chiave personalizzata:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Inviare una richiesta GET al controller di token per ottenere un token di connessione valido, il metodo GenerateJSONWebToken è responsabile della creazione di un token corrispondente ai parametri configurati per lo sviluppo:Send a GET request to the Token controller to get a valid bearer token, the method _GenerateJSONWebToken_ is responsible to create a token matching the parameters configured for development:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Gestione del provisioning e del deprovisioning degli utenti

***Esempio 1. Eseguire una query sul servizio per un utente corrispondente***

Azure Active Directory esegue query nel servizio per trovare un utente con valore dell'attributo externalId corrispondente al valore dell'attributo mailNickname di un utente in Azure AD. La query viene espressa come richiesta HTTP (Hypertext Transfer Protocol), ad esempio questo esempio, in cui jyoung è un esempio di mailNickname di un utente in Azure Active Directory.

>[!NOTE]
> Questo è solo un esempio. Non tutti gli utenti disporranno di un attributo mailNickname e il valore di un utente potrebbe non essere univoco nella directory. Inoltre, l'attributo utilizzato per la corrispondenza (che in questo caso è externalId) è configurabile nei mapping degli attributi di [Azure AD.](customize-application-attributes.md)

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Nel codice di esempio la richiesta viene convertita in una chiamata al metodo QueryAsync del provider del servizio. Ecco la firma del metodo: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

Nella query di esempio, per un utente con un determinato valore per l'attributo externalId, i valori degli argomenti passati al metodo QueryAsync sono:

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Esempio 2. Effettuare il provisioning di un utente***

Se la risposta a una query al servizio Web per un utente con un valore dell'attributo externalId che corrisponde al valore dell'attributo mailNickname di un utente non restituisce alcun utente, Azure Active Directory richiede che il servizio esegete il provisioning di un utente corrispondente a quello in Azure Active Directory.  Ecco un esempio di questa richiesta: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

Nel codice di esempio la richiesta viene convertita in una chiamata al metodo CreateAsync del provider del servizio. Ecco la firma del metodo: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

In una richiesta di provisioning di un utente, il valore dell'argomento della risorsa è un'istanza della classe Microsoft.SCIM.Core2EnterpriseUser, definita nella libreria Microsoft.SCIM.Schemas.  Se la richiesta di provisioning dell'utente ha esito positivo, l'implementazione del metodo deve restituire un'istanza della classe Microsoft.SCIM.Core2EnterpriseUser, con il valore della proprietà Identifier impostato sull'identificatore univoco del nuovo provisioning Utente.  

***Esempio 3. Eseguire una query sullo stato corrente di un utente*** 

Per aggiornare un utente la cui esistenza è nota in un archivio identità gestito da SCIM, Azure Active Directory richiede al servizio lo stato corrente dell'utente con una richiesta simile alla seguente: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

Nel codice di esempio la richiesta viene convertita in una chiamata al metodo RetrieveAsync del provider del servizio. Ecco la firma del metodo: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

Nell'esempio di una richiesta per recuperare lo stato corrente di un utente, i valori delle proprietà dell'oggetto fornito come valore dell'argomento parameters sono analoghi ai seguenti: 
  
* Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Esempio 4. Eseguire una query sul valore di un attributo di riferimento da aggiornare*** 

Se un attributo di riferimento deve essere aggiornato, Azure Active Directory esegue una query sul servizio per determinare se il valore corrente dell'attributo di riferimento nell'archivio identità collegato dal servizio corrisponde già al valore di tale attributo in Azure Active Directory. Per gli utenti, l'unico attributo il cui valore corrente viene sottoposto a query con questa modalità è l'attributo manager. Ecco un esempio di una richiesta per determinare se l'attributo manager di un oggetto utente ha attualmente un determinato valore: nel codice di esempio la richiesta viene convertita in una chiamata al metodo QueryAsync del provider del servizio. Il valore delle proprietà dell'oggetto fornito come valore dell'argomento parameters è analogo al seguente: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

In questo caso, il valore dell'indice x può essere 0 e il valore dell'indice y può essere 1 oppure il valore di x può essere 1 e il valore di y può essere 0, a seconda dell'ordine delle espressioni del parametro di query del filtro.   

***Esempio 5. Richiedere da Azure AD a un servizio SCIM per aggiornare un utenteRequest from Azure AD to an SCIM service to update a user*** 

Ecco un esempio di una richiesta di Azure Active Directory a un servizio SCIM per l'aggiornamento di un utente: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

Nel codice di esempio la richiesta viene convertita in una chiamata al metodo UpdateAsync del provider del servizio. Ecco la firma del metodo: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

Nell'esempio di una richiesta per l'aggiornamento di un utente, i valori delle proprietà dell'oggetto fornito come valore dell'argomento patch sono analoghi ai seguenti: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***Esempio 6. Eseguire il deprovisioning di un utenteDeprovision a user***

Per eseguire il deprovisioning di un utente da un archivio identità collegato da un servizio SCIM, Azure AD invia una richiesta come:To deprovision a user from an identity store fronted by an SCIM service, Azure AD sends a request such as:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

Nel codice di esempio la richiesta viene convertita in una chiamata al metodo DeleteAsync del provider del servizio. Ecco la firma del metodo: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

L'oggetto fornito come valore dell'argomento resourceIdentifier ha questi valori di proprietà nell'esempio di una richiesta di deprovisioning di un utente: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Passaggio 4: Integrare l'endpoint SCIM con il client SCIM di Azure ADStep 4: Integrate your SCIM endpoint with the Azure AD SCIM client

Azure AD può essere configurato per eseguire automaticamente il provisioning di utenti e gruppi assegnati ad applicazioni che implementano un profilo specifico del [protocollo SCIM 2.0.](https://tools.ietf.org/html/rfc7644) Le specifiche del profilo sono documentate nel [Passaggio 2: Comprendere l'implementazione di Azure AD SCIM](#step-2-understand-the-azure-ad-scim-implementation).

Verificare la conformità ai requisiti sopra riportati con il provider dell'applicazione o consultando la documentazione fornita dal provider.

> [!IMPORTANT]
> L'implementazione SCIM di Azure AD si basa sul servizio di provisioning degli utenti di Azure AD, progettato per mantenere costantemente sincronizzati gli utenti tra Azure AD e l'applicazione di destinazione e implementa un set molto specifico di operazioni standard. È importante comprendere questi comportamenti per comprendere il comportamento del client SCIM di Azure AD. Per ulteriori informazioni, vedere la sezione [Cicli di provisioning: iniziale e incrementale](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Funzionamento del provisioning](how-provisioning-works.md).

### <a name="getting-started"></a>Introduzione

Le applicazioni che supportano il profilo SCIM descritto in questo articolo possono essere connesse ad Azure Active Directory usando la funzionalità "Applicazione non nella raccolta" nella raccolta di applicazioni di Azure AD. Una volta stabilita la connessione, Azure AD esegue un processo di sincronizzazione ogni 40 minuti in cui interroga l'endpoint SCIM dell'applicazione in merito agli utenti e ai gruppi assegnati e li crea o li modifica in base alle istruzioni di assegnazione.

**Per connettere un'applicazione che supporta SCIM:**

1. Accedere al [portale](https://aad.portal.azure.com)di Azure Active Directory . Si noti che è possibile ottenere l'accesso a una versione di valutazione gratuita per Azure Active Directory con licenze P2 iscrivendosi al [programma per sviluppatoriNote](https://developer.microsoft.com/office/dev-program) that you can get access a free trial for Azure Active Directory with P2 licenses by signing up for the developer program
2. Selezionare **Applicazioni aziendali** nel riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
3. Selezionare **: Nuova applicazione** > **Tutte le** > **applicazioni non gallery**.
4. Immettere un nome per l'applicazione e selezionare **Aggiungi** per creare un oggetto app. La nuova app viene aggiunta all'elenco delle applicazioni aziendali e si apre alla relativa schermata di gestione delle app.

   ![Screenshot mostra la raccolta di applicazioni di Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Raccolta di applicazioni di Azure ADAzure AD application gallery*

5. Nella schermata di gestione delle app, seleziona **Provisioning** nel riquadro sinistro.
6. Nel menu **Modalità di provisioning** selezionare **Automatica**.

   ![Esempio: pagina Provisioning di un'app nel portale di AzureExample: An's Provisioning page in the Azure portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Configurazione del provisioning nel portale di AzureConfiguring provisioning in the Azure portal*

7. Nel campo **URL tenant** immettere l'URL dell'endpoint SCIM dell'applicazione. Esempio: `https://api.contoso.com/scim/`
8. Se l'endpoint SCIM richiede un token di connessione OAuth da un'autorità di certificazione diversa da Azure AD, copiare il token di connessione OAuth nel campo **Token segreto** facoltativo. Se questo campo viene lasciato vuoto, Azure AD include un token di connessione OAuth emesso da Azure AD con ogni richiesta. Le app che usano Azure AD come provider di identità possono convalidare il token rilasciato da Azure AD. 
   > [!NOTE]
   > Non ***è*** consigliabile lasciare vuoto questo campo e basarsi su un token generato da Azure AD. Questa opzione è disponibile principalmente a scopo di test.
9. Selezionare **Test connessione** per fare in modo che Azure Active Directory tenti di connettersi all'endpoint SCIM. Se il tentativo non riesce, vengono visualizzate le informazioni sull'errore.  

    > [!NOTE]
    > **Test connessione** esegue query sull'endpoint SCIM per cercare un utente che non esiste usando un GUID casuale come proprietà corrispondente selezionata nella configurazione di Azure AD. La risposta corretta prevista è HTTP 200 OK con un messaggio ListResponse SCIM vuoto.

10. Se i tentativi di connessione all'applicazione hanno esito positivo, selezionare **Salva** per salvare le credenziali di amministratore.
11. Nella sezione **Mapping** sono disponibili due set selezionabili di mapping di [attributi:](customize-application-attributes.md)uno per gli oggetti utente e uno per gli oggetti gruppo. Selezionare ognuno dei due set per esaminare gli attributi sincronizzati da Active Directory di Azure con l'app. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli utenti e i gruppi nell'app per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    > [!NOTE]
    > Facoltativamente, è possibile disattivare la sincronizzazione degli oggetti gruppo disabilitando il mapping relativo ai gruppi.

12. In **Impostazioni** il campo **Ambito** definisce gli utenti e i gruppi che devono essere sincronizzati. Selezionare Sincronizza solo gli utenti e i **gruppi assegnati** (scelta consigliata) per sincronizzare solo gli utenti e i gruppi assegnati nella scheda **Utenti e gruppi.**
13. Una volta completata la configurazione, impostare **Lo stato** di provisioning su **Attivato**.
14. Selezionare **Salva** per avviare il servizio di provisioning di Azure AD.
15. Se la sincronizzazione solo degli utenti e dei gruppi assegnati (scelta consigliata), assicurarsi di selezionare la scheda **Utenti e gruppi** e assegnare gli utenti o i gruppi da sincronizzare.

Una volta avviato il ciclo iniziale, puoi selezionare Registri di **provisioning** nel riquadro sinistro per monitorare lo stato di avanzamento, che mostra tutte le azioni eseguite dal servizio di provisioning nell'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

> [!NOTE]
> L'esecuzione del ciclo iniziale richiede più tempo rispetto alle sincronizzazioni successive, che si verificano approssimativamente ogni 40 minuti, purché il servizio sia in esecuzione.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Passaggio 5: Pubblicare l'applicazione nella raccolta di applicazioni di Azure ADStep 5: Publish your application to the Azure AD application gallery

Se si sta compilando un'applicazione che verrà usata da più tenant, è possibile renderla disponibile nella raccolta di applicazioni di Azure AD. In questo modo sarà più semplice per le organizzazioni individuare l'applicazione e configurare il provisioning. Pubblicare l'app nella raccolta di Azure AD e rendere il provisioning disponibile ad altri utenti è semplice. Consultare i passaggi [qui](../develop/howto-app-gallery-listing.md) Microsoft collaborerà con l'utente per integrare l'applicazione nella raccolta, testare l'endpoint e rilasciare [la documentazione](../saas-apps/tutorial-list.md) sull'onboarding per i clienti. 

### <a name="gallery-onboarding-checklist"></a>Elenco di controllo per l'onboarding della raccolta
Seguire l'elenco di controllo riportato di seguito per assicurarsi che l'applicazione sia caricata rapidamente e che i clienti abbiano un'esperienza di distribuzione fluida. Le informazioni saranno raccolte da voi durante l'onboarding alla galleria. 
> [!div class="checklist"]
> * Supportare un endpoint utente e gruppo [SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) (ne è richiesto solo uno, ma entrambi sono consigliati)
> * Supporto di almeno 25 richieste al secondo per tenant (obbligatorio)
> * Stabilire contatti di progettazione e supporto per guidare i clienti a pubblicare l'onboarding della galleria (obbligatorio)
> * 3 Credenziali di test non in scadenza per l'applicazione (obbligatorio)
> * Supportare la concessione del codice di autorizzazione OAuth o un token di lunga durata come descritto di seguito (Obbligatorio)
> * Stabilire un punto di contatto di progettazione e supporto per supportare i clienti dopo l'onboarding della galleria (obbligatorio)
> * Supportare l'aggiornamento di più appartenenze a gruppi con un singolo PATCH (consigliato)Support updating multiple group memberships with a single PATCH (Recommended) 
> * Documentare pubblicamente l'endpoint SCIM (scelta consigliata) 
> * [Individuazione dello schema](https://tools.ietf.org/html/rfc7643#section-6) di supporto (scelta consigliata)Support schema discovery (Recommended)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorizzazione per il provisioning dei connettori nella raccolta di applicazioniAuthorization for provisioning connectors in the application gallery
La specifica SCIM non definisce uno schema specifico di SCIM per l'autenticazione e l'autorizzazione. Essa si basa sull'uso degli standard di settore esistenti. Il client di provisioning di Azure AD supporta due metodi di autorizzazione per le applicazioni nella raccolta. 

|Metodo di autorizzazione|Vantaggi|Svantaggi|Supporto|
|--|--|--|--|
|Nome utente e password (non consigliati o supportati da Azure AD)|Facile da implementare|Insecure - [Il tuo $word non conta](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Supportato caso per caso per le app della galleria. Non supportato per le app non della raccolta.|
|Token di portatore di lunga durata|I token di lunga durata non richiedono la presenza di un utente. Sono facili da usare per gli amministratori durante la configurazione del provisioning.|I token di lunga durata possono essere difficili da condividere con un amministratore senza usare metodi non sicuri come la posta elettronica. |Supportato per app galleria e non di raccolta. |
|Concessione del codice di autorizzazione OAuth|I token di accesso sono molto più brevi delle password e dispongono di un meccanismo di aggiornamento automatico che i token di connessione di lunga durata non hanno.  Un utente reale deve essere presente durante l'autorizzazione iniziale, aggiungendo un livello di responsabilità. |Richiede la presenza di un utente. Se l'utente lascia l'organizzazione, il token non è valido e l'autorizzazione dovrà essere completata di nuovo.|Supportato per le app della galleria. È in corso il supporto per le app non della raccolta.|
|Concessione di credenziali client OAuth|I token di accesso sono molto più brevi delle password e dispongono di un meccanismo di aggiornamento automatico che i token di connessione di lunga durata non hanno. Sia la concessione del codice di autorizzazione che le credenziali client concedono creano lo stesso tipo di token di accesso, pertanto lo spostamento tra questi metodi è trasparente per l'API.  Il provisioning può essere completamente automatizzato e i nuovi token possono essere richiesti automaticamente senza l'interazione dell'utente. ||Non supportato per le app galleria e non della raccolta. Il supporto è nel nostro backlog.|

[!NOTE] Non è consigliabile lasciare vuoto il campo del token nell'interfaccia utente dell'app di configurazione del provisioning di Azure AD. Il token generato è disponibile principalmente a scopo di test.

Flusso di **concessione del codice di autorizzazione OAuth:OAuth authorization code grant flow:** Il servizio di provisioning supporta il codice di [autorizzazione grant.](https://tools.ietf.org/html/rfc6749#page-24) Dopo aver inviato la richiesta di pubblicazione dell'app nella raccolta, il nostro team collaborerà per raccogliere le seguenti informazioni:
*  URL di autorizzazione: un URL dal client per ottenere l'autorizzazione dal proprietario della risorsa tramite il reindirizzamento dell'agente utente. L'utente viene reindirizzato a questo URL per autorizzare l'accesso. 
*  URL di scambio di token: un URL dal client per scambiare una concessione di autorizzazione per un token di accesso, in genere con l'autenticazione client.
*  ID client: il server di autorizzazione invia al client registrato un identificatore client, ovvero una stringa univoca che rappresenta le informazioni di registrazione fornite dal client.  L'identificatore del client non è un segreto. è esposto al proprietario della risorsa e **non deve** essere utilizzato da solo per l'autenticazione client.  
*  Segreto client: il segreto client è un segreto generato dal server di autorizzazione. Deve essere un valore univoco noto solo al server di autorizzazione. 

Si noti che OAuth v1 non è supportato a causa dell'esposizione del segreto client. OAuth v2 è supportato.  

Procedure consigliate (consigliate ma non obbligatorie):
* Supporta più URL di reindirizzamento. Gli amministratori possono configurare il provisioning sia da "portal.azure.com" che da "aad.portal.azure.com". Il supporto di più URL di reindirizzamento garantisce che gli utenti possano autorizzare l'accesso da entrambi i portali.
* Supporta più segreti per garantire un facile rinnovo dei segreti, senza tempi di inattività. 

**Token di connessione OAuth di lunga durata:** Se l'applicazione non supporta il flusso di concessione del codice di autorizzazione OAuth, è anche possibile generare un token di connessione OAuth di lunga durata rispetto a quello che un amministratore può utilizzare per configurare l'integrazione di provisioning. Il token deve essere perpetuo, altrimenti il processo di provisioning verrà messo in [quarantena](application-provisioning-quarantine-status.md) alla scadenza del token. Le dimensioni di questo token devono essere inferiori a 1 KB.  

Per ulteriori metodi di autenticazione e autorizzazione, comunicacelo su [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Elenco di controllo del lancio della galleria go-to-market
Per contribuire a sensibilizzare e richiedere la nostra integrazione congiunta, ti consigliamo di aggiornare la documentazione esistente e amplificare l'integrazione nei tuoi canali di marketing.  Di seguito è riportata una serie di attività di elenco di controllo che si consiglia di completare per supportare il

* **Disponibilità alle vendite e all'assistenza clienti.** Assicurati che i team di vendita e supporto siano consapevoli e possano parlare con le funzionalità di integrazione. Breve il vostro team di vendita e supporto, fornire loro domande frequenti e includere l'integrazione nei materiali di vendita. 
* **Post di blog e/o comunicato stampa.** Crea un post sul blog o un comunicato stampa che descriva l'integrazione congiunta, i vantaggi e come iniziare. [Esempio: comunicato stampa di Impere e Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **I social media.** Sfrutta i tuoi social media come Twitter, Facebook o LinkedIn per promuovere l'integrazione con i tuoi clienti. Assicurati di @AzureAD includere in modo che possiamo ritwittare il tuo post. [Esempio: Imindiretto Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Sito web di marketing.** Crea o aggiorna le tue pagine di marketing (ad es. pagina di integrazione, pagina partner, pagina dei prezzi e così via) per includere la disponibilità dell'integrazione congiunta. [Esempio: Pagina di integrazione Pingboard](https://pingboard.com/org-chart-for), [pagina di integrazione Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [pagina dei prezzi Monday.com](https://monday.com/pricing/) 
* **Documentazione tecnica.** Crea un articolo del centro assistenza o una documentazione tecnica su come i clienti possono iniziare. [Esempio: Integrazione con Envoy e Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Comunicazione con il cliente.** Avvisai i clienti della nuova integrazione attraverso la comunicazione con i clienti (newsletter mensili, campagne e-mail, note sulla versione del prodotto). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Consenti agli indirizzi IP usati dal servizio di provisioning di Azure AD di effettuare richieste SCIMAllow IP addresses used by the Azure AD provisioning service to make SCIM requests

Alcune app consentono il traffico in ingresso verso la propria app. Affinché il servizio di provisioning di Azure AD funzioni come previsto, gli indirizzi IP usati devono essere consentiti. Per un elenco degli indirizzi IP per ogni tag del servizio/area, vedere il file JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Indirizzi IP di Azure e tag del servizio - Cloud pubblico). È possibile scaricare e programmare questi indirizzi IP nel firewall in base alle esigenze. Gli intervalli IP riservati per il provisioning di Azure AD sono disponibili in "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Articoli correlati

* [Automatizzare il provisioning e il deprovisioning degli utenti nelle app SaaSAutomate user provisioning and deprovisioning to SaaS apps](user-provisioning.md)
* [Personalizzare i mapping degli attributi per il provisioning degli utenti](customize-application-attributes.md)
* [Scrittura di espressioni per i mapping di attributiWriting expressions for attribute mappings](functions-for-customizing-application-data.md)
* [Applicazione dell'ambito ai filtri per il provisioning degli utenti](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notifiche relative al provisioning dell'account](user-provisioning.md)
* [Elenco di tutorial su come integrare le app SaaS](../saas-apps/tutorial-list.md)
