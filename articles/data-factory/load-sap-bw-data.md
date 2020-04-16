---
title: Caricare i dati da SAP Business Warehouse
description: Usare Azure Data Factory per copiare dati da SAP Business Warehouse (BW)Use Azure Data Factory to copy data from SAP Business Warehouse (BW)
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 96b23696164514ad2f16de72f0f76aa237ffce2e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415847"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiare dati da SAP Business Warehouse usando Azure Data FactoryCopy data from SAP Business Warehouse by using Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare Azure Data Factory per copiare dati da SAP Business Warehouse (BW) tramite Open Hub ad Azure Data Lake Storage Gen2.This article shows how to use Azure Data Factory to copy data from SAP Business Warehouse (BW) via Open Hub to Azure Data Lake Storage Gen2. È possibile utilizzare un processo simile per copiare i dati in altri [archivi dati sink supportati.](copy-activity-overview.md#supported-data-stores-and-formats)

> [!TIP]
> Per informazioni generali sulla copia dei dati da SAP BW, incluso l'integrazione di SAP BW Open Hub e il flusso di estrazione delta, vedere [Copiare dati da SAP Business Warehouse tramite Open Hub tramite Azure Data Factory.](connector-sap-business-warehouse-open-hub.md)

## <a name="prerequisites"></a>Prerequisiti

- **Azure Data Factory:** se non ne hai uno, segui i passaggi per [creare una data factory.](quickstart-create-data-factory-portal.md#create-a-data-factory)

- **SAP BW Open Hub Destination (OHD) con tipo di destinazione "Database Table":** per creare un FILE OHD o per verificare che l'OHD sia configurato correttamente per l'integrazione di Data Factory, vedere la sezione [Configurazioni SAP BW Open Hub Destination](#sap-bw-open-hub-destination-configurations) di questo articolo.

- **L'utente SAP BW necessita delle autorizzazioni seguenti:**

  - Autorizzazione per chiamate di funzione remote (RFC) e SAP BW.
  - Autorizzazioni per l'attività "Esecuzione" dell'oggetto **autorizzazione S_SDSAUTH.**

- Un runtime di ** [integrazione self-hosted (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) con connettore SAP .NET 3.0**. Attenersi alla seguente procedura di configurazione:

  1. Installare e registrare il runtime di integrazione self-hosted, versione 3.13 o successiva. (Questo processo è descritto più avanti in questo articolo.)

  2. Scaricare il connettore SAP a [64 bit per Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) dal sito Web sap e installarlo nello stesso computer del componente di accesso client. Durante l'installazione, assicurarsi di selezionare **Installa assembly nella Global Assembly Cache** nella finestra di dialogo Passaggi di installazione **facoltativi,** come illustrato nell'immagine seguente:

     ![Finestra di dialogo Configura connettore SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Eseguire una copia completa da SAP BW Open Hub

Nel portale di Azure accedere alla data factory. Selezionare **Crea & Monitor** per aprire l'interfaccia utente di Data Factory in una scheda separata.

1. Nella pagina **Introduzione** selezionare **Copia dati** per aprire lo strumento Copia dati.

2. Nella pagina **Proprietà** specificare un **Nome attività**e quindi scegliere **Avanti**.

3. Nella pagina **Archivio dati di origine** selezionare **Crea nuova connessione**. Selezionare **SAP BW Open Hub** dalla raccolta connettori, quindi selezionare **Continua**. Per filtrare i connettori, è possibile digitare **SAP** nella casella di ricerca.

4. Nella pagina Specificare la **connessione SAP BW Open Hub** eseguire la procedura seguente per creare una nuova connessione.

   ![Pagina Crea servizio collegato all'hub aperto SAP BW](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Nell'elenco **Connetti tramite runtime** di integrazione selezionare un runtime di integrazione esistente. In alternativa, scegli di crearne uno se non ne hai ancora uno.

      Per creare un nuovo iR auto-ospitato, selezionare **Nuovo**, quindi **Self-hosted**. Immettere un **Nome**, quindi scegliere **Avanti**. Selezionare **Configurazione rapida** per eseguire l'installazione nel computer corrente oppure seguire i passaggi di **configurazione manuale** forniti.

      Come indicato in [Prerequisiti](#prerequisites), assicurarsi di avere installato SAP Connector per Microsoft .NET 3.0 nello stesso computer in cui è in esecuzione il componente di accesso qualità utente.

   2. Compilare i **campi nome server**SAP BW , Numero di **sistema**, ID **client ,** **Lingua** (se diverso da **EN**), **Nome utente**e **Password**.

   3. Selezionare **Verifica connessione** per convalidare le impostazioni e quindi selezionare **Fine**.

   4. Viene creata una nuova connessione. Selezionare **Avanti**.

5. Nella pagina **Selezione destinazioni hub aperte** sfogliare le destinazioni Hub aperte disponibili in SAP BW. Selezionare la FUNZIONE OHD da cui copiare i dati, quindi scegliere **Avanti**.

   ![Selezionare la tabella SAP BW Open Hub Destination](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Specificare un filtro, se necessario. Se l'ORD contiene solo dati di un singolo processo di trasferimento dati (DTP) con un singolo ID richiesta oppure si è certi che il DTP sia terminato e si desidera copiare i dati, deselezionare la casella di controllo **Escludi ultima richiesta.**

   Per altre informazioni su queste impostazioni, vedere la sezione [Configurazioni SAP BW Open Hub Destination](#sap-bw-open-hub-destination-configurations) di questo articolo. Selezionare **Convalida** per verificare quali dati verranno restituiti. Fare quindi clic su **Avanti**.

   ![Configurare il filtro Sap BW Open Hub](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Nella pagina **Archivio dati** di destinazione selezionare **Crea nuova connessione** > **Azure Data Lake Storage Gen2** > **continua**.

8. Nella pagina **Specificare** la connessione di Archiviazione dati di Azure eseguire la procedura seguente per creare una connessione.

   ![Creare una pagina del servizio collegato ADLS Gen2Create an ADLS Gen2 linked service page](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selezionare l'account con supporto per Data Lake Storage Gen2 dall'elenco a discesa **Nome.**
   2. Selezionare **Fine** per creare la connessione. Fare quindi clic su **Avanti**.

9. Nella pagina **Scegliere il file o la cartella** di output immettere **copyfromopenhub** come nome della cartella di output. Fare quindi clic su **Avanti**.

   ![Pagina Scegli cartella di output](media/load-sap-bw-data/choose-output-folder.png)

10. Nella pagina **Impostazione formato file** selezionare **Avanti** per utilizzare le impostazioni predefinite.

    ![Specificare la pagina del formato sink](media/load-sap-bw-data/specify-sink-format.png)

11. Nella pagina **Impostazioni** espandere **Impostazioni prestazioni**. Immettere un valore per **Grado di parallelismo della copia,** ad esempio 5, per caricare da SAP BW in parallelo. Fare quindi clic su **Avanti**.

    ![Configurare le impostazioni di copia](media/load-sap-bw-data/configure-copy-settings.png)

12. Nella pagina **Riepilogo** esaminare le impostazioni. Fare quindi clic su **Avanti**.

13. Nella pagina **Distribuzione** selezionare **Monitoraggio** per monitorare la pipeline.

    ![Pagina Distribuzione](media/load-sap-bw-data/deployment.png)

14. Si noti che la scheda **Monitoraggio** sul lato sinistro della pagina viene selezionata automaticamente. La colonna **Azioni** include collegamenti per visualizzare i dettagli dell'esecuzione dell'attività e per rieseguire la pipeline.

    ![Visualizzazione di monitoraggio della pipelinePipeline monitoring view](media/load-sap-bw-data/pipeline-monitoring.png)

15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare **Visualizza esecuzioni attività** nella colonna **Azioni.** Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione con duca della pipeline, selezionare il collegamento **Pipeline** nella parte superiore. Selezionare **Aggiorna** per aggiornare l'elenco.

    ![Schermata di monitoraggio delle attività](media/load-sap-bw-data/activity-monitoring.png)

16. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli,** ovvero un'icona a forma di occhiali sotto **Azioni** nella visualizzazione di monitoraggio dell'attività. I dettagli disponibili includono il volume di dati copiato dall'origine al sink, la velocità effettiva dei dati, i passaggi e la durata di esecuzione e le configurazioni usate.

    ![Dettagli monitoraggio attività](media/load-sap-bw-data/activity-monitoring-details.png)

17. Per visualizzare **l'ID richiesta massimo,** tornare alla visualizzazione di monitoraggio attività e selezionare **Output** in **Azioni**.

    ![Schermata di output dell'attività](media/load-sap-bw-data/activity-output.png)

    ![Visualizzazione dei dettagli dell'output dell'attività](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Copia incrementale da SAP BW Open Hub

> [!TIP]
> Vedere [SAP BW Open Hub Connector delta extraction flow](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) per scoprire come il connettore SAP BW Open Hub in Data Factory copia i dati incrementali da SAP BW. Questo articolo consente inoltre di comprendere la configurazione di base del connettore.

A questo punto, continueremo a configurare la copia incrementale da SAP BW Open Hub.

La copia incrementale utilizza un meccanismo di "filigrana elevata" basato **sull'ID richiesta.** Tale ID viene generato automaticamente nella destinazione Open Hub SAP BW dal DTP. Il diagramma seguente mostra questo flusso di lavoro:The following diagram shows this workflow:

![Diagramma di flusso del flusso di lavoro della copia incrementale](media/load-sap-bw-data/incremental-copy-workflow.png)

Nella pagina **Data factory Iniziamo** selezionare **Crea pipeline da modello** per usare il modello predefinito.

1. Cercare **SAP BW** per trovare e selezionare il modello **Copia incrementale da SAP BW ad Azure Data Lake Storage Gen2.** Questo modello copia i dati in Azure Data Lake Storage Gen2.This template copies data into Azure Data Lake Storage Gen2. È possibile utilizzare un flusso di lavoro simile per copiare in altri tipi di sink.

2. Nella pagina principale del modello selezionare o creare le tre connessioni seguenti e quindi selezionare **Usa questo modello** nell'angolo inferiore destro della finestra.

   - **Archiviazione BLOB**di Azure: in questa procedura dettagliata viene usato l'archiviazione BLOB di Azure per archiviare il limite massimo, ovvero l'ID massimo della *richiesta copiato.*
   - **SAP BW Open Hub**: Questa è l'origine da cui copiare i dati. Fare riferimento alla procedura dettagliata di copia completa precedente per la configurazione dettagliata.
   - **Azure Data Lake Storage Gen2:** questo è il sink in cui copiare i dati. Fare riferimento alla procedura dettagliata di copia completa precedente per la configurazione dettagliata.

   ![Copia incrementale dal modello SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Questo modello genera una pipeline con le tre attività seguenti e le rende concatenate in caso di esito positivo: *Lookup*, *Copy Data*e *Web*.

   Passare alla scheda **Parametri** pipeline. Vengono visualizzate tutte le configurazioni che è necessario fornire.

   ![Copia incrementale dalla configurazione SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Specificare il nome della tabella Open Hub da cui copiare i dati.

   - **Data_Destination_Container:** specificare il contenitore Azure Data Lake Storage Gen2 di destinazione in cui copiare i dati. Se il contenitore non esiste, l'attività di copia di Data Factory ne crea una durante l'esecuzione.
  
   - **Data_Destination_Directory:** specificare il percorso della cartella nel contenitore Azure Data Lake Storage Gen2 in cui copiare i dati. Se il percorso non esiste, l'attività di copia di Data Factory crea un percorso durante l'esecuzione.
  
   - **HighWatermarkBlobContainer:** specificare il contenitore in cui archiviare il valore high-watermark.

   - **HighWatermarkBlobDirectory:** specificare il percorso della cartella nel contenitore in cui archiviare il valore high-watermark.

   - **HighWatermarkBlobName:** specificare il nome del BLOB in `requestIdCache.txt`cui archiviare il valore massimo della filigrana, ad esempio . Nell'archivio BLOB passare al percorso corrispondente di HighWatermarkBlobContainer, HighWatermarkBlobDirectory, HighWatermarkBlobName, ad esempio *container/path/requestIdCache.txt*. Creare un BLOB con contenuto 0.Create a blob with content 0.

      ![Contenuto BLOB](media/load-sap-bw-data/blob.png)

   - **LogicAppURL:** in questo modello viene usata WebActivity per chiamare app per la logica di Azure per impostare il valore di filigrana elevata nell'archivio BLOB. In alternativa, è possibile usare il database SQL di Azure per archiviarlo. Utilizzare un'attività di stored procedure per aggiornare il valore.

      È innanzitutto necessario creare un'app per la logica, come illustrato nell'immagine seguente. Quindi, incollare **l'URL HTTP POST**.

      ![Configurazione dell'app per la logica](media/load-sap-bw-data/logic-app-config.png)

      1. Accedere al portale di Azure. Selezionare un nuovo servizio **App per la logica.** Selezionare **: Applicazione per la logica vuota** per passare a **Progettazione app per la logica**.

      2. Creare un trigger di Quando viene ricevuta una **richiesta HTTP.** Specificare il corpo della richiesta HTTP come segue:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Aggiungere un'azione **Crea BLOB.** Per **Percorso cartella** e Nome **BLOB**, utilizzare gli stessi valori configurati in precedenza in *HighWatermarkBlobContainer, HighWatermarkBlobDirectory* e *HighWatermarkBlobName*.

      4. Selezionare **Salva**. Copiare quindi il valore **dell'URL HTTP POST** da utilizzare nella pipeline di Data Factory.

4. Dopo aver fornito i parametri della pipeline di Data Factory, selezionare **Debug** > **Fine** per richiamare un'esecuzione per convalidare la configurazione. In alternativa, selezionare **Pubblica** per pubblicare tutte le modifiche e quindi selezionare **Aggiungi trigger** per eseguire un'esecuzione.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurazioni SAP BW Open Hub Destination

In questa sezione viene illustrata la configurazione del lato SAP BW per utilizzare il connettore SAP BW Open Hub in Data Factory per copiare i dati.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurare l'estrazione delta in SAP BW

Se sono necessarie sia la copia cronologica che la copia incrementale o solo la copia incrementale, configurare l'estrazione delta in SAP BW.

1. Creare la destinazione hub aperta. È possibile creare l'OHD in SAP Transaction RSA1, che crea automaticamente il processo di trasformazione e trasferimento dei dati richiesto. Usare le seguenti impostazioni:

   - **ObjectType**: È possibile utilizzare qualsiasi tipo di oggetto. In questo caso, utilizziamo **InfoCube** come esempio.
   - **Tipo di destinazione**: Seleziona **tabella database**.
   - **Chiave della tabella**: Seleziona **chiave tecnica**.
   - **Estrazione**: Selezionare **Mantieni dati e Inserisci record nella tabella**.

   ![Finestra di dialogo Crea estrazione delta OHD SAP BW](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Finestra di dialogo Di estrazione delta2 DI SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   È possibile aumentare il numero di processi di lavoro SAP in esecuzione paralleli per il DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Pianificare il DTP nelle catene di processo.

   Un DTP delta per un cubo funziona solo se le righe necessarie non sono state compresse. Assicurarsi che la compressione del cubo BW non sia in esecuzione prima che il DTP alla tabella Open Hub. Il modo più semplice per eseguire questa operazione consiste nell'integrare il DTP nelle catene di processi esistenti. Nell'esempio seguente, il DTP (per l'OHD) viene inserito nella catena di processi tra i passaggi *Adjust* (rollup aggregato) e *Collapse* (compressione cubo).

   ![Creare un diagramma di flusso della catena di processi SAP BWCreate SAP BW process chain flow chart](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurare l'estrazione completa in SAP BW

Oltre all'estrazione delta, potrebbe essere necessario un'estrazione completa dello stesso SAP BW InfoProvider. Questo di solito si applica se si desidera eseguire la copia completa ma non incrementale, o si desidera [risincronizzare l'estrazione delta](#resync-delta-extraction).

Non è possibile avere più di un DTP per lo stesso OHD. Pertanto, è necessario creare un FILE OHD aggiuntivo prima dell'estrazione delta.

![Creazione di SAP BW OHD completa](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Per un OHD a pieno carico, scegliere opzioni diverse da quelle per l'estrazione delta:

- In OHD: impostare l'opzione **Estrazione** su **Elimina dati e inserisci record**. In caso contrario, i dati verranno estratti più volte quando si ripete il DTP in una catena di processi BW.

- Nel DTP: Impostare **la modalità** di estrazione **su Completo**. È necessario modificare il DTP creato automaticamente da **Delta** a **Completo** subito dopo la creazione dell'OHD, come illustrato nell'immagine seguente:

   ![Finestra di dialogo Crea SA-OHD configurata per l'estrazione "Completa"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Nel connettore BW Open Hub di Data Factory: disattiva **Escludi ultima richiesta**. In caso contrario, non verrà estratto nulla.

In genere si esegue manualmente il DTP completo. In alternativa, è possibile creare una catena di processi per l'intero DTP. Si tratta in genere di una catena separata che è indipendente dalle catene di processi esistenti. In entrambi i casi, *assicurarsi che il DTP sia terminato prima di avviare l'estrazione utilizzando Copia Data Factory*. In caso contrario, verranno copiati solo i dati parziali.

### <a name="run-delta-extraction-the-first-time"></a>Eseguire l'estrazione delta la prima volta

La prima estrazione delta è tecnicamente *un'estrazione completa.* Per impostazione predefinita, il connettore SAP BW Open Hub esclude l'ultima richiesta durante la copia dei dati. Per la prima estrazione delta, nessun dato viene estratto dall'attività di copia di Data Factory finché un DTP successivo non genera dati delta nella tabella con un ID richiesta separato. Esistono due modi per evitare questo scenario:There are two ways to avoid this scenario:

- Disattivare l'opzione **Escludi ultima richiesta** per la prima estrazione differenziale. Assicurarsi che il primo DTP delta sia terminato prima di avviare l'estrazione delta la prima volta.
-  Utilizzare la procedura per risincronizzare l'estrazione delta, come descritto nella sezione successiva.

### <a name="resync-delta-extraction"></a>Estrazione delta di risincronizzazione

Gli scenari seguenti modificano i dati nei cubi SAP BW ma non vengono considerati dal DTP delta:

- Eliminazione selettiva SAP BW (di righe utilizzando qualsiasi condizione di filtro)
- Eliminazione richiesta SAP BW (di richieste erresi che causano richieste)

Una destinazione SAP Open Hub non è una destinazione dati controllata dai dati (in tutti i pacchetti di supporto SAP BW dal 2015). Pertanto, è possibile eliminare i dati da un cubo senza modificare i dati nell'OHD. È quindi necessario risincronizzare i dati del cubo con Data Factory:

1. Eseguire un'estrazione completa in Data Factory (utilizzando un DTP completo in SAP).
2. Eliminare tutte le righe nella tabella Open Hub per il DTP delta.
3. Impostare lo stato del DTP delta su **Fetched**.

Successivamente, tutte le successive dTP delta e le estrazioni delta di Data Factory funzionano come previsto.

Per impostare lo stato del Valore DTP delta su **Fetched**, è possibile utilizzare la seguente opzione per eseguire manualmente il DTP delta:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul supporto dei connettori SAP BW Open Hub:

> [!div class="nextstepaction"]
>[Connettore Open Hub SAP Business Warehouse](connector-sap-business-warehouse-open-hub.md)
