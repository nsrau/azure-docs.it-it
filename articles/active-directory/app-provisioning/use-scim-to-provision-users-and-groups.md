---
title: Sviluppare un endpoint SCIM per il provisioning utenti nelle app da Azure AD
description: System for Cross-domain Identity Management (SCIM) standardizza il provisioning utenti automatico. Questo articolo illustra come sviluppare un endpoint SCIM, integrare l'API SCIM con Azure Active Directory e iniziare ad automatizzare il provisioning di utenti e gruppi nelle applicazioni cloud.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 2fbdf947eb36e1591cc9da52a85e389be63c8535
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826656"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>Creare un endpoint SCIM e configurare il provisioning utenti con Azure AD

Gli sviluppatori di applicazioni possono usare l'API SCIM (System for Cross-domain Identity Management) di gestione degli utenti per abilitare il provisioning automatico di utenti e gruppi tra l'applicazione e Azure AD. Questo articolo illustra come creare un endpoint SCIM ed eseguire l'integrazione con il servizio di provisioning di Azure AD. La specifica SCIM offre uno schema utente comune per il provisioning. In combinazione con standard di federazione come OpenID Connect o SAML, SCIM offre agli amministratori una soluzione end-to-end basata su standard per la gestione degli accessi.

SCIM è una definizione standardizzata di due endpoint: un endpoint /Users e un endpoint /Groups. Usa verbi REST comuni per creare, aggiornare ed eliminare oggetti e uno schema predefinito per attributi comuni come nome del gruppo, nome utente, nome, cognome e indirizzo di posta elettronica. Le app che offrono un'API REST SCIM 2.0 possono ridurre o eliminare le difficoltà associate all'uso di un'API proprietaria di gestione degli utenti. Tutti i client SCIM conformi, ad esempio, sono in grado di inviare un HTTP POST di un oggetto JSON all'endpoint /Users per creare una nuova voce utente. Invece di richiedere un'API leggermente diversa per le stesse azioni di base, le app conformi allo standard SCIM possono sfruttare immediatamente i vantaggi del codice, dei client e degli strumenti preesistenti. 

![Provisioning da Azure AD a un'app con SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Lo schema dell'oggetto utente standard e le API REST per la gestione definiti in SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643) e [7644](https://tools.ietf.org/html/rfc7644)) facilitano l'integrazione tra provider di identità e app. Gli sviluppatori di applicazioni che creano un endpoint SCIM possono effettuare l'integrazione con qualsiasi client conforme a SCIM senza che siano necessarie operazioni personalizzate.

Per automatizzare il provisioning in un'applicazione è necessario creare un endpoint SCIM e integrarlo con il client SCIM Azure AD. Per avviare il provisioning di utenti e gruppi nell'applicazione, seguire questa procedura. 
    
  * **[Passaggio 1: Progettare lo schema degli utenti e dei gruppi.](#step-1-design-your-user-and-group-schema)** Identificare gli oggetti e gli attributi necessari per l'applicazione e determinarne il mapping allo schema degli utenti e dei gruppi supportato dall'implementazione SCIM di Azure AD.

  * **[Passaggio 2: Comprendere l'implementazione SCIM di Azure AD.](#step-2-understand-the-azure-ad-scim-implementation)** Comprendere come è implementato il client SCIM Azure AD e modellare la gestione delle richieste del protocollo SCIM e le risposte.

  * **[Passaggio 3: Creare un endpoint SCIM.](#step-3-build-a-scim-endpoint)** Per l'integrazione con il servizio di provisioning di Azure AD, l'endpoint deve essere compatibile con SCIM 2.0. Se si vuole, per creare l'endpoint si possono usare le librerie e gli esempi di codice Microsoft Common Language Infrastructure (CLI). Gli esempi sono solo a scopo di riferimento e di test. Non è consigliabile creare il codice dell'app di produzione in modo che contenga dipendenze da tali esempi.

  * **[Passaggio 4: Integrare l'endpoint SCIM con il client SCIM Azure AD.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Se l'organizzazione usa un'applicazione di terze parti che implementa il profilo di SCIM 2.0 supportato da Azure AD, è possibile iniziare immediatamente ad automatizzare sia il provisioning che il deprovisioning di utenti e gruppi.

  * **[Passaggio 5: Pubblicare l'applicazione nella raccolta di applicazioni di Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Consentire ai clienti di individuare l'applicazione e configurare il provisioning con facilità. 

![Passaggi per l'integrazione di un endpoint SCIM con Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Passaggio 1: Progettare lo schema degli utenti e dei gruppi

Ogni applicazione richiede attributi diversi per la creazione di un utente o un gruppo. Iniziare l'integrazione identificando gli oggetti (utenti e gruppi) e gli attributi (nome, manager, posizione e così via) richiesti dall'applicazione. Lo standard SCIM definisce uno schema per la gestione di utenti e gruppi. Lo schema utente di base richiede solo tre attributi: **id** (identificatore definito dal provider di servizi), **externalId** (identificatore definito dal client) e **meta** (metadati di sola lettura gestiti dal provider di servizi). Tutti gli altri attributi sono facoltativi. Oltre allo schema utente di base, lo standard SCIM definisce un'estensione per utenti aziendali e un modello per estendere lo schema utente in modo da soddisfare le esigenze dell'applicazione. Se l'applicazione richiede un manager dell'utente, ad esempio, è possibile usare lo schema utente aziendale per raccogliere tale dato e lo schema di base per raccogliere l'indirizzo di posta elettronica dell'utente. Per progettare lo schema, seguire questa procedura:
  1. Elencare gli attributi richiesti dall'applicazione. Può essere utile suddividere i requisiti in attributi necessari per l'autenticazione (ad esempio, loginName ed email), attributi necessari per gestire il ciclo di vita dell'utente (ad esempio, status/active) e altri attributi necessari per il funzionamento dell'applicazione specifica (ad esempio, manager e tag).
  2. Controllare se tali attributi sono già definiti nello schema utente di base o nello schema utente aziendale. Se gli schemi utente di base o aziendale non coprono attributi necessari, si dovrà definire un'estensione per lo schema utente che includa tali attributi. Nell'esempio seguente è stata aggiunta un'estensione all'utente per consentire il provisioning di un "tag" per un utente. È preferibile iniziare solo con gli schemi utente di base e aziendale ed espandersi con altri schemi personalizzati in un secondo momento.  
  3. Eseguire il mapping degli attributi SCIM agli attributi utente in Azure AD. Se uno degli attributi definiti nell'endpoint SCIM non ha una controparte chiara nello schema utente di Azure AD, è probabile che nella maggior parte dei tenant i dati non vengano archiviati nell'oggetto utente. Valutare se l'attributo possa essere facoltativo per la creazione di un utente. Se l'attributo è fondamentale per il funzionamento dell'applicazione, indicare all'amministratore del tenant come estendere lo schema o usare un attributo di estensione, come illustrato di seguito per la proprietà "tag".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabella 1: Definire gli attributi necessari 
| Passaggio 1: Determinare gli attributi richiesti dall'app| Passaggio 2: Eseguire il mapping dei requisiti dell'app allo standard SCIM| Passaggio 3: Eseguire il mapping degli attributi SCIM agli attributi di Azure AD|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|workMail|emails[type eq "work"].value|Posta elettronica|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|active|isSoftDeleted (valore calcolato non archiviato per l'utente)|

Lo schema definito sopra verrà rappresentato con il payload JSON riportato di seguito. Si noti che, oltre agli attributi necessari per l'applicazione, la rappresentazione JSON include gli attributi "id", "externalId" e "meta" obbligatori.

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

### <a name="table-2-default-user-attribute-mapping"></a>Tabella 2: Mapping predefinito degli attributi utente
È quindi possibile usare la tabella seguente per comprendere come può essere eseguito il mapping degli attributi richiesti dall'applicazione a un attributo in Azure AD e nell'RFC di SCIM. È possibile [personalizzare](customize-application-attributes.md) il mapping degli attributi tra Azure AD e l'endpoint SCIM. Si noti che non è necessario supportare sia utenti che gruppi o tutti gli attributi riportati di seguito. Si tratta di informazioni di riferimento che illustrano come viene in genere eseguito il mapping degli attributi di Azure AD alle proprietà del protocollo SCIM. 

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


### <a name="table-3-default-group-attribute-mapping"></a>Tabella 3: Mapping predefinito degli attributi dei gruppi

| Gruppo di Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| Membri di |Membri di |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

Nell'RFC di SCIM sono definiti diversi endpoint. È possibile iniziare con l'endpoint /User e quindi espandersi. L'endpoint/Schemas è utile quando si usano attributi personalizzati o se lo schema viene modificato frequentemente. Consente a un client di recuperare automaticamente lo schema più aggiornato. L'endpoint/Bulk è particolarmente utile quando si supportano gruppi. La tabella seguente descrive i vari endpoint definiti nello standard SCIM. L'endpoint/Schemas è utile quando si usano attributi personalizzati o se lo schema viene modificato frequentemente. Consente a un client di recuperare automaticamente lo schema più aggiornato. L'endpoint/Bulk è particolarmente utile quando si supportano gruppi. La tabella seguente descrive i vari endpoint definiti nello standard SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabella 4: Determinare gli endpoint da sviluppare
|ENDPOINT|DESCRIZIONE|
|--|--|
|/User|Eseguire operazioni CRUD su un oggetto utente.|
|/Group|Eseguire operazioni CRUD su un oggetto gruppo.|
|/ServiceProviderConfig|Offre informazioni dettagliate sulle funzionalità dello standard SCIM supportate, ad esempio sulle risorse supportate e sul metodo di autenticazione.|
|/ResourceTypes|Specifica i metadati relativi a ogni risorsa.|
|/Schemas|Il set di attributi supportato da ogni client e provider di servizi può variare. Un provider di servizi potrebbe includere "name", "title" e "emails", mentre un altro usa "name", "title" e "phoneNumbers". L'endpoint degli schemi consente l'individuazione degli attributi supportati.|
|/Bulk|Le operazioni in blocco consentono di eseguire azioni su una grande raccolta di oggetti risorsa in un'unica operazione, come ad esempio aggiornare le appartenenze per un gruppo di grandi dimensioni.|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Passaggio 2: Comprendere l'implementazione SCIM di Azure AD
> [!IMPORTANT]
> L'ultimo aggiornamento del comportamento dell'implementazione SCIM di Azure AD è stato eseguito il 18 dicembre 2018. Per informazioni sulle modifiche, vedere [Conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Se si crea un'applicazione che supporta un'API SCIM 2.0 di gestione degli utenti, questa sezione offre una descrizione dettagliata del modo in cui viene implementato il client SCIM Azure AD. Illustra anche come modellare la gestione delle richieste del protocollo SCIM e le risposte. Dopo aver implementato l'endpoint SCIM, è possibile testarlo seguendo la procedura descritta nella sezione precedente.

Nell'ambito della [specifica del protocollo SCIM 2.0](http://www.simplecloud.info/#Specification), l'applicazione deve soddisfare i requisiti seguenti:

* Supportare la creazione di utenti e facoltativamente anche di gruppi, come indicato nella [sezione 3.3 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Supportare la modifica di utenti o gruppi con richieste PATCH, come indicato nella [sezione 3.5.2 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Supportare il recupero di una risorsa nota per un utente o un gruppo creato in precedenza, come indicato nella [sezione 3.4.1 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Supportare l'esecuzione di query su utenti o gruppi, come indicato nella [sezione 3.4.2 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Per impostazione predefinita, gli utenti vengono recuperati in base al valore di `id` e le query vengono eseguite in base a `username` e `externalid` per gli utenti e in base a `displayName` per i gruppi.  
* Supportare query sugli utenti in base all'ID o al manager, come indicato nella sezione 3.4.2 del protocollo SCIM.  
* Supportare query sui gruppi in base all'ID e ai membri, come indicato nella sezione 3.4.2 del protocollo SCIM.  
* Accettare un singolo token di connessione per l'autenticazione e l'autorizzazione di Azure AD per l'applicazione.

Quando si implementa un endpoint SCIM, per garantire la compatibilità con Azure AD seguire queste linee guida generali:

* `id` è una proprietà obbligatoria per tutte le risorse. In tutte le risposte che restituiscono una risorsa deve essere garantita la presenza di questa proprietà per ogni risorsa, tranne in caso di `ListResponse` con zero membri.
* La risposta a una richiesta di query/filtro dovrà essere sempre un messaggio `ListResponse`.
* I gruppi sono facoltativi, ma sono supportati solo se l'implementazione di SCIM supporta le richieste PATCH.
* Non è necessario includere l'intera risorsa nella risposta PATCH.
* Microsoft Azure AD usa solo gli operatori seguenti:  
    - `eq`
    - `and`
* Non richiedere una corrispondenza con distinzione tra maiuscole e minuscole negli elementi strutturali in SCIM, in particolare nei valori delle operazioni `op` di PATCH, come indicato in https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD genera i valori di "op" `Add`, `Replace` e `Remove`.
* Microsoft Azure AD effettua richieste per recuperare un utente e un gruppo casuali e verificare così che l'endpoint e le credenziali siano validi. Questa operazione viene eseguita anche come parte del flusso **Test connessione** nel [portale di Azure](https://portal.azure.com). 
* L'attributo per cui possono essere eseguite query sulle risorse deve essere impostato come attributo corrispondente nell'applicazione nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [Personalizzazione dei mapping degli attributi del provisioning utenti](customize-application-attributes.md).

### <a name="user-provisioning-and-deprovisioning"></a>Provisioning e deprovisioning utenti

La figura seguente illustra i messaggi inviati da Azure Active Directory a un servizio SCIM per gestire il ciclo di vita di un utente nell'archivio identità dell'applicazione.  

![Visualizzazione della sequenza di provisioning e deprovisioning utenti](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sequenza di provisioning e deprovisioning utenti*

### <a name="group-provisioning-and-deprovisioning"></a>Provisioning e deprovisioning di gruppi

Il provisioning e il deprovisioning di gruppi sono facoltativi. Se sono implementati e abilitati, Azure AD invia messaggi a un servizio SCIM per gestire il ciclo di vita di un utente nell'archivio identità dell'applicazione come illustrato nella figura seguente.  Questi messaggi si differenziano dai messaggi relativi agli utenti in due modi:

* Le richieste per il recupero di gruppi specificano che l'attributo members deve essere escluso da qualsiasi risorsa fornita in risposta alla richiesta.  
* Le richieste per determinare se un attributo reference ha un determinato valore sono richieste relative all'attributo members.  

![Visualizzazione della sequenza di provisioning e deprovisioning di gruppi](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sequenza di provisioning e deprovisioning di gruppi*

### <a name="scim-protocol-requests-and-responses"></a>Richieste e risposte del protocollo SCIM
Questa sezione contiene esempi delle richieste SCIM generate dal client SCIM Azure AD e delle risposte previste. Per ottenere risultati ottimali, è consigliare creare il codice dell'app in modo da gestire tali richieste in questo formato e generare le risposte previste.

> [!IMPORTANT]
> Per informazioni su come e quando il servizio di provisioning utenti di Azure AD genera le operazioni descritte di seguito, vedere la sezione [Cicli di provisioning: iniziale e incrementale](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Come funziona il provisioning](how-provisioning-works.md).

[Operazioni relative agli utenti](#user-operations)
  - [Creare un utente](#create-user) ([richiesta](#request) / [risposta](#response))
  - [Ottenere un utente](#get-user) ([richiesta](#request-1) / [risposta](#response-1))
  - [Ottenere un utente tramite query](#get-user-by-query) ([richiesta](#request-2) / [risposta](#response-2))
  - [Ottenere un utente tramite query, zero risultati](#get-user-by-query---zero-results) ([richiesta](#request-3)
/ [risposta](#response-3))
  - [Aggiornare un utente [proprietà multivalore]](#update-user-multi-valued-properties) ([richiesta](#request-4) /  [risposta](#response-4))
  - [Aggiornare un utente [proprietà a valore singolo]](#update-user-single-valued-properties) ([richiesta](#request-5)
/ [risposta](#response-5)) 
  - [Disabilitare un utente](#disable-user) ([richiesta](#request-14) / 
[risposta](#response-14))
  - [Eliminare un utente](#delete-user) ([richiesta](#request-6) / 
[risposta](#response-6))


[Operazioni relative ai gruppi](#group-operations)
  - [Creare un gruppo](#create-group) ([richiesta](#request-7) / [risposta](#response-7))
  - [Ottenere un gruppo](#get-group) ([richiesta](#request-8) / [risposta](#response-8))
  - [Ottenere un gruppo in base a displayName](#get-group-by-displayname) ([richiesta](#request-9) / [risposta](#response-9))
  - [Aggiornare un gruppo [attributi non relativi ai membri]](#update-group-non-member-attributes) ([richiesta](#request-10) /
 [risposta](#response-10))
  - [Aggiornare un gruppo [aggiungere membri]](#update-group-add-members) ([richiesta](#request-11) /
[risposta](#response-11))
  - [Aggiornare un gruppo [rimuovere membri]](#update-group-remove-members) ([richiesta](#request-12) /
[risposta](#response-12))
  - [Eliminare un gruppo](#delete-group) ([richiesta](#request-13) /
[risposta](#response-13))

### <a name="user-operations"></a>Operazioni relative agli utenti

* È possibile eseguire query sugli utenti in base agli attributi `userName` o `email[type eq "work"]`.  

#### <a name="create-user"></a>Crea utente

###### <a name="request"></a>Richiesta

*POST /Users*
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

*HTTP/1.1 201 (Creato)*
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

#### <a name="get-user"></a>Ottenere un utente

###### <a name="request"></a><a name="request-1"></a>Richiesta
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Risposta (utente trovato)
*HTTP/1.1 200 (OK)*
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
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Risposta (utente non trovato; si noti che è obbligatorio solo status, non detail)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Ottenere un utente tramite query

##### <a name="request"></a><a name="request-2"></a>Richiesta

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Risposta

*HTTP/1.1 200 (OK)*
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

#### <a name="get-user-by-query---zero-results"></a>Ottenere un utente tramite query, zero risultati

##### <a name="request"></a><a name="request-3"></a>Richiesta

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a><a name="response-3"></a>Risposta

*HTTP/1.1 200 (OK)*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Aggiornare un utente [proprietà multivalore]

##### <a name="request"></a><a name="request-4"></a>Richiesta

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a><a name="response-4"></a>Risposta

*HTTP/1.1 200 (OK)*
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

#### <a name="update-user-single-valued-properties"></a>Aggiornare un utente [proprietà a valore singolo]

##### <a name="request"></a><a name="request-5"></a>Richiesta

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-5"></a>Risposta

*HTTP/1.1 200 (OK)*
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

### <a name="disable-user"></a>Disabilitare un utente

##### <a name="request"></a><a name="request-14"></a>Richiesta

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-14"></a>Risposta

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

##### <a name="response"></a><a name="response-6"></a>Risposta

*HTTP/1.1 204 (Nessun contenuto)*

### <a name="group-operations"></a>Operazioni relative ai gruppi

* I gruppi dovranno essere sempre creati con un elenco di membri vuoto.
* È possibile eseguire query sui gruppi in base all'attributo `displayName`.
* La richiesta PATCH di aggiornamento del gruppo dovrà restituire una risposta con codice *HTTP 204 (Nessun contenuto)* . Non è consigliabile restituire un corpo con un elenco di tutti i membri.
* Non è necessario supportare la restituzione di tutti i membri del gruppo.

#### <a name="create-group"></a>Creare un gruppo

##### <a name="request"></a><a name="request-7"></a>Richiesta

*POST /Groups HTTP/1.1*
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

##### <a name="response"></a><a name="response-7"></a>Risposta

*HTTP/1.1 201 (Creato)*
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

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Risposta
*HTTP/1.1 200 (OK)*
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

#### <a name="get-group-by-displayname"></a>Ottenere un gruppo in base a displayName

##### <a name="request"></a><a name="request-9"></a>Richiesta
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Risposta

*HTTP/1.1 200 (OK)*
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

#### <a name="update-group-non-member-attributes"></a>Aggiornare un gruppo [attributi non relativi ai membri]

##### <a name="request"></a><a name="request-10"></a>Richiesta

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Risposta

*HTTP/1.1 204 (Nessun contenuto)*

### <a name="update-group-add-members"></a>Aggiornare un gruppo [aggiungere membri]

##### <a name="request"></a><a name="request-11"></a>Richiesta

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Risposta

*HTTP/1.1 204 (Nessun contenuto)*

#### <a name="update-group-remove-members"></a>Aggiornare un gruppo [rimuovere membri]

##### <a name="request"></a><a name="request-12"></a>Richiesta

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Risposta

*HTTP/1.1 204 (Nessun contenuto)*

#### <a name="delete-group"></a>Eliminare un gruppo

##### <a name="request"></a><a name="request-13"></a>Richiesta

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Risposta

*HTTP/1.1 204 (Nessun contenuto)*

### <a name="security-requirements"></a>Requisiti di sicurezza
**Versioni del protocollo TLS**

Le uniche versioni del protocollo TLS accettabili sono TLS 1.2 e TLS 1.3. Non sono consentite altre versioni di TLS, né alcuna versione di SSL. 
- Le chiavi RSA devono essere di almeno 2.048 bit.
- Le chiavi ECC devono essere di almeno 256 bit ed essere generate con una curva ellittica approvata.


**Lunghezza delle chiavi**

Tutti i servizi devono usare certificati X.509 generati con chiavi crittografiche di lunghezza sufficiente, come illustrato di seguito.

**Pacchetti di crittografia**

Tutti i servizi devono essere configurati per usare i pacchetti di crittografia seguenti, nell'ordine esatto in cui sono specificati. Si noti che se si ha solo un certificato RSA, l'installazione dei pacchetti di crittografia ECDSA non avrà alcun effetto. </br>

Livello minimo dei pacchetti di crittografia TLS 1.2:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Passaggio 3: Creare un endpoint SCIM

Dopo aver progettato lo schema e aver compreso l'implementazione SCIM di Azure AD, si può iniziare a sviluppare l'endpoint SCIM. Anziché iniziare da zero e creare autonomamente l'intera implementazione, è possibile usare diverse librerie SCIM open source pubblicate dalla community SCIM.

Una risorsa di questo tipo, che consente di accelerare le attività di sviluppo, è il [codice di riferimento](https://aka.ms/SCIMReferenceCode) .NET Core open source pubblicato dal team di provisioning di Azure AD. Dopo aver creato l'endpoint SCIM, sarà opportuno testarlo. È possibile usare la raccolta di [test Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) offerti come parte del codice di riferimento oppure eseguire le richieste e le risposte di esempio riportate [sopra](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

   > [!Note]
   > Il codice di riferimento è utile per iniziare a creare l'endpoint SCIM e viene fornito "COSÌ COM'È". Sono benvenuti i contributi della community per la compilazione e la gestione del codice.

La soluzione è costituita da due progetti: _Microsoft.SCIM_ e _Microsoft.SCIM.WebHostSample_.

Il progetto _Microsoft.SCIM_ è la libreria che definisce i componenti del servizio Web conforme alla specifica SCIM. Dichiara l'interfaccia _Microsoft.SCIM.IProvider_ e le richieste vengono convertite in chiamate ai metodi del provider, che saranno programmati per eseguire operazioni su un archivio identità.

![Dettaglio: conversione di una richiesta in chiamate ai metodi del provider](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Il progetto _Microsoft.SCIM.WebHostSample_ è un'applicazione Web ASP.NET Core di Visual Studio basata sul modello _Vuoto_. Questo consente di distribuire il codice di esempio come codice autonomo, ospitato in contenitori o in Internet Information Services. Viene implementata anche l'interfaccia _Microsoft.SCIM.IProvider_, mantenendo le classi in memoria come archivio identità di esempio.

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

### <a name="building-a-custom-scim-endpoint"></a>Creazione di un endpoint SCIM personalizzato

Il servizio SCIM deve avere un indirizzo HTTP e un certificato di autenticazione server la cui autorità di certificazione radice è uno dei nomi seguenti:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK include un certificato di sviluppo HTTPS utilizzabile durante lo sviluppo, che viene installato durante il completamento dell'installazione. A seconda di come viene eseguita, l'applicazione Web ASP.NET Core sarà in ascolto di una porta diversa.

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Per altre informazioni su HTTPS in ASP.NET Core, usare il collegamento seguente: [Imporre HTTPS in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Gestione dell'autenticazione dell'endpoint

Le richieste da Azure Active Directory includono un token di connessione OAuth 2.0. Qualsiasi servizio che riceve la richiesta deve autenticare l'autorità emittente come Azure Active Directory per il tenant di Azure Active Directory previsto.

Nel token, l'autorità emittente è identificata da un'attestazione iss, ad esempio `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`. In questo esempio, l'indirizzo di base del valore attestazione, `https://sts.windows.net`, identifica Azure Active Directory come autorità emittente, mentre il segmento dell'indirizzo relativo, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, è un identificatore univoco del tenant di Azure Active Directory per cui è stato rilasciato il token.

I destinatari del token saranno l'ID modello di applicazione per l'applicazione nella raccolta. Ogni applicazione registrata in un singolo tenant potrebbe ricevere la stessa attestazione `iss` con le richieste SCIM. L'ID modello di applicazione per tutte le app personalizzate è _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_. Il token generato dal servizio di provisioning di Azure AD dovrà essere usato solo a scopo di test, non in ambienti di produzione.

Nel codice di esempio, le richieste vengono autenticate usando il pacchetto Microsoft.AspNetCore.Authentication.JwtBearer. Il codice seguente impone l'autenticazione delle richieste per qualsiasi endpoint del servizio con il token di connessione rilasciato da Azure Active Directory per un tenant specificato:

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

È necessario un token di connessione anche per usare i [test Postman](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) forniti ed eseguire il debug locale con localhost. Il codice di esempio usa ambienti ASP.NET Core per modificare le opzioni di autenticazione durante la fase di sviluppo e abilitare l'uso di un token autofirmato.

Per altre informazioni sull'uso di più ambienti in ASP.NET Core, usare il collegamento seguente: [Usare più ambienti in ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

Il codice seguente impone l'autenticazione delle richieste per qualsiasi endpoint del servizio con un token di connessione firmato con una chiave personalizzata:

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

Inviare una richiesta GET al controller dei token per ottenere un token di connessione valido. Il metodo _GenerateJSONWebToken_ è responsabile della creazione di un token corrispondente ai parametri configurati per lo sviluppo:

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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Gestione del provisioning e del deprovisioning di utenti

***Esempio 1. Eseguire query sul servizio per trovare un utente corrispondente***

Azure Active Directory esegue query nel servizio per trovare un utente con valore dell'attributo externalId corrispondente al valore dell'attributo mailNickname di un utente in Azure AD. La query viene espressa come richiesta HTTP (Hypertext Transfer Protocol) come in questo esempio, in cui jyoung è un esempio di mailNickname di un utente in Azure Active Directory.

>[!NOTE]
> Questo è solo un esempio. Non tutti gli utenti avranno un attributo mailNickname e il valore di un utente potrebbe non essere univoco nella directory. L'attributo usato per la corrispondenza (che in questo caso è externalId), inoltre, è configurabile nei [mapping degli attributi di Azure AD](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Nel codice di esempio, la richiesta viene convertita in una chiamata al metodo QueryAsync del provider del servizio. Ecco la firma del metodo: 

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

Nella query di esempio, per un utente con un determinato valore dell'attributo externalId, i valori degli argomenti passati al metodo QueryAsync sono i seguenti:

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***Esempio 2. Effettuare il provisioning di un utente***

Se la risposta a una query sul servizio Web per trovare un utente con valore dell'attributo externalId corrispondente al valore dell'attributo mailNickname di un utente non restituisce alcun utente, Azure Active Directory richiede che il servizio effettui il provisioning di un utente corrispondente a quello in Azure Active Directory.  Ecco un esempio di questa richiesta: 

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

Nel codice di esempio, la richiesta viene convertita in una chiamata al metodo CreateAsync del provider del servizio. Ecco la firma del metodo: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

In una richiesta di provisioning di un utente, il valore dell'argomento resource è un'istanza della classe Microsoft.SCIM.Core2EnterpriseUser, definita nella libreria Microsoft.SCIM.Schemas.  Se la richiesta di provisioning dell'utente ha esito positivo, è previsto che l'implementazione del metodo restituisca un'istanza della classe Microsoft.SCIM.Core2EnterpriseUser, con il valore della proprietà Identifier impostato sull'identificatore univoco dell'utente di cui è appena stato effettuato il provisioning.  

***Esempio 3. Eseguire query sullo stato corrente di un utente*** 

Per aggiornare un utente la cui esistenza è nota in un archivio identità gestito da SCIM, Azure Active Directory richiede al servizio lo stato corrente dell'utente con una richiesta simile alla seguente: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

Nel codice di esempio, la richiesta viene convertita in una chiamata al metodo RetrieveAsync del provider del servizio. Ecco la firma del metodo: 

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

***Esempio 4. Eseguire query sul valore di un attributo di riferimento da aggiornare*** 

Se è necessario aggiornare un attributo di riferimento, Azure Active Directory esegue query sul servizio per determinare se il valore corrente dell'attributo nell'archivio identità gestito dal servizio corrisponde già al valore di tale attributo in Azure Active Directory. Per gli utenti, l'unico attributo il cui valore corrente viene sottoposto a query con questa modalità è l'attributo manager. L'esempio illustra una richiesta per determinare se l'attributo manager di un oggetto utente ha attualmente un determinato valore. Nel codice di esempio, la richiesta viene convertita in una chiamata al metodo QueryAsync del provider del servizio. Il valore delle proprietà dell'oggetto fornito come valore dell'argomento parameters è analogo al seguente: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Il valore dell'indice x può essere 0 e il valore dell'indice y può essere 1 oppure il valore di x può essere 1 e il valore di y può essere 0, a seconda dell'ordine delle espressioni del parametro di filtro della query.   

***Esempio 5. Inviare una richiesta da Azure AD a un servizio SCIM per aggiornare un utente*** 

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

Nel codice di esempio, la richiesta viene convertita in una chiamata al metodo UpdateAsync del provider del servizio. Ecco la firma del metodo: 

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

***Esempio 6. Effettuare il deprovisioning di un utente***

Per effettuare il deprovisioning di un utente da un archivio identità gestito da un servizio SCIM, Azure AD invia una richiesta come la seguente:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

Nel codice di esempio, la richiesta viene convertita in una chiamata al metodo DeleteAsync del provider del servizio. Ecco la firma del metodo: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Nell'esempio di richiesta per il deprovisioning di un utente, l'oggetto specificato come valore dell'argomento resourceIdentifier ha i valori delle proprietà seguenti. 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Passaggio 4: Integrare l'endpoint SCIM con il client SCIM Azure AD

È possibile configurare Azure AD per il provisioning automatico di utenti e gruppi assegnati nelle applicazioni che implementano un profilo specifico del [protocollo SCIM 2.0](https://tools.ietf.org/html/rfc7644). Le specifiche del profilo sono documentate in [Passaggio 2: Comprendere l'implementazione SCIM di Azure AD](#step-2-understand-the-azure-ad-scim-implementation).

Verificare la conformità ai requisiti sopra riportati con il provider dell'applicazione o consultando la documentazione fornita dal provider.

> [!IMPORTANT]
> L'implementazione SCIM di Azure AD è basata sul servizio di provisioning utenti di Azure AD, progettato per mantenere gli utenti costantemente sincronizzati tra Azure AD e l'applicazione di destinazione, e implementa un set molto specifico di operazioni standard. È importante conoscere questi comportamenti per comprendere il comportamento del client SCIM Azure AD. Per altre informazioni, vedere la sezione [Cicli di provisioning: iniziale e incrementale](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [Come funziona il provisioning](how-provisioning-works.md).

### <a name="getting-started"></a>Introduzione

Le applicazioni che supportano il profilo SCIM descritto in questo articolo possono essere connesse ad Azure Active Directory usando la funzionalità "Applicazione non nella raccolta" nella raccolta di applicazioni di Azure AD. Una volta stabilita la connessione, Azure AD esegue un processo di sincronizzazione ogni 40 minuti in cui interroga l'endpoint SCIM dell'applicazione in merito agli utenti e ai gruppi assegnati e li crea o li modifica in base alle istruzioni di assegnazione.

**Per connettere un'applicazione che supporta SCIM:**

1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com). Si noti che è possibile ottenere l'accesso a una versione di valutazione gratuita per Azure Active Directory con licenze P2 iscrivendosi al [programma per sviluppatori](https://developer.microsoft.com/office/dev-program).
2. Selezionare **Applicazioni aziendali** nel riquadro sinistro. Verrà visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
3. Selezionare **+ Nuova applicazione** > **Tutte** > **Applicazione non nella raccolta**.
4. Immettere un nome per l'applicazione e selezionare **Aggiungi** per creare un oggetto app. La nuova app verrà aggiunta all'elenco delle applicazioni aziendali e verrà visualizzata la schermata di gestione dell'app.

   ![Screenshot che mostra la raccolta di applicazioni di Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Raccolta di applicazioni di Azure AD*

5. Nella schermata di gestione dell'app selezionare **Provisioning** nel pannello sinistro.
6. Nel menu **Modalità di provisioning** selezionare **Automatica**.

   ![Esempio: pagina Provisioning di un'app nel portale di Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Configurazione del provisioning nel portale di Azure*

7. Nel campo **URL tenant** immettere l'URL dell'endpoint SCIM dell'applicazione. Esempio: `https://api.contoso.com/scim/`
8. Se l'endpoint SCIM richiede un token di connessione OAuth da un'autorità di certificazione diversa da Azure AD, copiare il token di connessione OAuth nel campo **Token segreto** facoltativo. Se questo campo viene lasciato vuoto, Azure AD include in ogni richiesta un token di connessione OAuth rilasciato da Azure AD. Le app che usano Azure AD come provider di identità possono convalidare il token rilasciato da Azure AD. 
   > [!NOTE]
   > ***Non*** è consigliabile lasciare vuoto questo campo e usare un token generato da Azure AD. Questa opzione è disponibile principalmente a scopo di test.
9. Selezionare **Test connessione** affinché Azure Active Directory tenti la connessione all'endpoint SCIM. Se il tentativo non riesce, verranno visualizzate le informazioni sull'errore.  

    > [!NOTE]
    > **Test connessione** esegue query sull'endpoint SCIM per cercare un utente che non esiste usando un GUID casuale come proprietà corrispondente selezionata nella configurazione di Azure AD. La risposta corretta prevista è HTTP 200 OK con un messaggio ListResponse SCIM vuoto.

10. Se il tentativo di connessione all'applicazione ha esito positivo, selezionare **Salva** per salvare le credenziali di amministratore.
11. Nella sezione **Mapping** sono disponibili due set di [mapping degli attributi](customize-application-attributes.md) selezionabili: uno per gli oggetti utente e uno per gli oggetti gruppo. Selezionare ognuno dei due set per esaminare gli attributi sincronizzati da Active Directory di Azure con l'app. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli utenti e i gruppi nell'app per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    > [!NOTE]
    > Facoltativamente, è possibile disattivare la sincronizzazione degli oggetti gruppo disabilitando il mapping relativo ai gruppi.

12. In **Impostazioni** il campo **Ambito** definisce gli utenti e i gruppi che devono essere sincronizzati. Selezionare **Sincronizza solo utenti e gruppi assegnati** (scelta consigliata) per sincronizzare solo gli utenti e i gruppi assegnati nella scheda **Utenti e gruppi**.
13. Al termine della configurazione, impostare **Stato del provisioning** su **Sì**.
14. Selezionare **Salva** per avviare il servizio di provisioning di Azure AD.
15. Se si sceglie di sincronizzare solo gli utenti e i gruppi assegnati (scelta consigliata), assicurarsi di selezionare la scheda **Utenti e gruppi** e di assegnare gli utenti o i gruppi da sincronizzare.

Dopo l'avvio del ciclo iniziale, è possibile selezionare la scheda **Log di provisioning** nel pannello sinistro per monitorare lo stato di avanzamento, visualizzando tutte le azioni eseguite dal servizio di provisioning sull'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

> [!NOTE]
> Il ciclo iniziale richiede più tempo rispetto alle sincronizzazioni successive, che vengono eseguite circa ogni 40 minuti fintanto che il servizio è in esecuzione.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Passaggio 5: Pubblicare l'applicazione nella raccolta di applicazioni di Azure AD

Se si crea un'applicazione che verrà usata da più tenant, è possibile renderla disponibile nella raccolta di applicazioni di Azure AD. In questo modo, le organizzazioni potranno individuare l'applicazione e configurare il provisioning con facilità. Pubblicare l'app nella raccolta di Azure AD e rendere il provisioning disponibile ad altri è semplice. Consultare i passaggi [qui](../develop/howto-app-gallery-listing.md) Microsoft collaborerà all'integrazione dell'applicazione nella raccolta, al test dell'endpoint e al rilascio di [documentazione](../saas-apps/tutorial-list.md) sull'onboarding utilizzabile dai clienti. 

### <a name="gallery-onboarding-checklist"></a>Elenco di controllo per l'onboarding nella raccolta
Seguire questo elenco di controllo per completare rapidamente l'onboarding dell'applicazione e offrire ai clienti un'esperienza di distribuzione ottimale. Queste informazioni verranno raccolte durante l'onboarding nella raccolta. 
> [!div class="checklist"]
> * Supportare un endpoint [SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) per gli utenti e per i gruppi (ne è necessario uno solo, ma è consigliabile usare entrambi)
> * Supportare almeno 25 richieste al secondo per tenant (obbligatorio)
> * Stabilire i contatti di progettazione e supporto tecnico per assistere i clienti dopo l'onboarding nella raccolta (obbligatorio)
> * Usare 3 credenziali di test non in scadenza per l'applicazione (obbligatorio)
> * Supportare la concessione del codice di autorizzazione OAuth o un token di lunga durata come descritto di seguito (obbligatorio)
> * Stabilire un punto di contatto per la progettazione e il supporto tecnico per supportare i clienti dopo l'onboarding nella raccolta (obbligatorio)
> * Supportare l'aggiornamento di più appartenenze ai gruppi con una singola operazione PATCH (consigliato) 
> * Documentare pubblicamente l'endpoint SCIM (consigliato) 
> * [Supportare l'individuazione dello schema](https://tools.ietf.org/html/rfc7643#section-6) (consigliato)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorizzazione per i connettori di provisioning nella raccolta di applicazioni
La specifica SCIM non definisce uno schema specifico di SCIM per l'autenticazione e l'autorizzazione. Si basa sull'uso di standard di settore esistenti. Il client di provisioning Azure AD supporta due metodi di autorizzazione per le applicazioni nella raccolta. 

|Metodo di autorizzazione|Vantaggi|Svantaggi|Supporto|
|--|--|--|--|
|Nome utente e password (non consigliato o supportato da Azure AD)|Facile da implementare|Non sicuro. [La pa$$word è irrilevante](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984).|Supportato caso per caso per le app della raccolta. Non supportato per le app non nella raccolta.|
|Token di connessione di lunga durata|I token di lunga durata non richiedono che sia presente un utente. Possono essere usati facilmente dagli amministratori durante la configurazione del provisioning.|I token di lunga durata possono essere difficili da condividere con un amministratore senza usare metodi non sicuri come la posta elettronica. |Supportato sia per le app della raccolta che per quelle non nella raccolta. |
|Concessione del codice di autorizzazione OAuth|I token di accesso hanno una durata molto più breve rispetto alle password e un meccanismo di aggiornamento automatico che non è disponibile per i token di connessione di lunga durata.  Durante l'autorizzazione iniziale deve essere presente un utente reale e questo offre un livello di affidabilità aggiuntivo. |Deve essere presente un utente. Se l'utente lascia l'organizzazione, il token non è valido ed è necessario completare nuovamente l'autorizzazione.|Supportato per le app della raccolta. È in corso di implementazione il supporto per le app non nella raccolta.|
|Concessione di credenziali client OAuth|I token di accesso hanno una durata molto più breve rispetto alle password e un meccanismo di aggiornamento automatico che non è disponibile per i token di connessione di lunga durata. Sia la concessione del codice di autorizzazione che la concessione delle credenziali client creano lo stesso tipo di token di accesso, quindi il passaggio tra questi metodi è trasparente per l'API.  Il provisioning può essere completamente automatizzato e i nuovi token possono essere richiesti automaticamente senza interazione dell'utente. ||Non supportato sia per le app della raccolta che per quelle non nella raccolta. Il supporto è incluso nel backlog di Microsoft.|

[!NOTE] Non è consigliabile lasciare vuoto il campo del token nell'interfaccia utente dell'app personalizzata per la configurazione del provisioning di Azure AD. Il token generato è disponibile principalmente a scopo di test.

**Flusso di concessione del codice di autorizzazione OAuth:** il servizio di provisioning supporta la [concessione del codice di autorizzazione](https://tools.ietf.org/html/rfc6749#page-24). Dopo l'invio della richiesta di pubblicazione dell'app nella raccolta, il team Microsoft collaborerà alla raccolta delle informazioni riportate di seguito.
*  URL di autorizzazione: URL usato dal client per ottenere l'autorizzazione dal proprietario della risorsa tramite il reindirizzamento dell'agente utente. L'utente viene reindirizzato a questo URL per l'autorizzazione dell'accesso. Si noti che questo URL non è attualmente configurabile per tenant.
*  URL per lo scambio di token: URL usato dal client per scambiare una concessione di autorizzazione con un token di accesso, in genere con l'autenticazione client. Si noti che questo URL non è attualmente configurabile per tenant.
*  ID client: il server di autorizzazione rilascia al client registrato un identificatore client, ossia una stringa univoca che rappresenta le informazioni di registrazione fornite dal client.  L'identificatore client non è un segreto. Viene esposto al proprietario della risorsa e **non deve** essere usato da solo per l'autenticazione client.  
*  Segreto client: il segreto client è un segreto generato dal server di autorizzazione. Deve essere un valore univoco noto solo al server di autorizzazione. 

OAuth v1 non è supportato a causa dell'esposizione del segreto client. OAuth v2 è supportato.  

Procedure consigliate (ma non obbligatorie):
* Supportare più URL di reindirizzamento. Gli amministratori possono configurare il provisioning sia da "portal.azure.com" che da "aad.portal.azure.com". Il supporto di più URL di reindirizzamento garantirà agli utenti la possibilità di ottenere l'autorizzazione dell'accesso da uno dei due portali.
* Supportare più segreti per garantirne un rinnovo ottimale, senza tempi di inattività. 

**Token di connessione OAuth di lunga durata:** se l'applicazione non supporta il flusso di concessione del codice di autorizzazione OAuth, è anche possibile generare un token di connessione OAuth di lunga durata che potrà essere usato da un amministratore per configurare l'integrazione del provisioning. Il token deve essere perpetuo, altrimenti il processo di provisioning verrà messo [in quarantena](application-provisioning-quarantine-status.md) alla scadenza del token. Il token deve essere di dimensioni inferiori a 1 KB.  

Per altri metodi di autenticazione e autorizzazione, comunicare questa esigenza in [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Elenco di controllo per il lancio go-to-market nella raccolta
Per favorire la conoscenza e la richiesta dell'integrazione congiunta, è consigliabile aggiornare la documentazione esistente e promuovere l'integrazione nei canali di marketing.  Di seguito è riportato un elenco di controllo con un set di attività che è consigliabile completare per supportare il lancio.

* **Preparazione dei team di vendita e supporto tecnico.** Assicurarsi che i team di vendita e supporto tecnico conoscano le funzionalità di integrazione e siano capaci di illustrarle. Informare i team di vendita e supporto tecnico, fornire loro domande frequenti e includere l'integrazione nei materiali di vendita. 
* **Post di blog e/o comunicato stampa.** Creare un post di blog o un comunicato stampa che descriva l'integrazione congiunta, i relativi vantaggi e come iniziare a usarla. [Esempio: comunicato stampa di imprivata su Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft). 
* **Social media.** Sfruttare i social media, come Twitter, Facebook o LinkedIn, per promuovere l'integrazione presso i clienti. Assicurarsi di includere @AzureAD per consentire a Microsoft di fare retweet del post. [Esempio: post di imprivata su Twitter](https://twitter.com/azuread/status/1123964502909779968).
* **Sito Web di marketing.** Creare o aggiornare le pagine di marketing (ad esempio, le pagine sulle integrazioni, sui partner, sui prezzi e così via) per includere la disponibilità dell'integrazione congiunta. [Esempio: pagina relativa alle integrazioni di Pingboard](https://pingboard.com/org-chart-for), [pagina sull'integrazione di Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) e [pagina dei prezzi di Monday.com](https://monday.com/pricing/). 
* **Documentazione tecnica.** Creare un articolo del centro assistenza o documentazione tecnica che illustri ai clienti come iniziare a usare l'integrazione. [Esempio: integrazione di Envoy e Microsoft Azure Active Directory](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
). 
* **Comunicazione ai clienti.** Avvisare i clienti della nuova integrazione tramite la comunicazione ai clienti (newsletter mensili, campagne di posta elettronica, note sulla versione del prodotto). 

## <a name="related-articles"></a>Articoli correlati

* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
* [Personalizzare i mapping degli attributi per il provisioning degli utenti](customize-application-attributes.md)
* [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
* [Filtri di ambito per il provisioning utenti](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notifiche relative al provisioning dell'account](user-provisioning.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
