---
title: Automatizzare il provisioning delle app usando SCIM in Azure Active Directory | Microsoft Docs
description: Azure Active Directory può effettuare automaticamente il provisioning di utenti e gruppi in qualsiasi applicazione o archivio identità gestito da un servizio Web con interfaccia definita nella specifica del protocollo SCIM
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
ms.date: 5/06/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad90cd66d922c29887aaa8094e798edb28022b27
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66015450"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Uso di System for Cross-Domain Identity Management (SCIM) per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory ad applicazioni

## <a name="overview"></a>Panoramica

SCIM è protocolli standardizzati e lo schema che mira a ottenere una maggiore coerenza di unità nella gestione delle identità tra i sistemi. Quando un'applicazione supporta un endpoint SCIM per la gestione degli utenti, il servizio di provisioning utenti di Azure AD può inviare richieste per creare, modificare o eliminare utenti e gruppi a questo endpoint assegnati. 

Molte delle applicazioni per che supporta Azure AD [preintegrato il provisioning utenti automatico](../saas-apps/tutorial-list.md) implementare SCIM in quanto i mezzi per utente di ricevere le notifiche di modifica.  Oltre a questi, i clienti possono connettere le applicazioni che supportano un profilo specifico del [specifica del protocollo SCIM 2.0](https://tools.ietf.org/html/rfc7644) utilizzando l'opzione di integrazione "non inclusa nella raccolta" generico nel portale di Azure. 

L'obiettivo principale di questo articolo è il profilo SCIM 2.0 che Azure AD viene implementata come parte del relativo connettore SCIM generico per le app non inclusa nella raccolta. Tuttavia, corretta di test di un'applicazione che supporta SCIM di Azure ad generico connettore è un passo per arrivare a un'app nella raccolta di Azure AD come che supportano il provisioning degli utenti. Per ulteriori informazioni sulla tua applicazione nella raccolta di applicazioni Azure AD, vedere [come: Includi la tua applicazione nella raccolta di applicazioni Azure AD](../develop/howto-app-gallery-listing.md).
 

>[!IMPORTANT]
>L'ultimo aggiornamento del comportamento dell'implementazione SCIM di Azure AD è stato eseguito il 18 dicembre 2018. Per informazioni sulle modifiche, vedere [Conformità al protocollo SCIM 2.0 del servizio di provisioning utenti di Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Figura 1: Il provisioning da Azure Active Directory in un'applicazione o archivio identità che implementa SCIM*

Questo articolo è suddivisa in quattro sezioni:

* **[Provisioning di utenti e gruppi alle applicazioni di terze parti che supportano SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)**  : se l'organizzazione Usa un'applicazione di terze parti che implementa il profilo SCIM 2.0 da Azure AD supporta, è possibile iniziare ad automatizzare entrambi provisioning e deprovisioning di utenti e gruppi oggi stesso.

* **[Comprendere l'implementazione SCIM di Azure AD](#understanding-the-azure-ad-scim-implementation)**  -se si sta creando un'applicazione che supporta un'API di gestione utente SCIM 2.0, in questa sezione descrive in dettaglio come viene implementato il client SCIM di Azure AD e come deve modellare il protocollo SCIM richiedere la gestione e le risposte.
  
* **[Creazione di un endpoint SCIM usando librerie Microsoft CLI](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  -librerie Common Language Infrastructure (CLI) ed esempi di codice mostrano come sviluppare un endpoint SCIM e convertire messaggi SCIM.  

* **[Riferimento allo schema di utenti e gruppi](#user-and-group-schema-reference)**  -viene descritto lo schema di utenti e gruppi è supportato dall'implementazione SCIM di Azure AD per le app non inclusa nella raccolta. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Provisioning di utenti e gruppi in applicazioni che supportano SCIM
Azure AD può essere configurato a automaticamente il provisioning di determinati utenti e gruppi per le applicazioni che implementano un profilo specifico del [protocollo SCIM 2.0](https://tools.ietf.org/html/rfc7644). Le specifiche del profilo sono documentate in [comprendere l'implementazione SCIM di Azure AD](#understanding-the-azure-ad-scim-implementation).

Verificare la conformità ai requisiti sopra riportati con il provider dell'applicazione o consultando la documentazione fornita dal provider.

>[!IMPORTANT]
>L'implementazione SCIM di Azure AD si basa su utente di Azure AD provisioning del servizio, che è progettato per garantire costantemente degli utenti sincronizzati tra Azure AD e l'applicazione di destinazione e implementa un set molto specifico delle operazioni standard. È importante comprendere tali comportamenti a comprendere il comportamento del client SCIM di Azure AD. Per altre informazioni, vedere [cosa accade durante il provisioning degli utenti?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Introduzione
Le applicazioni che supportano il profilo SCIM descritto in questo articolo possono essere connesse ad Azure Active Directory usando la funzionalità "Applicazione non nella raccolta" nella raccolta di applicazioni di Azure AD. Una volta stabilita la connessione, Azure AD esegue un processo di sincronizzazione ogni 40 minuti in cui interroga l'endpoint SCIM dell'applicazione in merito agli utenti e ai gruppi assegnati e li crea o li modifica in base alle istruzioni di assegnazione.

**Per connettere un'applicazione che supporta SCIM:**

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com). 

1. Selezionare **applicazioni aziendali** nel riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app che sono state aggiunte dalla raccolta.

1. Selezionare **+ nuova applicazione** > **tutti** > **applicazione Non nella raccolta**.

1. Immettere un nome per l'applicazione e selezionare **Add** per creare un oggetto app. La nuova app viene aggiunta all'elenco di applicazioni aziendali e viene visualizzata la schermata di gestione delle app.
    
   ![][1]
   *Figura 2: Raccolta di applicazioni di Azure AD*
    
1. Nella schermata di gestione di app, selezionare **Provisioning** nel riquadro sinistro.
1. Nel menu **Modalità di provisioning** selezionare **Automatica**.
    
   ![][2]
   *Figura 3: Configurazione del provisioning nel portale di Azure*
    
1. Nel campo **URL tenant** immettere l'URL dell'endpoint SCIM dell'applicazione. Esempio: https://api.contoso.com/scim/v2/
1. Se l'endpoint SCIM richiede un token di connessione OAuth da un'autorità di certificazione diversa da Azure AD, copiare il token di connessione OAuth nel campo **Token segreto** facoltativo. Se questo campo viene lasciato vuoto, Azure AD include un token di connessione OAuth emesso da Azure AD con ogni richiesta. Le app che usano Azure AD come provider di identità possono convalidare il token rilasciato da Azure AD.
1. Selezionare **Test connessione** avere Azure Active Directory prova a connettersi all'endpoint SCIM. Se il tentativo non riesce, vengono visualizzate informazioni di errore.  

    >[!NOTE]
    >**Test connessione** esegue query sull'endpoint SCIM per cercare un utente che non esiste usando un GUID casuale come proprietà corrispondente selezionata nella configurazione di Azure AD. La risposta corretta prevista è HTTP 200 OK con un messaggio ListResponse SCIM vuoto. 

1. Se i tentativi di connessione per l'applicazione hanno esito positivo, quindi selezionare **salvare** per salvare le credenziali di amministratore.
1. Nella sezione **Mapping** sono disponibili due set selezionabili di mapping degli attributi: uno per gli oggetti utente e uno per gli oggetti gruppo. Selezionare ognuno dei due set per esaminare gli attributi sincronizzati da Active Directory di Azure con l'app. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli utenti e i gruppi nell'app per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

    >[!NOTE]
    >Facoltativamente, è possibile disattivare la sincronizzazione degli oggetti gruppo disabilitando il mapping relativo ai gruppi. 

1. In **Impostazioni** il campo **Ambito** definisce gli utenti e i gruppi che devono essere sincronizzati. Selezionare **Sincronizza solo utenti e gruppi assegnati** (scelta consigliata) per sincronizzare solo gli utenti e gruppi assegnati nella **utenti e gruppi** scheda.
1. Dopo aver completata la configurazione, impostare il **stato del Provisioning** al **su**.
1. Selezionare **salvare** per avviare il servizio di provisioning di Azure AD. 
1. Se la sincronizzazione solo utenti e gruppi assegnati (scelta consigliati), assicurarsi di selezionare il **utenti e gruppi** scheda e assegnare gli utenti o gruppi che si desidera sincronizzare.

Una volta avviata la sincronizzazione iniziale, è possibile selezionare **log di controllo** nel riquadro sinistro per monitorare lo stato di avanzamento, che mostra tutte le azioni eseguite dal servizio di provisioning sull'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

> [!NOTE]
> La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti fino a quando il servizio è in esecuzione. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Comprendere l'implementazione SCIM di Azure AD

Se si sta creando un'applicazione che supporta un'API di gestione utente SCIM 2.0, in questa sezione descrive in dettaglio come viene implementato il client SCIM di Azure AD e come si deve modellare il protocollo SCIM richiedere la gestione e le risposte. Dopo aver implementato l'endpoint SCIM, è possibile testarlo seguendo la procedura descritta nella sezione precedente.

All'interno di [specifica del protocollo SCIM 2.0](http://www.simplecloud.info/#Specification), l'applicazione deve soddisfare questi requisiti:

* Supporta la creazione di utenti e, facoltativamente, anche i gruppi, come indicato nella sezione [3.3 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Supportare la modifica di utenti o gruppi con richieste PATCH, come descritto [sezione 3.5.2 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Supporta il recupero di una risorsa nota per un utente o gruppo creato in precedenza, come descritto [sezione 3.4.1 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Supportare l'interrogazione di utenti o gruppi, come indicato nella sezione [3.4.2 del protocollo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Per impostazione predefinita, gli utenti vengono recuperati dal loro `id` ed eseguire una query da loro `username` e `externalid`, e i gruppi vengono interrogati da `displayName`.  
* Supportare l'interrogazione di utenti tramite ID e gestione, come descritto nella sezione 3.4.2 del protocollo SCIM.  
* Supportare l'interrogazione di gruppi tramite ID e membro, come indicato nella sezione 3.4.2 del protocollo SCIM.  
* Accetta un singolo token per l'autenticazione e autorizzazione di Azure AD per l'applicazione.

Quando si implementa un endpoint SCIM per garantire la compatibilità con Azure AD, seguire queste linee guida generali:

* `id` è una proprietà obbligatoria per tutte le risorse. Ogni risposta che restituisce una risorsa deve assicurarsi che ogni risorsa dispone di questa proprietà, ad eccezione di `ListResponse` con zero i membri.
* Risposta a una richiesta di query/filtro deve essere sempre un `ListResponse`.
* Gruppi sono facoltativi, ma solo se l'implementazione SCIM supporta le richieste PATCH è supportata.
* Non è necessario includere l'intera risorsa nella risposta PATCH.
* Microsoft Azure AD Usa solo gli operatori seguenti:  
     - `eq`
     - `and`
* Non richiedono una corrispondenza tra maiuscole e minuscole per gli elementi strutturali in SCIM, in particolare delle PATCH `op` valori, operazione, come definito https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD genera i valori di 'op' come `Add`, `Replace`, e `Remove`.
* Microsoft Azure AD invia una richiesta per recuperare un utente casuale e un gruppo per assicurarsi che l'endpoint e le credenziali siano valide. Viene eseguita anche come parte della **Test connessione** a Microsoft flow nel [portale di Azure](https://portal.azure.com). 
* L'attributo che le risorse possono essere interrogate in deve essere impostata come un attributo corrispondente sull'applicazione nel [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [personalizzazione utente dei mapping degli attributi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Provisioning e deprovisioning utenti
La seguente figura illustra i messaggi che Azure Active Directory invia al servizio SCIM per gestire il ciclo di vita di un utente nell'archivio di identità dell'applicazione.  

![][4]
*Figura 4: Sequenza di provisioning e deprovisioning utenti*

### <a name="group-provisioning-and-de-provisioning"></a>Provisioning e deprovisioning gruppi
Gruppo di provisioning e deprovisioning è facoltativo. Quando implementata e abilitata, la figura seguente mostra i messaggi che Azure AD invia al servizio SCIM per gestire il ciclo di vita di un gruppo di nell'archivio di identità dell'applicazione.  Questi messaggi si differenziano dai messaggi relativi agli utenti in due modi: 

* Le richieste per recuperare i gruppi di specificare che l'attributo members deve essere escluso da qualsiasi risorsa fornita in risposta alla richiesta.  
* Le richieste per determinare se un attributo reference ha un determinato valore sono richieste relative all'attributo members.  

![][5]
*Figura 5: Sequenza di provisioning e deprovisioning gruppi*

### <a name="scim-protocol-requests-and-responses"></a>Richieste del protocollo SCIM e risposte
Questa sezione vengono fornite le richieste SCIM di esempio generati da client SCIM di Azure AD e nell'esempio le risposte previste. Per ottenere risultati ottimali, è consigliabile codificare l'app per gestire le richieste in questo formato e generano risposte previsto.

>[!IMPORTANT]
>Per comprendere come e quando il servizio di provisioning utenti di Azure AD emette le operazioni descritte di seguito, vedere [cosa accade durante il provisioning degli utenti?](user-provisioning.md#what-happens-during-provisioning).

- [Operazioni dell'utente](#user-operations)
  - [Crea utente](#create-user)
    - [Richiesta](#request)
    - [Risposta](#response)
  - [Ottieni utente](#get-user)
    - [Richiesta](#request-1)
    - [Risposta](#response-1)
  - [Ottieni utente dalla query](#get-user-by-query)
    - [Richiesta](#request-2)
    - [Risposta](#response-2)
  - [Ottieni utente dalla query - Zero risultati](#get-user-by-query---zero-results)
    - [Richiesta](#request-3)
    - [Risposta](#response-3)
  - [Aggiornamento dell'utente [proprietà multivalore]](#update-user-multi-valued-properties)
    - [Richiesta](#request-4)
    - [Risposta](#response-4)
  - [Aggiornamento dell'utente [proprietà a valore singolo]](#update-user-single-valued-properties)
    - [Richiesta](#request-5)
    - [Risposta](#response-5)
  - [Elimina utente](#delete-user)
    - [Richiesta](#request-6)
    - [Risposta](#response-6)
- [Operazioni sui gruppi](#group-operations)
  - [Creare gruppo](#create-group)
    - [Richiesta](#request-7)
    - [Risposta](#response-7)
  - [Ottenere un gruppo](#get-group)
    - [Richiesta](#request-8)
    - [Risposta](#response-8)
  - [Ottieni gruppo da displayName](#get-group-by-displayname)
    - [Richiesta](#request-9)
    - [Risposta](#response-9)
  - [Gruppo di aggiornamento [attributi Non-membro]](#update-group-non-member-attributes)
    - [Richiesta](#request-10)
    - [Risposta](#response-10)
  - [Gruppo di aggiornamento [aggiungere membri]](#update-group-add-members)
    - [Richiesta](#request-11)
    - [Risposta](#response-11)
  - [Gruppo di aggiornamento [rimuovere membri]](#update-group-remove-members)
    - [Richiesta](#request-12)
    - [Risposta](#response-12)
  - [Elimina gruppo](#delete-group)
    - [Richiesta](#request-13)
    - [Risposta](#response-13)

### <a name="user-operations"></a>Operazioni dell'utente

* Gli utenti possono eseguire query `userName` o `email[type eq "work"]` attributi.  

#### <a name="create-user"></a>Crea utente

###### <a name="request"></a>Richiesta
*/Users POST*
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
*Protocollo HTTP/1.1 201 creato*
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

###### <a name="request"></a>Richiesta
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response"></a>Risposta
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
#### <a name="get-user-by-query"></a>Ottieni utente dalla query

##### <a name="request"></a>Richiesta
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a>Risposta
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

#### <a name="get-user-by-query---zero-results"></a>Ottieni utente dalla query - Zero risultati

##### <a name="request"></a>Richiesta
*GET /Users? filtro = nome utente eq "utente inesistente"*

##### <a name="response"></a>Risposta
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

#### <a name="update-user-multi-valued-properties"></a>Aggiornamento dell'utente [proprietà multivalore]

##### <a name="request"></a>Richiesta
*Applicare PATCH/Users/6764549bef60420686bc protocollo HTTP/1.1*
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

##### <a name="response"></a>Risposta
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

#### <a name="update-user-single-valued-properties"></a>Aggiornamento dell'utente [proprietà a valore singolo]

##### <a name="request"></a>Richiesta
*Applicare PATCH/Users/5171a35d82074e068ce2 protocollo HTTP/1.1*
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

##### <a name="response"></a>Risposta
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

##### <a name="request"></a>Richiesta
*Elimina /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a>Risposta
*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>Operazioni sui gruppi

* Sono sempre possibile creare gruppi con un elenco di membri vuoti.
* I gruppi possono eseguire query di `displayName` attributo.
* Aggiornamento per la richiesta PATCH gruppo deve restituire un *HTTP 204 Nessun contenuto* nella risposta. Restituzione di un corpo con un elenco di tutti i membri non è consigliabile.
* Non è necessario supportare la restituzione di tutti i membri del gruppo.

#### <a name="create-group"></a>Creare un gruppo

##### <a name="request"></a>Richiesta
*POST /Groups HTTP/1.1*
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

##### <a name="response"></a>Risposta
*Protocollo HTTP/1.1 201 creato*
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

##### <a name="request"></a>Richiesta
*GET/gruppi/40734ae655284ad3abcc? excludedAttributes = membri protocollo HTTP/1.1*

##### <a name="response"></a>Risposta
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

#### <a name="get-group-by-displayname"></a>Ottieni gruppo da displayName

##### <a name="request"></a>Richiesta
*GET /Groups? excludedAttributes = membri & filtro = displayName eq "displayName" protocollo HTTP/1.1*

##### <a name="response"></a>Risposta
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
#### <a name="update-group-non-member-attributes"></a>Gruppo di aggiornamento [attributi Non-membro]

##### <a name="request"></a>Richiesta
*Applicare PATCH/gruppi/fa2ce26709934589afc5 protocollo HTTP/1.1*
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

##### <a name="response"></a>Risposta
*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>Gruppo di aggiornamento [aggiungere membri]

##### <a name="request"></a>Richiesta
*Applicare PATCH/gruppi/a99962b9f99d4c4fac67 protocollo HTTP/1.1*
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

##### <a name="response"></a>Risposta
*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>Gruppo di aggiornamento [rimuovere membri]

##### <a name="request"></a>Richiesta
*Applicare PATCH/gruppi/a99962b9f99d4c4fac67 protocollo HTTP/1.1*
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

##### <a name="response"></a>Risposta
*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>Elimina gruppo

##### <a name="request"></a>Richiesta
*Elimina /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a>Risposta
*HTTP/1.1 204 No Content*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Creazione di un endpoint SCIM usando le librerie CLI Microsoft
Tramite la creazione di un servizio web SCIM che si interfaccia con Azure Active Directory, è possibile abilitare il provisioning utenti automatico per qualsiasi applicazione o archivio identità.

Il servizio funziona nel modo seguente:

1. Azure AD fornisce una libreria common language infrastructure (CLI) denominata systemforcrossdomainidentitymanagement, incluso con il codice esempi descrivono di seguito. Gli sviluppatori e integratori di sistemi possono usare questa libreria per creare e distribuire un endpoint del servizio web basato su SCIM in grado di connettersi all'archivio identità di qualsiasi applicazione Azure AD.
2. I mapping vengono implementati nel servizio Web per il mapping dello schema utente standardizzato allo schema utente e al protocollo richiesto dall'applicazione. 
3. L'URL dell'endpoint viene registrato in Azure AD come parte di un'applicazione personalizzata nella raccolta di applicazioni.
4. Gli utenti e i gruppi vengono assegnati a questa applicazione in Azure AD. In base all'assegnazione, si sta inseriti in una coda per essere sincronizzato con l'applicazione di destinazione. Il processo di sincronizzazione che gestisce la coda viene eseguito ogni 40 minuti.

### <a name="code-samples"></a>Esempi di codice
Per rendere più semplice, questo processo [esempi di codice](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) vengono messe a disposizione, che creano un SCIM endpoint servizio web e dimostrare il provisioning automatico. L'esempio è relativo a un provider che gestisce un file con valori delimitati da virgole che rappresentano gli utenti e gruppi di righe.    

**Prerequisiti**

* Visual Studio 2013 o versioni successive
* [Azure SDK per .NET](https://azure.microsoft.com/downloads/)
* Computer Windows che supporta ASP.NET Framework 4.5 da usare come endpoint SCIM. In questo computer deve essere accessibile dal cloud.
* [Una sottoscrizione di Azure con una versione di prova o concessa in licenza di Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Introduzione
Il modo più semplice per implementare un endpoint SCIM in grado di accettare richieste di provisioning da Azure AD consiste nel compilare e distribuire l'esempio di codice che fornisce come output gli utenti con provisioning in un file CSV (Comma-Separated Value).

#### <a name="to-create-a-sample-scim-endpoint"></a>Per creare un endpoint SCIM di esempio

1. Scaricare il pacchetto del codice di esempio dal sito [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Decomprimere il pacchetto e salvarlo nel computer Windows in un percorso analogo a C:\AzureAD-BYOA-Provisioning-Samples\.
1. In questa cartella, avviare il progetto FileProvisioning\Host\FileProvisioningService.csproj in Visual Studio.
1. Selezionare **degli strumenti** > **Gestione pacchetti NuGet** > **Package Manager Console**ed eseguire i comandi seguenti per il Progetto FileProvisioningService per risolvere i riferimenti alla soluzione:

   ```powershell
    Update-Package -Reinstall
   ```

1. Compilare il progetto FileProvisioningService.
1. Avviare l'applicazione prompt dei comandi in Windows come amministratore e usare il comando **cd** per passare alla cartella **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**.
1. Eseguire il comando seguente, sostituendo `<ip-address>` con il nome di dominio o l'indirizzo IP del computer Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. In Windows sotto **le impostazioni di Windows** > **rete e Internet**, selezionare il **Firewall di Windows**  >   **Impostazioni avanzate**e creare un' **regola in ingresso** che consente l'accesso in ingresso alla porta 9000.
1. Se il computer Windows si trova dietro un router, il router deve essere configurato per l'esecuzione Network Access Translation tra la rispettiva porta 9000 esposta a internet e la porta 9000 nel computer Windows. Questa configurazione è necessaria per Azure AD per accedere a questo endpoint nel cloud.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Per registrare l'endpoint SCIM di esempio in Azure AD

1. Accedi per il [portale di Azure Active Directory](https://aad.portal.azure.com). 

1. Selezionare **applicazioni aziendali** nel riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app che sono state aggiunte dalla raccolta.

1. Selezionare **+ nuova applicazione** > **tutti** > **applicazione Non nella raccolta**.

1. Immettere un nome per l'applicazione e selezionare **Add** per creare un oggetto app. L'oggetto applicazione creato deve rappresentare l'app di destinazione in cui verrà effettuato il provisioning e per cui verrà implementato l'accesso Single Sign-On, non solo l'endpoint SCIM.

1. Nella schermata di gestione di app, selezionare **Provisioning** nel riquadro sinistro.

1. Nel menu **Modalità di provisioning** selezionare **Automatica**.
    
   ![][2]
   *Figura 6: Configurazione del provisioning nel portale di Azure*
    
1. Nel campo **URL tenant** immettere l'URL esposto a Internet e la porta dell'endpoint SCIM. Questa voce sarà simile a http://testmachine.contoso.com:9000 o a http://\<indirizzo-IP>:9000/, dove \<indirizzo-IP> è l'indirizzo IP esposto a Internet. 

1. Se l'endpoint SCIM richiede un token di connessione OAuth da un'autorità di certificazione diversa da Azure AD, copiare il token di connessione OAuth nel campo **Token segreto** facoltativo. Se questo campo viene lasciato vuoto, Azure AD includerà un token di connessione OAuth emesso da Azure AD con ogni richiesta. Le app che usano Azure AD come provider di identità possono convalidare il token rilasciato da Azure AD.

1. Selezionare **Test connessione** avere Azure Active Directory prova a connettersi all'endpoint SCIM. Se il tentativo non riesce, vengono visualizzate informazioni di errore.  

    >[!NOTE]
    >**Test connessione** esegue query sull'endpoint SCIM per cercare un utente che non esiste usando un GUID casuale come proprietà corrispondente selezionata nella configurazione di Azure AD. La risposta corretta prevista è HTTP 200 OK con un messaggio ListResponse SCIM vuoto

1. Se i tentativi di connessione per l'applicazione hanno esito positivo, quindi selezionare **salvare** per salvare le credenziali di amministratore.

1. Nella sezione **Mapping** sono disponibili due set selezionabili di mapping degli attributi: uno per gli oggetti utente e uno per gli oggetti gruppo. Selezionare ognuno dei due set per esaminare gli attributi sincronizzati da Active Directory di Azure con l'app. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli utenti e i gruppi nell'app per le operazioni di aggiornamento. Selezionare **Salva** per eseguire il commit delle modifiche.

1. In **Impostazioni**, il campo **Ambito** definisce gli utenti e/o i gruppi che devono essere sincronizzati. Selezionare **"Sincronizza solo utenti e gruppi assegnati** (scelta consigliata) per sincronizzare solo gli utenti e gruppi assegnati nella **utenti e gruppi** scheda.

1. Dopo aver completata la configurazione, impostare il **stato del Provisioning** al **su**.

1. Selezionare **salvare** per avviare il servizio di provisioning di Azure AD. 

1. Se la sincronizzazione solo utenti e gruppi assegnati (scelta consigliati), assicurarsi di selezionare il **utenti e gruppi** scheda e assegnare gli utenti o gruppi che si desidera sincronizzare.

Una volta avviata la sincronizzazione iniziale, è possibile selezionare **log di controllo** nel riquadro sinistro per monitorare lo stato di avanzamento, che mostra tutte le azioni eseguite dal servizio di provisioning sull'app. Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](check-status-user-account-provisioning.md).

Il passaggio finale della verifica dell'esempio consiste nell'aprire il file TargetFile.csv nella cartella \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug del computer Windows. Dopo l'esecuzione del processo di provisioning, questo file include i dettagli di tutti gli utenti e gruppi assegnati e sottoposti a provisioning.

### <a name="development-libraries"></a>Librerie di sviluppo
Per sviluppare un servizio Web personalizzato conforme alla specifica SCIM, è necessario prima di tutto acquisire familiarità con le librerie Microsoft seguenti per accelerare il processo di sviluppo: 

- Le librerie CLI (Common Language Infrastructure) vengono messe a disposizione per essere usate con linguaggi basati su questa infrastruttura, ad esempio C#. Una di queste librerie, Microsoft.SystemForCrossDomainIdentityManagement.Service, dichiara un'interfaccia, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, illustrata nella figura seguente. Uno sviluppatore che usa le librerie implementerà questa interfaccia con una classe a cui è possibile fare riferimento, in modo generico, come provider. Le librerie consentono agli sviluppatori di distribuire un servizio web conforme alla specifica SCIM. Il servizio web può essere ospitato sia in Internet Information Services o un assembly eseguibile della riga di comando. La richiesta viene convertita in chiamate ai metodi del provider, che saranno programmate dallo sviluppatore per agire su un archivio identità.
  
   ![][3]
  
- I [gestori di ExpressRoute](https://expressjs.com/guide/routing.html) sono disponibili per l'analisi di oggetti richiesta node.js che rappresentano chiamate, in base alla definizione della specifica SCIM, effettuate a un servizio Web node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Creazione di un endpoint SCIM personalizzato
Gli sviluppatori che usano le librerie CLI possono ospitare i servizi in un assembly eseguibile della riga di comando o in Internet Information Services. Ecco del codice di esempio per l'hosting di un servizio in un assembly eseguibile all'indirizzo http://localhost:9000: 

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

Per ospitare il servizio in Internet Information Services, uno sviluppatore deve creare un assembly di librerie dell'interfaccia della riga di codice con una classe denominata Startup nello spazio dei nomi predefinito dell'assembly.  Ecco un esempio di questa classe: 

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
Le richieste da Azure Active Directory includono un token di connessione OAuth 2.0.   Qualsiasi servizio che riceve la richiesta deve autenticare l'emittente come Azure Active Directory per il tenant di Azure Active Directory previsto, per l'accesso al servizio web Graph di Azure Active Directory.  Nel token, l'autorità emittente è identificata da un'attestazione iss, ad esempio "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  In questo esempio, l'indirizzo di base del valore dell'attestazione, https://sts.windows.net, identifica Azure Active Directory come autorità di certificazione, mentre il relativo indirizzo segmento, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, è un identificatore univoco del tenant di Azure Active Directory per che è stato emesso il token.  Se il token è stato emesso per l'accesso al servizio Web Graph di Azure Active Directory, l'identificatore di quel servizio, 00000002-0000-0000-c000-000000000000, deve essere incluso nel valore dell'attestazione aud del token.  Ognuna delle applicazioni registrate in un singolo tenant possa ricevere lo stesso `iss` un'attestazione con SCIM richieste.

Gli sviluppatori che usano le librerie CLI fornite da Microsoft per la creazione di un servizio SCIM possono autenticare le richieste da Azure Active Directory usando il pacchetto Microsoft.Owin.Security.ActiveDirectory seguendo questa procedura: 

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

2. Aggiungere il codice seguente al metodo, in modo che qualsiasi richiesta a uno degli endpoint del servizio venga autenticata come se includesse un token emesso da Azure Active Directory per un tenant specifico, per l'accesso al servizio web Graph di Azure AD: 

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
           ValidAudience = "00000002-0000-0000-c000-000000000000"
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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Il provisioning di gestione e il deprovisioning degli utenti

1. Azure Active Directory esegue query nel servizio per trovare un utente con valore dell'attributo externalId corrispondente al valore dell'attributo mailNickname di un utente in Azure AD. La query viene espressa come richiesta Hypertext Transfer Protocol (HTTP), che in questo esempio, dove jyoung è un esempio di mailNickname di un utente in Azure Active Directory.

    >[!NOTE]
    > Questo è solo un esempio. Non tutti gli utenti disporranno dell'attributo mailNickname, e il valore che dispone di un utente potrebbe non essere univoco nella directory. Inoltre, l'attributo usato per la corrispondenza, ovvero in questo caso externalId, è possibile configurare nel [i mapping degli attributi di Azure AD](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Se il servizio è stato creato mediante le librerie CLI fornite da Microsoft per implementare servizi SCIM, la richiesta viene convertita in una chiamata al metodo Query del provider del servizio.  Ecco la firma del metodo: 
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

2. Se la risposta a una query per il servizio web per un utente con un valore dell'attributo externalId corrispondente al valore dell'attributo mailNickname di un utente non restituisce alcun utente, Azure Active Directory richiede che il servizio effettui il provisioning di un utente corrispondente a quello in Azure Active Directory.  Ecco un esempio di questa richiesta: 

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
   Le librerie CLI fornite da Microsoft per implementare servizi SCIM convertiranno tale richiesta in una chiamata al metodo Create del provider del servizio.  Il metodo Create ha la firma seguente: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   In una richiesta di provisioning di un utente, il valore dell'argomento resource è un'istanza della classe Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, definita nella libreria Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Se la richiesta di provisioning dell'utente ha esito positivo, si prevede che l'implementazione del metodo restituisca un'istanza di Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser, con il valore della proprietà Identifier impostato sull'identificatore univoco dell'utente appena sottoposto a provisioning.  

3. Per aggiornare un utente la cui esistenza è nota in un archivio identità gestito da SCIM, Azure Active Directory richiede al servizio lo stato corrente dell'utente con una richiesta simile alla seguente: 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   In un servizio creato mediante le librerie CLI fornite da Microsoft per implementare servizi SCIM, la richiesta viene convertita in una chiamata al metodo Retrieve del provider del servizio.  Ecco la firma del metodo Retrieve: 
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

4. Se un attributo di riferimento deve essere aggiornato, quindi Azure Active Directory esegue query sul servizio per determinare che se il valore corrente dell'attributo reference nell'archivio identità gestito dal servizio corrisponde già il valore dell'attributo in Azure Active Directory. Per gli utenti, l'unico attributo il cui valore corrente viene sottoposto a query con questa modalità è l'attributo manager. Ecco un esempio di una richiesta per determinare se l'attributo manager di un oggetto utente specifico ha attualmente un determinato valore: 

   Se il servizio è stato creato mediante le librerie CLI fornite da Microsoft per implementare servizi SCIM, la richiesta viene convertita in una chiamata al metodo Query del provider del servizio. Il valore delle proprietà dell'oggetto fornito come valore dell'argomento parameters è analogo al seguente: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   In questo caso, il valore dell'indice x può essere 0 e il valore dell'indice y può essere 1, o il valore di x può essere 1 e il valore di y può essere 0, a seconda dell'ordine delle espressioni del parametro filter della query.   

5. Ecco un esempio di una richiesta di Azure Active Directory a un servizio SCIM per l'aggiornamento di un utente: 
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
   Le librerie CLI di Microsoft per implementare servizi SCIM convertiranno la richiesta in una chiamata al metodo Update del provider del servizio. Questa è la firma del metodo Update: 
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

  In questo caso, il valore dell'indice x può essere 0 e il valore dell'indice y può essere 1, o il valore di x può essere 1 e il valore di y può essere 0, a seconda dell'ordine delle espressioni del parametro filter della query.   

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
   Se il servizio è stato creato mediante le librerie CLI fornite da Microsoft per implementare servizi SCIM, la richiesta viene convertita in una chiamata al metodo Delete del provider del servizio.   Il metodo ha la firma seguente: 
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

Le risorse utente sono rilevate dall'identificatore dello schema, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, incluso in questa specifica del protocollo: https://tools.ietf.org/html/rfc7643.  Il mapping predefinito degli attributi degli utenti in Azure Active Directory agli attributi delle risorse dell'utente viene fornito nella tabella 1.  

Le risorse gruppo sono identificate dall'identificatore dello schema, `urn:ietf:params:scim:schemas:core:2.0:Group`. Tabella 2 è illustrato il mapping predefinito degli attributi di gruppi in Azure Active Directory agli attributi del gruppo di risorse.  

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
| objectId |ID |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabella 2: Mapping predefinito degli attributi dei gruppi

| Gruppo Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| membri |membri |
| objectId |ID |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Consentire l'IP indirizzi usati dal servizio di provisioning di Azure AD per effettuare richieste SCIM
Alcune App consentono il traffico in ingresso per la propria app. Affinché il servizio di provisioning di Azure AD a funzionare come previsto, è necessario consentire gli indirizzi IP usati. Per un elenco di indirizzi IP per ogni tag di servizio/area geografica, vedere il file JSON - [intervalli IP di Azure e i tag di servizio: Cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519). È possibile scaricare e programmare questi indirizzi IP nel firewall in base alle esigenze. Gli intervalli di IP riservati per il provisioning di Azure AD sono disponibili in "AzureActiveDirectoryDomainServices."
 

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
