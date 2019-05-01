---
title: SaaS evasione API V1 | Azure Marketplace
description: Viene illustrato come creare un'offerta SaaS in Azure Marketplace con l'API V1 di evasione degli ordini associati.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 03/28/2019
ms.author: pabutler
ROBOTS: NOINDEX
ms.openlocfilehash: 816bdc61f85fdf171870a5b552661b816ec65e2f
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943132"
---
# <a name="saas-fulfillment-apis-version-1--deprecated"></a>SaaS evasione API versione 1 (deprecata)

Questo articolo illustra come creare un'offerta SaaS con le API. Le API, composte da metodi REST e gli endpoint, sono necessari per consentire le sottoscrizioni per l'offerta SaaS se vendi tramite Azure selezionata.  

> [!WARNING]
> Questa versione iniziale dell'API SaaS evasione degli ordini è deprecata. Usare invece [SaaS evasione API V2](./cpp-saas-fulfillment-api-v2.md).  Questa API è attualmente gestita solo per gestire i server di pubblicazione esistente. 

Le API seguenti sono fornite per semplificare l'integrazione del servizio SaaS con Azure:

-   Risolvi
-   Sottoscrivi
-   Conversione
-   Annullare la sottoscrizione


## <a name="api-methods-and-endpoints"></a>Gli endpoint e metodi dell'API

Le sezioni seguenti descrivono i metodi dell'API e gli endpoint disponibili per l'abilitazione delle sottoscrizioni per un'offerta SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Endpoint per l'API Marketplace e versione dell'API

L'endpoint per l'API di Azure Marketplace è `https://marketplaceapi.microsoft.com`.

La versione API corrente è `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Risolvere la sottoscrizione

L'azione POST durante la risoluzione di un endpoint consente agli utenti di risolvere un token del Marketplace in un ID risorsa permanente.  L'ID risorsa è l'identificatore univoco per la sottoscrizione SAAS. 

Quando un utente viene reindirizzato al sito Web di un ISV, l'URL contiene un token nei parametri di query. Si presuppone che l'ISV usi questo token ed esegua una richiesta per risolverlo. La risposta contiene l'ID di sottoscrizione SAAS univoco, il nome, l'ID dell'offerta e il piano per la risorsa. Questo token è valido solo per un'ora.

*Richiesta*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nome parametro** |     **Descrizione**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  La versione dell'operazione da usare per questa richiesta.   |
|  |  |


*Intestazioni*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | No            | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
| x-ms-correlationid | No            | Valore stringa univoco per l'operazione sul client. Mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| Tipo di contenuto       | Sì          | `application/json`                                        |
| authorization      | Sì          | Il token di connessione JSON Web token (JWT).                    |
| x-ms-marketplace-token| Sì| Il parametro di query token nell'URL quando l'utente viene reindirizzato al sito Web dell'ISV SaaS da Azure. **Nota:** questo token è valido solo per un'ora. Decodificare inoltre l'URL nel valore del token dal browser prima dell'uso.|
|  |  |  |
  

*Corpo della risposta*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Nome parametro** | **Tipo di dati** | **Descrizione**                       |
|--------------------|---------------|---------------------------------------|
| id                 | string        | ID della sottoscrizione SaaS.          |
| subscriptionName| string| Nome della sottoscrizione SaaS impostata dall'utente in Azure durante la sottoscrizione al servizio SaaS.|
| OfferId            | string        | ID dell'offerta sottoscritta dall'utente. |
| planId             | string        | ID del piano sottoscritto dall'utente.  |
|  |  |  |


*Codici di risposta*

| **Codice di stato HTTP** | **Codice di errore**     | **Descrizione**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token risolti.                                                            |
| 400                  | `BadRequest`         | Mancano le intestazioni necessari oppure la versione dell'API specificata non è valida. Non è stato possibile risolvere il token in quanto non valido o scaduto. Una volta generato, il token rimane valido solo un'ora. |
| 403                  | `Forbidden`          | Il chiamante non è autorizzato a eseguire questa operazione.                                 |
| 429                  | `RequestThrottleId`  | Il servizio sta eseguendo l'elaborazione delle richieste, riprovare più tardi.                                |
| 503                  | `ServiceUnavailable` | Il servizio è temporaneamente non disponibile, riprovare più tardi.                                        |
|  |  |  |


*Intestazioni di risposta*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sì          | ID richiesta ricevuta dal client.                                                                   |
| x-ms-correlationid | Sì          | ID di correlazione, se viene passato dal client, in caso contrario, questo valore è l'ID di correlazione del server.                   |
| x-ms-activityid    | Sì          | Valore stringa univoco per tenere traccia della richiesta dal servizio. Questo ID viene usato per qualsiasi banche. |
| Retry-After        | No            | Questo valore viene impostato solo per una risposta 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Sottoscrivi

L'endpoint di sottoscrizione consente agli utenti di avviare una sottoscrizione a un servizio SaaS per un determinato piano e attiva la fatturazione nel sistema commerciale.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome parametro**  | **Descrizione**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | Sottoscrizione di ID di SaaS univoca ottenuto dopo aver risolto il token tramite l'API di risolvere.                              |
| api-version         | La versione dell'operazione da usare per questa richiesta. |
|  |  |

*Intestazioni*

|  **Chiave dell'intestazione**        | **Obbligatorio** |  **Descrizione**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   No          | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| x-ms-correlationid     |   No          | Valore stringa univoco per l'operazione sul client. Questo valore mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| If-Match/If-None-Match |   No          |   Valore assoluto del validator ETag.                                                          |
| content-type           |   Sì        |    `application/json`                                                                   |
|  authorization         |   Sì        |    Il token di connessione JSON Web token (JWT).                                               |
| x-ms-marketplace-modalità sessione| No  | Flag per abilitare la modalità di esecuzione durante la sottoscrizione di un'offerta SaaS. Se impostato, non verrà addebitato nessun importo di sottoscrizione. Ciò è utile per scenari di test di ISV. Impostarlo su **"dryrun"**|
|  |  |  |

*Corpo*

``` json
{
    "lanId": "",
}
```

| **Nome dell'elemento** | **Tipo di dati** | **Descrizione**                      |
|------------------|---------------|--------------------------------------|
| planId           | Stringa (Obbligatoria)        | Id del piano del servizio SaaS che l'utente sta sottoscrivendo.  |
|  |  |  |

*Codici di risposta*

| **Codice di stato HTTP** | **Codice di errore**     | **Descrizione**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Attivazione della sottoscrizione SaaS ricevuta per un determinato piano.                   |
| 400                  | `BadRequest`         | Mancano le intestazioni necessarie oppure il corpo della richiesta in formato JSON non è valido. |
| 403                  | `Forbidden`          | Il chiamante non è autorizzato a eseguire questa operazione.                   |
| 404                  | `NotFound`           | Sottoscrizione non trovata con l'ID specificato                                  |
| 409                  | `Conflict`           | Nella sottoscrione è in corso un'altra operazione.                     |
| 429                  | `RequestThrottleId`  | Il servizio sta eseguendo l’elaborazione delle richieste, riprovare più tardi.                  |
| 503                  | `ServiceUnavailable` | Il servizio è temporaneamente non disponibile, riprovare più tardi.                          |
|  |  |  |

In caso di risposta 202, seguire lo stato dell'operazione richiesta nell'intestazione "Operation-location". L'autenticazione è la stessa usata per le altre API Marketplace.

*Intestazioni di risposta*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sì          | ID richiesta ricevuta dal client.                                                                   |
| x-ms-correlationid | Sì          | ID di correlazione, se viene passato dal client, in caso contrario, questo valore è l'ID di correlazione del server.                   |
| x-ms-activityid    | Sì          | Valore stringa univoco per tenere traccia della richiesta dal servizio. Questo valore viene usato per eventuali riconciliazioni. |
| Retry-After        | Sì          | Intervallo per cui il client può controllare lo stato.                                                       |
| Operation-Location | Sì          | Collegamento a una risorsa per ottenere lo stato dell'operazione.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Modificare il piano endpoint

L'endpoint di modifica consente all'utente di convertire il piano attualmente sottoscritto in un nuovo piano.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome parametro**  | **Descrizione**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID della sottoscrizione SaaS.                              |
| api-version         | La versione dell'operazione da usare per questa richiesta. |
|  |  |

*Intestazioni*

| **Chiave dell'intestazione**          | **Obbligatorio** | **Descrizione**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | No            | Valore stringa univoco per tenere traccia della richiesta dal client. Consigliare un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.   |
| x-ms-correlationid      | No            | Valore stringa univoco per l'operazione sul client. Questo valore mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| If-Match /If-None-Match | No            | Valore assoluto del validator ETag.                              |
| content-type            | Sì          | `application/json`                                        |
| authorization           | Sì          | Il token di connessione JSON Web token (JWT).                    |
|  |  |  |

*Corpo*

```json
{
    "planId": ""
}
```

|  **Nome dell'elemento** |  **Tipo di dati**  | **Descrizione**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Stringa (Obbligatoria)         | Id del piano del servizio SaaS che l'utente sta sottoscrivendo.          |
|  |  |  |

*Codici di risposta*

| **Codice di stato HTTP** | **Codice di errore**     | **Descrizione**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Attivazione della sottoscrizione SaaS ricevuta per un determinato piano.                   |
| 400                  | `BadRequest`         | Mancano le intestazioni necessarie oppure il corpo della richiesta in formato JSON non è valido. |
| 403                  | `Forbidden`          | Il chiamante non è autorizzato a eseguire questa operazione.                   |
| 404                  | `NotFound`           | Sottoscrizione non trovata con l'ID specificato                                  |
| 409                  | `Conflict`           | Nella sottoscrione è in corso un'altra operazione.                     |
| 429                  | `RequestThrottleId`  | Il servizio sta eseguendo l’elaborazione delle richieste, riprovare più tardi.                  |
| 503                  | `ServiceUnavailable` | Il servizio è temporaneamente non disponibile, riprovare più tardi.                          |
|  |  |  |

*Intestazioni di risposta*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sì          | ID richiesta ricevuta dal client.                                                                   |
| x-ms-correlationid | Sì          | ID di correlazione, se viene passato dal client, in caso contrario, questo valore è l'ID di correlazione del server.                   |
| x-ms-activityid    | Sì          | Valore stringa univoco per tenere traccia della richiesta dal servizio. Questo valore viene usato per eventuali riconciliazioni. |
| Retry-After        | Sì          | Intervallo per cui il client può controllare lo stato.                                                       |
| Operation-Location | Sì          | Collegamento a una risorsa per ottenere lo stato dell'operazione.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Eliminare una sottoscrizione

L'azione Eliminare dell'endpoint "sottoscrivere" consente all'utente di eliminare una sottoscrizione con un ID specifico.

*Richiesta*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome parametro**  | **Descrizione**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID della sottoscrizione SaaS.                              |
| api-version         | La versione dell'operazione da usare per questa richiesta. |
|  |  |

*Intestazioni*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | No            | Valore stringa univoco per tenere traccia della richiesta dal client. Consigliare un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.                                                           |
| x-ms-correlationid | No            | Valore stringa univoco per l'operazione sul client. Questo valore mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| authorization      | Sì          | Il token di connessione JSON Web token (JWT).                    |
|  |  |  |

*Codici di risposta*

| **Codice di stato HTTP** | **Codice di errore**     | **Descrizione**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Attivazione della sottoscrizione SaaS ricevuta per un determinato piano.                   |
| 400                  | `BadRequest`         | Mancano le intestazioni necessarie oppure il corpo della richiesta in formato JSON non è valido. |
| 403                  | `Forbidden`          | Il chiamante non è autorizzato a eseguire questa operazione.                   |
| 404                  | `NotFound`           | Sottoscrizione non trovata con l'ID specificato                                  |
| 429                  | `RequestThrottleId`  | Il servizio è occupato a elaborare le richieste, riprovare più tardi.                  |
| 503                  | `ServiceUnavailable` | Il servizio è temporaneamente interrotto, riprovare più tardi. Riprovare più tardi.                          |
|  |  |  |

In caso di risposta 202, seguire lo stato dell'operazione richiesta nell'intestazione "Operation-location". L'autenticazione è la stessa usata per le altre API Marketplace.

*Intestazioni di risposta*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sì          | ID richiesta ricevuta dal client.                                                                   |
| x-ms-correlationid | Sì          | ID di correlazione, se viene passato dal client, in caso contrario, questo è l'ID di correlazione del server.                   |
| x-ms-activityid    | Sì          | Valore stringa univoco per tenere traccia della richiesta dal servizio. Viene usato per eventuali riconciliazioni. |
| Retry-After        | Sì          | Intervallo per cui il client può controllare lo stato.                                                       |
| Operation-Location | Sì          | Collegamento a una risorsa per ottenere lo stato dell'operazione.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Ottenere lo stato dell'operazione

Questo endpoint consente all'utente di tracciare lo stato di un'operazione asincrona attivata (piano di Sottoscrivere/Annullamento della sottoscrizione/Modificare).

*Richiesta*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Nome parametro**  | **Descrizione**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | ID univoco per l'operazione attivata.                |
| api-version         | La versione dell'operazione da usare per questa richiesta. |
|  |  |

*Intestazioni*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | No            | Valore stringa univoco per tenere traccia della richiesta dal client. Consigliare un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.   |
| x-ms-correlationid | No            | Valore stringa univoco per l'operazione sul client. Questo valore mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
| authorization      | Sì          | Il token di connessione JSON Web token (JWT).                    |
|  |  |  | 

*Corpo della risposta*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome parametro** | **Tipo di dati** | **Descrizione**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | string        | ID dell'operazione.                                                                      |
| status             | Enum          | Stato dell'operazione, uno dei seguenti: `In Progress`, `Succeeded` o `Failed`.          |
| resourceLocation   | string        | Collegamento alla sottoscrizione creata o modificata. In questo modo, il client ottiene lo stato successivo all'operazione aggiornato. Questo valore non è impostato per le operazioni `Unsubscribe`. |
| created            | DateTime      | Ora della creazione dell'operazione in formato UTC.                                                           |
| LastModified       | DateTime      | Ultimo aggiornamento dell'operazione in formato UTC.                                                      |
|  |  |  |

*Codici di risposta*

| **Codice di stato HTTP** | **Codice di errore**     | **Descrizione**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | È stata risolta la richiesta di recupero e il corpo contiene la risposta.    |
| 400                  | `BadRequest`         | Mancano le intestazioni richieste oppure è la versione dell'API specificata non è valida. |
| 403                  | `Forbidden`          | Il chiamante non è autorizzato a eseguire questa operazione.                      |
| 404                  | `NotFound`           | Sottoscrizione non trovata con l'ID specificato                                     |
| 429                  | `RequestThrottleId`  | Il servizio sta eseguendo l'elaborazione delle richieste, riprovare più tardi.                     |
| 503                  | `ServiceUnavailable` | Il servizio è temporaneamente non disponibile, riprovare più tardi.                             |
|  |  |  |

*Intestazioni di risposta*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sì          | ID richiesta ricevuta dal client.                                                                   |
| x-ms-correlationid | Sì          | ID di correlazione, se viene passato dal client, in caso contrario, questo è l'ID di correlazione del server.                   |
| x-ms-activityid    | Sì          | Valore stringa univoco per tenere traccia della richiesta dal servizio. Viene usato per eventuali riconciliazioni. |
| Retry-After        | Sì          | Intervallo per cui il client può controllare lo stato.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Ottenere una sottoscrizione

L'azione Ottieni nell'endpoint di sottoscrizione consente agli utenti di recuperare una sottoscrizione con un identificatore di risorsa specificato.

*Richiesta*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome parametro**  | **Descrizione**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID della sottoscrizione SaaS.                              |
| api-version         | La versione dell'operazione da usare per questa richiesta. |
|  |  |

*Intestazioni*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | No            | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.                                                           |
| x-ms-correlationid | No            | Valore stringa univoco per l'operazione sul client. Questo valore mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| authorization      | Sì          | Il token di connessione JSON Web token (JWT).                                                                    |
|  |  |  |

*Corpo della risposta*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome parametro**     | **Tipo di dati** | **Descrizione**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | string        | ID di una risorsa di sottoscrizione SaaS in Azure.    |
| offerId                | string        | ID dell'offerta sottoscritta dall'utente.         |
| planId                 | string        | ID del piano sottoscritto dall'utente.          |
| saasSubscriptionName   | string        | Nome della sottoscrizione SaaS.                |
| saasSubscriptionStatus | Enum          | Stato dell'operazione.  Uno dei seguenti:  <br/> - `Subscribed`: la sottoscrizione è attiva.  <br/> - `Pending`: la risorsa viene creata dall'utente ma non viene attivata dall'ISV.   <br/> - `Unsubscribed`: l'utente ha annullato la sottoscrizione.   <br/> - `Suspended`: l'utente ha sospeso la sottoscrizione.   <br/> - `Deactivated`:  la sottoscrizione di Azure è sospesa.  |
| created                | DateTime      | Valore timestamp relativo alla creazione della sottoscrizione in formato UTC. |
| LastModified           | DateTime      | Valore timestamp relativo alla modifica della sottoscrizione in formato UTC. |
|  |  |  |

*Codici di risposta*

| **Codice di stato HTTP** | **Codice di errore**     | **Descrizione**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | È stata risolta la richiesta di recupero e il corpo contiene la risposta.    |
| 400                  | `BadRequest`         | Mancano le intestazioni richieste oppure è la versione dell'API specificata non è valida. |
| 403                  | `Forbidden`          | Il chiamante non è autorizzato a eseguire questa operazione.                      |
| 404                  | `NotFound`           | Sottoscrizione non trovata con l'ID specificato                                     |
| 429                  | `RequestThrottleId`  | Il servizio sta eseguendo l’elaborazione delle richieste, riprovare più tardi.                     |
| 503                  | `ServiceUnavailable` | Il servizio è temporaneamente non disponibile, riprovare più tardi.                             |
|  |  |  |

*Intestazioni di risposta*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sì          | ID richiesta ricevuta dal client.                                                                   |
| x-ms-correlationid | Sì          | ID di correlazione, se viene passato dal client, in caso contrario, questo è l'ID di correlazione del server.                   |
| x-ms-activityid    | Sì          | Valore stringa univoco per tenere traccia della richiesta dal servizio. Viene usato per eventuali riconciliazioni. |
| Retry-After        | No            | Intervallo per cui il client può controllare lo stato.                                                       |
| eTag               | Sì          | Collegamento a una risorsa per ottenere lo stato dell'operazione.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Ottenere le sottoscrizioni

L'azione Ottieni nell'endpoint di sottoscrizione consente all'utente di recuperare tutte le sottoscrizioni per tutte le offerte dall'ISV.

*Richiesta*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nome parametro**  | **Descrizione**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | La versione dell'operazione da usare per questa richiesta. |
|  |  |

*Intestazioni*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | No            | Valore stringa univoco per tenere traccia della richiesta dal client. Consigliare un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.             |
| x-ms-correlationid | No            | Valore stringa univoco per l'operazione sul client. Questo valore mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| authorization      | Sì          | Il token di connessione JSON Web token (JWT).                    |
|  |  |  |

*Corpo della risposta*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome parametro**     | **Tipo di dati** | **Descrizione**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | string        | ID di una risorsa di sottoscrizione SaaS in Azure.    |
| offerId                | string        | ID dell'offerta sottoscritta dall'utente.         |
| planId                 | string        | ID del piano sottoscritto dall'utente.          |
| saasSubscriptionName   | string        | Nome della sottoscrizione SaaS.                |
| saasSubscriptionStatus | Enum          | Stato dell'operazione.  Uno dei seguenti:  <br/> - `Subscribed`: la sottoscrizione è attiva.  <br/> - `Pending`: la risorsa viene creata dall'utente ma non viene attivata dall'ISV.   <br/> - `Unsubscribed`: l'utente ha annullato la sottoscrizione.   <br/> - `Suspended`: l'utente ha sospeso la sottoscrizione.   <br/> - `Deactivated`:  la sottoscrizione di Azure è sospesa.  |
| created                | DateTime      | Valore timestamp relativo alla creazione della sottoscrizione in formato UTC. |
| LastModified           | DateTime      | Valore timestamp relativo alla modifica della sottoscrizione in formato UTC. |
|  |  |  |

*Codici di risposta*

| **Codice di stato HTTP** | **Codice di errore**     | **Descrizione**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | È stata risolta la richiesta di recupero e il corpo contiene la risposta.    |
| 400                  | `BadRequest`         | Mancano le intestazioni richieste oppure è la versione dell'API specificata non è valida. |
| 403                  | `Forbidden`          | Il chiamante non è autorizzato a eseguire questa operazione.                      |
| 404                  | `NotFound`           | Sottoscrizione non trovata con l'ID specificato                                     |
| 429                  | `RequestThrottleId`  | Il servizio è occupato a elaborare le richieste, riprovare più tardi.                     |
| 503                  | `ServiceUnavailable` | Il servizio è temporaneamente interrotto, riprovare più tardi. Riprovare più tardi.                             |
|  |  |  |

*Intestazioni di risposta*

| **Chiave dell'intestazione**     | **Obbligatorio** | **Descrizione**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sì          | ID richiesta ricevuta dal client.                                                                   |
| x-ms-correlationid | Sì          | ID di correlazione, se viene passato dal client, in caso contrario, questo è l'ID di correlazione del server.                   |
| x-ms-activityid    | Sì          | Valore stringa univoco per tenere traccia della richiesta dal servizio. Viene usato per eventuali riconciliazioni. |
| Retry-After        | No            | Intervallo per cui il client può controllare lo stato.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>Webhook SaaS

Un webhook SaaS viene usato per notificare le modifiche in modo proattivo al servizio SaaS. Questa API POST deve essere non autenticata e verrà chiamata dal servizio Microsoft. Il servizio SaaS dovrà chiamare l'API delle operazioni per la convalida e l'autorizzazione prima di intraprendere un'azione in base alla notifica del webhook. 

*Corpo*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Nome parametro**     | **Tipo di dati** | **Descrizione**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | string       | ID univoco per l'operazione attivata.                |
| activityId   | string        | Valore stringa univoco per tenere traccia della richiesta dal servizio. Viene usato per eventuali riconciliazioni.               |
| subscriptionId                     | string        | ID di una risorsa di sottoscrizione SaaS in Azure.    |
| offerId                | string        | ID dell'offerta sottoscritta dall'utente. Fornito solo con l'azione di aggiornamento.        |
| publisherId                | string        | ID dell'autore dell'offerta SaaS.         |
| planId                 | string        | ID del piano sottoscritto dall'utente. Fornito solo con l'azione di aggiornamento.          |
| action                 | string        | Azione che attiva questa notifica. I valori possibili sono Activate, Delete, Suspend, Reinstate, Update.          |
| Timestamp                 | string        | Valore del timestamp in UTC in cui questa notifica è stata attivata.          |
|  |  |  |


## <a name="next-steps"></a>Passaggi successivi

Gli sviluppatori possono recuperare anche a livello di codice e la manipolazione dei carichi di lavoro, offerte e server di pubblicazione dei profili utilizzando il [API REST di Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
