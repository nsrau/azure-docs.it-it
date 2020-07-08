---
title: Recuperare le offerte API-Azure Marketplace
description: API per recuperare un elenco riepilogato di offerte in uno spazio dei nomi del server di pubblicazione.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ee8d0c773a5ec4d362eae66e289838b9646247e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515958"
---
<a name="retrieve-offers"></a>Recuperare le offerte
===============

> [!NOTE]
> Le API del portale Cloud Partner sono integrate con il Centro per i partner e continueranno a funzionare dopo la migrazione delle offerte in esso. L'integrazione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la migrazione al centro per i partner.

L'API recupera un elenco riepilogativo delle offerte, in uno spazio dei nomi del server di pubblicazione.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametri URI
--------------

| **Nome**         |  **Descrizione**                         |  **Tipo di dati** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identificatore dell'editore, per esempio `contoso` |   string    |
|  api-version     | Ultima versione dell'API                    |    Data        |
|  |  |


<a name="header"></a>Header
------

|  **Nome**        |         **Valore**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Autorizzazione   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Esempio di corpo
------------

### <a name="response"></a>Risposta

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Proprietà del corpo della risposta

|  **Nome**       |       **Descrizione**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifica il tipo di offerta                                                                                           |
|  publisherId    | Identificatore univoco dell'editore                                                                      |
|  status         | Stato dell'offerta. Per l'elenco di valori possibili, vedere [Stato dell'offerta](#offer-status) di seguito:                         |
|  id             | GUID che identifica in modo univoco l'offerta, nello spazio dei nomi del server di pubblicazione.                                                    |
|  version        | Versione corrente dell'offerta. La proprietà della versione non può essere modificata dal client. Viene incrementata dopo ogni pubblicazione. |
|  Definizione     | Contiene la visualizzazione del riepilogo della definizione stessa del carico di lavoro. Per ottenere una definizione dettagliata, usare l'API per il [recupero di un'offerta specifica](./cloud-partner-portal-api-retrieve-specific-offer.md). |
|  changedTime    | Ora UTC dell'ultima modifica dell'offerta                                                                              |
|  |  |


### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice**  |  **Descrizione**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - La richiesta è stata elaborata correttamente e tutte le offerte nel server di pubblicazione sono state restituite al client.  |
|  400      | `Bad/Malformed request` - Il corpo della risposta di errore può contenere altre informazioni.                                    |
|  403      | `Forbidden`: il client non ha accesso allo spazio dei nomi specificato.                                          |
|  404      | `Not found` - L'entità specificata è inesistente.                                                                 |
|  |  |


### <a name="offer-status"></a>Stato dell'offerta

|  **Nome**                    | **Descrizione**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | L'offerta non è mai stata pubblicata.                  |
|  NotStarted                  | L'offerta è nuova, ma non è stata attivata.                 |
|  WaitingForPublisherReview   | L'offerta è in attesa di approvazione da parte dell'editore.         |
|  In esecuzione                     | Invio dell'offerta in corso.             |
|  Operazione riuscita                   | Invio dell'offerta completato.       |
|  Cancellati                    | Invio dell'offerta annullato.                   |
|  Operazione non riuscita                      | Invio dell'offerta non riuscito.                         |
|  |  |
