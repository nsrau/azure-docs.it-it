---
title: Provisioning utenti di SCIM con Azure Active Directory | Microsoft Docs
description: Informazioni su come creare un endpoint SCIM, integrare l'API SCIM con Azure Active Directory e avviare l'automazione del provisioning di utenti e gruppi nelle applicazioni.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: d8bb9b507763c935ab244c42584120a279063954
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195458"
---
# <a name="scim-user-provisioning-with-azure-active-directory-azure-ad"></a>Provisioning utenti di SCIM con Azure Active Directory (Azure AD)

Questo articolo descrive come usare System for Cross-Domain Identity Management ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)) per automatizzare il provisioning e il deprovisioning di utenti e gruppi in un'applicazione. La specifica SCIM fornisce uno schema utente comune per il provisioning. Se usato in combinazione con gli standard di federazione come SAML o OpenID Connect, SCIM offre agli amministratori una soluzione end-to-end basata sugli standard per la gestione degli accessi.

SCIM è una definizione standardizzata di due endpoint: un endpoint/Users e un endpoint/groups. Usa verbi REST comuni per creare, aggiornare ed eliminare oggetti e uno schema predefinito per attributi comuni come nome del gruppo, nome utente, nome, cognome e indirizzo di posta elettronica. Le app che offrono un'API REST SCIM 2,0 possono ridurre o eliminare il dolore dovuto all'uso di un'API proprietaria per la gestione degli utenti. Ad esempio, tutti i client SCIM conformi sanno come creare un POST HTTP di un oggetto JSON nell'endpoint/Users per creare una nuova voce utente. Anziché richiedere un'API leggermente diversa per le stesse azioni di base, le app conformi allo standard SCIM possono sfruttare immediatamente i vantaggi di client, strumenti e codice preesistenti. 

![Provisioning da Azure AD a un'app con SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Lo schema dell'oggetto utente standard e le API REST per la gestione definite in SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) consentono a provider di identità e app di integrarsi più facilmente tra loro. Gli sviluppatori di applicazioni che compilano un endpoint SCIM possono integrarsi con qualsiasi client conforme a SCIM senza dover eseguire operazioni personalizzate.

L'automazione del provisioning in un'applicazione richiede la compilazione e l'integrazione di un endpoint SCIM con la Azure AD conforme a SCIM. Eseguire la procedura seguente per avviare il provisioning di utenti e gruppi nell'applicazione. 
    
  * **[Passaggio 1: progettare lo schema utente e gruppo.](#step-1-design-your-user-and-group-schema)** Identificare gli oggetti e gli attributi necessari per l'applicazione e determinare il modo in cui vengono mappati allo schema utente e gruppo supportato dall'implementazione di Azure AD SCIM.

  * **[Passaggio 2: comprendere l'implementazione di Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Comprendere il modo in cui viene implementato il client di Azure AD SCIM e modellare le risposte e la gestione delle richieste del protocollo SCIM.

  * **[Passaggio 3: compilare un endpoint SCIM.](#step-3-build-a-scim-endpoint)** Un endpoint deve essere compatibile con SCIM 2,0 per l'integrazione con il servizio di provisioning di Azure AD. In alternativa, è possibile usare le librerie di Microsoft Common Language Infrastructure (CLI) ed esempi di codice per compilare l'endpoint. Questi esempi sono solo a fini di riferimento e di testing; è consigliabile evitare di codificare l'app di produzione per prendere una dipendenza.

  * **[Passaggio 4: integrare l'endpoint SCIM con il client SCIM Azure AD.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Se l'organizzazione usa un'applicazione di terze parti che implementa il profilo di SCIM 2,0 supportato da Azure AD, è possibile iniziare immediatamente ad automatizzare il provisioning e il deprovisioning di utenti e gruppi.

  * **[Passaggio 5: pubblicare l'applicazione nella raccolta di applicazioni Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Per i clienti è facile individuare l'applicazione e configurare facilmente il provisioning. 

![Passaggi per l'integrazione di un endpoint SCIM con Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Passaggio 1: progettare lo schema utente e gruppo

Ogni applicazione richiede attributi diversi per creare un utente o un gruppo. Avviare l'integrazione identificando gli oggetti (utenti, gruppi) e gli attributi (nome, Manager, titolo del processo e così via) richiesti dall'applicazione. È quindi possibile usare la tabella seguente per comprendere il modo in cui gli attributi richiesti dall'applicazione potrebbero eseguire il mapping a un attributo in Azure AD e SCIM RFC. Si noti che è possibile [personalizzare](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) la modalità di mapping degli attributi tra Azure ad e l'endpoint SCIM. 

Le risorse utente sono identificate dall'identificatore dello schema, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, incluso in questa specifica del protocollo: https://tools.ietf.org/html/rfc7643.  Il mapping predefinito degli attributi degli utenti in Azure AD agli attributi delle risorse utente è indicato nella tabella 1.  

Le risorse gruppo sono identificate dall'identificatore dello schema, `urn:ietf:params:scim:schemas:core:2.0:Group`. Nella tabella 2 viene illustrato il mapping predefinito degli attributi dei gruppi nel Azure AD agli attributi delle risorse del gruppo.

Si noti che non è necessario supportare utenti e gruppi o tutti gli attributi mostrati di seguito. Si tratta di un riferimento per il modo in cui gli attributi di Azure AD vengono spesso mappati alle proprietà del protocollo SCIM.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabella 1: mapping predefinito degli attributi utente

| Utente Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |ID |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabella 2: mapping predefinito degli attributi gruppo

| Gruppo di Azure Active Directory | urn: IETF: params: SCIM: schemas: Core: 2.0: Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| Membri di |Membri di |
| objectId |ID |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Passaggio 2: comprendere l'implementazione di SCIM Azure AD
> [!IMPORTANT]
> L'ultimo aggiornamento del comportamento dell'implementazione SCIM di Azure AD è stato eseguito il 18 dicembre 2018. Per informazioni sulle modifiche, vedere [Conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Se si compila un'applicazione che supporta un'API di gestione utenti SCIM 2,0, in questa sezione viene descritto in dettaglio il modo in cui viene implementato il client SCIM Azure AD. Viene inoltre illustrato come modellare le risposte e la gestione delle richieste del protocollo SCIM. Una volta implementato l'endpoint SCIM, è possibile testarlo attenendosi alla procedura descritta nella sezione precedente.

All'interno della [specifica del protocollo SCIM 2,0](http://www.simplecloud.info/#Specification), l'applicazione deve soddisfare i requisiti seguenti:

* Supporta la creazione di utenti e, facoltativamente, anche i gruppi, come indicato [nella sezione 3,3 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Supporta la modifica di utenti o gruppi con richieste PATCH, come per [la sezione 3.5.2 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Supporta il recupero di una risorsa nota per un utente o un gruppo creato in precedenza, come per [la sezione 3.4.1 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Supporta l'esecuzione di query su utenti o gruppi, in base alla sezione [3.4.2 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Per impostazione predefinita, gli utenti vengono recuperati dal `id` e sottoposti a query in base ai `username` e `externalid`e i gruppi vengono sottoposti a query per `displayName`.  
* Supporta l'esecuzione di query su User by ID e Manager, come per la sezione 3.4.2 del protocollo SCIM.  
* Supporta l'esecuzione di query sui gruppi in base all'ID e al membro, come indicato nella sezione 3.4.2 del protocollo SCIM.  
* Accetta una singola bearer token per l'autenticazione e l'autorizzazione di Azure AD all'applicazione.

Quando si implementa un endpoint SCIM per garantire la compatibilità con Azure AD, attenersi alle linee guida generali seguenti:

* `id` è una proprietà obbligatoria per tutte le risorse. Ogni risposta che restituisce una risorsa deve garantire che ogni risorsa abbia questa proprietà, ad eccezione di `ListResponse` con zero membri.
* La risposta a una richiesta di query/filtro deve essere sempre un `ListResponse`.
* I gruppi sono facoltativi, ma sono supportati solo se l'implementazione di SCIM supporta le richieste PATCH.
* Non è necessario includere l'intera risorsa nella risposta PATCH.
* Microsoft Azure AD utilizza solo gli operatori seguenti:  
    - `eq`
    - `and`
* Non richiedere una corrispondenza con distinzione tra maiuscole e minuscole negli elementi strutturali in SCIM, in particolare PATCH `op` valori dell'operazione, come definito in https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD emette i valori di "op" come `Add`, `Replace`e `Remove`.
* Microsoft Azure AD esegue richieste per recuperare un utente e un gruppo casuali per verificare che l'endpoint e le credenziali siano validi. Viene anche eseguita come parte del flusso di **test della connessione** nella [portale di Azure](https://portal.azure.com). 
* L'attributo su cui è possibile eseguire query sulle risorse deve essere impostato come attributo corrispondente nell'applicazione nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [personalizzazione dei mapping degli attributi per il provisioning degli utenti](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-deprovisioning"></a>Provisioning e deprovisioning utenti

La figura seguente illustra i messaggi che Azure Active Directory Invia a un servizio SCIM per gestire il ciclo di vita di un utente nell'archivio identità dell'applicazione.  

![Mostra la sequenza di provisioning e deprovisioning utenti][4]<br/>
*Figura 4: sequenza di provisioning e deprovisioning utenti*

### <a name="group-provisioning-and-deprovisioning"></a>Provisioning e deprovisioning di gruppi

Il provisioning e il deprovisioning del gruppo sono facoltativi. Quando implementato e abilitato, nella figura seguente vengono mostrati i messaggi inviati da Azure AD a un servizio SCIM per gestire il ciclo di vita di un gruppo nell'archivio identità dell'applicazione.  Tali messaggi sono diversi dai messaggi sugli utenti in due modi:

* Le richieste di recupero dei gruppi specificano che l'attributo members deve essere escluso da qualsiasi risorsa fornita in risposta alla richiesta.  
* Le richieste per determinare se un attributo reference ha un determinato valore sono richieste relative all'attributo members.  

![Mostra la sequenza di provisioning e deprovisioning del gruppo][5]<br/>
*Figura 5: sequenza di provisioning e deprovisioning del gruppo*

### <a name="scim-protocol-requests-and-responses"></a>Richieste e risposte del protocollo SCIM
Questa sezione fornisce le richieste SCIM di esempio emesse dal client Azure AD SCIM e dalle risposte previste di esempio. Per ottenere risultati ottimali, è necessario codificare l'app in modo che gestisca queste richieste in questo formato e generare le risposte previste.

> [!IMPORTANT]
> Per comprendere come e quando il servizio di provisioning utenti Azure AD emette le operazioni descritte di seguito, vedere [cosa accade durante il provisioning dell'utente](user-provisioning.md#what-happens-during-provisioning)?

[Operazioni utente](#user-operations)
  - [Crea utente](#create-user) ([richiesta](#request) / [risposta](#response))
  - [Ottieni utente](#get-user) ([richiesta](#request-1) / [risposta](#response-1))
  - [Ottenere l'utente in base alla query](#get-user-by-query) ([richiesta](#request-2) / [risposta](#response-2))
  - [Ottenere l'utente in base alla query: risultati zero](#get-user-by-query---zero-results) ([richiesta](#request-3)
/ [risposta](#response-3))
  - [Aggiornare l'utente [proprietà multivalore]](#update-user-multi-valued-properties) ([richiesta](#request-4) /  [risposta](#response-4))
  - [Aggiornare l'utente [proprietà a valore singolo]](#update-user-single-valued-properties) ([richiesta](#request-5)
/ [risposta](#response-5)) 
  - [Elimina utente](#delete-user) ([richiesta](#request-6) / 
[risposta](#response-6))

[Operazioni sui gruppi](#group-operations)
  - [Crea gruppo](#create-group) ( [richiesta](#request-7) / [risposta](#response-7))
  - [Ottenere un gruppo](#get-group) ( [richiesta](#request-8) / [risposta](#response-8))
  - [Ottenere Group by DisplayName](#get-group-by-displayname) ([Request](#request-9) / [Response](#response-9))
  - [Gruppo di aggiornamento [attributi non membri]](#update-group-non-member-attributes) ([richiesta](#request-10) /
 [risposta](#response-10))
  - [Gruppo di aggiornamento [Add members]](#update-group-add-members) ( [Request](#request-11) /
[Response](#response-11))
  - [Gruppo di aggiornamento [Remove members]](#update-group-remove-members) ( [Request](#request-12) /
[Response](#response-12)) (
  - [Elimina gruppo](#delete-group) ([richiesta](#request-13) /
[risposta](#response-13))

### <a name="user-operations"></a>Operazioni utente

* Gli utenti possono eseguire query sugli attributi `userName` o `email[type eq "work"]`.  

#### <a name="create-user"></a>Create User

###### <a name="request"></a>Request

*POST/Users*
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

##### <a name="response"></a>response

*HTTP/1.1 201 creato*
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

###### <a name="request-1"></a>Richiesta
*OTTENERE/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Risposta (utente trovato)
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

###### <a name="request"></a>Request
*OTTENERE/Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Risposta (l'utente non è stato trovato. Si noti che i dettagli non sono obbligatori, ma solo dello stato.

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

##### <a name="request-2"></a>Richiesta

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Risposta

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

#### <a name="get-user-by-query---zero-results"></a>Ottenere l'utente in base alla query: risultati zero

##### <a name="request-3"></a>Richiesta

*OTTENERE/Users? filter = userName EQ "utente non esistente"*

##### <a name="response-3"></a>Risposta

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

#### <a name="update-user-multi-valued-properties"></a>Aggiornare l'utente [proprietà multivalore]

##### <a name="request-4"></a>Richiesta

*PATCH/Users/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response-4"></a>Risposta

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

#### <a name="update-user-single-valued-properties"></a>Aggiornare l'utente [proprietà a valore singolo]

##### <a name="request-5"></a>Richiesta

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response-5"></a>Risposta

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

#### <a name="delete-user"></a>Eliminazione di un utente.

##### <a name="request-6"></a>Richiesta

*Elimina/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

### <a name="group-operations"></a>Operazioni sui gruppi

* I gruppi devono essere sempre creati con un elenco di membri vuoto.
* I gruppi possono essere sottoposti a query dall'attributo `displayName`.
* L'aggiornamento alla richiesta PATCH di gruppo deve restituire un *contenuto HTTP 204 senza contenuto* nella risposta. Non è consigliabile restituire un corpo con un elenco di tutti i membri.
* Non è necessario supportare la restituzione di tutti i membri del gruppo.

#### <a name="create-group"></a>Creare un gruppo

##### <a name="request-7"></a>Richiesta

*POST/groups HTTP/1.1*
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

##### <a name="response-7"></a>Risposta

*HTTP/1.1 201 creato*
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

#### <a name="get-group"></a>Ottieni gruppo

##### <a name="request-8"></a>Richiesta

*GET/groups/40734ae655284ad3abcc? excludedAttributes = members HTTP/1.1*

##### <a name="response-8"></a>Risposta
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

#### <a name="get-group-by-displayname"></a>Ottenere Group by displayName

##### <a name="request-9"></a>Richiesta
*GET/groups? excludedAttributes = members & Filter = displayName EQ "displayName" HTTP/1.1*

##### <a name="response-9"></a>Risposta

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

#### <a name="update-group-non-member-attributes"></a>Gruppo di aggiornamento [attributi non membro]

##### <a name="request-10"></a>Richiesta

*PATCH/groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response-10"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

### <a name="update-group-add-members"></a>Gruppo di aggiornamento [Aggiungi membri]

##### <a name="request-11"></a>Richiesta

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-11"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

#### <a name="update-group-remove-members"></a>Gruppo di aggiornamento [Rimuovi membri]

##### <a name="request-12"></a>Richiesta

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-12"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

#### <a name="delete-group"></a>Eliminare un gruppo

##### <a name="request-13"></a>Richiesta

*Elimina/groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

## <a name="step-3-build-a-scim-endpoint"></a>Passaggio 3: creare un endpoint SCIM

Grazie alla creazione di un servizio Web SCIM che si interfaccia con Azure Active Directory, è possibile abilitare il provisioning utenti automatico per praticamente qualsiasi applicazione o archivio identità.

Il servizio funziona nel modo seguente:

1. Azure AD fornisce una libreria CLI (Common Language Infrastructure) denominata Microsoft. SystemForCrossDomainIdentityManagement, inclusa negli esempi di codice riportati di seguito. Gli integratori di sistemi e gli sviluppatori possono usare questa libreria per creare e distribuire un endpoint del servizio Web basato su SCIM in grado di connettersi Azure AD all'archivio identità di qualsiasi applicazione.
2. I mapping vengono implementati nel servizio Web per il mapping dello schema utente standardizzato allo schema utente e al protocollo richiesto dall'applicazione. 
3. L'URL dell'endpoint viene registrato in Azure AD come parte di un'applicazione personalizzata nella raccolta di applicazioni.
4. Gli utenti e i gruppi vengono assegnati a questa applicazione in Azure AD. Al momento dell'assegnazione, vengono inseriti in una coda per essere sincronizzati con l'applicazione di destinazione. Il processo di sincronizzazione che gestisce la coda viene eseguito ogni 40 minuti.

### <a name="code-samples"></a>Esempi di codice

Per semplificare questo processo, vengono forniti [esempi di codice](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) che creano un endpoint del servizio Web scim e dimostrano il provisioning automatico. L'esempio è un provider che gestisce un file con righe di valori delimitati da virgole che rappresentano utenti e gruppi.

**Prerequisiti**

* Visual Studio 2013 o versioni successive
* [Azure SDK per .NET](https://azure.microsoft.com/downloads/)
* Computer Windows che supporta ASP.NET Framework 4.5 da usare come endpoint SCIM. Il computer deve essere accessibile dal cloud.
* [Una sottoscrizione di Azure con una versione di prova o concessa in licenza di Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Per iniziare

Il modo più semplice per implementare un endpoint SCIM in grado di accettare richieste di provisioning da Azure AD consiste nel compilare e distribuire l'esempio di codice che fornisce come output gli utenti con provisioning in un file CSV (Comma-Separated Value).

#### <a name="to-create-a-sample-scim-endpoint"></a>Per creare un endpoint SCIM di esempio

1. Scaricare il pacchetto del codice di esempio dal sito [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Decomprimere il pacchetto e salvarlo nel computer Windows in un percorso analogo a C:\AzureAD-BYOA-Provisioning-Samples\.
1. In questa cartella, avviare il progetto FileProvisioning\Host\FileProvisioningService.csproj in Visual Studio.
1. Selezionare **strumenti** > **gestione pacchetti NuGet** > **console di gestione pacchetti**ed eseguire i comandi seguenti per il progetto FileProvisioningService per risolvere i riferimenti alla soluzione:

   ```powershell
    Update-Package -Reinstall
   ```

1. Compilare il progetto FileProvisioningService.
1. Avviare l'applicazione prompt dei comandi in Windows come amministratore e usare il comando **cd** per passare alla cartella **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**.
1. Eseguire il comando seguente, sostituendo `<ip-address>` con l'indirizzo IP o il nome di dominio del computer Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. In Windows **impostazioni di windows** > **rete & Internet**Selezionare il **Windows Firewall** > **Impostazioni avanzate**e creare una **regola in ingresso** che consenta l'accesso in ingresso alla porta 9000.
1. Se il computer Windows si trova dietro un router, è necessario configurare il router in modo che esegua la conversione di accesso alla rete tra la porta 9000 esposta a Internet e la porta 9000 nel computer Windows. Questa configurazione è necessaria per Azure AD accedere a questo endpoint nel cloud.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Per registrare l'endpoint SCIM di esempio in Azure AD

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com). 
1. Selezionare **applicazioni aziendali** dal riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare **+ nuova applicazione** > **tutte** > **applicazione non della raccolta**.
1. Immettere un nome per l'applicazione e selezionare **Aggiungi** per creare un oggetto app. L'oggetto applicazione creato deve rappresentare l'app di destinazione in cui verrà effettuato il provisioning e per cui verrà implementato l'accesso Single Sign-On, non solo l'endpoint SCIM.
1. Nella schermata gestione app selezionare **provisioning** nel riquadro sinistro.
1. Nel menu **Modalità di provisioning** selezionare **Automatica**.    
1. Nel campo **URL tenant** immettere l'URL dell'endpoint SCIM dell'applicazione. Esempio: https://api.contoso.com/scim/

1. Se l'endpoint SCIM richiede un token di connessione OAuth da un'autorità di certificazione diversa da Azure AD, copiare il token di connessione OAuth nel campo **Token segreto** facoltativo. Se questo campo viene lasciato vuoto, Azure AD include un bearer token OAuth emesso da Azure AD a ogni richiesta. Le app che usano Azure AD come provider di identità possono convalidare il token rilasciato da Azure AD.
1. Selezionare **Test connessione** per fare in modo che Azure Active Directory tenti di connettersi all'endpoint SCIM. Se il tentativo non riesce, vengono visualizzate le informazioni sull'errore.  

    > [!NOTE]
    > **Test connessione** esegue query sull'endpoint SCIM per cercare un utente che non esiste usando un GUID casuale come proprietà corrispondente selezionata nella configurazione di Azure AD. La risposta corretta prevista è HTTP 200 OK con un messaggio ListResponse SCIM vuoto

1. Se i tentativi di connessione all'applicazione hanno esito positivo, selezionare **Salva** per salvare le credenziali di amministratore.
1. Nella sezione **Mapping** sono disponibili due set selezionabili di mapping degli attributi: uno per gli oggetti utente e uno per gli oggetti gruppo. Selezionare ognuno dei due set per esaminare gli attributi sincronizzati da Active Directory di Azure con l'app. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli utenti e i gruppi nell'app per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.
1. In **Impostazioni**, il campo **Ambito** definisce gli utenti e/o i gruppi che devono essere sincronizzati. Selezionare **"Sincronizza solo utenti e gruppi assegnati** (scelta consigliata) per sincronizzare solo gli utenti e i gruppi assegnati nella scheda **utenti e gruppi** .
1. Al termine della configurazione, impostare lo **stato del provisioning** **su on**.
1. Selezionare **Save (Salva** ) per avviare il servizio di provisioning Azure ad.
1. Se si sincronizzano solo utenti e gruppi assegnati (scelta consigliata), assicurarsi di selezionare la scheda **utenti e gruppi** e di assegnare gli utenti o i gruppi che si desidera sincronizzare.

Una volta avviato il ciclo iniziale, è possibile selezionare i **log di controllo** nel riquadro sinistro per monitorare lo stato di avanzamento, che Mostra tutte le azioni eseguite dal servizio di provisioning nell'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

Il passaggio finale della verifica dell'esempio consiste nell'aprire il file TargetFile.csv nella cartella \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug del computer Windows. Dopo l'esecuzione del processo di provisioning, questo file include i dettagli di tutti gli utenti e gruppi assegnati e sottoposti a provisioning.

### <a name="development-libraries"></a>Librerie di sviluppo

Per sviluppare un servizio Web personalizzato conforme alla specifica SCIM, è necessario prima di tutto acquisire familiarità con le librerie Microsoft seguenti per accelerare il processo di sviluppo:

* Le librerie CLI (Common Language Infrastructure) vengono messe a disposizione per essere usate con linguaggi basati su questa infrastruttura, ad esempio C#. Una di queste librerie, Microsoft. SystemForCrossDomainIdentityManagement. Service, dichiara un'interfaccia, Microsoft. SystemForCrossDomainIdentityManagement. IProvider, illustrata nella figura seguente. Uno sviluppatore che usa le librerie implementerà questa interfaccia con una classe a cui è possibile fare riferimento, in modo generico, come provider. Le librerie consentono allo sviluppatore di distribuire un servizio Web conforme alla specifica SCIM. Il servizio Web può essere ospitato all'interno Internet Information Services o qualsiasi assembly CLI eseguibile. La richiesta viene convertita in chiamate ai metodi del provider, che saranno programmate dallo sviluppatore per agire su un archivio identità.
  
   ![Suddivisione: una richiesta convertita in chiamate ai metodi del provider][3]
  
* I [gestori di ExpressRoute](https://expressjs.com/guide/routing.html) sono disponibili per l'analisi di oggetti richiesta node.js che rappresentano chiamate, in base alla definizione della specifica SCIM, effettuate a un servizio Web node.js.

### <a name="building-a-custom-scim-endpoint"></a>Compilazione di un endpoint SCIM personalizzato

Gli sviluppatori che usano le librerie dell'interfaccia della riga di comando possono ospitare i servizi all'interno di qualsiasi assembly CLI eseguibile o in Internet Information Services. Ecco del codice di esempio per l'hosting di un servizio in un assembly eseguibile all'indirizzo http://localhost:9000: 

```csharp
 private static void Main(string[] arguments)
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
   new DevelopersMonitor();
 Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
   new DevelopersProvider(arguments[1]);
 Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
 try
 {
     webService = new WebService(monitor, provider);
     webService.Start("http://localhost:9000");

     Console.ReadKey(true);
 }
 finally
 {
     if (webService != null)
     {
         webService.Dispose();
         webService = null;
     }
 }
 }

 public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
 {
 private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
 private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

 public WebService(
   Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
   Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
 {
     this.monitor = monitoringBehavior;
     this.provider = providerBehavior;
 }

 public override IMonitor MonitoringBehavior
 {
     get
     {
         return this.monitor;
     }

     set
     {
         this.monitor = value;
     }
 }

 public override IProvider ProviderBehavior
 {
     get
     {
         return this.provider;
     }

     set
     {
         this.provider = value;
     }
 }
 }
```

Questo servizio deve avere un indirizzo HTTP e un certificato di autenticazione server la cui autorità di certificazione radice è uno dei nomi seguenti: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Un certificato di autenticazione server può essere associato a una porta su un host Windows usando l'utilità shell di rete:

```
netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

Il valore fornito per l'argomento certhash è l'identificazione personale del certificato, mentre il valore specificato per l'argomento appid è un identificatore univoco globale arbitrario.  

Per ospitare il servizio in Internet Information Services, uno sviluppatore compila un assembly della libreria di codice CLI con una classe denominata Startup nello spazio dei nomi predefinito dell'assembly.  Ecco un esempio di questa classe: 

```csharp
 public class Startup
 {
 // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
 // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
 // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

 Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

 public Startup()
 {
     Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
       new DevelopersMonitor();
     Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
       new DevelopersProvider();
     this.starter = 
       new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
         provider, 
         monitor);
 }

 public void Configuration(
   Owin.IAppBuilder builder) // Defined in Owin.dll.  
 {
     this.starter.ConfigureApplication(builder);
 }
 }
```

### <a name="handling-endpoint-authentication"></a>Gestione dell'autenticazione dell'endpoint

Le richieste da Azure Active Directory includono un token di connessione OAuth 2.0.   Qualsiasi servizio che riceve la richiesta deve autenticare l'autorità emittente come Azure Active Directory per il tenant di Azure Active Directory previsto, per l'accesso al servizio Web di Azure Active Directory Graph.  Nel token, l'emittente è identificato da un'attestazione ISS, ad esempio "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  In questo esempio, l'indirizzo di base del valore dell'attestazione, https://sts.windows.net, identifica Azure Active Directory come emittente, mentre il segmento dell'indirizzo relativo, cbb1a5ac-f33b-45FA-9BF5-f37db0fed422, è un identificatore univoco del tenant Azure Active Directory per il quale è stato emesso il token. I destinatari del token saranno l'ID del modello di applicazione per l'app nella raccolta. L'ID del modello di applicazione per tutte le app personalizzate è 8adf8e6e-67b2-4cf2-A259-e3dc5476c621. L'ID del modello di applicazione per ogni app nella raccolta varia. Per domande sull'ID modello dell'applicazione per un'applicazione della raccolta, contattare ProvisioningFeedback@microsoft.com. Ogni applicazione registrata in un singolo tenant può ricevere lo stesso `iss` attestazione con richieste SCIM.

Gli sviluppatori che usano le librerie dell'interfaccia della riga di comando fornite da Microsoft per la creazione di un servizio SCIM possono autenticare le richieste da Azure Active Directory usando il pacchetto Microsoft. Owin. Security. ActiveDirectory seguendo questa procedura: 

In primo luogo, in un provider, implementare la proprietà Microsoft. SystemForCrossDomainIdentityManagement. IProvider. Startupbehavior facendo in modo che restituisca un metodo da chiamare ogni volta che il servizio viene avviato: 

```csharp
  public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
  {
    get
    {
      return this.OnServiceStartup;
    }
  }

  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
    System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
  {
  }
```

Successivamente, aggiungere il codice seguente al metodo per avere una richiesta a uno degli endpoint del servizio autenticato come contenente un token emesso da Azure Active Directory per un tenant specifico, per l'accesso al servizio Web di Azure AD Graph: 

```csharp
  private void OnServiceStartup(
    Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
    System.Web.Http.HttpConfiguration HttpConfiguration configuration)
  {
    // IFilter is defined in System.Web.Http.dll.  
    System.Web.Http.Filters.IFilter authorizationFilter = 
      new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

    // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
    // System.IdentityModel.Token.Jwt.dll.
    SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
      new TokenValidationParameters()
      {
        ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
      };

    // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
    // Microsoft.Owin.Security.ActiveDirectory.dll
    Microsoft.Owin.Security.ActiveDirectory.
    WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
      new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
      TokenValidationParameters = tokenValidationParameters,
      Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                    // identifier for this one.  
    };

    applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
  }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Gestione del provisioning e deprovisioning degli utenti

***Esempio 1. Eseguire una query sul servizio per un utente corrispondente***

Azure Active Directory esegue query nel servizio per trovare un utente con valore dell'attributo externalId corrispondente al valore dell'attributo mailNickname di un utente in Azure AD. La query viene espressa come richiesta di Hypertext Transfer Protocol (HTTP), ad esempio questo esempio, in cui jyoung è un esempio di mailNickname di un utente in Azure Active Directory.

>[!NOTE]
> Questo è solo un esempio. Non tutti gli utenti avranno un attributo mailNickname e il valore che un utente potrebbe non essere univoco nella directory. Inoltre, l'attributo usato per la corrispondenza (che in questo caso è externalId) è configurabile nei [mapping degli attributi Azure ad](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Se il servizio è stato compilato usando le librerie dell'interfaccia della riga di comando fornite da Microsoft per implementare i servizi SCIM, la richiesta viene convertita in una chiamata al metodo di query del provider del servizio.  Ecco la firma del metodo: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
   Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
   string correlationIdentifier);
```

Ecco la definizione dell'interfaccia Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

```csharp
 public interface IQueryParameters: 
   Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
     System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
     { get; }
 }

 public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
 {
   system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
   { get; }
   System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
   { get; }
   string SchemaIdentifier 
   { get; }
 }
```

```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
```

Se il servizio è stato creato mediante le librerie Common Language Infrastructure fornite da Microsoft per implementare i servizi SCIM, la richiesta viene convertita in una chiamata al metodo Query del provider del servizio.  Ecco la firma del metodo: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
  // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

  System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
    Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
    string correlationIdentifier);
```

Ecco la definizione dell'interfaccia Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

```csharp
  public interface IQueryParameters: 
    Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
      System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
      { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
  {
    system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
    { get; }
    System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
    { get; }
    string SchemaIdentifier 
    { get; }
  }

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
  {
      Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
        { get; set; }
      string AttributePath 
        { get; } 
      Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
        { get; }
      string ComparisonValue 
        { get; }
  }

  public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
  {
      Equals
  }
```

Nell'esempio seguente di una query per un utente con un determinato valore per l'attributo externalId, i valori degli argomenti passati al metodo Query sono: 
* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

***Esempio 2. Effettuare il provisioning di un utente***

Se la risposta a una query al servizio Web per un utente con un valore di attributo externalId che corrisponde al valore dell'attributo mailNickname di un utente non restituisce alcun utente, Azure Active Directory richiede che il servizio effettui il provisioning di un utente corrispondente a quello in Azure Active Directory.  Ecco un esempio di questa richiesta: 

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

Le librerie dell'interfaccia della riga di comando fornite da Microsoft per implementare i servizi SCIM convertivano la richiesta in una chiamata al metodo create del provider del servizio.  Il metodo Create ha la firma seguente:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
   Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
   string correlationIdentifier);
```

In una richiesta di provisioning di un utente, il valore dell'argomento resource è un'istanza della classe Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, definita nella libreria Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Se la richiesta di provisioning dell'utente ha esito positivo, si prevede che l'implementazione del metodo restituisca un'istanza di Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, con il valore della proprietà Identifier impostato sull'identificatore univoco dell'utente appena sottoposto a provisioning.  

***Esempio 3. Eseguire una query sullo stato corrente di un utente*** 

Per aggiornare un utente la cui esistenza è nota in un archivio identità gestito da SCIM, Azure Active Directory richiede al servizio lo stato corrente dell'utente con una richiesta simile alla seguente: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

In un servizio compilato usando le librerie CLI fornite da Microsoft per implementare i servizi SCIM, la richiesta viene convertita in una chiamata al metodo retrieve del provider del servizio.  Ecco la firma del metodo Retrieve:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
 // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
 // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
    Retrieve(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
        parameters, 
        string correlationIdentifier);

 public interface 
   Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
     IRetrievalParameters
     {
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
         ResourceIdentifier 
           { get; }
 }
 public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
 {
     string Identifier 
       { get; set; }
     string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
       { get; set; }
 }
```

Nell'esempio di una richiesta per recuperare lo stato corrente di un utente, i valori delle proprietà dell'oggetto fornito come valore dell'argomento parameters sono analoghi ai seguenti: 
  
* Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Esempio 4. Eseguire una query sul valore di un attributo di riferimento da aggiornare*** 

Se è necessario aggiornare un attributo di riferimento, Azure Active Directory esegue una query sul servizio per determinare se il valore corrente dell'attributo Reference nell'archivio identità gestito dal servizio corrisponde già al valore dell'attributo in Azure Active Directory. Per gli utenti, l'unico attributo il cui valore corrente viene sottoposto a query con questa modalità è l'attributo manager. Ecco un esempio di una richiesta per determinare se l'attributo manager di un oggetto utente specifico ha attualmente un determinato valore: 

Se il servizio è stato compilato usando le librerie dell'interfaccia della riga di comando fornite da Microsoft per implementare i servizi SCIM, la richiesta viene convertita in una chiamata al metodo di query del provider del servizio. Il valore delle proprietà dell'oggetto fornito come valore dell'argomento parameters è analogo al seguente: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Il valore dell'indice x può essere 0 e il valore dell'indice y può essere 1 oppure il valore di x può essere 1 e il valore di y può essere 0, a seconda dell'ordine delle espressioni del parametro di query del filtro.   

***Esempio 5. Richiesta da Azure AD a un servizio SCIM per aggiornare un utente*** 

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

Le librerie Microsoft Common Language Infrastructure per implementare servizi SCIM convertiranno la richiesta in una chiamata al metodo Update del provider del servizio. Questa è la firma del metodo Update: 

```csharp
  // System.Threading.Tasks.Tasks and 
  // System.Collections.Generic.IReadOnlyCollection<T>
  // are defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
  // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
  // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
  // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

  System.Threading.Tasks.Task Update(
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
    string correlationIdentifier);

  public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
  {
  Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
    PatchRequest 
      { get; set; }
  Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
    ResourceIdentifier 
      { get; set; }        
  }

  public class PatchRequest2: 
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
  {
  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
      Operations
      { get;}

  public void AddOperation(
    Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
  }

  public class PatchOperation
  {
  public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
    Name
    { get; set; }

  public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
    Path
    { get; set; }

  public System.Collections.Generic.IReadOnlyCollection
    <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
    { get; }

  public void AddValue(
    Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
  }

  public enum OperationName
  {
    Add,
    Remove,
    Replace
  }

  public interface IPath
  {
    string AttributePath { get; }
    System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
    Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
  }

  public class OperationValue
  {
    public string Reference
    { get; set; }

    public string Value
    { get; set; }
  }
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

Per eseguire il deprovisioning di un utente da un archivio identità gestito da un servizio SCIM, Azure AD invia una richiesta, ad esempio:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

Se il servizio è stato creato mediante le librerie Common Language Infrastructure fornite da Microsoft per implementare i servizi SCIM, la richiesta viene convertita in una chiamata al metodo Delete del provider del servizio.   Il metodo ha la firma seguente: 

```csharp
  // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
  // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
  // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
  System.Threading.Tasks.Task Delete(
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
      resourceIdentifier, 
    string correlationIdentifier);
```

L'oggetto fornito come valore dell'argomento resourceIdentifier presenta questi valori di proprietà nell'esempio di una richiesta di deprovisioning di un utente: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Passaggio 4: integrare l'endpoint SCIM con il client di Azure AD SCIM

Azure AD può essere configurato per eseguire automaticamente il provisioning di utenti e gruppi assegnati ad applicazioni che implementano un profilo specifico del [protocollo SCIM 2,0](https://tools.ietf.org/html/rfc7644). Le specifiche del profilo sono documentate nel [passaggio 2: comprendere l'implementazione di Azure ad scim](#step-2-understand-the-azure-ad-scim-implementation).

Verificare la conformità ai requisiti sopra riportati con il provider dell'applicazione o consultando la documentazione fornita dal provider.

> [!IMPORTANT]
> Il Azure AD implementazione di SCIM si basa sul servizio di provisioning utenti Azure AD, progettato per consentire agli utenti di sincronizzare costantemente gli utenti tra Azure AD e l'applicazione di destinazione e implementa un set molto specifico di operazioni standard. È importante comprendere questi comportamenti per comprendere il comportamento del client di Azure AD SCIM. Per ulteriori informazioni, vedere [cosa accade durante il provisioning dell'utente?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Per iniziare

Le applicazioni che supportano il profilo SCIM descritto in questo articolo possono essere connesse ad Azure Active Directory usando la funzionalità "Applicazione non nella raccolta" nella raccolta di applicazioni di Azure AD. Una volta stabilita la connessione, Azure AD esegue un processo di sincronizzazione ogni 40 minuti in cui interroga l'endpoint SCIM dell'applicazione in merito agli utenti e ai gruppi assegnati e li crea o li modifica in base alle istruzioni di assegnazione.

**Per connettere un'applicazione che supporta SCIM:**

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com). Si noti che è possibile ottenere l'accesso a una versione di valutazione gratuita per Azure Active Directory con licenze P2 iscrivendosi al [programma per sviluppatori](https://developer.microsoft.com/office/dev-program)
2. Selezionare **applicazioni aziendali** dal riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
3. Selezionare **+ nuova applicazione** > **tutte** > **applicazione non della raccolta**.
4. Immettere un nome per l'applicazione e selezionare **Aggiungi** per creare un oggetto app. La nuova app viene aggiunta all'elenco di applicazioni aziendali e si apre alla relativa schermata di gestione delle app.

   ![screenshot mostra la raccolta di applicazioni Azure AD][1]<br/>
   *Figura 2: Azure AD raccolta di applicazioni*

5. Nella schermata gestione app selezionare **provisioning** nel riquadro sinistro.
6. Nel menu **Modalità di provisioning** selezionare **Automatica**.

   ![esempio: pagina di provisioning di un'app nel portale di Azure][2]<br/>
   *Figura 3: configurazione del provisioning nel portale di Azure*

7. Nel campo **URL tenant** immettere l'URL dell'endpoint SCIM dell'applicazione. Esempio: https://api.contoso.com/scim/
8. Se l'endpoint SCIM richiede un token di connessione OAuth da un'autorità di certificazione diversa da Azure AD, copiare il token di connessione OAuth nel campo **Token segreto** facoltativo. Se questo campo viene lasciato vuoto, Azure AD include un bearer token OAuth emesso da Azure AD a ogni richiesta. Le app che usano Azure AD come provider di identità possono convalidare il token rilasciato da Azure AD. 
   > [!NOTE]
   > ***Non*** è consigliabile lasciare vuoto questo campo e fare affidamento su un token generato da Azure ad. Questa opzione è disponibile principalmente a scopo di test.
9. Selezionare **Test connessione** per fare in modo che Azure Active Directory tenti di connettersi all'endpoint SCIM. Se il tentativo non riesce, vengono visualizzate le informazioni sull'errore.  

    > [!NOTE]
    > **Test connessione** esegue query sull'endpoint SCIM per cercare un utente che non esiste usando un GUID casuale come proprietà corrispondente selezionata nella configurazione di Azure AD. La risposta corretta prevista è HTTP 200 OK con un messaggio ListResponse SCIM vuoto.

10. Se i tentativi di connessione all'applicazione hanno esito positivo, selezionare **Salva** per salvare le credenziali di amministratore.
11. Nella sezione **mapping** sono disponibili due set selezionabili di [mapping degli attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes): uno per gli oggetti utente e uno per gli oggetti gruppo. Selezionare ognuno dei due set per esaminare gli attributi sincronizzati da Active Directory di Azure con l'app. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli utenti e i gruppi nell'app per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    > [!NOTE]
    > Facoltativamente, è possibile disattivare la sincronizzazione degli oggetti gruppo disabilitando il mapping relativo ai gruppi.

12. In **Impostazioni** il campo **Ambito** definisce gli utenti e i gruppi che devono essere sincronizzati. Selezionare **Sincronizza solo utenti** e gruppi assegnati (scelta consigliata) per sincronizzare solo gli utenti e i gruppi assegnati nella scheda **utenti e gruppi** .
13. Al termine della configurazione, impostare lo **stato del provisioning** **su on**.
14. Selezionare **Save (Salva** ) per avviare il servizio di provisioning Azure ad.
15. Se si sincronizzano solo utenti e gruppi assegnati (scelta consigliata), assicurarsi di selezionare la scheda **utenti e gruppi** e di assegnare gli utenti o i gruppi che si desidera sincronizzare.

Una volta avviato il ciclo iniziale, è possibile selezionare i **log di provisioning** nel riquadro sinistro per monitorare lo stato di avanzamento, che Mostra tutte le azioni eseguite dal servizio di provisioning nell'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere [Esercitazione: creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

> [!NOTE]
> Il ciclo iniziale richiede più tempo delle sincronizzazioni successive, che vengono eseguite approssimativamente ogni 40 minuti, a condizione che il servizio sia in esecuzione.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Passaggio 5: pubblicare l'applicazione nella raccolta di applicazioni Azure AD

Se si sta creando un'applicazione che verrà usata da più di un tenant, è possibile renderla disponibile nella raccolta di applicazioni Azure AD. In questo modo è più semplice per le organizzazioni individuare l'applicazione e configurare il provisioning. La pubblicazione dell'app nella raccolta Azure AD e l'esecuzione del provisioning per altri è facile. Consultare i passaggi [qui](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing) Microsoft collaborerà con l'utente per integrare l'applicazione nella raccolta, testare l'endpoint e rilasciare la [documentazione](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) di onboarding per l'uso da parte dei clienti. 


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorizzazione per il provisioning dei connettori nella raccolta di applicazioni
La specifica SCIM non definisce uno schema specifico di SCIM per l'autenticazione e l'autorizzazione. Si basa sull'uso di standard di settore esistenti. Il client di provisioning Azure AD supporta due metodi di autorizzazione per le applicazioni nella raccolta. 

**Flusso di concessione del codice di autorizzazione OAuth:** Il servizio di provisioning supporta la [concessione del codice di autorizzazione](https://tools.ietf.org/html/rfc6749#page-24). Dopo aver inviato la richiesta di pubblicazione dell'app nella raccolta, il Team collaborerà con l'utente per raccogliere le informazioni seguenti:
*  URL autorizzazione: URL del client per ottenere l'autorizzazione dal proprietario della risorsa tramite il reindirizzamento dell'agente utente. L'utente viene reindirizzato a questo URL per autorizzare l'accesso. 
*  URL di scambio di token: URL del client per scambiare una concessione di autorizzazione per un token di accesso, in genere con l'autenticazione client.
*  ID client: il server di autorizzazione rilascia al client registrato un identificatore client, ovvero una stringa univoca che rappresenta le informazioni di registrazione fornite dal client.  L'identificatore client non è un segreto. viene esposto al proprietario della risorsa e **non deve** essere usato da solo per l'autenticazione client.  
*  Segreto client: il segreto client è un segreto generato dal server di autorizzazione. Deve essere un valore univoco noto solo al server di autorizzazione. 

Procedure consigliate (consigliato ma non obbligatorio):
* Supporta più URL di reindirizzamento. Gli amministratori possono configurare il provisioning da "portal.azure.com" e "aad.portal.azure.com". Il supporto di più URL di reindirizzamento garantisce che gli utenti possano autorizzare l'accesso da uno dei due portale.
* Supporta più segreti per garantire il rinnovo del segreto senza tempi di inattività. 

**Token di porta OAuth di lunga durata:** Se l'applicazione non supporta il flusso di concessione del codice di autorizzazione OAuth, è anche possibile generare un bearer token OAuth di lunga durata che può essere usato da un amministratore per configurare l'integrazione del provisioning. Il token deve essere perpetuo, altrimenti il processo di provisioning viene [messo in quarantena](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) quando il token scade. Il token deve essere di dimensioni inferiori a 1 KB.  

Per ulteriori metodi di autenticazione e autorizzazione, informare Microsoft su [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Consenti indirizzi IP usati dal servizio di provisioning Azure AD per eseguire richieste SCIM

Determinate app consentono il traffico in ingresso verso l'app. Affinché il servizio di provisioning Azure AD funzioni come previsto, è necessario consentire gli indirizzi IP utilizzati. Per un elenco degli indirizzi IP per ogni tag del servizio/area, vedere il file JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Indirizzi IP di Azure e tag del servizio - Cloud pubblico). È possibile scaricare e programmare questi indirizzi IP nel firewall, se necessario. Gli intervalli IP riservati per il provisioning di Azure AD sono disponibili in "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Articoli correlati

* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
* [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](customize-application-attributes.md)
* [Scrittura di espressioni per il mapping degli attributi](functions-for-customizing-application-data.md)
* [Ambito dei filtri per il Provisioning utente](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notifiche relative al provisioning dell'account](user-provisioning.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
