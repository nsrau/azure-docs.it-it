---
title: Annulla operazione API-Microsoft Commercial Marketplace
description: API per annullare un'operazione attualmente in corso sull'offerta
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 095d9cf68374ee81cafdb6e7a32df29a57d122cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287923"
---
# <a name="cancel-operation"></a>Annulla operazione

> [!NOTE]
> Le API portale Cloud Partner sono integrate con e continueranno a funzionare nel centro per i partner. La transizione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la transizione al centro per i partner. Le API CPP devono essere usate solo per i prodotti esistenti già integrati prima della transizione al centro per i partner; i nuovi prodotti devono usare le API di invio del centro per i partner.

Questa API consente di annullare un'operazione in corso sull'offerta. Usare l'[API di recupero informazioni](./cloud-partner-portal-api-retrieve-operations.md) per ottenere un `operationId` da passare a questa API. L'annullamento è in genere un'operazione sincrona, ma in alcuni scenari complessi può essere necessaria una nuova operazione per cancellarne una esistente. In questo caso, il corpo della risposta HTTP contiene il percorso dell'operazione da usare per eseguire una query sullo stato.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametri URI

--------------

|  **Nome**    |      **Descrizione**                                  |    **Tipo di dati**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identificatore del server di pubblicazione, ad esempio `contoso`         |   string          |
| offerId      |  Identificatore dell'offerta                                     |   string          |
| api-version  |  Versione corrente dell'API                               |    Data           |
|  |  |  |

## <a name="header"></a>Intestazione
------

|  **Nome**              |  **Valore**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorizzazione         |  Bearer TOKEN |
|  |  |

## <a name="body-example"></a>Esempio di corpo
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

#### <a name="migrated-offers"></a>Offerte migrate

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Offerte non migrate

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Intestazione di risposta

|  **Nome**             |    **Valore**                       |
|  ---------            |    ----------                      |
| Location    | Percorso relativo per recuperare lo stato di questa operazione. |
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
