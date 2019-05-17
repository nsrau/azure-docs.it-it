---
title: SaaS evasione API V2 | Azure Marketplace
description: Viene illustrato come creare un'offerta SaaS in AppSource e Azure Marketplace con l'API V2 di evasione degli ordini associati.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pabutler
ms.openlocfilehash: 551f3be2ca23bc18224d28faeea6a6df80eba1db
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823528"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS evasione API versione 2 

Questo articolo descrive in dettaglio l'API che consente a fornitori di software indipendenti (ISV) di vendere le proprie applicazioni SaaS in Azure Marketplace e AppSource. Questa API è un requisito per l'elenco SaaS offre sul Azure Marketplace e AppSource.

## <a name="managing-the-saas-subscription-lifecycle"></a>La gestione del ciclo di vita di sottoscrizione di SaaS

Microsoft SaaS Service gestisce l'intero ciclo di vita di un acquisto dell'abbonamento SaaS e Usa l'API di evasione come un meccanismo per guidare l'evasione di ordini effettivo, modifiche ai piani e l'eliminazione della sottoscrizione con il fornitore di software indipendente. Il cliente viene addebitato lo stato della sottoscrizione SaaS gestito da Microsoft. Il diagramma seguente illustra gli Stati e le operazioni che determinano le modifiche tra stati.

![Stati del ciclo di vita della sottoscrizione SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stati di una sottoscrizione di SaaS

La tabella seguente elenca gli stati di provisioning per una sottoscrizione di SaaS, tra cui un diagramma di sequenza e descrizione per ogni (se applicabile). 

#### <a name="provisioning"></a>Provisioning

Quando un cliente avvia un acquisto, l'ISV riceve queste informazioni in un codice di autenticazione in una pagina in web interattiva dei clienti usando un parametro URL. Ad esempio: `https://contoso.com/signup?token=..`, dove è il provider di URL pagina di destinazione nel centro per i partner `https://contoso.com/signup`. Il codice di autorizzazione può essere convalidato e scambiato per i dettagli di ciò che devono essere sottoposte a provisioning chiama l'API di risolvere.  Al termine del servizio SaaS di provisioning, invia una chiamata di attivazione per segnalare che è stata completata l'evasione di ordini e l'addebito al cliente può.  Il diagramma seguente mostra la sequenza di chiamate API per uno scenario di provisioning.  

![Chiamate API per il provisioning di un servizio SaaS.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Sottoposto a provisioning

Questo stato è lo stato stazionario di un servizio di provisioning.

#### <a name="provisioning-for-update"></a>Il provisioning per l'aggiornamento
(da marketplace) 

Questo stato rappresenta un aggiornamento a un oggetto esistente il servizio è in sospeso. L'aggiornamento può essere avviata dal cliente nel marketplace o nel servizio SaaS (solo per diretto alle transazioni del cliente.) Il diagramma seguente mostra le azioni durante un aggiornamento viene avviato dal marketplace.

![Quando si avvia un aggiornamento dal marketplace di chiamate API.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Il provisioning per l'aggiornamento  
(dal servizio SaaS) 

Il diagramma seguente mostra le azioni durante un aggiornamento viene avviato dal servizio SaaS. (La chiamata al webhook è sostituita da un aggiornamento alla sottoscrizione avviato da Service il SaaS. 

![Quando si avvia un aggiornamento dal servizio SaaS chiamate API.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

Questo stato indica che non è stato ricevuto il pagamento del cliente. Dai criteri, verranno forniti al cliente un periodo di tolleranza prima unfulfilling la sottoscrizione. Quando una sottoscrizione è in questo stato: 

- In qualità di ISV, è possibile ridurre le prestazioni o bloccare l'accesso dell'utente al servizio. 
- La sottoscrizione deve restare in uno stato recuperabile che è possibile ripristinare la funzionalità completa senza alcuna perdita di dati o impostazioni. 
- È possibile prevedere ottenere una richiesta di reintegro per questa sottoscrizione tramite l'API di evasione degli ordini o una richiesta di deprovisioning al termine del periodo di tolleranza. 

#### <a name="unsubscribed"></a>Sottoscrizione annullata 

Le sottoscrizioni raggiungano questo stato in risposta a una richiesta esplicita di clienti o in risposta a mancato pagamento delle quote. L'aspettativa dal sito dell'ISV è che i dati dei clienti vengano mantenuti per il ripristino su richiesta per un minimo di X giorni e quindi eliminati. 


## <a name="api-reference"></a>Informazioni di riferimento per l'API

Questa sezione vengono illustrate le SaaS *API abbonamento* e *operazioni API*.  Il valore della `api-version` parametro per la versione 2 sono API `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definizioni di parametro ed entità

Nella tabella seguente sono elencate le definizioni per i parametri comuni e le entità utilizzate dalle API di evasione degli ordini.

|     Entità/parametro     |     Definizione                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identificatore GUID per una risorsa di SaaS  |
| `name`                   | Nome descrittivo fornito per questa risorsa da parte del cliente |
| `publisherId`            | Identificatore di stringa univoco per ogni server di pubblicazione, ad esempio "contoso" |
| `offerId`                | Identificatore di stringa univoco per ogni offerta, ad esempio "offer1"  |
| `planId`                 | Identificatore di stringa univoco per ogni piano/sku, ad esempio "argento" |
| `operationId`            | Identificatore GUID per una determinata operazione  |
|  `action`                | L'azione eseguita su una risorsa, ovvero `subscribe`, `unsubscribe`, `suspend`, `reinstate`, o `changePlan`, `changeQuantity`, `transfer`  |
|   |   |

Gli identificatori univoci globali ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) sono numeri (32 cifre esadecimali) a 128 bit che in genere vengono generati automaticamente. 

#### <a name="resolve-a-subscription"></a>Risolvere una sottoscrizione 

L'endpoint di risoluzione consente all'editore risolvere un token di marketplace a un ID di risorsa permanente. L'ID risorsa è l'identificatore univoco per la sottoscrizione SAAS.  Quando un utente viene reindirizzato al sito Web di un ISV, l'URL contiene un token nei parametri di query. Si presuppone che l'ISV usi questo token ed esegua una richiesta per risolverlo. La risposta contiene l'ID di sottoscrizione SAAS univoco, il nome, l'ID dell'offerta e il piano per la risorsa. Questo token è valido solo per un'ora. 

**messaggio:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versione dell'operazione da usare per questa richiesta  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo contenuto      | `application/json` |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |
|  x-ms-marketplace-token  |  Parametro di query token nell'URL quando l'utente viene reindirizzato al sito Web dell'ISV SaaS di Azure (per es: `https://contoso.com/signup?token=..`). *Nota:* L'URL decodifica il valore del token dal browser prima di poterla usare.  |

*Codici di risposta:*

Codice: 200<br>
Consente di risolvere il token opaco a una sottoscrizione di SaaS.<br>

```json
Response body:
{
    "subscriptionId": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Codice: 404<br>
Non trovato

Codice: 400<br>
Richiesta non valida. x-ms-marketplace-token è scaduto, in formato non valido o mancante.

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API della sottoscrizione

La sottoscrizione API supporta le operazioni HTTPS seguenti: **Ottenere**, **Post**, **Patch**, e **eliminare**.


#### <a name="list-subscriptions"></a>Elenca sottoscrizioni

Elenca tutte le sottoscrizioni di SaaS per un server di pubblicazione.

**Ottieni:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Parametri di query:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Tipo contenuto       |  `application/json`  |
| x-ms-requestid     |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
| authorization      |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codici di risposta:*

Codice: 200 <br/>
Basata sul token auth, ottenere il server di pubblicazione e sottoscrizioni corrispondenti per le offerte dell'editore.<br> Payload della risposta:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Il token di continuazione saranno presente solo se sono presenti altre "pagine" di piani da recuperare. 

Codice: 403 <br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente. 

Codice: 500 - Errore interno del server

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Ottieni una sottoscrizione

Ottiene la sottoscrizione di SaaS specificata. Usare questa chiamata per ottenere informazioni sulla licenza e informazioni sul piano.

**Ottieni:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token tramite l'API di risoluzione   |
|  ApiVersion        |   Versione dell'operazione da usare per questa richiesta   |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo contenuto      |  `application/json`  |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codici di risposta:*

Codice: 200<br>
Ottiene SaaS sottoscrizione dall'identificatore<br> Payload della risposta:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Codice: 404<br>
Non trovato<br> 

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 500<br>
Internal Server Error<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Elenco di piani disponibili

Usare questa chiamata per scoprire se esistono eventuali offerte pubblica/privata per il server di pubblicazione corrente.

**Ottieni:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Versione dell'operazione da usare per questa richiesta  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo contenuto     |  `application/json` |
|   x-ms-requestid   |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  x-ms-correlationid  | Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  authorization     |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Codici di risposta:*

Codice: 200<br>
Ottenere un elenco dei piani disponibili per un cliente.<br>

Corpo della risposta:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Codice: 404<br>
Non trovato<br> 

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente. <br> 

Codice: 500<br>
Internal Server Error<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Attivare una sottoscrizione

**Post:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versione dell'operazione da usare per questa richiesta  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risoluzione  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo contenuto      | `application/json`  |
|  x-ms-requestid    | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid  | Valore stringa univoco per l'operazione sul client. Questa stringa mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) |

*Richiesta:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Codici di risposta:*

Codice: 200<br>
Attiva la sottoscrizione.<br>

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Modificare il piano della sottoscrizione

Aggiornare il piano della sottoscrizione.

**benda:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo contenuto      | `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.    |
| authorization      |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Payload della richiesta:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Collegamento a una risorsa per ottenere lo stato dell'operazione.   |

*Codici di risposta:*

Codice: 202<br>
La richiesta di modifica piano è stata accettata. L'ISV è previsto per eseguire il polling Operation-Location per determinare un esito positivo o negativo. <br>

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida delle richieste non valida.

>[!Note]
>Solo un piano o una quantità può essere stato modificato con patch in una sola volta, ma non entrambi. Modifica di una sottoscrizione con **Update** non è presente nel `allowedCustomerOperations`.

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-quantity-on-the-subscription"></a>Modificare la quantità di sottoscrizione

Aggiornare la quantità della sottoscrizione.

**benda:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo contenuto      | `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.    |
| authorization      |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Payload della richiesta:*

```json
Request Body:
{
    "quantity": 5
}
```

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Collegamento a una risorsa per ottenere lo stato dell'operazione.   |

*Codici di risposta:*

Codice: 202<br>
Accettato. La richiesta per modificare la quantità è stata accettata. L'ISV è previsto per eseguire il polling Operation-Location per determinare un esito positivo o negativo. <br>

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida delle richieste non valida.

>[!Note]
>Solo un piano o una quantità può essere stato modificato con patch in una sola volta, ma non entrambi. Modifica di una sottoscrizione con **Update** non è presente nel `allowedCustomerOperations`.

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Elimina una sottoscrizione

Annullare la sottoscrizione ed eliminare la sottoscrizione specificata.

**Delete:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo contenuto     |  `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.   |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.   |
|  authorization     |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codici di risposta:*

Codice: 202<br>
Chiamata di ISV avviata per indicare annullare la sottoscrizione in una sottoscrizione di SaaS.<br>

Codice: 404<br>
Non trovato

Codice: 400<br>
Eliminare una sottoscrizione con **eliminare** non in `allowedCustomerOperations`.

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>Operazioni API

L'API di operazioni supporta le seguenti operazioni Patch e Get.

#### <a name="list-outstanding-operations"></a>Elenco di operazioni in sospeso 

Elenca le operazioni in sospeso per il server di pubblicazione corrente. 

**Ottieni:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Parametri di query:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   La versione dell'operazione da usare per questa richiesta.                |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo contenuto     |  `application/json` |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codici di risposta:*

Codice: 200<br> Ottiene l'elenco di operazioni su una sottoscrizione in sospeso.<br>
Payload della risposta:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Ottieni stato dell'operazione

Consente di tenere traccia dello stato dell'operazione asincrona di attivazione specificato nel server di pubblicazione (sottoscrizione / annullare la sottoscrizione / Modifica pianificazione / modifica quantità).

**Ottieni:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo contenuto      |  `application/json`   |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |[Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Codici di risposta:* Codice: 200<br> Ottiene l'oggetto specificato SaaS operazione in sospeso<br>
Payload della risposta:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.
 
Codice: 500<br> Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aggiornare lo stato di un'operazione

Aggiornare lo stato di un'operazione per indicare esito positivo o negativo con i valori forniti.

**benda:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |
|  operationId       | L'operazione in corso di completamento. |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo contenuto     | `application/json`   |
|   x-ms-requestid   |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
|  authorization     |  [Ottenere il token bearer JSON web token (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  |

*Payload della richiesta:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Codici di risposta:*

Codice: 200<br> Chiamata a informare del completamento di un'operazione sul lato fornitore di software indipendente. Ad esempio, questa risposta è stato possibile segnalare la modifica dei posti a sedere/piani.

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Autorizzazioni mancanti. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 409<br>
Conflitto. Ad esempio, una transazione più recente viene già acquisita

Codice: 500<br> Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Webhook nel servizio SaaS

Il server di pubblicazione deve implementare un webhook nel servizio SaaS per ricevere proattivamente notifiche agli utenti delle modifiche nel proprio servizio. L'API deve essere non autenticato e verrà chiamato dal servizio Microsoft SaaS. Il servizio SaaS è previsto per chiamare le operazioni API per convalidare e l'autorizzazione prima di intraprendere un'azione sulla notifica di webhook.

```json
{
    "operationId": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Subscribe",
    "timeStamp": "2018-12-01T00:00:00"
}
```

Azione in cui può essere uno dei seguenti: 
- `Subscribe`  (Quando la risorsa è stata attivata)
- `Unsubscribe` (Quando la risorsa è stata eliminata)
- `ChangePlan` (Quando ha completato l'operazione del piano di modifica)
- `ChangeQuantity` (Quando ha completato l'operazione di modifica quantità)
- `Suspend` (Quando la risorsa è stata sospesa)
- `Reinstate` (Quando risorsa è stata reintrodotta dopo la sospensione)


## <a name="mock-api"></a>API fittizia

È possibile usare le nostre API fittizie che consentono di introduzione allo sviluppo, in particolare la creazione di prototipi e test di progetti. 

Endpoint dell'host: `https://marketplaceapi.microsoft.com/api` <br/>
Versione dell'API: `2018-09-15` <br/>
Nessuna autenticazione <br/>
Uri di esempio: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

I percorsi di endpoint API siano gli stessi in simulazione sia le API di reale, ma le versioni dell'API sono diverse. La versione è 2018-09-15 per la simulazione e 2018-08-31 per la versione di produzione. 

Una delle chiamate API in questo articolo può accadere per l'endpoint host fittizio. È possibile prevedere ottenere dati fittizi nuovamente come risposta. In generale, è possibile prevedere ottenere dati fittizi nuovamente come risposta. Chiamate ai metodi di sottoscrizione di aggiornamento sull'API fittizia restituiscono sempre 500. 

## <a name="next-steps"></a>Passaggi successivi

Gli sviluppatori possono recuperare anche a livello di codice e la manipolazione dei carichi di lavoro, offerte e server di pubblicazione dei profili utilizzando il [API REST di Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
