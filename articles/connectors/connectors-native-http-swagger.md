---
title: Connettersi agli endpoint REST dalle app per la logica di AzureConnect to REST endpoints from Azure Logic Apps
description: Monitorare gli endpoint REST in attività, processi e flussi di lavoro automatizzati usando App per la logica di AzureMonitor REST endpoints in automated tasks, processes, and workflows by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787370"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chiamare gli endpoint REST usando le app per la logica di AzureCall REST endpoints by using Azure Logic Apps

Con [le app per](../logic-apps/logic-apps-overview.md) la logica di Azure e il connettore HTTP e Swagger incorporato, è possibile automatizzare i flussi di lavoro che chiamano regolarmente qualsiasi endpoint REST tramite un file [Swagger](https://swagger.io) creando app per la logica. Il trigger e l'azione HTTP - Swagger funzionano allo stesso modo del [trigger e dell'azione HTTP,](connectors-native-http.md) ma offrono un'esperienza migliore in Progettazione app per la logica esponendo la struttura dell'API e gli output descritti dal file Swagger. Per implementare un trigger di polling, seguire il modello di polling descritto in [Creare API personalizzate per chiamare altre API, servizi e sistemi dalle app per la logica.](../logic-apps/logic-apps-create-api-app.md#polling-triggers)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* URL per il file Swagger (non OpenAPI) che descrive l'endpoint REST di destinazione

  In genere, l'endpoint REST deve soddisfare questi criteri affinché il connettore funzioni:Typically, the REST endpoint must meet this criteria for the connector to work:

  * Il file Swagger deve essere ospitato in un URL HTTPS accessibile pubblicamente.

  * Il file Swagger deve avere [Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) abilitato.

  Per fare riferimento a un file Swagger non ospitato o che non soddisfa i requisiti di sicurezza e di origine incrociata, è possibile [caricare il file Swagger in un contenitore BLOB in un account](#host-swagger)di archiviazione di Azure e abilitare CORS per tale account di archiviazione in modo da poter fare riferimento al file.

  Negli esempi di questo argomento viene utilizzata [l'API Viso di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/face/overview), che richiede un account di Servizi cognitivi e una chiave di [accesso.](../cognitive-services/cognitive-services-apis-create-account.md)

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md)

* App per la logica da cui si vuole chiamare l'endpoint di destinazione. Per iniziare con il trigger HTTP e Swagger, [creare un'app per la logica vuota.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Per usare l'azione HTTP e Swagger, avviare l'app per la logica con qualsiasi trigger desiderato. In questo esempio viene utilizzato il trigger HTTP - Swagger come primo passaggio.

## <a name="add-an-http--swagger-trigger"></a>Aggiunta di un trigger HTTP e Swagger

Questo trigger incorporato invia una richiesta HTTP a un URL per un file Swagger che descrive un'API REST e restituisce una risposta che include il contenuto del file.

1. Accedere al [portale](https://portal.azure.com)di Azure . Aprire l'app per la logica vuota in Progettazione app per la logica.

1. Nella finestra di progettazione, nella casella di ricerca, immettere "swagger" come filtro. Dall'elenco **Trigger,** selezionare il trigger **HTTP - Swagger.**

   ![Selezionare il trigger HTTP e Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Nella casella **URL SWAGGER ENDPOINT** immettere l'URL del file Swagger e selezionare **Avanti**.

   In questo esempio viene utilizzato l'URL Swagger che si trova nell'area Stati Uniti occidentali per [l'API Affronta di Servizi cognitivi:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Immettere l'URL per l'endpoint Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando nella finestra di progettazione vengono visualizzate le operazioni descritte dal file Swagger, selezionare l'operazione che si desidera utilizzare.

   ![Operazioni nel file Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Specificare i valori per i parametri di trigger, che variano in base all'operazione selezionata, che si desidera includere nella chiamata all'endpoint. Impostare la ricorrenza per la frequenza con cui si desidera che il trigger chiami l'endpoint.

   In questo esempio il trigger viene rinominato in "HTTP - Swagger trigger: Face - Detect" in modo che il passaggio abbia un nome più descrittivo.

   ![Dettagli dell'operazione](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

   Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP e Swagger, vedere [Aggiungere l'autenticazione alle chiamate in uscita.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="add-an-http--swagger-action"></a>Aggiungere un'azione HTTP e Swagger

Questa azione predefinita effettua una richiesta HTTP all'URL per il file Swagger che descrive un'API REST e restituisce una risposta che include il contenuto del file.

1. Accedere al [portale](https://portal.azure.com)di Azure . Aprire l'app per la logica in Logic App Designer.

1. Sotto il passaggio in cui si desidera aggiungere l'azione HTTP - Swagger , selezionare **Nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. Nella finestra di progettazione, nella casella di ricerca, immettere "swagger" come filtro. Dall'elenco **Azioni,** selezionare l'azione **HTTP - Swagger.**

    ![Selezionare l'azione HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Nella casella **URL SWAGGER ENDPOINT** immettere l'URL del file Swagger e selezionare **Avanti**.

   In questo esempio viene utilizzato l'URL Swagger che si trova nell'area Stati Uniti occidentali per [l'API Affronta di Servizi cognitivi:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Immettere l'URL per l'endpoint Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando nella finestra di progettazione vengono visualizzate le operazioni descritte dal file Swagger, selezionare l'operazione che si desidera utilizzare.

   ![Operazioni nel file Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Specificare i valori per i parametri dell'azione, che variano in base all'operazione selezionata, che si desidera includere nella chiamata all'endpoint.

   In questo esempio non sono presenti parametri, ma l'azione viene rinominata in "HTTP - Swagger action: Face - Identify" in modo che il passaggio abbia un nome più descrittivo.

   ![Dettagli dell'operazione](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

   Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP e Swagger, vedere [Aggiungere l'autenticazione alle chiamate in uscita.](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Host Swagger in Azure Storage

È possibile fare riferimento a un file Swagger non ospitato o che non soddisfa i requisiti di sicurezza e tra origini caricando il file nel contenitore BLOB in un account di archiviazione di Azure e abilitando CORS in tale account di archiviazione. Per creare, configurare e archiviare i file Swagger in Archiviazione di Azure, eseguire la procedura seguente:To create, set up, and store Swagger files in Azure Storage, follow these steps:

1. Creare un account di archiviazione di [Azure.Create an Azure storage account](../storage/common/storage-create-storage-account.md).

1. Abilitare ora CORS per il BLOB. Nel menu dell'account di archiviazione selezionare **CORS**. Nella scheda **Servizio BLOB** specificare questi valori e quindi selezionare **Salva**.

   | Proprietà | valore |
   |----------|-------|
   | **Origini consentite** | `*` |
   | **Metodi consentiti** | `GET`, `HEAD`, `PUT` |
   | **Intestazioni consentite** | `*` |
   | **Intestazioni esposte** | `*` |
   | **Età massima** (in secondi) | `200` |
   |||

   Anche se in questo esempio viene utilizzato il portale di [Azure,](https://portal.azure.com)è possibile usare uno strumento come [Azure Storage Explorer](https://storageexplorer.com/)o configurare automaticamente questa impostazione usando questo script di [PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)di esempio.

1. [Creare un contenitore BLOB.](../storage/blobs/storage-quickstart-blobs-portal.md) Nel riquadro **Panoramica** del contenitore selezionare **Modifica livello**di accesso . Nell'elenco **Livello di accesso pubblico** selezionare BLOB **(accesso in lettura anonimo solo per i BLOB)** e **scegliere OK**.

1. [Caricare il file Swagger nel contenitore BLOB,](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)tramite il portale di [Azure](https://portal.azure.com) o Azure [Storage Explorer.](https://storageexplorer.com/)

1. Per fare riferimento al file nel contenitore BLOB, usare un collegamento HTTPS che segue questo formato, che fa distinzione tra maiuscole e minuscole:To reference the file in the blob container, use an HTTPS link that follows this format, which is case-sensitive:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Di seguito sono riportate ulteriori informazioni sugli output di un trigger o di un'azione HTTP- Swagger. La chiamata HTTP e Swagger restituisce queste informazioni:

| Nome proprietà | Type | Descrizione |
|---------------|------|-------------|
| headers | object | Le intestazioni della richiesta |
| Corpo | object | Oggetto JSON | Oggetto con il contenuto del corpo della richiesta |
| Codice di stato | INT | Il codice di stato della richiesta |
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
