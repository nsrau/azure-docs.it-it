---
title: Recupero dello stato dell'offerta | Microsoft Docs
description: L'API recupera lo stato corrente dell'offerta.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9233a5919ad86adcbb7947cd095945654ed015a7
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808277"
---
<a name="retrieve-offer-status"></a>Recupero dello stato dell'offerta 
=====================

Recupera lo stato corrente dell'offerta.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parametri URI
--------------

|  **Nome**       |   **Descrizione**                            |  **Tipo di dati** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identificatore dell'editore, per esempio `Contoso`  |     string     |
|  offerId        | GUID che identifica in modo univoco l'offerta      |     string     |
|  api-version    | Ultima versione dell'API                        |     Data       |
|  |  |


<a name="header"></a>Intestazione
------

|  NOME           |  Valore               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Authorization  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Esempio di corpo
------------

### <a name="response"></a>Risposta

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Proprietà del corpo della risposta

|  **Nome**             |    **Descrizione**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Lo stato dell'offerta. Per l'elenco di valori possibili, vedere [Stato dell'offerta](#offer-status) di seguito: |
|  del cloud al dispositivo             | Matrice di messaggi associati all'offerta                                                    |
|  steps                | Matrice dei passaggi di pubblicazione di un'offerta                      |
|  estimatedTimeFrame   | Stima del tempo necessario per completare questo passaggio, in formato descrittivo                       |
|  id                   | Identificatore del passaggio                                                                         |
|  stepName             | Nome del passaggio                                                                               |
|  description          | Descrizione del passaggio                                                                        |
|  status               | Stato del passaggio. Per l'elenco di valori possibili, vedere [Stato del passaggio](#step-status) di seguito:    |
|  del cloud al dispositivo             | Matrice di messaggi correlati al passaggio                                                          |
|  processPercentage    | Percentuale di completamento del passaggio                                                              |
|  previewLinks         | *Non attualmente implementato*                                                                    |
|  liveLinks            | *Non attualmente implementato*                                                                    |
|  notificationEmails   | Elenco delimitato da virgole degli indirizzi e-mail a cui inviare una notifica dello stato di avanzamento dell'operazione        |
|  |  |


### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice** |   **Descrizione**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - La richiesta è stata elaborata correttamente e lo stato attuale dell'offerta è stato restituito. |
|  400     | `Bad/Malformed request` - Il corpo della risposta di errore può contenere altre informazioni.                 |
|  404     | `Not found` - L'entità specificata è inesistente.                                                |
|  |  |


### <a name="offer-status"></a>Stato dell'offerta

|  **Nome**                    |    **Descrizione**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | L'offerta non è mai stata pubblicata.                          |
|  NotStarted                  | L'offerta è nuova e non è stata attivata.                            |
|  WaitingForPublisherReview   | L'offerta è in attesa di approvazione da parte dell'editore.                 |
|  In esecuzione                     | Invio dell'offerta in corso.                     |
|  Succeeded                   | Invio dell'offerta completato.               |
|  Canceled                    | Invio dell'offerta annullato.                           |
|  Operazione non riuscita                      | Invio dell'offerta non riuscito.                                 |
|  |  |


### <a name="step-status"></a>Stato del passaggio

|  **Nome**                    |    **Descrizione**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Passaggio non avviato.                        |
|  InProgress                  | Il passaggio è in esecuzione.                             |
|  WaitingForPublisherReview   | Il passaggio è in attesa di approvazione da parte dell'editore.      |
|  WaitingForApproval          | Il passaggio è in attesa di approvazione da parte del processo.        |
|  Bloccato                     | Il passaggio è bloccato.                             |
|  Rifiutato                    | Il passaggio è stato rifiutato.                            |
|  Complete                    | Il passaggio è stato completato.                            |
|  Canceled                    | Il passaggio è stato annullato.                           |
|  |  |
