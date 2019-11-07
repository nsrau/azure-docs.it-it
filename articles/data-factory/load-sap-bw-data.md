---
title: Caricare i dati da SAP Business Warehouse usando Azure Data Factory
description: Usare Azure Data Factory per copiare dati da SAP Business Warehouse (BW)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 0c96ecff27a57b3277e7c8105766059b739d11af
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672658"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiare dati da SAP Business Warehouse usando Azure Data Factory

Questo articolo illustra come usare Azure Data Factory per copiare dati da SAP Business Warehouse (BW) tramite Open Hub a Azure Data Lake Storage Gen2. È possibile usare un processo simile per copiare i dati in altri [archivi dati di sink supportati](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Per informazioni generali sulla copia di dati da SAP BW, tra cui SAP BW l'integrazione dell'hub aperto e il flusso di estrazione Delta, vedere [copiare dati da SAP Business Warehouse tramite l'hub aperto usando Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Prerequisiti

- **Azure Data Factory**: se non è presente, seguire i passaggi per [creare una data factory](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW OHD (Open Hub Destination) con tipo di destinazione "tabella di database"** : per creare un OHD o per verificare che la OHD sia configurata correttamente per l'integrazione Data Factory, vedere la sezione [SAP BW configurazioni di destinazione dell'hub aperto](#sap-bw-open-hub-destination-configurations) di questo articolo.

- Per **l'utente SAP BW sono necessarie le autorizzazioni seguenti**:

  - Autorizzazione per le chiamate di funzioni remote (RFC) e SAP BW.
  - Autorizzazioni per l'attività "Execute" dell'oggetto di autorizzazione **S_SDSAUTH** .

- **Un [runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime) con SAP .NET Connector 3,0**. Seguire questa procedura di configurazione:

  1. Installare e registrare il runtime di integrazione self-hosted versione 3,13 o successiva. Questo processo è descritto più avanti in questo articolo.

  2. Scaricare il [connettore SAP a 64 bit per Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) dal sito Web di SAP e installarlo nello stesso computer del runtime di integrazione self-hosted. Durante l'installazione, assicurarsi di selezionare **Installa assembly nella GAC** nella finestra di dialogo **procedure di installazione facoltative** , come illustrato nella figura seguente:

     ![Finestra di dialogo Configura connettore SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Eseguire una copia completa da SAP BW Hub aperto

Nella portale di Azure passare al data factory. Selezionare **crea & monitoraggio** per aprire l'interfaccia utente di data factory in una scheda separata.

1. Nella pagina attività **iniziali** selezionare **copia dati** per aprire lo strumento copia dati.

2. Nella pagina **Proprietà** specificare un nome di **attività**e quindi fare clic su **Avanti**.

3. Nella pagina **archivio dati di origine** selezionare **+ Crea nuova connessione**. Selezionare **SAP BW Hub aperto** dalla raccolta di connettori, quindi selezionare **continua**. Per filtrare i connettori, è possibile digitare **SAP** nella casella di ricerca.

4. Nella pagina **specificare SAP BW connessione all'hub aperto** , attenersi alla seguente procedura per creare una nuova connessione.

   ![Creare SAP BW pagina del servizio collegato all'hub aperto](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Dall'elenco **Connetti tramite Integration Runtime** selezionare un runtime di integrazione self-hosted esistente. In alternativa, scegliere di crearne uno se non ne è già presente uno.

      Per creare un nuovo runtime di integrazione self-hosted, selezionare **+ nuovo**, quindi selezionare **self-hosted**. Immettere un **nome**e quindi fare clic su **Avanti**. Selezionare **installazione rapida** da installare nel computer corrente o attenersi alla procedura di **configurazione manuale** fornita.

      Come indicato nei [prerequisiti](#prerequisites), assicurarsi che SAP Connector per Microsoft .NET 3,0 sia installato nello stesso computer in cui è in esecuzione il runtime di integrazione self-hosted.

   2. Immettere il nome del **Server**SAP BW, il **numero di sistema**, l' **ID client,** la **lingua** (se diverso da **en**), il **nome utente**e la **password**.

   3. Selezionare **Test connessione** per convalidare le impostazioni e quindi fare clic su **fine**.

   4. Viene creata una nuova connessione. Selezionare **Avanti**.

5. Nella pagina **Seleziona destinazioni Hub aperte** individuare le destinazioni di hub aperte disponibili nell'SAP BW. Selezionare il OHD da cui copiare i dati e quindi fare clic su **Avanti**.

   ![Selezionare SAP BW tabella di destinazione dell'hub aperto](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Se necessario, specificare un filtro. Se il OHD contiene solo i dati di una singola esecuzione del processo di trasferimento dei dati (DTP) con un solo ID richiesta o se si è certi che il DTP è terminato e si vogliono copiare i dati, deselezionare la casella di controllo **Escludi ultima richiesta** .

   Altre informazioni su queste impostazioni sono disponibili nella sezione [SAP BW configurazioni di destinazione dell'hub aperto](#sap-bw-open-hub-destination-configurations) di questo articolo. Selezionare **convalida** per controllare i dati che verranno restituiti. Quindi selezionare **Avanti**.

   ![Configurare SAP BW filtro Hub aperto](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Nella pagina **archivio dati di destinazione** selezionare **+ crea nuova connessione** > **Azure Data Lake storage Gen2** > **continua**.

8. Nella pagina **specifica Azure Data Lake storage connessione** seguire questa procedura per creare una connessione.

   ![Creare una pagina del servizio collegato ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selezionare l'account che supporta Data Lake Storage Gen2 dall'elenco a discesa **nome** .
   2. Selezionare **Fine** per creare la connessione. Quindi selezionare **Avanti**.

9. Nella pagina **scegliere il file o la cartella** di output immettere **copyfromopenhub** come nome della cartella di output. Quindi selezionare **Avanti**.

   ![Pagina Scegli cartella di output](media/load-sap-bw-data/choose-output-folder.png)

10. Nella pagina **impostazione formato file** selezionare **Avanti** per usare le impostazioni predefinite.

    ![Pagina specifica formato sink](media/load-sap-bw-data/specify-sink-format.png)

11. Nella pagina **Impostazioni** espandere **Impostazioni prestazioni**. Immettere un valore per il **grado di parallelismo di copia** , ad esempio 5, da caricare SAP BW in parallelo. Quindi selezionare **Avanti**.

    ![Configurare le impostazioni di copia](media/load-sap-bw-data/configure-copy-settings.png)

12. Nella pagina **Riepilogo** esaminare le impostazioni. Quindi selezionare **Avanti**.

13. Nella pagina **distribuzione** selezionare **monitoraggio** per monitorare la pipeline.

    ![Pagina Distribuzione](media/load-sap-bw-data/deployment.png)

14. Si noti che la scheda **monitoraggio** sul lato sinistro della pagina è selezionata automaticamente. Nella colonna **azioni** sono inclusi i collegamenti per visualizzare i dettagli delle attività e per eseguire di nuovo la pipeline.

    ![Visualizzazione monitoraggio pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare **Visualizza esecuzioni attività** nella colonna **azioni** . Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco.

    ![Schermata Monitoraggio attività](media/load-sap-bw-data/activity-monitoring.png)

16. Per monitorare i dettagli di esecuzione di ogni attività di copia, selezionare il collegamento **Details (dettagli** ), ovvero un'icona di occhiali sotto **Actions** (monitoraggio attività). I dettagli disponibili includono il volume di dati copiato dall'origine al sink, la velocità effettiva dei dati, le fasi di esecuzione e la durata e le configurazioni usate.

    ![Dettagli Monitoraggio attività](media/load-sap-bw-data/activity-monitoring-details.png)

17. Per visualizzare l' **ID di richiesta massimo**, tornare alla visualizzazione Monitoraggio attività e selezionare **output** in **azioni**.

    ![Schermata di output attività](media/load-sap-bw-data/activity-output.png)

    ![Visualizzazione dettagli output attività](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Copia incrementale dall'hub SAP BW aperto

> [!TIP]
> Per informazioni su come il connettore Open Hub SAP BW in Data Factory copia i dati incrementali dal SAP BW, vedere [SAP BW flusso di estrazione Delta del connettore Hub](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) . Questo articolo può essere utile anche per comprendere la configurazione di base del connettore.

A questo punto, è possibile continuare a configurare la copia incrementale da SAP BW Hub aperto.

La copia incrementale usa un meccanismo "High-Watermark" basato sull' **ID richiesta**. Tale ID viene generato automaticamente in SAP BW destinazione dell'hub aperto dal DTP. Il diagramma seguente illustra questo flusso di lavoro:

![Diagramma di flusso del flusso di lavoro di copia incrementale](media/load-sap-bw-data/incremental-copy-workflow.png)

Nella pagina data factory attività **iniziali** selezionare **crea pipeline da modello** per usare il modello predefinito.

1. Cercare **SAP BW** per trovare e selezionare la **copia incrementale da SAP BW a Azure Data Lake storage Gen2** modello. Questo modello copia i dati in Azure Data Lake Storage Gen2. È possibile utilizzare un flusso di lavoro simile per la copia in altri tipi di sink.

2. Nella pagina principale del modello selezionare o creare le tre connessioni seguenti e quindi selezionare **Usa questo modello** nell'angolo inferiore destro della finestra.

   - **Archiviazione BLOB di Azure**: in questa procedura dettagliata si usa l'archivio BLOB di Azure per archiviare il limite massimo, che è l' *ID richiesta massimo copiato*.
   - **SAP BW Hub aperto**: origine da cui copiare i dati. Per informazioni dettagliate sulla configurazione, vedere la procedura dettagliata precedente per la copia completa.
   - **Azure Data Lake storage Gen2**: si tratta del sink in cui copiare i dati. Per informazioni dettagliate sulla configurazione, vedere la procedura dettagliata precedente per la copia completa.

   ![Copia incrementale dal modello di SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Questo modello genera una pipeline con le tre attività seguenti e le rende concatenate su-Success: *Lookup*, *copia dati*e *Web*.

   Passare alla scheda **parametri** pipeline. Vengono visualizzate tutte le configurazioni che è necessario fornire.

   ![Copia incrementale dalla configurazione di SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: specificare il nome della tabella Hub aperta da cui copiare i dati.

   - **ADLSGen2SinkPath**: specificare il percorso di Azure Data Lake storage Gen2 di destinazione in cui copiare i dati. Se il percorso non esiste, l'attività di copia Data Factory crea un percorso durante l'esecuzione.

   - **HighWatermarkBlobPath**: specificare il percorso in cui archiviare il valore del limite massimo, ad esempio `container/path`.

   - **HighWatermarkBlobName**: specificare il nome del BLOB per archiviare il valore del limite massimo, ad esempio `requestIdCache.txt`. Nell'archivio BLOB passare al percorso corrispondente di HighWatermarkBlobPath + HighWatermarkBlobName, ad esempio *container/Path/requestIdCache. txt*. Creare un BLOB con il contenuto 0.

      ![Contenuto BLOB](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: in questo modello si usa webactivity per chiamare app per la logica di Azure per impostare il valore limite massimo nell'archivio BLOB. In alternativa, è possibile usare il database SQL di Azure per archiviarlo. Per aggiornare il valore, utilizzare un'attività stored procedure.

      È necessario creare prima di tutto un'app per la logica, come illustrato nella figura seguente. Incollare quindi l' **URL http post**.

      ![Configurazione dell'app per la logica](media/load-sap-bw-data/logic-app-config.png)

      1. Accedere al portale di Azure. Selezionare un nuovo servizio app per la **logica** . Selezionare **+ app per la logica vuota** per passare alla **finestra di progettazione di app**per la logica.

      2. Crea un trigger di **quando viene ricevuta una richiesta http**. Specificare il corpo della richiesta HTTP come indicato di seguito:

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

      3. Aggiungere un'azione **Crea BLOB** . Per **percorso cartella** e **nome BLOB**usare gli stessi valori configurati in precedenza in **HighWatermarkBlobPath** e **HighWatermarkBlobName**.

      4. Selezionare **Salva**. Quindi, copiare il valore dell' **URL http post** da usare nella pipeline Data Factory.

4. Dopo aver fornito i parametri della pipeline di Data Factory, selezionare **Debug** > **fine** per richiamare un'esecuzione per convalidare la configurazione. In alternativa, selezionare **pubblica tutto** per pubblicare le modifiche e quindi selezionare **trigger** per eseguire un'esecuzione.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW configurazioni di destinazione dell'hub aperto

Questa sezione introduce la configurazione del lato SAP BW per usare il connettore SAP BW Apri Hub in Data Factory per copiare i dati.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurare l'estrazione Delta in SAP BW

Se è necessaria una copia cronologica e una copia incrementale o solo una copia incrementale, configurare l'estrazione Delta in SAP BW.

1. Creare la destinazione di hub aperto. È possibile creare il OHD in SAP Transaction RSA1, che crea automaticamente la trasformazione e il processo di trasferimento dei dati necessari. Usare le seguenti impostazioni:

   - **ObjectType**: è possibile usare qualsiasi tipo di oggetto. Qui viene usato **InfoCube** come esempio.
   - **Tipo destinazione**: selezionare **tabella di database**.
   - **Chiave della tabella**: selezionare **codice tecnico**.
   - **Estrazione**: selezionare **Mantieni dati e inserire i record nella tabella**.

   ![Crea SAP BW finestra di dialogo di estrazione Delta OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Finestra di dialogo Crea SAP BW estrazione Delta2 OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   È possibile aumentare il numero di processi di lavoro SAP con esecuzione parallela per il DTP:

   ![creazione-SAP-BW-OHD-Delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Pianificare il DTP nelle catene di processi.

   Un DTP Delta per un cubo funziona solo se le righe necessarie non sono state compresse. Assicurarsi che la compressione del cubo BW non sia in esecuzione prima di DTP nella tabella Hub aperta. Il modo più semplice per eseguire questa operazione consiste nell'integrare il DTP nelle catene di processi esistenti. Nell'esempio seguente, il DTP (per OHD) viene inserito nella catena di processi tra i passaggi di *regolazione* (rollup aggregato) e *Comprimi* (compressione del cubo).

   ![Creare SAP BW diagramma di flusso a catena di processi](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurare l'estrazione completa in SAP BW

Oltre all'estrazione Delta, è possibile che si desideri eseguire un'estrazione completa della stessa SAP BW InfoProvider. Questo problema si verifica in genere se si vuole eseguire una copia completa, ma non incrementale, oppure si vuole [risincronizzare l'estrazione Delta](#resync-delta-extraction).

Non è possibile avere più di un DTP per lo stesso OHD. Quindi, è necessario creare un OHD aggiuntivo prima dell'estrazione Delta.

![Crea SAP BW OHD completo](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Per un OHD di caricamento completo, scegliere opzioni diverse rispetto a per l'estrazione Delta:

- In OHD: impostare l'opzione di **estrazione** per **eliminare i dati e inserire i record**. In caso contrario, i dati verranno estratti più volte quando si ripete il DTP in una catena di processi BW.

- In DTP: impostare la **modalità di estrazione** su **full**. È necessario modificare il DTP creato automaticamente da **Delta** a **full** immediatamente dopo la creazione del OHD, come illustrato nella figura seguente:

   ![Finestra di dialogo Crea SAP BW OHD configurata per l'estrazione completa](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Nel connettore di hub aperto BW di Data Factory: Disattiva **ultima richiesta di esclusione**. In caso contrario, non verrà estratto alcun elemento.

Il DTP completo viene in genere eseguito manualmente. In alternativa, è possibile creare una catena di processi per il DTP completo. Si tratta in genere di una catena separata indipendente dalle catene di processi esistenti. In entrambi i casi, *verificare che il DTP sia terminato prima di avviare l'estrazione usando Data Factory Copy*. In caso contrario, verranno copiati solo i dati parziali.

### <a name="run-delta-extraction-the-first-time"></a>Esegui estrazione Delta la prima volta

La prima estrazione Delta è tecnicamente un' *estrazione completa*. Per impostazione predefinita, il connettore Open Hub SAP BW esclude l'ultima richiesta quando copia i dati. Per la prima estrazione Delta, nessun dato viene estratto dall'attività di copia Data Factory fino a quando un DTP successivo non genera dati Delta nella tabella con un ID richiesta separato. Esistono due modi per evitare questo scenario:

- Disattivare l'opzione **Escludi ultima richiesta** per la prima estrazione Delta. Verificare che il primo DTP Delta sia terminato prima di avviare l'estrazione Delta la prima volta.
-  Usare la procedura per risincronizzare l'estrazione Delta, come descritto nella sezione successiva.

### <a name="resync-delta-extraction"></a>Risincronizza estrazione Delta

Gli scenari seguenti modificano i dati in SAP BW cubi, ma non sono considerati dal DTP Delta:

- SAP BW eliminazione selettiva (di righe usando qualsiasi condizione di filtro)
- Eliminazione richiesta SAP BW (di richieste non riuscite)

Una destinazione di hub aperto SAP non è una destinazione dati controllata dal Data Mart (in tutti i pacchetti di supporto SAP BW a partire da 2015). È quindi possibile eliminare i dati da un cubo senza modificare i dati in OHD. È quindi necessario risincronizzare i dati del cubo con Data Factory:

1. Eseguire un'estrazione completa in Data Factory (usando un DTP completo in SAP).
2. Eliminare tutte le righe nella tabella Hub aperta per il DTP Delta.
3. Impostare lo stato del DTP Delta su **recuperato**.

In seguito, tutte le DTPs delta successive e le estrazioni differenziali Data Factory funzionano come previsto.

Per impostare lo stato del DTP Delta su **recuperato**, è possibile usare l'opzione seguente per eseguire manualmente il DTP Delta:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul supporto di SAP BW connettore di hub aperto:

> [!div class="nextstepaction"]
>[Connettore Open Hub di SAP Business Warehouse](connector-sap-business-warehouse-open-hub.md)
