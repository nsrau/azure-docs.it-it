---
title: Pubblicare un'offerta-Azure Marketplace
description: API per pubblicare l'offerta specificata.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 60e75aff79913896bdf1dcdc8754b6ecf5620b06
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87272046"
---
# <a name="publish-an-offer"></a>Pubblicare un'offerta

> [!NOTE]
> Le API portale Cloud Partner sono integrate con e continueranno a funzionare nel centro per i partner. La transizione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la transizione al centro per i partner. Le API CPP devono essere usate solo per i prodotti esistenti già integrati prima della transizione al centro per i partner; i nuovi prodotti devono usare le API di invio del centro per i partner.

Avviare il processo di pubblicazione per l'offerta specificata. L'operazione può richiedere molto tempo.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametri URI
--------------

|  **Nome**      |    **Descrizione**                               |  **Tipo di dati** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificatore dell'editore, per esempio `contoso`      |   Stringa       |
|  offerId       | Identificatore dell'offerta                                 |   Stringa       |
|  api-version   | Versione più recente dell'API                        |   Data         |
|  |  |

## <a name="header"></a>Intestazione
------

|  **Nome**        |    **Valore**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorizzazione   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Esempio di corpo
------------

### <a name="request"></a>Richiesta

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Proprietà del corpo della richiesta

|  **Nome**               |   **Descrizione**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notifiche tramite posta elettronica    | Elenco delimitato da virgole degli indirizzi e-mail a cui inviare una notifica relativa allo stato di avanzamento dell'operazione di pubblicazione. |
|  |  |

### <a name="response"></a>Risposta

#### <a name="migrated-offers"></a>Offerte migrate

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Offerte non migrate

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Intestazione di risposta

|  **Nome**             |    **Valore**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Location    | Percorso relativo per recuperare lo stato di questa operazione     |
|  |  |

### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice** |  **Descrizione**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - La richiesta è stata accettata correttamente. La risposta contiene un percorso che può essere usato per tenere traccia dell'operazione avviata. |
| 400   | `Bad/Malformed request` - Il corpo della risposta di errore può specificare altre informazioni.                                                               |
| 422   | `Un-processable entity` -Indica che non è stato possibile convalidare l'entità da pubblicare.                                                        |
| 404   | `Not found` - L'entità specificata dal client non esiste.                                                                              |
|  |  |
