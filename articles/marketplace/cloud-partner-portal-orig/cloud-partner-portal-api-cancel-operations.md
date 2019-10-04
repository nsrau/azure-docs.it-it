---
title: Annullare l'operazione API | Azure Marketplace
description: Annullare le operazioni.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 70ffd13be4ba934b423e3bb5344eea0a9c36886c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935555"
---
# <a name="cancel-operation"></a>Annulla operazione 

Questa API consente di annullare un'operazione in corso sull'offerta. Usare l'[API di recupero informazioni](./cloud-partner-portal-api-retrieve-operations.md) per ottenere un `operationId` da passare a questa API. L'annullamento è in genere un'operazione sincrona, ma in alcuni scenari complessi può essere necessaria una nuova operazione per cancellarne una esistente. In questo caso, il corpo della risposta HTTP contiene il percorso dell'operazione da usare per eseguire una query sullo stato.

È possibile specificare un elenco delimitato da virgole di indirizzi di posta elettronica con la richiesta e l'API invierà a questi indirizzi una notifica sullo stato di avanzamento dell'operazione.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametri URI
--------------

|  **Nome**    |      **Descrizione**                                  |    **Tipo di dati**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identificatore del server di pubblicazione, ad esempio `contoso`         |   String          |
| offerId      |  Identificatore dell'offerta                                     |   String          |
| api-version  |  Versione corrente dell'API                               |    Date           |
|  |  |  |


<a name="header"></a>Intestazione
------

|  **Nome**              |  **Valore**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Authorization         |  Bearer TOKEN |
|  |  |


<a name="body-example"></a>Esempio di corpo
------------

### <a name="request"></a>Richiesta

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Proprietà del corpo della richiesta

|  **Nome**                |  **Descrizione**                                               |
|  --------                |  ---------------                                               |
|  notifiche tramite posta elettronica     | Elenco delimitato da virgole degli ID degli indirizzi e-mail a cui inviare una notifica dello stato di avanzamento dell'operazione di pubblicazione. |
|  |  |


### <a name="response"></a>Risposta

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Intestazione di risposta

|  **Nome**             |    **Valore**                       |
|  ---------            |    ----------                      |
| Operation-Location    | URL su cui è possibile eseguire una query che consente di determinare lo stato corrente dell'operazione. |
|  |  |


### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice**  |  **Descrizione**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK. La richiesta è stata elaborata correttamente e l'operazione viene annullata in modo sincrono. |
|  202      | Accettato. La richiesta è stata elaborata correttamente e l'operazione è in corso di annullamento. Il percorso dell'operazione di annullamento viene restituito nell'intestazione della risposta. |
|  400      | Richiesta non valida o errata. Il corpo della risposta di errore può specificare altre informazioni.  |
|  403      | Accesso negato. Il client non ha accesso allo spazio dei nomi specificato nella richiesta. |
|  404      | Non trovato. L'entità specificata non esiste. |
|  |  |
