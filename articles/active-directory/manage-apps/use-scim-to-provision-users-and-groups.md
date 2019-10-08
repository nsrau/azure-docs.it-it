---
title: Provisioning utenti di SCIM con Azure Active Directory | Microsoft Docs
description: Informazioni su come creare un endpoint SCIM, integrare l'API SCIM con Azure Active Directory e iniziare ad automatizzare gli utenti e i gruppi provisioning nelle applicazioni.
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
ms.date: 10/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5c24a2340775712f1105448b2aacfdc9a75f1a6
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001717"
---
# <a name="scim-user-provisioning-with-azure-active-directory"></a>Provisioning utenti di SCIM con Azure Active Directory

System for Cross-Domain Identity Management ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)) è un protocollo standardizzato e uno schema che mira a garantire una maggiore coerenza nella modalità di gestione delle identità tra i sistemi. Quando un'applicazione supporta un endpoint SCIM per la gestione degli utenti, il Azure AD servizio di provisioning utenti può inviare richieste per creare, modificare o eliminare utenti e gruppi assegnati a questo endpoint.

Molte delle applicazioni per le quali Azure AD supporta il [provisioning utente automatico pre-integrato](../saas-apps/tutorial-list.md) implementano SCIM come mezzo per ricevere le notifiche di modifica dell'utente.  Oltre a questi, i clienti possono connettere le applicazioni che supportano un profilo specifico della [specifica del protocollo SCIM 2,0](https://tools.ietf.org/html/rfc7644) usando l'opzione di integrazione generica "non raccolta" nel portale di Azure.

L'obiettivo principale di questo articolo è il profilo di SCIM 2,0 che Azure AD implementa come parte del connettore SCIM generico per le app non incluse nella raccolta. Tuttavia, il test di un'applicazione che supporta SCIM con il connettore Azure AD generico è un passaggio per ottenere un'app elencata nella raccolta Azure AD come supporto del provisioning utenti. Per ulteriori informazioni su come ottenere l'applicazione nell'elenco Azure AD raccolta di applicazioni, vedere [How per: Elencare l'applicazione in Azure AD raccolta di applicazioni @ no__t-0.

> [!IMPORTANT]
> L'ultimo aggiornamento del comportamento dell'implementazione SCIM di Azure AD è stato eseguito il 18 dicembre 2018. Per informazioni sulle modifiche, vedere [Conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD](application-provisioning-config-problem-scim-compatibility.md).

provisioning di ![Shows da Azure AD a un'app o a un archivio identità @ no__t-1<br/>
*Figura 1: Provisioning da Azure Active Directory a un'applicazione o a un archivio identità che implementa SCIM @ no__t-0

Questo articolo è suddiviso in quattro sezioni:

* **[Provisioning di utenti e gruppi in applicazioni di terze parti che supportano SCIM 2,0](#provisioning-users-and-groups-to-applications-that-support-scim)** : se l'organizzazione usa un'applicazione di terze parti che implementa il profilo di SCIM 2,0 supportato da Azure ad, è possibile iniziare ad automatizzare il provisioning e deprovisioning di utenti e gruppi oggi stesso.
* **[Informazioni sull'implementazione di Azure ad scim](#understanding-the-azure-ad-scim-implementation)** : se si sta creando un'applicazione che supporta un'API di gestione utenti scim 2,0, in questa sezione viene descritto in dettaglio il modo in cui viene implementato il client Azure ad scim e viene illustrato come modellare il protocollo scim gestione delle richieste e risposte.
* **[Compilazione di un endpoint scim usando](#building-a-scim-endpoint-using-microsoft-cli-libraries)** le librerie dell'interfaccia della riga di comando di Microsoft: le librerie Common Language Infrastructure (CLI) insieme agli esempi di codice illustrano come sviluppare un endpoint scim e convertire messaggi SCIM.  
* **[Riferimento allo schema di utenti e gruppi](#user-and-group-schema-reference)** : descrive lo schema utente e gruppo supportato dall'implementazione Azure ad scim per le app non della raccolta.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Provisioning di utenti e gruppi in applicazioni che supportano SCIM

Azure AD può essere configurato per eseguire automaticamente il provisioning di utenti e gruppi assegnati ad applicazioni che implementano un profilo specifico del [protocollo SCIM 2,0](https://tools.ietf.org/html/rfc7644). Le specifiche del profilo sono documentate in [informazioni sull'implementazione di Azure ad scim](#understanding-the-azure-ad-scim-implementation).

Verificare la conformità ai requisiti sopra riportati con il provider dell'applicazione o consultando la documentazione fornita dal provider.

> [!IMPORTANT]
> Il Azure AD implementazione di SCIM si basa sul servizio di provisioning utenti Azure AD, progettato per consentire agli utenti di sincronizzare costantemente gli utenti tra Azure AD e l'applicazione di destinazione e implementa un set molto specifico di operazioni standard. È importante comprendere questi comportamenti per comprendere il comportamento del client di Azure AD SCIM. Per ulteriori informazioni, vedere [cosa accade durante il provisioning dell'utente?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Introduzione

Le applicazioni che supportano il profilo SCIM descritto in questo articolo possono essere connesse ad Azure Active Directory usando la funzionalità "Applicazione non nella raccolta" nella raccolta di applicazioni di Azure AD. Una volta stabilita la connessione, Azure AD esegue un processo di sincronizzazione ogni 40 minuti in cui interroga l'endpoint SCIM dell'applicazione in merito agli utenti e ai gruppi assegnati e li crea o li modifica in base alle istruzioni di assegnazione.

**Per connettere un'applicazione che supporta SCIM:**

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com). Si noti che è possibile ottenere l'accesso a una versione di valutazione gratuita per Azure Active Directory con licenze P2 iscrivendosi al [programma per sviluppatori](https://developer.microsoft.com/office/dev-program)
1. Selezionare **applicazioni aziendali** dal riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare **+ nuova applicazione** > **tutti** > **applicazione non della raccolta**.
1. Immettere un nome per l'applicazione e selezionare **Aggiungi** per creare un oggetto app. La nuova app viene aggiunta all'elenco di applicazioni aziendali e si apre alla relativa schermata di gestione delle app.

   ![Screenshot Mostra la raccolta di applicazioni Azure AD @ no__t-1<br/>
   *Figura 2: Raccolta di applicazioni di Azure AD*

1. Nella schermata gestione app selezionare **provisioning** nel riquadro sinistro.
1. Nel menu **Modalità di provisioning** selezionare **Automatica**.

   ![Esempio: Pagina di provisioning di un'app nel portale di Azure @ no__t-0<br/>
   *Figura 3: Configurazione del provisioning nel portale di Azure*

1. Nel campo **URL tenant** immettere l'URL dell'endpoint SCIM dell'applicazione. Esempio: https://api.contoso.com/scim/
1. Se l'endpoint SCIM richiede un token di connessione OAuth da un'autorità di certificazione diversa da Azure AD, copiare il token di connessione OAuth nel campo **Token segreto** facoltativo. Se questo campo viene lasciato vuoto, Azure AD include un bearer token OAuth emesso da Azure AD a ogni richiesta. Le app che usano Azure AD come provider di identità possono convalidare il token rilasciato da Azure AD.
1. Selezionare **Test connessione** per fare in modo che Azure Active Directory tenti di connettersi all'endpoint SCIM. Se il tentativo non riesce, vengono visualizzate le informazioni sull'errore.  

    > [!NOTE]
    > **Test connessione** esegue query sull'endpoint SCIM per cercare un utente che non esiste usando un GUID casuale come proprietà corrispondente selezionata nella configurazione di Azure AD. La risposta corretta prevista è HTTP 200 OK con un messaggio ListResponse SCIM vuoto.

1. Se i tentativi di connessione all'applicazione hanno esito positivo, selezionare **Salva** per salvare le credenziali di amministratore.
1. Nella sezione **Mapping** sono disponibili due set selezionabili di mapping degli attributi: uno per gli oggetti utente e uno per gli oggetti gruppo. Selezionare ognuno dei due set per esaminare gli attributi sincronizzati da Active Directory di Azure con l'app. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli utenti e i gruppi nell'app per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    > [!NOTE]
    > Facoltativamente, è possibile disattivare la sincronizzazione degli oggetti gruppo disabilitando il mapping relativo ai gruppi.

1. In **Impostazioni** il campo **Ambito** definisce gli utenti e i gruppi che devono essere sincronizzati. Selezionare **Sincronizza solo utenti** e gruppi assegnati (scelta consigliata) per sincronizzare solo gli utenti e i gruppi assegnati nella scheda **utenti e gruppi** .
1. Al termine della configurazione, impostare lo **stato del provisioning** **su on**.
1. Selezionare **Save (Salva** ) per avviare il servizio di provisioning Azure ad.
1. Se si sincronizzano solo utenti e gruppi assegnati (scelta consigliata), assicurarsi di selezionare la scheda **utenti e gruppi** e di assegnare gli utenti o i gruppi che si desidera sincronizzare.

Una volta avviato il ciclo iniziale, è possibile selezionare i **log di controllo** nel riquadro sinistro per monitorare lo stato di avanzamento, che Mostra tutte le azioni eseguite dal servizio di provisioning nell'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

> [!NOTE]
> Il ciclo iniziale richiede più tempo delle sincronizzazioni successive, che vengono eseguite approssimativamente ogni 40 minuti, a condizione che il servizio sia in esecuzione.

**Per pubblicare l'applicazione nel Azure AD raccolta di applicazioni:**

Se si compila un'applicazione che verrà usata da più di un tenant, è possibile renderla disponibile nella raccolta di applicazioni Azure AD. In questo modo è più semplice per le organizzazioni individuare l'applicazione e configurare il provisioning. La pubblicazione dell'app nella raccolta Azure AD e l'esecuzione del provisioning per altri è facile. Consultare i passaggi [qui](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing) 
## <a name="understanding-the-azure-ad-scim-implementation"></a>Informazioni sull'implementazione di Azure AD SCIM

Se si compila un'applicazione che supporta un'API di gestione utenti SCIM 2,0, in questa sezione viene descritto in dettaglio il modo in cui viene implementato il client di Azure AD SCIM e viene illustrato come modellare le risposte e la gestione delle richieste di protocollo SCIM. Una volta implementato l'endpoint SCIM, è possibile testarlo attenendosi alla procedura descritta nella sezione precedente.

All'interno della [specifica del protocollo SCIM 2,0](http://www.simplecloud.info/#Specification), l'applicazione deve soddisfare i requisiti seguenti:

* Supporta la creazione di utenti e, facoltativamente, anche i gruppi, come indicato [nella sezione 3,3 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Supporta la modifica di utenti o gruppi con richieste PATCH, come per [la sezione 3.5.2 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Supporta il recupero di una risorsa nota per un utente o un gruppo creato in precedenza, come per [la sezione 3.4.1 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Supporta l'esecuzione di query su utenti o gruppi, in base alla sezione [3.4.2 del protocollo scim](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Per impostazione predefinita, gli utenti vengono recuperati dal `id` e sottoposti a query in base ai `username` e `externalid` e i gruppi vengono sottoposti a query da `displayName`.  
* Supporta l'esecuzione di query su User by ID e Manager, come per la sezione 3.4.2 del protocollo SCIM.  
* Supporta l'esecuzione di query sui gruppi in base all'ID e al membro, come indicato nella sezione 3.4.2 del protocollo SCIM.  
* Accetta una singola bearer token per l'autenticazione e l'autorizzazione di Azure AD all'applicazione.

Quando si implementa un endpoint SCIM per garantire la compatibilità con Azure AD, attenersi alle linee guida generali seguenti:

* `id` è una proprietà obbligatoria per tutte le risorse. Ogni risposta che restituisce una risorsa deve garantire che ogni risorsa abbia questa proprietà, ad eccezione di `ListResponse` con membri zero.
* La risposta a una richiesta di query/filtro deve essere sempre un `ListResponse`.
* I gruppi sono facoltativi, ma sono supportati solo se l'implementazione di SCIM supporta le richieste PATCH.
* Non è necessario includere l'intera risorsa nella risposta PATCH.
* Microsoft Azure AD utilizza solo gli operatori seguenti:  
     - `eq`
     - `and`
* Non richiedere una corrispondenza con distinzione tra maiuscole e minuscole negli elementi strutturali in SCIM, in particolare i valori delle operazioni `op` della PATCH, come definito in https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD emette i valori di "op" come `Add`, `Replace` e `Remove`.
* Microsoft Azure AD esegue richieste per recuperare un utente e un gruppo casuali per verificare che l'endpoint e le credenziali siano validi. Viene anche eseguita come parte del flusso di **test della connessione** nella [portale di Azure](https://portal.azure.com). 
* L'attributo su cui è possibile eseguire query sulle risorse deve essere impostato come attributo corrispondente nell'applicazione nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [personalizzazione dei mapping degli attributi per il provisioning degli utenti](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Provisioning e deprovisioning utenti

La figura seguente illustra i messaggi che Azure Active Directory Invia a un servizio SCIM per gestire il ciclo di vita di un utente nell'archivio identità dell'applicazione.  

![Shows la sequenza di provisioning e deprovisioning dell'utente @ no__t-1<br/>
*Figura 4: Sequenza di provisioning e deprovisioning utenti*

### <a name="group-provisioning-and-de-provisioning"></a>Provisioning e deprovisioning gruppi

Il provisioning e il deprovisioning di gruppi sono facoltativi. Quando implementato e abilitato, nella figura seguente vengono mostrati i messaggi inviati da Azure AD a un servizio SCIM per gestire il ciclo di vita di un gruppo nell'archivio identità dell'applicazione.  Tali messaggi sono diversi dai messaggi sugli utenti in due modi:

* Le richieste di recupero dei gruppi specificano che l'attributo members deve essere escluso da qualsiasi risorsa fornita in risposta alla richiesta.  
* Le richieste per determinare se un attributo reference ha un determinato valore sono richieste relative all'attributo members.  

![Shows la sequenza di provisioning e deprovisioning del gruppo @ no__t-1<br/>
*Figura 5: Sequenza di provisioning e deprovisioning gruppi*

### <a name="scim-protocol-requests-and-responses"></a>Richieste e risposte del protocollo SCIM
Questa sezione fornisce le richieste SCIM di esempio emesse dal client Azure AD SCIM e dalle risposte previste di esempio. Per ottenere risultati ottimali, è necessario codificare l'app in modo che gestisca queste richieste in questo formato e generare le risposte previste.

> [!IMPORTANT]
> Per comprendere come e quando il servizio di provisioning utenti Azure AD emette le operazioni descritte di seguito, vedere [cosa accade durante il provisioning dell'utente?](user-provisioning.md#what-happens-during-provisioning).

- [Operazioni utente](#user-operations)
  - [Crea utente](#create-user)
    - [Richiesta](#request)
    - [Risposta](#response)
  - [Ottieni utente](#get-user)
    - [Richiesta](#request-1)
    - [Risposta](#response-1)
  - [Ottieni utente per query](#get-user-by-query)
    - [Richiesta](#request-2)
    - [Risposta](#response-2)
  - [Ottenere l'utente in base alla query: risultati zero](#get-user-by-query---zero-results)
    - [Richiesta](#request-3)
    - [Risposta](#response-3)
  - [Aggiornare l'utente [proprietà multivalore]](#update-user-multi-valued-properties)
    - [Richiesta](#request-4)
    - [Risposta](#response-4)
  - [Aggiornare l'utente [proprietà a valore singolo]](#update-user-single-valued-properties)
    - [Richiesta](#request-5)
    - [Risposta](#response-5)
  - [Elimina utente](#delete-user)
    - [Richiesta](#request-6)
    - [Risposta](#response-6)
- [Operazioni sui gruppi](#group-operations)
  - [Crea gruppo](#create-group)
    - [Richiesta](#request-7)
    - [Risposta](#response-7)
  - [Ottieni gruppo](#get-group)
    - [Richiesta](#request-8)
    - [Risposta](#response-8)
  - [Ottenere Group by displayName](#get-group-by-displayname)
    - [Richiesta](#request-9)
    - [Risposta](#response-9)
  - [Gruppo di aggiornamento [attributi non membro]](#update-group-non-member-attributes)
    - [Richiesta](#request-10)
    - [Risposta](#response-10)
  - [Gruppo di aggiornamento [Aggiungi membri]](#update-group-add-members)
    - [Richiesta](#request-11)
    - [Risposta](#response-11)
  - [Gruppo di aggiornamento [Rimuovi membri]](#update-group-remove-members)
    - [Richiesta](#request-12)
    - [Risposta](#response-12)
  - [Elimina gruppo](#delete-group)
    - [Richiesta](#request-13)
    - [Risposta](#response-13)

### <a name="user-operations"></a>Operazioni utente

* Gli utenti possono eseguire query sugli attributi `userName` o `email[type eq "work"]`.  

#### <a name="create-user"></a>Crea utente

###### <a name="request"></a>Richiedi

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

##### <a name="response"></a>Risposta

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

###### <a name="response-1"></a>Risposta
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

#### <a name="delete-user"></a>Elimina utente

##### <a name="request-6"></a>Richiesta

*Elimina/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

### <a name="group-operations"></a>Operazioni sui gruppi

* I gruppi devono essere sempre creati con un elenco di membri vuoto.
* I gruppi possono essere sottoposti a query dall'attributo `displayName`.
* L'aggiornamento alla richiesta PATCH di gruppo deve restituire un *contenuto HTTP 204 senza contenuto* nella risposta. Non è consigliabile restituire un corpo con un elenco di tutti i membri.
* Non è necessario supportare la restituzione di tutti i membri del gruppo.

#### <a name="create-group"></a>Crea gruppo

##### <a name="request-7"></a>Richiesta

*POST/groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
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

#### <a name="delete-group"></a>Elimina gruppo

##### <a name="request-13"></a>Richiesta

*Elimina/groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Risposta

*HTTP/1.1 204 nessun contenuto*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Compilazione di un endpoint SCIM usando le librerie CLI Microsoft

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

### <a name="getting-started"></a>Introduzione

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

1. In Windows in **Impostazioni windows** > **rete & Internet**selezionare le**Impostazioni avanzate** **Windows Firewall** >  e creare una **regola in ingresso** che consenta l'accesso in ingresso alla porta 9000.
1. Se il computer Windows si trova dietro un router, è necessario configurare il router in modo che esegua la conversione di accesso alla rete tra la porta 9000 esposta a Internet e la porta 9000 nel computer Windows. Questa configurazione è necessaria per Azure AD accedere a questo endpoint nel cloud.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Per registrare l'endpoint SCIM di esempio in Azure AD

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com). 
1. Selezionare **applicazioni aziendali** dal riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare **+ nuova applicazione** > **tutti** > **applicazione non della raccolta**.
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

Una volta avviato il ciclo iniziale, è possibile selezionare i **log di controllo** nel riquadro sinistro per monitorare lo stato di avanzamento, che Mostra tutte le azioni eseguite dal servizio di provisioning nell'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

Il passaggio finale della verifica dell'esempio consiste nell'aprire il file TargetFile.csv nella cartella \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug del computer Windows. Dopo l'esecuzione del processo di provisioning, questo file include i dettagli di tutti gli utenti e gruppi assegnati e sottoposti a provisioning.

### <a name="development-libraries"></a>Librerie di sviluppo

Per sviluppare un servizio Web personalizzato conforme alla specifica SCIM, è necessario prima di tutto acquisire familiarità con le librerie Microsoft seguenti per accelerare il processo di sviluppo:

* Le librerie CLI (Common Language Infrastructure) vengono messe a disposizione per essere usate con linguaggi basati su questa infrastruttura, ad esempio C#. Una di queste librerie, Microsoft. SystemForCrossDomainIdentityManagement. Service, dichiara un'interfaccia, Microsoft. SystemForCrossDomainIdentityManagement. IProvider, illustrata nella figura seguente. Uno sviluppatore che usa le librerie implementerà questa interfaccia con una classe a cui è possibile fare riferimento, in modo generico, come provider. Le librerie consentono allo sviluppatore di distribuire un servizio Web conforme alla specifica SCIM. Il servizio Web può essere ospitato all'interno Internet Information Services o qualsiasi assembly CLI eseguibile. La richiesta viene convertita in chiamate ai metodi del provider, che saranno programmate dallo sviluppatore per agire su un archivio identità.
  
   ![Ripartizione Richiesta convertita in chiamate ai metodi del provider][3]
  
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

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

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

Le richieste da Azure Active Directory includono un token di connessione OAuth 2.0.   Qualsiasi servizio che riceve la richiesta deve autenticare l'autorità emittente come Azure Active Directory per il tenant di Azure Active Directory previsto, per l'accesso al servizio Web di Azure Active Directory Graph.  Nel token, l'emittente è identificato da un'attestazione ISS, ad esempio "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  In questo esempio, l'indirizzo di base del valore dell'attestazione, https://sts.windows.net, identifica Azure Active Directory come emittente, mentre il segmento dell'indirizzo relativo, cbb1a5ac-f33b-45FA-9BF5-f37db0fed422, è un identificatore univoco del tenant Azure Active Directory per il quale il il token è stato emesso. I destinatari del token saranno l'ID del modello di applicazione per l'app nella raccolta. L'ID del modello di applicazione per tutte le app personalizzate è 8adf8e6e-67b2-4cf2-A259-e3dc5476c621. L'ID del modello di applicazione per ogni app nella raccolta varia. Per domande sull'ID modello dell'applicazione per un'applicazione della raccolta, contattare ProvisioningFeedback@microsoft.com. Ogni applicazione registrata in un singolo tenant può ricevere la stessa attestazione `iss` con richieste SCIM.

Gli sviluppatori che usano le librerie dell'interfaccia della riga di comando fornite da Microsoft per la creazione di un servizio SCIM possono autenticare le richieste da Azure Active Directory usando il pacchetto Microsoft. Owin. Security. ActiveDirectory seguendo questa procedura: 

1. In un provider implementare la proprietà Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior facendo in modo che restituisca un metodo da chiamare ogni volta che viene avviato il servizio: 

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

1. Aggiungere il codice seguente al metodo in modo che qualsiasi richiesta a uno degli endpoint del servizio venga autenticata come se includesse un token emesso da Azure Active Directory per un tenant specifico, per l'accesso al servizio Web di Azure AD Graph: 

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

1. Azure Active Directory esegue query nel servizio per trovare un utente con valore dell'attributo externalId corrispondente al valore dell'attributo mailNickname di un utente in Azure AD. La query viene espressa come richiesta di Hypertext Transfer Protocol (HTTP), ad esempio questo esempio, in cui jyoung è un esempio di mailNickname di un utente in Azure Active Directory.

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

1. Se la risposta a una query al servizio Web per un utente con un valore di attributo externalId che corrisponde al valore dell'attributo mailNickname di un utente non restituisce alcun utente, Azure Active Directory richiede che il servizio effettui il provisioning di un utente corrispondente a quello in Azure Active Directory.  Ecco un esempio di questa richiesta: 

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

1. Per aggiornare un utente la cui esistenza è nota in un archivio identità gestito da SCIM, Azure Active Directory richiede al servizio lo stato corrente dell'utente con una richiesta simile alla seguente: 
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

1. Se è necessario aggiornare un attributo di riferimento, Azure Active Directory esegue una query sul servizio per determinare se il valore corrente dell'attributo Reference nell'archivio identità gestito dal servizio corrisponde già al valore dell'attributo in Azure Active Directory. Per gli utenti, l'unico attributo il cui valore corrente viene sottoposto a query con questa modalità è l'attributo manager. Ecco un esempio di una richiesta per determinare se l'attributo manager di un oggetto utente specifico ha attualmente un determinato valore: 

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

1. Ecco un esempio di una richiesta di Azure Active Directory a un servizio SCIM per l'aggiornamento di un utente: 
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
   Le librerie dell'interfaccia della riga di comando di Microsoft per implementare i servizi SCIM convertivano la richiesta in una chiamata al metodo Update del provider del servizio. Questa è la firma del metodo Update: 
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
   ```

   Se il servizio è stato creato mediante le librerie Common Language Infrastructure fornite da Microsoft per implementare i servizi SCIM, la richiesta viene convertita in una chiamata al metodo Query del provider del servizio. Il valore delle proprietà dell'oggetto fornito come valore dell'argomento parameters è analogo al seguente: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Il valore dell'indice x può essere 0 e il valore dell'indice y può essere 1 oppure il valore di x può essere 1 e il valore di y può essere 0, a seconda dell'ordine delle espressioni del parametro di query del filtro.   

1. Ecco un esempio di una richiesta di Azure Active Directory a un servizio SCIM per l'aggiornamento di un utente: 

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

1. Per effettuare il deprovisioning di un utente da un archivio identità gestito da un servizio SCIM, Azure Active Directory invia una richiesta simile alla seguente: 

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

   Nell'esempio di una richiesta per il deprovisioning di un utente, i valori delle proprietà dell'oggetto fornito come valore dell'argomento resourceIdentifier sono analoghi ai seguenti: 

1. Per effettuare il deprovisioning di un utente da un archivio identità gestito da un servizio SCIM, Azure Active Directory invia una richiesta simile alla seguente: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Se il servizio è stato compilato usando le librerie dell'interfaccia della riga di comando fornite da Microsoft per implementare i servizi SCIM, la richiesta viene convertita in una chiamata al metodo Delete del provider del servizio.   Il metodo ha la firma seguente: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   Nell'esempio di una richiesta per il deprovisioning di un utente, i valori delle proprietà dell'oggetto fornito come valore dell'argomento resourceIdentifier sono analoghi ai seguenti: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Riferimento allo schema di utenti e gruppi

Azure Active Directory può effettuare il provisioning di due tipi di risorse nei servizi Web SCIM,  ovvero utenti e gruppi.  

Le risorse utente sono identificate dall'identificatore dello schema, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, incluso in questa specifica del protocollo: https://tools.ietf.org/html/rfc7643.  Il mapping predefinito degli attributi degli utenti in Azure Active Directory agli attributi delle risorse utente è indicato nella tabella 1.  

Le risorse gruppo sono identificate dall'identificatore dello schema, `urn:ietf:params:scim:schemas:core:2.0:Group`. Nella tabella 2 viene illustrato il mapping predefinito degli attributi dei gruppi nel Azure Active Directory agli attributi delle risorse del gruppo.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabella 1: Mapping predefinito degli attributi utente

| Utente Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| responsabile |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabella 2: Mapping predefinito degli attributi dei gruppi

| Gruppo Azure Active Directory | urn: IETF: params: SCIM: schemas: Core: 2.0: Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| membri |membri |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Consenti indirizzi IP usati dal servizio di provisioning Azure AD per eseguire richieste SCIM

Determinate app consentono il traffico in ingresso verso l'app. Affinché il servizio di provisioning Azure AD funzioni come previsto, è necessario consentire gli indirizzi IP utilizzati. Per un elenco di indirizzi IP per ogni tag di servizio/area, vedere il file JSON- [intervalli IP di Azure e tag del servizio-cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519). È possibile scaricare e programmare questi indirizzi IP nel firewall, se necessario. Gli intervalli IP riservati per il provisioning di Azure AD sono disponibili in "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Articoli correlati

* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
* [Personalizzazione dei mapping degli attributi per il Provisioning dell’utente](customize-application-attributes.md)
* [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
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
