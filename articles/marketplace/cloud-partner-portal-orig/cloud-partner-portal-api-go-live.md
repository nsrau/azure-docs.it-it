---
title: Proprietà Go Live (Vai in diretta) Azure Marketplace
description: L'API Go Live avvia il processo di presentazione in tempo reale dell'offerta.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288581"
---
<a name="go-live"></a>Go Live
=======

Questa API avvia il processo che consente di inviare un'app alla produzione. Questa operazione è in genere a esecuzione prolungata. Questa chiamata usa l'elenco di indirizzi di posta elettronica per le notifiche dell'operazione API [Pubblica](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parametri URI
--------------

|  **Nome**      |   **Descrizione**                                                           | **Tipo di dati** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificatore dell'autore dell'offerta da recuperare, ad esempio `contoso`       |  string       |
| offerId        | Identificatore dell'offerta da recuperare                                   |  string       |
| api-version    | Versione più recente dell'API                                                   |  Data         |
|  |  |  |


<a name="header"></a>Intestazione
------

|  **Nome**       |     **Valore**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Autorizzazione   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Esempio di corpo
------------

### <a name="response"></a>Risposta

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Intestazione di risposta

|  **Nome**             |      **Valore**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  URL per l'esecuzione della query che consente di determinare lo stato corrente dell'operazione            |
|  |  |


### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice** |  **Descrizione**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - La richiesta è stata accettata correttamente. La risposta contiene un percorso per tenere traccia dello stato dell'operazione. |
|  400     | `Bad/Malformed request` - Nel corpo della risposta sono presenti informazioni aggiuntive sull'errore. |
|  404     |  `Not found`: l'entità specificata non esiste.                                       |
|  |  |
