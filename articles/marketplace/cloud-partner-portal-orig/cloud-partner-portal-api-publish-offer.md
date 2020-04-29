---
title: Pubblicare un'offerta | Azure Marketplace
description: API per pubblicare l'offerta specificata.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255941"
---
# <a name="publish-an-offer"></a>Pubblicare un'offerta

> [!NOTE]
> Le API portale Cloud Partner sono integrate con il centro per i partner e continueranno a funzionare dopo la migrazione delle offerte al centro per i partner. L'integrazione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) per assicurarsi che il codice continui a funzionare dopo la migrazione al centro per i partner.

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
| Percorso    | Percorso relativo per recuperare lo stato di questa operazione     |
|  |  |


### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice** |  **Descrizione**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - La richiesta è stata accettata correttamente. La risposta contiene un percorso che può essere usato per tenere traccia dell'operazione avviata. |
| 400   | `Bad/Malformed request` - Il corpo della risposta di errore può specificare altre informazioni.                                                               |
| 422   | `Un-processable entity` -Indica che non è stato possibile convalidare l'entità da pubblicare.                                                        |
| 404   | `Not found` - L'entità specificata dal client non esiste.                                                                              |
|  |  |
