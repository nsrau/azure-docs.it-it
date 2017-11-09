---
title: Descrivere API e connettori personalizzati con Postman - App per la logica di Azure | Microsoft Docs
description: Creare raccolte di Postman per descrivere, raggruppare e organizzare le API e i connettori personalizzati
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Descrivere API e connettori personalizzati con Postman

Per semplificare e velocizzare lo sviluppo di [API personalizzate](../logic-apps/logic-apps-create-api-app.md) e [connettori personalizzati](../logic-apps/custom-connector-overview.md), è possibile creare raccolte di [Postman](https://www.getpostman.com/), anziché documenti OpenAPI, per descrivere le API e i connettori. Le raccolte di Postman sono utili per organizzare e raggruppare le richieste API correlate. Ad esempio, è possibile usare Postman durante la creazione di connettori per App per la logica di Azure, Microsoft Flow o Microsoft PowerApps. 

Questa esercitazione illustra come creare una [raccolta di Postman](https://www.getpostman.com/docs/postman/collections/creating_collections) usando l'[API Rilevamento della lingua](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) in [Analisi del testo di Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/text-analytics/) come esempio. Questa API identifica la lingua, il sentiment e le frasi chiave nel testo passato all'API.

## <a name="prerequisites"></a>Prerequisiti

* Se non si conosce Postman, [installare l'app Postman](https://www.getpostman.com/apps).

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iniziare con un [account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, iscriversi per ottenere una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Se si dispone di una sottoscrizione di Azure, effettuare l'iscrizione per le API di Analisi del testo eseguendo l'[attività 1 qui](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md). 

## <a name="create-a-postman-collection"></a>Creare una raccolta di Postman

Prima di poter creare una raccolta, creare una richiesta HTTP per l'endpoint dell'API. 

### <a name="create-an-http-request-for-your-api"></a>Creare una richiesta HTTP per l'API

1. Aprire l'app Postman in modo da poter creare una [richiesta HTTP](https://www.getpostman.com/docs/postman/sending_api_requests/requests) per l'endpoint dell'API. Per altre informazioni, vedere la [documentazione relativa alle richieste](https://www.getpostman.com/docs/postman/sending_api_requests/requests) di Postman.

   1. Nella scheda **Builder** (Generatore) selezionare il metodo HTTP, immettere l'URL della richiesta per l'endpoint dell'API e selezionare l'eventuale protocollo di autorizzazione. 
   Al termine, scegliere **Params** (Parametri).

      Per questa esercitazione, è possibile usare queste impostazioni nell'esempio:

      ![Creare una richiesta: "HTTP method" (Metodo HTTP), "Request URL" (URL richiesta), "Authorization" (Autorizzazione)](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | Parametro | Valore consigliato | 
      | --------- | --------------- | 
      | **HTTP method (Metodo HTTP)** | POST | 
      | **Request URL (URL richiesta)** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **Autorizzazione** | "No Auth" (Senza autenticazione) | | 
      ||| 

   2. È ora possibile immettere coppie chiave-valore da usare come parametri di query o percorso nell'URL della richiesta. Postman combina questi elementi in una stringa di query.
   Al termine dell'operazione, scegliere **Headers** (Intestazioni).

      ![Continuazione della richiesta: "Params" (Parametri)](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | Parametro | Valore consigliato | 
      | --------- | --------------- | 
      | **Params (Parametri)** | **Key (Chiave)**: "numberOfLanguagesToDetect" </br>**Value (Valore)**: "1" | 
      ||| 

   3. Immettere le coppie chiave-valore per l'intestazione della richiesta. 
   Immettere la stringa desiderata per il nome dell'intestazione. Per le intestazioni HTTP comuni è possibile selezionare nell'elenco a discesa. Al termine dell'operazione, scegliere **Body** (Corpo). 
   
      ![Continuazione della richiesta: "Headers" (Intestazioni)](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | Parametro | Valore | 
      | --------- | ----- | 
      | **Intestazioni** | **Key (Chiave)**: "Ocp-Apim-Subscription-Key" </br>**Value (Valore)**: *chiave-sottoscrizione-API* che è possibile trovare nell'account di Servizi cognitivi <p>**Key (Chiave)**: "Content-Type" </br> **Value (Valore)**: "application/json" | 
      ||| 

   4. Immettere il contenuto che si vuole inviare nel corpo della richiesta. 
   Per verificare che la richiesta funzioni tramite il recupero di una risposta, scegliere **Send** (Invia). 
   
      ![Continuazione della richiesta: "Body" (Corpo)](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | Parametro | Valore consigliato | 
      | --------- | --------------- |    
      | **Corpo** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      Il campo della risposta contiene l'intera risposta dall'API, incluso il risultato o un errore, se presente.

      ![Ottenere una risposta per la richiesta](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. Dopo aver verificato il funzionamento della richiesta, salvare la richiesta in una raccolta di Postman. 

   1. Scegliere **Salva**. 
      
      ![Scegliere "Save" (Salva)](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. In **Save Request** (Salva richiesta) specificare un nome in **Request Name** (Nome richiesta) e facoltativamente una descrizione in **Request description** (Descrizione richiesta). 

      > [!NOTE]
      > Il connettore personalizzato usa questi valori in seguito. Assicurarsi quindi si specificare gli stessi valori che verranno usati in un secondo momento per il riepilogo delle operazioni e la descrizione dell'API personalizzata.

   3. Scegliere **+ Create Collection** (Crea raccolta) e specificare un nome per la raccolta. 
   Scegliere il segno di spunta, che crea una cartella della raccolta, quindi scegliere **Save to *nome-raccolta-Postman*** (Salva in).

      ![Salvare la richiesta](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>Salvare la risposta alla richiesta

Dopo avere salvato la richiesta, è possibile salvare la risposta. In questo modo, la risposta viene visualizzata come esempio quando si carica la richiesta in un secondo momento.

1. Sopra la finestra della risposta scegliere **Save Response** (Salva risposta). 

   ![Salvare la risposta](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   I connettori personalizzati supportano una sola risposta per ogni richiesta. 
   Se si salvano più risposte per ogni richiesta, viene usata solo la prima.

2. Specificare un nome per l'esempio e scegliere **Save example** (Salva esempio).

3. Continuare a compilare la raccolta di Postman con altre richieste e risposte.

### <a name="export-your-postman-collection"></a>Esporta la raccolta di Postman

1. Al termine, esportare la raccolta in un file JSON.

   ![Esportare la raccolta](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. Scegliere il formato di esportazione **Collection v1** e passare al percorso in cui si vuole salvare il file JSON.

   > [!NOTE]
   > Attualmente, per i connettori personalizzati funziona solo la versione 1.

   ![Scegliere il formato di esportazione: "Collection v1"](./media/custom-connector-api-postman-collection/09-export-format.png)
   
È ora possibile usare questa raccolta di Postman per la creazione di API e connettori personalizzati. Dopo avere esportato la raccolta, è possibile importare il file JSON in App per la logica, Flow o PowerApps.

> [!IMPORTANT]
> Se si crea un connettore personalizzato da una raccolta di Postman, assicurarsi di rimuovere l'intestazione `Content-type` da azioni e trigger. Il servizio di destinazione, ad esempio Flow, aggiunge automaticamente questa intestazione. Rimuovere anche le intestazioni di autenticazione nella sezione `securityDefintions` da azioni e trigger.

## <a name="next-steps"></a>Passaggi successivi

* [App per la logica: Registrare i connettori personalizzati](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: Registrare il connettore](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: Registrare il connettore](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [Domande frequenti sui connettori personalizzati](../logic-apps/custom-connector-faq.md)
