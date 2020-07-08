---
title: Go Live API-Azure Marketplace
description: L'API Go Live avvia il processo di presentazione in tempo reale dell'offerta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 5d550f576108447a88660321899f2f55ffeb3c77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516010"
---
# <a name="go-live"></a>Go Live

> [!NOTE]
> Le API del portale Cloud Partner sono integrate con il Centro per i partner e continueranno a funzionare dopo la migrazione delle offerte in esso. L'integrazione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la migrazione al centro per i partner.

Questa API avvia il processo che consente di inviare un'app alla produzione. Questa operazione è in genere a esecuzione prolungata. Questa chiamata usa l'elenco di indirizzi di posta elettronica per le notifiche dell'operazione API [Pubblica](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parametri URI
--------------

|  **Nome**      |   **Descrizione**                                                           | **Tipo di dati** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificatore dell'autore dell'offerta da recuperare, ad esempio `contoso`       |  string       |
| offerId        | Identificatore dell'offerta da recuperare                                   |  string       |
| api-version    | Versione più recente dell'API                                                   |  Data         |
|  |  |  |

## <a name="header"></a>Header
------

|  **Nome**       |     **Valore**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorizzazione   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Esempio di corpo

### <a name="response"></a>Risposta

#### <a name="migrated-offers"></a>Offerte migrate

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Offerte non migrate

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Intestazione di risposta

|  **Nome**             |      **Valore**                                                            |
|  --------             |      ----------                                                           |
| Location    |  Percorso relativo per recuperare lo stato di questa operazione            |
|  |  |

### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice** |  **Descrizione**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - La richiesta è stata accettata correttamente. La risposta contiene un percorso per tenere traccia dello stato dell'operazione. |
|  400     | `Bad/Malformed request` - Nel corpo della risposta sono presenti informazioni aggiuntive sull'errore. |
|  404     |  `Not found`: l'entità specificata non esiste.                                       |
|  |  |
