---
title: Connettersi agli endpoint REST dalle App per la logica di Azure
description: Monitorare gli endpoint REST in attività automatizzate, processi e flussi di lavoro usando le App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541680"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chiamare gli endpoint REST tramite App per la logica di Azure

Con [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e predefiniti HTTP + Swagger connector, è possibile automatizzare i flussi di lavoro regolarmente chiamare qualsiasi endpoint REST tramite un [file Swagger](https://swagger.io) creando App per la logica. HTTP + Swagger trigger e azione funzionano come le [trigger HTTP e azione](connectors-native-http.md) ma offrono un'esperienza migliore in Progettazione App per la logica esponendo la struttura dell'API e l'output descritto dal file Swagger. Per implementare un trigger di poll, seguire il modello di polling descritto in [creare API personalizzate per chiamare altre API, servizi e sistemi da App per la logica](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'URL per il file Swagger che descrive l'endpoint REST di destinazione

  In genere, l'endpoint REST deve soddisfare questo criterio per il connettore possa funzionare:

  * Il file Swagger deve essere ospitato in un URL HTTPS accessibile pubblicamente.

  * Deve avere il file Swagger [Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) abilitata.

  Per fare riferimento a un file Swagger che non sia ospitato o che non soddisfa i requisiti di sicurezza e cross-origin, è possibile [caricare il file Swagger in un contenitore blob in un account di archiviazione di Azure](#host-swagger)e abilitare CORS nell'account di archiviazione così che è possibile fare riferimento al file.

  Gli esempi in questo argomento viene utilizzato il [API viso di servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/face/overview), che richiede un [chiave di accesso e account di servizi cognitivi](../cognitive-services/cognitive-services-apis-create-account.md).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

* L'app per la logica da cui si desidera chiamare l'endpoint di destinazione. Al trigger HTTP + Swagger [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare HTTP + Swagger azione, avviare l'app per la logica con un qualsiasi trigger desiderato. In questo esempio Usa il protocollo HTTP + Swagger trigger come primo passaggio.

## <a name="add-an-http--swagger-trigger"></a>Aggiungere un HTTP + Swagger trigger

Questo trigger predefinito invia una richiesta HTTP a un URL per un file Swagger che descrive un'API REST e restituisce una risposta che contiene il contenuto del file.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in Progettazione App per la logica.

1. Nella finestra di progettazione, nella casella di ricerca immettere "swagger" come filtro. Dal **trigger** elenco, selezionare la **HTTP + Swagger** trigger.

   ![Selezionare HTTP + Swagger trigger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Nel **URL ENDPOINT SWAGGER** casella, immettere l'URL per il file Swagger e selezionare **successivo**.

   In questo esempio Usa l'URL di Swagger che si trova nell'area Stati Uniti occidentali per il [API viso di servizi cognitivi](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Immettere l'URL per l'endpoint Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando la finestra di progettazione Mostra le operazioni descritte per il file Swagger, selezionare l'operazione che si desidera utilizzare.

   ![Operazioni nel file Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Specificare i valori per i parametri di trigger, che variano in base all'operazione selezionata, che si desidera includere nella chiamata dell'endpoint. Impostare la ricorrenza per la frequenza con cui il trigger per chiamare l'endpoint.

   Questo esempio mostra come rinominare il trigger "HTTP + Swagger attivare: Viso: rilevare"in modo che il passaggio ha un nome più descrittivo.

   ![Dettagli dell'operazione](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Per aggiungere altri parametri disponibili, aprire il **Aggiungi nuovo parametro** elencare e selezionare i parametri desiderati.

   Per altre informazioni sui tipi di autenticazione disponibili per HTTP + Swagger, vedere [l'autenticazione HTTP trigger e azioni](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione, selezionare **salvare**.

## <a name="add-an-http--swagger-action"></a>Aggiungere un HTTP + Swagger azione

Questa azione integrata esegue una richiesta HTTP all'URL per il file Swagger che descrive un'API REST e restituisce una risposta che contiene il contenuto del file.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

1. Sotto il passaggio in cui si desidera aggiungere HTTP + Swagger azione, selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) che viene visualizzata e quindi selezionare **Aggiungi un'azione**.

1. Nella finestra di progettazione, nella casella di ricerca immettere "swagger" come filtro. Dal **azioni** elenco, selezionare la **HTTP + Swagger** azione.

    ![Selezionare l'azione HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Nel **URL ENDPOINT SWAGGER** casella, immettere l'URL per il file Swagger e selezionare **successivo**.

   In questo esempio Usa l'URL di Swagger che si trova nell'area Stati Uniti occidentali per il [API viso di servizi cognitivi](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Immettere l'URL per l'endpoint Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando la finestra di progettazione Mostra le operazioni descritte per il file Swagger, selezionare l'operazione che si desidera utilizzare.

   ![Operazioni nel file Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Specificare i valori per parametri dell'azione, che variano in base all'operazione selezionata, che si desidera includere nella chiamata dell'endpoint.

   Questo esempio non ha alcun parametro, ma consente di rinominare l'azione da "HTTP + Swagger azione: Viso: identificare"in modo che il passaggio ha un nome più descrittivo.

   ![Dettagli dell'operazione](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Per aggiungere altri parametri disponibili, aprire il **Aggiungi nuovo parametro** elencare e selezionare i parametri desiderati.

   Per altre informazioni sui tipi di autenticazione disponibili per HTTP + Swagger, vedere [l'autenticazione HTTP trigger e azioni](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione, selezionare **salvare**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Ospitare Swagger in archiviazione di Azure

È possibile fare riferimento a un file Swagger che non sia ospitato o per caricare tale file del contenitore blob in un account di archiviazione di Azure e l'abilitazione della condivisione CORS nell'account di archiviazione che non soddisfa i requisiti di sicurezza e cross-origin. Per creare, configurare e archiviare i file di Swagger in archiviazione di Azure, seguire questa procedura:

1. [Creare un account di Archiviazione di Azure](../storage/common/storage-create-storage-account.md).

1. A questo punto abilitare CORS per il blob. Nel menu dell'account di archiviazione, selezionare **CORS**. Nel **servizio Blob** scheda specificare questi valori e quindi selezionare **salvare**.

   | Proprietà | Value |
   |----------|-------|
   | **Origini consentite** | `*` |
   | **Metodi consentiti** | `GET`, `HEAD`, `PUT` |
   | **Intestazioni consentite** | `*` |
   | **Intestazioni esposte** | `*` |
   | **Validità massima** (in secondi) | `200` |
   |||

   Sebbene questo esempio Usa la [portale di Azure](https://portal.azure.com), è possibile usare uno strumento, ad esempio [Azure Storage Explorer](https://storageexplorer.com/), o configurare automaticamente questa impostazione usando questo esempio [scriptdiPowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Creare un contenitore blob](../storage/blobs/storage-quickstart-blobs-portal.md). Per il contenitore **Overview** riquadro, selezionare **Cambia livello di accesso**. Dal **livello di accesso pubblico** elenco, selezionare **Blob (accesso in lettura anonimo solo per BLOB)** e selezionare **OK**.

1. [Caricare il file Swagger nel contenitore blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), ad esempio tramite il [portale di Azure](https://portal.azure.com) oppure [Azure Storage Explorer](https://storageexplorer.com/).

1. Per fare riferimento al file nel contenitore blob, usare un collegamento HTTPS che segue questo formato, ovvero distinzione maiuscole/minuscole:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Di seguito sono riportate altre informazioni sugli output da un HTTP + Swagger trigger o azione. HTTP + Swagger chiamata restituisce queste informazioni:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| headers | object | Le intestazioni della richiesta |
| body | object | Oggetto JSON | L'oggetto con il contenuto del corpo della richiesta |
| status code | int | Il codice di stato della richiesta |
|||

| status code | Descrizione |
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