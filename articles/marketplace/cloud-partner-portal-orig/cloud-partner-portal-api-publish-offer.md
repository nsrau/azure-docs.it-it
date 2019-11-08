---
title: Pubblicare un'offerta | Azure Marketplace
description: API per pubblicare l'offerta specificata.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b7ad8086c417cf1f14d9116fa4abcb0a88030922
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819647"
---
<a name="publish-an-offer"></a>Pubblicare un'offerta
================

Avviare il processo di pubblicazione per l'offerta specificata. L'operazione può richiedere molto tempo.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametri URI
--------------

|  **Nome**      |    **Descrizione**                               |  **Tipo di dati** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificatore dell'editore, per esempio `contoso`      |   String       |
|  offerId       | Identificatore dell'offerta                                 |   String       |
|  api-version   | Versione più recente dell'API                        |   Data         |
|  |  |


<a name="header"></a>Intestazione
------

|  **Nome**        |    **Valore**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorizzazione   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Esempio di corpo
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


### <a name="response"></a>Response

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Intestazione di risposta

|  **Nome**             |    **Valore**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | URL su cui è possibile eseguire una query che consente di determinare lo stato corrente dell'operazione.    |
|  |  |


### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice** |  **Descrizione**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - La richiesta è stata accettata correttamente. La risposta contiene un percorso che può essere usato per tenere traccia dell'operazione avviata. |
| 400   | `Bad/Malformed request` - Il corpo della risposta di errore può specificare altre informazioni.                                                               |
| 422   | `Un-processable entity` -Indica che non è stato possibile convalidare l'entità da pubblicare.                                                        |
| 404   | `Not found` - L'entità specificata dal client non esiste.                                                                              |
|  |  |
