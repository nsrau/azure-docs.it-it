---
title: Connettersi a un endpoint REST da App per la logica di Azure
description: Monitorare un endpoint REST in attività, processi e flussi di lavoro automatizzati con App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: a5c00dc64dd39ba2fdbb734f4e9749fbe42e246e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831926"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chiamare un endpoint REST con App per la logica di Azure

Con [App per la logica di Azure](../logic-apps/logic-apps-overview.md) e il connettore HTTP + Swagger integrato, è possibile automatizzare i flussi di lavoro che chiamano regolarmente endpoint REST tramite un [file Swagger](https://swagger.io) compilando app per la logica. Il trigger e l'azione HTTP + Swagger funzionano come i [trigger e le azioni HTTP](connectors-native-http.md), ma offrono un'esperienza migliore nella finestra di progettazione di App per la logica, esponendo la struttura dell'API e gli output descritti dal file Swagger. Per implementare un trigger di poll, seguire il modello di polling descritto in [Creare API personalizzate per chiamare altre API, servizi e sistemi da app per la logica](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* URL per il file Swagger (non OpenAPI) che descrive l'endpoint REST di destinazione

  In genere, l'endpoint REST deve soddisfare questi criteri perché il connettore possa funzionare:

  * Il file Swagger deve essere ospitato in un URL HTTPS accessibile pubblicamente.

  * Sul file Swagger deve essere abilitata la [condivisione risorse tra le origini (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

  Per fare riferimento a un file Swagger non ospitato o che non soddisfa i requisiti di sicurezza e condivisione tra le origini, è possibile [caricare il file Swagger in un contenitore BLOB su un account di archiviazione di Azure](#host-swagger) e abilitare CORS su tale account di archiviazione, in modo da poter fare riferimento al file.

  Gli esempi illustrati in questo argomento usano l'[API Viso di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/face/overview), che richiede un [account di Servizi cognitivi e una chiave di accesso](../cognitive-services/cognitive-services-apis-create-account.md).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

* App per la logica da cui si vuole chiamare l'endpoint di destinazione. Per iniziare con un trigger HTTP + Swagger, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare l'azione HTTP + Swagger, avviare l'app per la logica con il trigger desiderato. In questo esempio viene usato il trigger HTTP + Swagger come primo passaggio.

## <a name="add-an-http--swagger-trigger"></a>Aggiungere un trigger HTTP + Swagger

Questo trigger predefinito invia una richiesta HTTP a un URL per un file Swagger che descrive un'API REST e restituisce una risposta che contiene il contenuto del file.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota nella finestra di progettazione di App per la logica.

1. Nella casella di ricerca della finestra di progettazione immettere "swagger" come filtro. Nell'elenco dei **trigger**, selezionare il trigger **HTTP + Swagger**.

   ![Selezionare il trigger HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Nella casella **URL DELL'ENDPOINT SWAGGER**, immettere l'URL per il file Swagger e selezionare **Avanti**.

   Questo esempio usa l'URL Swagger che si trova nell'area Stati Uniti occidentali per l'[API Viso di Servizi cognitivi](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Immettere l'URL per l'endpoint Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando nella finestra di progettazione vengono visualizzate le operazioni descritte dal file Swagger, selezionare l'operazione da usare.

   ![Operazioni nel file Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Specificare i valori per i parametri del trigger, che variano in base all'operazione selezionata da includere nella chiamata all'endpoint. Impostare la frequenza con cui si desidera che il trigger chiami l'endpoint.

   In questo esempio, il trigger viene rinominato "Trigger HTTP + Swagger: Rilevamento viso", in modo da dare al passaggio un nome più descrittivo.

   ![Dettagli dell'operazione](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

   Per altre informazioni sui tipi di autenticazione disponibili per HTTP + Swagger, vedere [Aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="add-an-http--swagger-action"></a>Aggiungere un'azione HTTP + Swagger

Questa azione predefinita invia una richiesta HTTP a un URL per il file Swagger che descrive un'API REST e restituisce una risposta che contiene il contenuto del file.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

1. Nel passaggio in cui si vuole aggiungere l'azione HTTP + Swagger, selezionare **Nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. Nella casella di ricerca della finestra di progettazione immettere "swagger" come filtro. Nell'elenco **Azioni**, selezionare l'azione **HTTP + Swagger**.

    ![Selezionare l'azione HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Nella casella **URL DELL'ENDPOINT SWAGGER**, immettere l'URL per il file Swagger e selezionare **Avanti**.

   Questo esempio usa l'URL Swagger che si trova nell'area Stati Uniti occidentali per l'[API Viso di Servizi cognitivi](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Immettere l'URL per l'endpoint Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando nella finestra di progettazione vengono visualizzate le operazioni descritte dal file Swagger, selezionare l'operazione da usare.

   ![Operazioni nel file Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Specificare i valori per i parametri dell'azione, che variano in base all'operazione selezionata da includere nella chiamata all'endpoint.

   Questo esempio non contiene parametri, ma rinomina l'azione in "Azione HTTP + Swagger: Identificazione viso", in modo che il passaggio abbia un nome più descrittivo.

   ![Dettagli dell'operazione](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

   Per altre informazioni sui tipi di autenticazione disponibili per HTTP + Swagger, vedere [Aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Ospitare Swagger in Archiviazione di Azure

È possibile fare riferimento a un file Swagger non ospitato o che non soddisfa i requisiti di sicurezza e condivisione tra le origini caricando il file in un contenitore BLOB su un account di archiviazione di Azure e abilitando CORS su tale account di archiviazione. Per creare, configurare e archiviare file Swagger in Archiviazione di Azure, attenersi alla procedura seguente:

1. [Creare un account di Archiviazione di Azure](../storage/common/storage-create-storage-account.md).

1. Abilitare quindi CORS per il BLOB. Nel menu dell'account di archiviazione, selezionare **CORS**. Nella scheda **Servizio BLOB**, specificare questi valori e quindi selezionare **Salva**.

   | Proprietà | valore |
   |----------|-------|
   | **Origini consentite** | `*` |
   | **Metodi consentiti** | `GET`, `HEAD`, `PUT` |
   | **Intestazioni consentite** | `*` |
   | **Intestazioni esposte** | `*` |
   | **Validità massima** (in secondi) | `200` |
   |||

   Sebbene in questo esempio venga usato il [portale di Azure](https://portal.azure.com), è possibile usare uno strumento come [Azure Storage Explorer](https://storageexplorer.com/) oppure configurare automaticamente questa impostazione avvalendosi di questo [script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) di esempio.

1. [Creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). Nel riquadro **Panoramica** del contenitore, selezionare **Modifica livello di accesso**. Dall'elenco **Livello di accesso pubblico**, selezionare **BLOB (accesso in lettura anonimo solo per BLOB)** , quindi selezionare **OK**.

1. [Caricare il file Swagger nel contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) tramite il [portale di Azure](https://portal.azure.com) o [Azure Storage Explorer](https://storageexplorer.com/).

1. Per fare riferimento al file nel contenitore BLOB, ottenere l'URL HTTPS in questo formato, che fa distinzione tra maiuscole e minuscole, da Azure Storage Explorer:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Di seguito sono riportate altre informazioni sugli output di un trigger o di un'azione HTTP + Swagger. La chiamata HTTP + Swagger restituisce le seguenti informazioni:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| headers | object | Intestazioni della richiesta |
| Corpo | object | Oggetto JSON | Oggetto con il contenuto del corpo della richiesta |
| Codice di stato | INT | Codice di stato della risposta |
|||

| Codice di stato | Descrizione |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Richiesta non valida |
| 401 | Non autorizzata |
| 403 | Accesso negato |
| 404 | Non trovato |
| 500 | Errore interno del server. Si è verificato un errore sconosciuto. |
|||

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
