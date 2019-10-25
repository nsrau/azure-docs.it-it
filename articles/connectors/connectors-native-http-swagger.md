---
title: Connettersi agli endpoint REST da app per la logica di Azure
description: Monitorare gli endpoint REST in attività automatizzate, processi e flussi di lavoro usando app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 663ef16511269dd61a6567d6570f3445b7da6447
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804250"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Chiamare gli endpoint REST usando app per la logica di Azure

Con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e il connettore http + spavalderia incorporato è possibile automatizzare i flussi di lavoro che chiamano regolarmente qualsiasi endpoint REST tramite un file di [spavalderia](https://swagger.io) compilando app per la logica. Il trigger e l'azione HTTP + spavalderia funzionano come il [trigger e l'azione http](connectors-native-http.md) , ma offrono un'esperienza migliore nella finestra di progettazione di app per la logica esponendo la struttura dell'API e gli output descritti dal file spavalderia. Per implementare un trigger di polling, seguire il modello di polling descritto in [creare API personalizzate per chiamare altre API, servizi e sistemi da app per la logica](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* URL del file spavalderia che descrive l'endpoint REST di destinazione

  In genere, l'endpoint REST deve soddisfare questo criterio per il funzionamento del connettore:

  * Il file di spavalderia deve essere ospitato in un URL HTTPS accessibile pubblicamente.

  * Per il file di spavalderia è necessario che sia abilitata la [condivisione di risorse tra le origini (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

  Per fare riferimento a un file di spavalderia non ospitato o che non soddisfi i requisiti di sicurezza e tra le origini, è possibile [caricare il file spavalderia in un contenitore BLOB in un account di archiviazione di Azure](#host-swagger)e abilitare CORS in tale account di archiviazione in modo che sia possibile fare riferimento a file.

  Gli esempi in questo argomento usano la [API viso di servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/face/overview), che richiede un [account di servizi cognitivi e una chiave di accesso](../cognitive-services/cognitive-services-apis-create-account.md).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se non si ha familiarità con App per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md).

* App per la logica da cui si vuole chiamare l'endpoint di destinazione. Per iniziare con il trigger HTTP + spavalderia, [creare un'app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per usare l'azione HTTP + spavalderia, avviare l'app per la logica con tutti i trigger desiderati. Questo esempio usa il trigger HTTP + spavalderia come primo passaggio.

## <a name="add-an-http--swagger-trigger"></a>Aggiungere un trigger HTTP + spavalderia

Questo trigger predefinito invia una richiesta HTTP a un URL per un file spavalderia che descrive un'API REST e restituisce una risposta che contiene il contenuto del file.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica vuota in progettazione app per la logica.

1. Nella casella di ricerca della finestra di progettazione immettere "spavalderia" come filtro. Dall'elenco **trigger** selezionare il trigger **http + spavalderia** .

   ![Selezionare il trigger HTTP + spavalderia](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Nella casella **URL endpoint spavalderia** immettere l'URL per il file di spavalderia e selezionare **Avanti**.

   Questo esempio usa l'URL di spavalderia che si trova nell'area Stati Uniti occidentali per i [Servizi cognitivi API viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Immettere l'URL per l'endpoint di spavalderia](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Quando nella finestra di progettazione vengono visualizzate le operazioni descritte dal file spavalderia, selezionare l'operazione che si desidera utilizzare.

   ![Operazioni nel file spavalderia](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Specificare i valori per i parametri del trigger, che variano in base all'operazione selezionata che si desidera includere nella chiamata all'endpoint. Impostare la ricorrenza per la frequenza con cui si desidera che il trigger chiami l'endpoint.

   Questo esempio rinomina il trigger in "HTTP + spavalderia trigger: Face-Detect" in modo che il passaggio abbia un nome più descrittivo.

   ![Dettagli dell'operazione](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

   Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP + spavalderia, vedere [aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Proseguire con la creazione del flusso di lavoro dell'app per la logica con azioni da eseguire all'attivazione del trigger.

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

## <a name="add-an-http--swagger-action"></a>Aggiungere un'azione HTTP + spavalderia

Questa azione predefinita esegue una richiesta HTTP all'URL per il file spavalderia che descrive un'API REST e restituisce una risposta che contiene il contenuto del file.

1. Accedere al [portale di Azure](https://portal.azure.com). Aprire l'app per la logica in Logic App Designer.

1. Nel passaggio in cui si vuole aggiungere l'azione HTTP + spavalderia selezionare **nuovo passaggio**.

   Per aggiungere un'azione tra i passaggi, spostare il puntatore del mouse sulla freccia tra i passaggi. Selezionare il segno più ( **+** ) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. Nella casella di ricerca della finestra di progettazione immettere "spavalderia" come filtro. Nell'elenco **azioni** selezionare l'azione **http + spavalderia** .

    ![Selezionare l'azione HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Nella casella **URL endpoint spavalderia** immettere l'URL per il file di spavalderia e selezionare **Avanti**.

   Questo esempio usa l'URL di spavalderia che si trova nell'area Stati Uniti occidentali per i [Servizi cognitivi API viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Immettere l'URL per l'endpoint di spavalderia](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Quando nella finestra di progettazione vengono visualizzate le operazioni descritte dal file spavalderia, selezionare l'operazione che si desidera utilizzare.

   ![Operazioni nel file spavalderia](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Specificare i valori per i parametri dell'azione, che variano in base all'operazione selezionata che si desidera includere nella chiamata all'endpoint.

   Questo esempio non include parametri, ma Rinomina l'azione in "HTTP + spavalderia Action: Face-Identifit" in modo che il passaggio abbia un nome più descrittivo.

   ![Dettagli dell'operazione](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Per aggiungere altri parametri disponibili, aprire l'elenco **Aggiungi nuovo parametro** e selezionare i parametri desiderati.

   Per ulteriori informazioni sui tipi di autenticazione disponibili per HTTP + spavalderia, vedere [aggiungere l'autenticazione alle chiamate in uscita](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Al termine, ricordarsi di salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Spavalderia host in archiviazione di Azure

È possibile fare riferimento a un file di spavalderia non ospitato o che non soddisfi i requisiti di sicurezza e tra le origini caricando il file nel contenitore BLOB in un account di archiviazione di Azure e abilitando CORS in tale account di archiviazione. Per creare, configurare e archiviare i file di spavalderia in archiviazione di Azure, seguire questa procedura:

1. [Creare un account di Archiviazione di Azure](../storage/common/storage-create-storage-account.md).

1. Abilitare ora CORS per il BLOB. Scegliere **CORS**dal menu dell'account di archiviazione. Nella scheda **servizio BLOB** specificare questi valori e quindi selezionare **Salva**.

   | Proprietà | Value |
   |----------|-------|
   | **Origini consentite** | `*` |
   | **Metodi consentiti** | `GET`, `HEAD`, `PUT` |
   | **Intestazioni consentite** | `*` |
   | **Intestazioni esposte** | `*` |
   | **Validità massima** (in secondi) | `200` |
   |||

   Anche se in questo esempio viene usato il [portale di Azure](https://portal.azure.com), è possibile usare uno strumento come [Azure Storage Explorer](https://storageexplorer.com/)oppure configurare automaticamente questa impostazione usando questo [script di PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)di esempio.

1. [Creare un contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). Nel riquadro **Panoramica** del contenitore selezionare **modifica livello di accesso**. Dall'elenco **livello di accesso pubblico** selezionare **BLOB (accesso in lettura anonimo solo per BLOB)** e fare clic su **OK**.

1. [Caricare il file di spavalderia nel contenitore BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), tramite il [portale di Azure](https://portal.azure.com) o [Azure Storage Explorer](https://storageexplorer.com/).

1. Per fare riferimento al file nel contenitore BLOB, usare un collegamento HTTPS che segue questo formato, che fa distinzione tra maiuscole e minuscole:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Di seguito sono riportate altre informazioni sugli output di un trigger o un'azione HTTP + spavalderia. La chiamata HTTP + spavalderia restituisce queste informazioni:

| Nome proprietà | Type | Description |
|---------------|------|-------------|
| Headers | object | Intestazioni della richiesta |
| body | object | Oggetto JSON | Oggetto con il contenuto del corpo della richiesta |
| Codice di stato | int | Codice di stato della richiesta |
|||

| Codice di stato | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Richiesta non valida |
| 401 | Non autorizzato |
| 403 | Accesso negato |
| 404 | Non trovato |
| 500 | Errore interno del server. Si è verificato un errore sconosciuto. |
|||

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)