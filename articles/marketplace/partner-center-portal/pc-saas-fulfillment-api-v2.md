---
title: API di evasione SaaS V2 in Microsoft Commercial Marketplace
description: Informazioni su come creare e gestire un'offerta SaaS in Microsoft AppSource e in Azure Marketplace usando le API di evasione versione 2.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 6c890e9fbda316bfa7f5f3a42572f35ca73811ea
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931802"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>API di evasione SaaS versione 2 nel Marketplace commerciale

Questo articolo illustra in dettaglio le API che consentono ai partner di vendere le proprie offerte SaaS in Microsoft AppSource e in Azure Marketplace. Un editore è necessario per implementare l'integrazione con queste API per pubblicare un'offerta SaaS transazionale nel centro per i partner.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gestione del ciclo di vita della sottoscrizione SaaS

Il Marketplace commerciale gestisce l'intero ciclo di vita di una sottoscrizione SaaS dopo l'acquisto da parte del cliente finale.  Usa la pagina di destinazione, le API di evasione, le API per le operazioni e il webhook come meccanismo per gestire l'attivazione e l'utilizzo effettivo della sottoscrizione SaaS, gli aggiornamenti e l'annullamento della sottoscrizione.  La fattura del cliente finale è basata sullo stato della sottoscrizione SaaS gestita da Microsoft. 

### <a name="states-of-a-saas-subscription"></a>Stati di una sottoscrizione SaaS

Vengono visualizzati gli Stati di una sottoscrizione SaaS e le azioni applicabili.

![Ciclo di vita di una sottoscrizione SaaS nel Marketplace](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>Acquistato ma non ancora attivato (*PendingFulfillmentStart*)

Dopo che un cliente finale (o CSP) acquista un'offerta SaaS nel Marketplace, l'editore deve ricevere una notifica dell'acquisto in modo che un nuovo account SaaS venga creato e configurato per il cliente finale sul lato dell'editore.

Per la creazione dell'account:

1. Il cliente deve fare clic sul pulsante **Configura** che è disponibile per un'offerta SaaS dopo il completamento dell'acquisto in Microsoft AppSource o portale di Azure. O nel messaggio di posta elettronica che il cliente riceverà poco dopo l'acquisto.
2. Microsoft informa quindi il partner sull'acquisto aprendo nella scheda nuovo browser l'URL della pagina di destinazione con il parametro token (il token di identificazione dell'acquisto del Marketplace commerciale).

Un esempio di tale chiamata è `https://contoso.com/signup?token=<blob>` , mentre l'URL della pagina di destinazione per questa offerta SaaS in Partner Center è configurato come `https://contoso.com/signup` . Questo token fornisce al server di pubblicazione un ID che identifica in modo univoco l'acquisto SaaS e il cliente.

>[!NOTE]
>Il server di pubblicazione non riceverà alcuna notifica dell'acquisto SaaS finché il cliente non avvierà il processo di configurazione dal lato Microsoft.

L'URL della pagina di destinazione deve essere attivo e in esecuzione 24x7 e pronto a ricevere sempre nuove chiamate da Microsoft. Se la pagina di destinazione non è più disponibile, i clienti non saranno in grado di iscriversi al servizio SaaS e iniziare a usarlo.

Il *token* deve quindi essere passato di nuovo a Microsoft dal server di pubblicazione chiamando l' [API di risoluzione Saas](#resolve-a-purchased-subscription), come valore del `x-ms-marketplace-token header` parametro header.  Come risultato della chiamata all'API Resolve, il token viene scambiato per i dettagli dell'acquisto SaaS, ad esempio l'ID univoco dell'acquisto, l'ID offerta acquistato, l'ID piano acquistato e così via.

Nella pagina di destinazione il cliente deve essere connesso all'account SaaS nuovo o esistente tramite Azure Active Directory (Azure AD) Single Sign-on (SSO).

Il server di pubblicazione deve implementare l'accesso SSO per fornire l'esperienza utente richiesta da Microsoft per questo flusso.  Quando si configura l'accesso SSO, assicurarsi di usare l'applicazione Azure AD multi-tenant, consentire account aziendali o dell'Istituto di istruzione o account Microsoft personali.  Questo requisito si applica solo alla pagina di destinazione e per gli utenti che vengono reindirizzati al servizio SaaS quando si è già connessi con le credenziali Microsoft. Non si applica a tutti gli account di accesso al servizio SaaS.

> [!NOTE]
>Se l'accesso SSO richiede che un amministratore conceda un'autorizzazione a un'app, la descrizione dell'offerta nel centro per i partner deve rivelare che è necessario l'accesso a livello di amministratore. In conformità con i criteri di [certificazione del Marketplace commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies#10003-authentication-options).

Una volta effettuato l'accesso, il cliente deve completare la configurazione SaaS sul lato server di pubblicazione. Il server di pubblicazione deve quindi chiamare l' [API di sottoscrizione Activate](#activate-a-subscription) per inviare un segnale al Marketplace che il provisioning dell'account Saas è stato completato.
Verrà avviato il ciclo di fatturazione del cliente. Se la chiamata all'API per l'attivazione della sottoscrizione non riesce, il cliente non viene addebitato per l'acquisto.


![Chiamate API per uno scenario di provisioning](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Attivo (sottoscritto)

Questo stato è lo stato stabile di una sottoscrizione SaaS di cui è stato effettuato il provisioning. Quando la chiamata all' [API di attivazione della sottoscrizione](#activate-a-subscription) viene elaborata sul lato Microsoft, la sottoscrizione Saas è contrassegnata come sottoscritta. Il servizio SaaS è ora pronto per essere usato dal cliente sul lato dell'editore e la fatturazione del cliente.

Quando la sottoscrizione SaaS è già attiva e il cliente sceglie di avviare **Gestisci** esperienza Saas dall'interfaccia di amministrazione di portale di Azure o M365, l' **URL della pagina di destinazione** viene di nuovo chiamato da Microsoft con il parametro *token* , come nel flusso di attivazione.  Il server di pubblicazione deve distinguere tra i nuovi acquisti e la gestione degli account SaaS esistenti e gestire di conseguenza la chiamata URL della pagina di destinazione.

#### <a name="being-updated-subscribed"></a>In corso di aggiornamento (sottoscrizione)

Questa azione indica che un aggiornamento a una sottoscrizione SaaS attiva esistente è stato elaborato da Microsoft e dal server di pubblicazione. Un aggiornamento di questo tipo può essere avviato da:

- il cliente del Marketplace commerciale.
- CSP dal Marketplace commerciale.
- il cliente dal sito SaaS dell'editore (non si applica agli acquisti del CSP).

Sono disponibili due tipi di aggiornamenti per una sottoscrizione SaaS:

- Piano di aggiornamento quando il cliente sceglie un altro piano per la sottoscrizione.
- Aggiornare la quantità quando il cliente modifica il numero di postazioni acquistate per la sottoscrizione

È possibile aggiornare solo una sottoscrizione attiva. Durante l'aggiornamento della sottoscrizione, il suo stato rimane attivo sul lato Microsoft.

##### <a name="update-initiated-from-the-marketplace"></a>Aggiornamento avviato dal Marketplace

In questo flusso il cliente modifica il piano di sottoscrizione o la quantità di postazioni dall'interfaccia di amministrazione di M365.  

1. Una volta immesso un aggiornamento, Microsoft chiamerà l'URL del webhook dell'editore, configurato nel campo **connessione webhook** nel centro per i partner, con un valore appropriato per l' *azione* e altri parametri pertinenti.  
1. Il lato server di pubblicazione deve apportare le modifiche necessarie al servizio SaaS e notificare a Microsoft il completamento della modifica chiamando lo [stato di aggiornamento dell'API dell'operazione](#update-the-status-of-an-operation).
1. Se la patch viene inviata con lo stato di errore, il processo di aggiornamento non verrà completato sul lato Microsoft.  La sottoscrizione SaaS verrà lasciata con il piano esistente e la quantità di postazioni.

Di seguito è illustrata la sequenza di chiamate API per uno scenario di aggiornamento avviato dal Marketplace.

![Chiamate API per un aggiornamento avviato dal Marketplace](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>Aggiornamento avviato dal server di pubblicazione

In questo flusso il cliente modifica il piano di sottoscrizione o la quantità di postazioni acquistate dal servizio SaaS stesso. 

1. Prima di apportare la modifica richiesta sul lato server di pubblicazione, è necessario che il codice dell'editore chiami l'API del [piano di modifica](#change-the-plan-on-the-subscription) e/o l'API di [modifica della quantità](#change-the-quantity-of-seats-on-the-saas-subscription) . 

1. Microsoft applicherà la modifica alla sottoscrizione e quindi invierà una notifica al server di pubblicazione tramite il **webhook di connessione** per applicare la stessa modifica.  

1. Solo il server di pubblicazione deve apportare la modifica necessaria alla sottoscrizione SaaS e inviare una notifica a Microsoft quando la modifica viene eseguita chiamando [lo stato di aggiornamento dell'API dell'operazione](#update-the-status-of-an-operation).

Sequenza di chiamate API per lo scenario di aggiornamento avviato dal lato server di pubblicazione.

![Chiamate API per un aggiornamento avviato dal lato Publisher](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>Sospesa (*sospesa*)

Questo stato indica che il pagamento di un cliente per il servizio SaaS non è stato ricevuto. Il server di pubblicazione riceverà una notifica di questa modifica nello stato di sottoscrizione SaaS da parte di Microsoft. La notifica viene eseguita tramite una chiamata a webhook con il parametro *Action* impostato su *suspended*.

Il server di pubblicazione può o meno apportare modifiche al servizio SaaS sul lato server di pubblicazione. È consigliabile che il server di pubblicazione renda disponibili tali informazioni ai clienti sospesi e limiti o blocchi l'accesso del cliente al servizio SaaS.  Esiste una probabilità che il pagamento non venga mai ricevuto.

Microsoft concede al cliente un periodo di tolleranza di 30 giorni prima di annullare automaticamente la sottoscrizione. Quando una sottoscrizione è in stato sospeso:

* L'account SaaS deve essere mantenuto in uno stato reversibile da parte dell'ISV. È possibile ripristinare le funzionalità complete senza alcuna perdita di dati o impostazioni.
* Si prevede di ottenere una richiesta di reintegro per questa sottoscrizione se il pagamento viene ricevuto durante il periodo di tolleranza o una richiesta di deprovisioning alla fine del periodo di tolleranza, sia tramite il meccanismo webhook.

Lo stato della sottoscrizione viene modificato in sospeso sul lato Microsoft prima che il server di pubblicazione accetti qualsiasi azione. È possibile sospendere solo le sottoscrizioni attive.

#### <a name="reinstated-suspended"></a>Reintegrato (*sospeso*)

È in corso il ripristino della sottoscrizione.

Questa azione indica che lo strumento di pagamento del cliente è stato nuovamente valido e viene effettuato un pagamento per la sottoscrizione SaaS.  È in corso il ripristino della sottoscrizione. In questo caso: 

1. Microsoft chiama il webhook con un parametro di *azione* impostato sul valore di *reintegro* .  
1. Il server di pubblicazione garantisce che la sottoscrizione sia completamente operativa sul lato dell'editore.
1. Il server di pubblicazione chiama l' [API dell'operazione patch](#update-the-status-of-an-operation) con stato di esito positivo.  
1. Il ripristino avrà esito positivo e il cliente verrà fatturato nuovamente per la sottoscrizione SaaS. 
1. Se la patch viene inviata con lo stato di errore, il processo di ripristino non verrà completato sul lato Microsoft. La sottoscrizione resterà sospesa.

Se la patch viene inviata con lo stato di errore, il processo di ripristino non verrà completato sul lato Microsoft.  La sottoscrizione resterà sospesa.

È possibile ripristinare solo una sottoscrizione sospesa.  Durante il ripristino di una sottoscrizione SaaS, il suo stato rimane sospeso.  Al termine dell'operazione, lo stato della sottoscrizione diventerà attivo.

#### <a name="renewed-subscribed"></a>Rinnovato (*sottoscritto*)

Alla fine del periodo di validità della sottoscrizione (dopo un mese o un anno), la sottoscrizione SaaS verrà rinnovata automaticamente da Microsoft.  Il valore predefinito per l'impostazione di rinnovo automatico è *true* per tutte le sottoscrizioni Saas. Le sottoscrizioni SaaS attive continueranno a essere rinnovate a cadenza regolare. Microsoft non invia una notifica al server di pubblicazione quando viene rinnovata una sottoscrizione. Un cliente può disabilitare il rinnovo automatico per una sottoscrizione SaaS tramite il portale di amministrazione di M365 o tramite portale di Azure.  In questo caso, la sottoscrizione SaaS verrà annullata automaticamente alla fine del periodo di fatturazione corrente.  I clienti possono anche annullare la sottoscrizione SaaS in qualsiasi momento.

Solo le sottoscrizioni attive vengono rinnovate automaticamente.  Le sottoscrizioni resteranno attive durante il processo di rinnovo e se il rinnovo automatico ha esito positivo.  Dopo il rinnovo, le date di inizio e di fine del periodo di validità della sottoscrizione verranno aggiornate alle date del nuovo termine.

Se un rinnovo automatico ha esito negativo a causa di un problema relativo al pagamento, la sottoscrizione verrà sospesa.  Verrà inviata una notifica al server di pubblicazione.

#### <a name="canceled-unsubscribed"></a>Annullato (annullamento della*sottoscrizione*) 

Le sottoscrizioni raggiungono questo stato in risposta a un'azione esplicita del cliente o del CSP mediante l'annullamento di una sottoscrizione dal sito del server di pubblicazione, portale di Azure o dall'interfaccia di amministrazione di M365.  Una sottoscrizione può anche essere annullata in modo implicito, a causa del mancato pagamento delle quote, dopo che è stato sospeso per 30 giorni.

Quando viene ricevuta una chiamata al webhook di annullamento, il server di pubblicazione deve conservare i dati del cliente per il ripristino su richiesta per almeno sette giorni. Solo i dati del cliente possono essere eliminati.

Una sottoscrizione SaaS può essere annullata in qualsiasi punto del ciclo di vita. Una volta annullata, non è possibile riattivare una sottoscrizione.

## <a name="api-reference"></a>Informazioni di riferimento sulle API

Questa sezione illustra la sottoscrizione SaaS e le API per le operazioni.

Le **API di sottoscrizione** devono essere usate per gestire il ciclo di vita della sottoscrizione Saas dall'acquisto all'annullamento.

**Le API per le operazioni** devono essere usate per:

* verificare e confermare le chiamate al webhook elaborate
* ottenere un elenco di app in sospeso in attesa di essere riconosciute dal server di pubblicazione

### <a name="enforcing-tls-12-note"></a>Applicazione della nota TLS 1,2

La versione di TLS 1,2 verrà applicata presto come versione minima per le comunicazioni HTTPS. Assicurarsi di usare questa versione di TLS nel codice.  Il protocollo TLS versione 1,0 e 1,1 verrà presto deprecato.

### <a name="subscription-apis"></a>API di sottoscrizione

#### <a name="resolve-a-purchased-subscription"></a>Risolvere una sottoscrizione acquistata

L'endpoint di risoluzione consente al server di pubblicazione di scambiare il token di identificazione di acquisto del Marketplace (definito *token* in [acquistato ma non ancora attivato](#purchased-but-not-yet-activated-pendingfulfillmentstart)) a un ID sottoscrizione Saas acquistato persistente e ai relativi dettagli.

Quando un cliente viene reindirizzato all'URL della pagina di destinazione del partner, il token di identificazione del cliente viene passato come parametro *token* in questa chiamata URL. È previsto che il partner usi questo token ed effettui una richiesta di risoluzione. La risposta dell'API Resolve contiene l'ID sottoscrizione SaaS e altri dettagli per identificare in modo univoco l'acquisto. Il *token* fornito con la chiamata URL della pagina di destinazione è in genere valido per 24 ore. Se il *token* ricevuto è già scaduto, è consigliabile fornire le linee guida seguenti al cliente finale:

"Non è stato possibile identificare l'acquisto. riaprire questa sottoscrizione SaaS in portale di Azure o nell'interfaccia di amministrazione di M365, quindi fare clic sul pulsante" Configura account "o" Gestisci account "".

La chiamata all'API di risoluzione restituirà i dettagli e lo stato della sottoscrizione per le sottoscrizioni SaaS in tutti gli stati supportati.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Inserisci`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilizzare 2018-08-31.   |

*Intestazioni della richiesta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  `x-ms-correlationid` |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `authorization`     |  Un token di accesso univoco che identifica il server di pubblicazione che effettua questa chiamata API. Il formato è `"Bearer <accessaccess_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |
|  `x-ms-marketplace-token`  | Parametro del *token* di identificazione dell'acquisto del Marketplace da risolvere.  Il token viene passato nella chiamata URL della pagina di destinazione quando il cliente viene reindirizzato al sito Web del partner SaaS (ad esempio: `https://contoso.com/signup?token=<token><authorization_token>` ). <br> <br>  *Nota:* Il valore del *token* da codificare fa parte dell'URL della pagina di destinazione, quindi deve essere decodificato prima di essere usato come parametro in questa chiamata API.  <br> <br> Un esempio di stringa codificata nell'URL è simile al seguente: `contoso.com/signup?token=ab%2Bcd%2Fef` , dove token è `ab%2Bcd%2Fef` .  Lo stesso token decodificato sarà: `Ab+cd/ef` |
| | |

*Codici di risposta:*

Codice: 200 restituisce identificatori di sottoscrizione SaaS univoci in base all'oggetto `x-ms-marketplace-token` fornito.

Esempio di corpo della risposta:

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased offer's plan ID
  "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
  "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
      "pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

Codice: 400 richiesta non valida. `x-ms-marketplace-token` mancante, non valido, non valido o scaduto.

Codice: 403 non consentito. Il token di autorizzazione non è valido, è scaduto o non è stato specificato.  La richiesta sta tentando di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) .

Codice: 500 errore interno del server.  Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="activate-a-subscription"></a>Attivare una sottoscrizione

Dopo aver configurato l'account SaaS per un cliente finale, il server di pubblicazione deve chiamare l'API di attivazione della sottoscrizione sul lato Microsoft.  Il cliente non verrà fatturato, a meno che questa chiamata API abbia esito positivo.

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Inserisci`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Utilizzare 2018-08-31.   |
| `subscriptionId` | Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l' [API di risoluzione](#resolve-a-purchased-subscription).
 |

*Intestazioni della richiesta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `x-ms-correlationid` |  Valore stringa univoco per l'operazione sul client.  Questa stringa mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `authorization`      |  Un token di accesso univoco che identifica il server di pubblicazione che effettua questa chiamata API. Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Esempio di payload della richiesta:*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*Codici di risposta:*

Codice: 200 la sottoscrizione è stata contrassegnata come sottoscritta sul lato Microsoft.

Nessun corpo della risposta per questa chiamata.

Codice: 400 richiesta non valida: convalida non riuscita.

* `planId` non esiste nel payload della richiesta.
* `planId` nel payload della richiesta non corrisponde a quello acquistato.
* `quantity` nel payload della richiesta non corrisponde a quello acquistato
* La sottoscrizione SaaS è in stato sottoscritto o sospeso.

Codice: 403 non consentito. Il token di autorizzazione non è valido, è scaduto o non è stato specificato. La richiesta sta tentando di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) .

Codice: 404 non trovato. La sottoscrizione SaaS è in stato non sottoscritto.

Codice: 500 errore interno del server.  Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-list-of-all-subscriptions"></a>Ottiene l'elenco di tutte le sottoscrizioni

Recupera un elenco di tutte le sottoscrizioni SaaS acquistate per tutte le offerte pubblicate dall'editore nel Marketplace.  Verranno restituite le sottoscrizioni SaaS in tutti gli stati possibili. Vengono restituite anche le sottoscrizioni SaaS non sottoscritte, perché queste informazioni non vengono eliminate sul lato Microsoft.

Questa API restituisce i risultati impaginati. La dimensione della pagina è 100.

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Ottieni`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  --------   |  ---------------  |
| `ApiVersion`  |  Utilizzare 2018-08-31.  |
| `continuationToken`  | Parametro facoltativo. Per recuperare la prima pagina di risultati, lasciare vuoto.  Usare il valore restituito in `@nextLink` Parameter per recuperare la pagina successiva. |

*Intestazioni della richiesta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `x-ms-correlationid` |  Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `authorization`      |  Un token di accesso univoco che identifica il server di pubblicazione che sta effettuando questa chiamata API.  Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Codici di risposta:*

Codice: 200 restituisce l'elenco di tutte le sottoscrizioni esistenti per tutte le offerte di questo server di pubblicazione, in base al token di autorizzazione del server di pubblicazione.

*Esempio di corpo della risposta:*

```json
{
  "subscriptions": [
    {
      "id": "<guid>", // purchased SaaS subscription ID
      "name": "Contoso Cloud Solution", // SaaS subscription name
      "publisherId": "contoso", // publisher ID
      "offerId": "offer1", // purchased offer ID
      "planId": "silver", // purchased plan ID
      "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
      "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": { // The period for which the subscription was purchased.
        "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
        "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
        "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
      },
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
      "sessionMode": "None", // not relevant
      "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
      "isTest": false, // not relevant
      "sandboxType": "None", // not relevant
      "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
    },
    // next SaaS subscription details, might be a different offer
    {
      "id": "<guid1>",
      "name": "Contoso Cloud Solution1",
      "publisherId": "contoso",
      "offerId": "offer2",
      "planId": "gold",
      "quantity": "",
      "beneficiary": {
        "emailId": " test@contoso.com",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "purchaser": {
        "emailId": "purchase@csp.com ",
        "objectId": "<guid>",
        "tenantId": "<guid>",
        "pid": "<ID of the user>"
      },
      "term": {
        "startDate": "2019-05-31",
        "endDate": "2020-04-30",
        "termUnit": "P1Y"
      },
      "allowedCustomerOperations": ["Read"],
      "sessionMode": "None",
      "isFreeTrial": false,
      "isTest": false,
      "sandboxType": "None",
      "saasSubscriptionStatus": "Suspended"
    }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

Se non viene trovata alcuna sottoscrizione SaaS acquistata per questo server di pubblicazione, viene restituito il corpo della risposta vuoto.

Codice: 403 non consentito. Il token di autorizzazione non è disponibile, non è valido o è scaduto.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) . 

Codice: 500 errore interno del server. Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-subscription"></a>Ottieni sottoscrizione

Recupera una sottoscrizione SaaS acquistata specificata per un'offerta SaaS pubblicata nel Marketplace dall'editore. Usare questa chiamata per ottenere tutte le informazioni disponibili per una sottoscrizione SaaS specifica in base al relativo ID anziché chiamare l'API per ottenere l'elenco di tutte le sottoscrizioni.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Ottieni `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   Utilizzare 2018-08-31. |
| `subscriptionId`     |  Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l'API di risoluzione. |

*Intestazioni della richiesta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  `x-ms-correlationid` |  Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  `authorization`     | Un token di accesso univoco che identifica il server di pubblicazione che sta effettuando questa chiamata API. Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codici di risposta:*

Codice: 200 restituisce i dettagli per una sottoscrizione SaaS basata sull'oggetto `subscriptionId` fornito.

*Esempio di corpo della risposta:*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
  "publisherId": "contoso", // publisher ID
  "offerId": "offer1", // purchased offer ID
  "planId": "silver", // purchased plan ID
  "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
  "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
    "emailId": "test@contoso.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
    "emailId": "test@test.com",
    "objectId": "<guid>",
    "tenantId": "<guid>",
    "pid": "<ID of the user>"
  },
  "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
  "sessionMode": "None", // not relevant
  "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
  "isTest": false, // not relevant
  "sandboxType": "None", // not relevant
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

Codice: 403 non consentito. Il token di autorizzazione non è valido, è scaduto e non è stato specificato. La richiesta sta tentando di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) . 

Codice: 404 non trovato.  Impossibile trovare la sottoscrizione SaaS con l'oggetto specificato `subscriptionId` .

Codice: 500 errore interno del server.  Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="list-available-plans"></a>Elenca i piani disponibili

Recupera tutti i piani per un'offerta SaaS identificata da `subscriptionId` di un acquisto specifico di questa offerta.  Usare questa chiamata per ottenere un elenco di tutti i piani privati e pubblici che possono essere aggiornati al beneficiario di una sottoscrizione SaaS per la sottoscrizione.  I piani restituiti saranno disponibili nella stessa area geografica del piano già acquistato.

Questa chiamata restituisce un elenco di piani disponibili per il cliente oltre a quello già acquistato.  L'elenco può essere presentato a un cliente finale sul sito del server di pubblicazione.  Un cliente finale può modificare il piano di sottoscrizione in uno dei piani nell'elenco restituito.  Il passaggio di un piano a uno non elencato nell'elenco avrà esito negativo.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Ottieni `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilizzare 2018-08-31.  |
|  `subscriptionId`    |  Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l'API di risoluzione. |

*Intestazioni della richiesta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  `x-ms-correlationid`  |  Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  `authorization`     |  Un token di accesso univoco che identifica il server di pubblicazione che sta effettuando questa chiamata API.  Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codici di risposta:*

Codice: 200 restituisce un elenco di tutti i piani disponibili per una sottoscrizione SaaS esistente, incluso quello già acquistato.

Esempio di corpo della risposta:

```json
{
  "plans": [
    {
      "planId": "Platinum001",
      "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
      "isPrivate": true //true or false
    },
    {
      "planId": "gold",
      "displayName": "Gold plan for Contoso",
      "isPrivate": false //true or false
    }
  ]
}
```

Se `subscriptionId` non viene trovato, viene restituito il corpo della risposta vuoto.

Codice: 403 non consentito. Il token di autorizzazione non è valido, è scaduto o non è stato specificato.  È possibile che la richiesta tenti di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) . 

Codice: 500 errore interno del server.  Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="change-the-plan-on-the-subscription"></a>Modificare il piano nella sottoscrizione

Aggiornare il piano esistente acquistato per una sottoscrizione SaaS a un nuovo piano (pubblico o privato).  Il server di pubblicazione deve chiamare questa API quando un piano viene modificato sul lato server di pubblicazione per una sottoscrizione SaaS acquistata nel Marketplace.

Questa API può essere chiamata solo per le sottoscrizioni attive.  Qualsiasi piano può essere modificato in qualsiasi altro piano esistente (pubblico o privato), ma non a se stesso.  Per i piani privati, il tenant del cliente deve essere definito come parte dei destinatari del piano nel centro per i partner.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilizzare 2018-08-31.  |
| `subscriptionId`     | Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l'API di risoluzione. |

*Intestazioni della richiesta:*
 
|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `x-ms-correlationid`  | Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `authorization`     |  Un token di accesso univoco che identifica il server di pubblicazione che sta effettuando questa chiamata API.  Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Esempio di payload della richiesta:*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*Codici di risposta:*

Codice: 202 la richiesta di modifica del piano è stata accettata e gestita in modo asincrono.  Si prevede che il partner esegua il polling dell' **URL del percorso operazione** per determinare l'esito positivo o negativo della richiesta del piano di modifica.  Il polling deve essere eseguito ogni secondi fino a quando non viene ricevuto lo stato finale di esito negativo, esito positivo o conflitto per l'operazione.  Lo stato finale dell'operazione dovrebbe essere restituito rapidamente, ma in alcuni casi possono essere necessari alcuni minuti.

Il partner riceverà anche una notifica webhook quando l'azione è pronta per essere completata correttamente sul lato Marketplace.  E solo il server di pubblicazione deve apportare la modifica del piano sul lato server di pubblicazione.

*Intestazioni risposta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  URL per ottenere lo stato dell'operazione.  Ad esempio: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Codice: 400 richiesta non valida: errori di convalida.

* Il nuovo piano non esiste o non è disponibile per questa sottoscrizione SaaS specifica.
* Tentativo di passare allo stesso piano.
* Lo stato della sottoscrizione SaaS non è sottoscritto.
* L'operazione di aggiornamento per una sottoscrizione SaaS non è inclusa in `allowedCustomerOperations` .

Codice: 403 non consentito. Il token di autorizzazione non è valido, è scaduto o non è stato specificato.  La richiesta sta tentando di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) .

Codice: 404 non trovato.  La sottoscrizione SaaS con `subscriptionId` non è stata trovata.

Codice: 500 errore interno del server.  Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!NOTE]
>Il piano o la quantità di postazioni può essere modificato in una sola volta, non in entrambi.

>[!Note]
>Questa API può essere chiamata solo dopo aver ricevuto l'approvazione esplicita dal cliente finale per la modifica.

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>Modificare la quantità di postazioni nella sottoscrizione SaaS

Aggiornamento (aumento o riduzione) della quantità di postazioni acquistate per una sottoscrizione SaaS.  Il server di pubblicazione deve chiamare questa API quando il numero di postazioni viene modificato dal lato server di pubblicazione per una sottoscrizione SaaS creata nel Marketplace.

La quantità di postazioni non può essere superiore a quella consentita nel piano corrente.  In questo caso, il piano deve essere modificato prima di modificare la quantità.

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilizzare 2018-08-31.  |
|  `subscriptionId`     | Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l'API di risoluzione.  |

*Intestazioni della richiesta:*
 
|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `x-ms-correlationid`  | Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `authorization`     | Un token di accesso univoco che identifica il server di pubblicazione che sta effettuando questa chiamata API.  Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Esempio di payload della richiesta:*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*Codici di risposta:*

Codice: 202 la richiesta di modifica della quantità è stata accettata e gestita in modo asincrono. Si prevede che il partner esegua il polling dell' **URL del percorso operazione** per determinare l'esito positivo o negativo della richiesta di modifica della quantità.  Il polling deve essere eseguito ogni secondi fino a quando non viene ricevuto lo stato finale di esito negativo, esito positivo o conflitto per l'operazione.  Lo stato finale dell'operazione dovrebbe essere restituito rapidamente, ma in alcuni casi possono essere necessari alcuni minuti.

Il partner riceverà anche una notifica webhook quando l'azione è pronta per essere completata correttamente sul lato Marketplace.  Il server di pubblicazione deve quindi apportare la modifica della quantità sul lato server di pubblicazione.

*Intestazioni risposta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Collegamento a una risorsa per ottenere lo stato dell'operazione.  Ad esempio: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`.  |

Codice: 400 richiesta non valida: errori di convalida.

* La nuova quantità è maggiore o minore del limite del piano corrente.
* La nuova quantità è mancante.
* Tentativo di passare alla stessa quantità.
* Lo stato della sottoscrizione SaaS non è sottoscritto.
* L'operazione di aggiornamento per una sottoscrizione SaaS non è inclusa in `allowedCustomerOperations` .

Codice: 403 non consentito.  Il token di autorizzazione non è valido, è scaduto o non è stato specificato.  La richiesta sta tentando di accedere a una sottoscrizione che non appartiene al server di pubblicazione corrente.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) . 

Codice: 404 non trovato.  La sottoscrizione SaaS con `subscriptionId` non è stata trovata.

Codice: 500 errore interno del server.  Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

>[!Note]
>È possibile modificare un solo piano o una quantità alla volta, non a entrambe.

>[!Note]
>Questa API può essere chiamata solo dopo aver ricevuto l'approvazione esplicita dal cliente finale per la modifica.

#### <a name="cancel-a-subscription"></a>Annullare una sottoscrizione

Annulla la sottoscrizione di una sottoscrizione SaaS specificata.  L'editore non deve usare questa API ed è consigliabile indirizzare i clienti a Marketplace, per annullare le sottoscrizioni SaaS.

Se l'editore decide di implementare l'annullamento della sottoscrizione SaaS acquistata in Marketplace sul lato dell'editore, deve chiamare questa API.  Al termine della chiamata, lo stato della sottoscrizione diventerà non *sottoscritto* sul lato Microsoft.

Se una sottoscrizione viene annullata entro i periodi di tolleranza seguenti, il cliente non verrà fatturato:

* 24 ore per una sottoscrizione mensile dopo l'attivazione.
* 14 giorni per una sottoscrizione annuale dopo l'attivazione.

Il cliente verrà fatturato se una sottoscrizione viene annullata dopo i periodi di tolleranza precedenti.  Al termine dell'annullamento, il cliente perderà immediatamente l'accesso alla sottoscrizione SaaS sul lato Microsoft.

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>eliminare`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilizzare 2018-08-31.  |
|  `subscriptionId`     | Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l'API di risoluzione.  |

*Intestazioni della richiesta:*
 
|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `x-ms-correlationid`  | Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `authorization`     |  Un token di accesso univoco che identifica il server di pubblicazione che effettua questa chiamata API.  Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Codici di risposta:*

Codice: 202 la richiesta di annullamento della sottoscrizione è stata accettata e gestita in modo asincrono.  Si prevede che il partner esegua il polling dell' **URL del percorso operazione** per determinare l'esito positivo o negativo della richiesta.  Il polling deve essere eseguito ogni secondi fino a quando non viene ricevuto lo stato finale di esito negativo, esito positivo o conflitto per l'operazione.  Lo stato finale dell'operazione dovrebbe essere restituito rapidamente, ma in alcuni casi possono essere necessari alcuni minuti.

Il partner riceverà anche una notifica webhook quando l'azione viene completata correttamente sul lato Marketplace.  E solo il server di pubblicazione deve annullare la sottoscrizione sul lato server di pubblicazione.

*Intestazioni risposta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  Collegamento a una risorsa per ottenere lo stato dell'operazione.  Ad esempio: `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31`. |

Codice: 400 richiesta non valida.  L'eliminazione non è `allowedCustomerOperations` presente nell'elenco per questa sottoscrizione Saas.

Codice: 403 non consentito.  Il token di autorizzazione non è valido, è scaduto o non è disponibile. La richiesta sta tentando di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) .

Codice: 404 non trovato.  La sottoscrizione SaaS con `subscriptionId` non è stata trovata.

Codice: 500 errore interno del server. Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="operations-apis"></a>API per operazioni

#### <a name="list-outstanding-operations"></a>Elencare le operazioni in attesa 

Ottiene l'elenco delle operazioni in sospeso per la sottoscrizione SaaS specificata.  Le operazioni restituite devono essere riconosciute dal server di pubblicazione chiamando l' [API patch dell'operazione](#update-the-status-of-an-operation).

Attualmente solo **le operazioni di reintegro** vengono restituite come risposta per questa chiamata API.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Ottieni `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  Utilizzare 2018-08-31.         |
|    `subscriptionId` | Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l'API di risoluzione.  |

*Intestazioni della richiesta:*
 
|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `x-ms-correlationid` |  Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `authorization`     |  Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codici di risposta:*

Codice: 200 restituisce l'operazione di ripristino in sospeso sulla sottoscrizione SaaS specificata.

*Esempio di payload di risposta:*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

Restituisce un JSON vuoto se non sono presenti operazioni di ripristino in sospeso.

Codice: 400 richiesta non valida: errori di convalida.

Codice: 403 non consentito. Il token di autorizzazione non è valido, è scaduto o non è stato specificato.  La richiesta sta tentando di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) . 

Codice: 404 non trovato.  La sottoscrizione SaaS con `subscriptionId` non è stata trovata.

Codice: 500 errore interno del server. Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="get-operation-status"></a>Ottenere lo stato dell'operazione

Consente al server di pubblicazione di tenere traccia dello stato dell'operazione asincrona specificata:  **unsubscribe**, **ChangePlan**o **ChangeQuantity**.

`operationId`Per questa chiamata API può essere recuperato dal valore restituito da **Operation-location**, get pending Operation API call o dal `<id>` valore del parametro ricevuto in una chiamata webhook.

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Ottieni `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  Utilizzare 2018-08-31.  |
|  `subscriptionId`    |  Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l'API di risoluzione. |
|  `operationId`       |  Identificatore univoco dell'operazione da recuperare. |

*Intestazioni della richiesta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  `x-ms-correlationid` |  Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  `authorization`     |  Un token di accesso univoco che identifica il server di pubblicazione che effettua questa chiamata API.  Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post).  |

*Codici di risposta:*

Codice: 200 ottiene i dettagli per l'operazione SaaS specificata. 

*Esempio di payload di risposta:*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

Codice: 403 non consentito. Il token di autorizzazione non è valido, è scaduto o non è stato specificato.  La richiesta sta tentando di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) . 

Codice: 404 non trovato.  

* La sottoscrizione con `subscriptionId` non è stata trovata.
* Operazione con `operationId` non trovata.

Codice: 500 errore interno del server.  Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

#### <a name="update-the-status-of-an-operation"></a>Aggiornare lo stato di un'operazione

Aggiornare lo stato di un'operazione in sospeso per indicare l'esito positivo o negativo dell'operazione sul lato del server di pubblicazione.

`operationId`Per questa chiamata API può essere recuperato dal valore restituito da **Operation-location**, get pending Operation API call o il `<id>` valore del parametro ricevuto in una chiamata webhook.

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametri di query:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  Utilizzare 2018-08-31.  |
|   `subscriptionId`   |  Identificatore univoco della sottoscrizione SaaS acquistata.  Questo ID viene ottenuto dopo la risoluzione del token di autorizzazione del Marketplace tramite l'API di risoluzione.  |
|   `operationId`      |  Identificatore univoco dell'operazione in corso di completamento. |

*Intestazioni della richiesta:*

|  Parametro         | Valore             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|   `x-ms-correlationid` |  Valore stringa univoco per l'operazione sul client.  Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server.  Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  `authorization`     |  Un token di accesso univoco che identifica il server di pubblicazione che sta effettuando questa chiamata API.  Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato in [ottenere un token basato sull'app Azure ad](./pc-saas-registration.md#get-the-token-with-an-http-post). |

*Esempio di payload della richiesta:*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*Codici di risposta:*

Codice: 200 una chiamata per informare il completamento di un'operazione sul lato del partner.  Questa risposta, ad esempio, potrebbe segnalare il completamento della modifica delle postazioni o dei piani sul lato server di pubblicazione.

Codice: 403 non consentito.  Il token di autorizzazione non è disponibile, non è valido o è scaduto. È possibile che la richiesta stia tentando di accedere a una sottoscrizione che non appartiene al server di pubblicazione corrente.
Non consentito.  Il token di autorizzazione non è valido, è scaduto o non è stato specificato.  La richiesta sta tentando di accedere a una sottoscrizione SaaS per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Questo errore è spesso un sintomo di non eseguire correttamente la [registrazione Saas](pc-saas-registration.md) .

Codice: 404 non trovato.

* La sottoscrizione con `subscriptionId` non è stata trovata.
* Operazione con `operationId` non trovata.

Codice: 409 conflitto.  Ad esempio, è già stato completato un aggiornamento più recente.

Codice: 500 errore interno del server.  Ripetere la chiamata API.  Se l'errore viene mantenuto, contattare il [supporto tecnico Microsoft](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementazione di un webhook nel servizio SaaS

Quando si crea un'offerta SaaS transazionale nel centro per i partner, il partner fornisce l'URL del **webhook di connessione** da usare come endpoint HTTP.  Questo webhook viene chiamato da Microsoft utilizzando la chiamata HTTP POST per notificare al lato editore gli eventi seguenti che si verificano sul lato Microsoft:

* Quando la sottoscrizione SaaS è in stato sottoscritto:
    * ChangePlan 
    * ChangeQuantity
    * Sospendi
    * Annullare la sottoscrizione
* Quando la sottoscrizione SaaS è in stato sospeso:
    * Ripristina
    * Annullare la sottoscrizione

Il server di pubblicazione deve implementare un webhook nel servizio SaaS per assicurare la coerenza dello stato della sottoscrizione SaaS con il lato Microsoft.  Il servizio SaaS è necessario per chiamare l'API get Operation per convalidare e autorizzare la chiamata e i dati di payload del webhook prima di eseguire un'azione in base alla notifica del webhook.  Il server di pubblicazione deve restituire HTTP 200 a Microsoft non appena viene elaborata la chiamata al webhook.  Questo valore conferma che la chiamata al webhook è stata ricevuta correttamente dal server di pubblicazione.

>[!Note]
>Il servizio URL webhook deve essere attivo e in esecuzione 24x7 e pronto a ricevere le nuove chiamate da Microsoft Time in qualsiasi momento.  Microsoft dispone di un criterio di ripetizione dei tentativi per la chiamata al webhook (500 tentativi in 8 ore), ma se il server di pubblicazione non accetta la chiamata e restituisce una risposta, l'operazione con cui viene inviata una notifica al webhook avrà esito negativo sul lato Microsoft.

*Esempi di payload del webhook:*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success
}
```

```json
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold",
  "quantity": " 20",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress"
}
```

## <a name="development-and-testing"></a>Sviluppo e test

Per avviare il processo di sviluppo, è consigliabile creare risposte API fittizie sul lato server di pubblicazione.  Queste risposte possono essere basate sulle risposte di esempio fornite in questo documento.

Quando il server di pubblicazione è pronto per il test end-to-end: 

* Pubblicare un'offerta SaaS in un gruppo di destinatari con anteprima limitata e mantenerla in fase di anteprima.
* Questa offerta deve avere un piano con 0 prezzo, quindi non attivare costi di fatturazione effettivi durante i test.  Un'altra opzione consiste nell'impostare un prezzo diverso da zero e annullare tutti gli acquisti di test entro 24 ore. 
* Assicurarsi che tutti i flussi vengano richiamati end-to-end, proprio come un cliente acquista l'offerta. 
* Se il partner vuole testare il flusso di acquisto e di fatturazione completo, effettuare questa operazione con l'offerta con prezzo superiore a $0.  L'acquisto verrà fatturato e verrà generata una fattura.

Un flusso di acquisto può essere attivato dal portale di Azure o da siti Microsoft AppSource, a seconda della posizione in cui viene pubblicata l'offerta.

Le azioni *modifica piano*, *modifica quantità*e *Annulla sottoscrizione* sono testate dal lato server di pubblicazione.  Dal lato Microsoft, l' *annullamento della sottoscrizione* può essere attivato sia dal portale di Azure che dal centro di amministrazione (il portale in cui vengono gestiti Microsoft AppSource acquisti).  La *modifica della quantità e del piano* può essere attivata solo dall'interfaccia di amministrazione.

## <a name="get-support"></a>Ottenere supporto

Vedere [il supporto per il programma Commercial Marketplace nel centro per i partner per le](support.md) opzioni di supporto dell'editore.


## <a name="next-steps"></a>Passaggi successivi

Per altre opzioni per le offerte SaaS nel Marketplace commerciale, vedere le [API del servizio di misurazione del Marketplace commerciale](marketplace-metering-service-apis.md) .

Esaminare e usare l' [SDK Saas](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) basato sulle API descritte in questo documento.
