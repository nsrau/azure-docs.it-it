---
title: Caricare dati da SAP Business Warehouse usando Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: 4cd61db3ec0e8d88c9b1c6d6ba427b120b3f1af1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66152410"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copiare dati da SAP Business Warehouse usando Azure Data Factory

Questo articolo illustra come usare Azure Data Factory per copiare dati da SAP Business Warehouse (BW) tramite Hub aperto in Azure Data Lake Storage Gen2. È possibile usare una procedura simile per copiare i dati ad altri [archivi dati sink supportati](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Per informazioni generali sulla copia di dati da SAP BW, tra cui integrazione di Hub aperto di SAP BW e flusso di estrazione differenziali, vedere [copiare dati da SAP Business Warehouse tramite Hub aperto usando Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Prerequisiti

- **Azure Data Factory**: Se non si dispone di uno, seguire i passaggi necessari per [creare una data factory](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Open Hub destinazione (OHD) con tipo di destinazione "Tabella di Database"**: Per creare un OHD o per verificare che il OHD sia configurato correttamente per l'integrazione con Data Factory, vedere la [configurazioni dalla destinazione SAP BW Open Hub](#sap-bw-open-hub-destination-configurations) sezione di questo articolo.

- **L'utente di SAP BW richiede le autorizzazioni seguenti**:

  - Autorizzazione per le chiamate di funzione remota (RFC) e SAP BW.
  - Le autorizzazioni per l'attività "Esegui" del **S_SDSAUTH** oggetto autorizzazioni.

- **Oggetto [runtime di integrazione (IR) self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime) con il connettore SAP .NET 3.0**. Seguire questi passaggi di installazione:

  1. Installare e registrare il runtime di integrazione self-hosted, 3,13 o versione successiva. (Questo processo è descritto più avanti in questo articolo).

  2. Scaricare il [a 64 bit SAP Connector per Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) dal sito Web di SAP e installarlo nello stesso computer di runtime di integrazione self-hosted. Durante l'installazione, assicurarsi di selezionare **installare gli assembly alla GAC** nel **procedure di configurazione facoltative** finestra di dialogo, come illustrato nell'immagine seguente:

     ![Impostare la finestra di dialogo SAP .NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Eseguire una copia completa da SAP BW Open Hub

Nel portale di Azure, passare alla data factory. Selezionare **crea e monitora** per aprire l'interfaccia utente di Data Factory in una scheda separata.

1. Nel **attività iniziali** pagina, selezionare **copia dati** per aprire lo strumento Copia dati.

2. Nel **proprietà** , specificare un **Task name**e quindi selezionare **Avanti**.

3. Nel **archivio dati di origine** pagina, selezionare **+ Crea nuova connessione**. Selezionare **Hub aperto di SAP BW** dalla raccolta di connettore e quindi selezionare **continua**. Per filtrare i connettori, è possibile digitare **SAP** nella casella di ricerca.

4. Nel **connessione specificare SAP BW Open Hub** pagina, attenersi alla procedura seguente per creare una nuova connessione.

   ![Pagina Crea servizio collegato SAP BW Open Hub](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Dal **Connetti tramite runtime di integrazione** selezionare un runtime self-hosted esistenti In alternativa, è possibile scegliere di crearne una se non è ancora.

      Per creare un nuovo runtime di integrazione self-hosted, selezionare **+ nuovo**, quindi selezionare **self-hosted**. Immettere un **Name**, quindi selezionare **successivo**. Selezionare **installazione rapida** per installare nel computer corrente o seguire il **installazione manuale** passaggi forniti.

      Come indicato nella [prerequisiti](#prerequisites), assicurarsi di aver SAP Connector per Microsoft .NET 3.0 sia installato nello stesso computer in cui è in esecuzione il runtime di integrazione self-hosted.

   2. Immettere in SAP BW **nome Server**, **numero sistema**, **ID Client e** **lingua** (se diverso da **EN**) , **Nome utente**, e **Password**.

   3. Selezionare **Test connessione** per convalidare le impostazioni e quindi selezionare **fine**.

   4. Viene creata una nuova connessione. Selezionare **Avanti**.

5. Nel **selezionare le destinazioni di Hub aperto** pagina, selezionare le destinazioni di Hub aperto che sono disponibili nella finestra di SAP BW. Selezionare il OHD per copiare dati da e quindi selezionare **successivo**.

   ![Selezionare la tabella di destinazione Hub aperto di SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Specificare un filtro, se necessario. Se il OHD contenga soltanto i dati dall'esecuzione del processo (DTP) unico trasferimento dei dati con un ID richiesta singola oppure si è certi che il DTP viene completata e si desidera copiare i dati, deselezionare il **escludere ultima richiesta** casella di controllo.

   Altre informazioni su queste impostazioni nella finestra di [configurazioni dalla destinazione SAP BW Open Hub](#sap-bw-open-hub-destination-configurations) sezione di questo articolo. Selezionare **Validate** per controllare quali dati verranno restituiti. Quindi selezionare **Avanti**.

   ![Configurare il filtro di Hub aperto di SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Nel **archivio dati di destinazione** pagina, selezionare **+ Crea nuova connessione** > **Gen2 di archiviazione di Azure Data Lake**  >   **Continuare**.

8. Nel **connessione specificare archiviazione di Azure Data Lake** pagina, attenersi alla procedura seguente per creare una connessione.

   ![Creare una pagina del servizio collegato Azure Data Lake Store Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selezionare l'account archivio Data Lake Gen2 in grado di supportare dal **nome** elenco a discesa.
   2. Selezionare **Fine** per creare la connessione. Quindi selezionare **Avanti**.

9. Nel **scegliere il file di output o la cartella** pagina, immettere **copyfromopenhub** come nome della cartella di output. Quindi selezionare **Avanti**.

   ![Scegliere la pagina cartella di output](media/load-sap-bw-data/choose-output-folder.png)

10. Nel **impostazione del formato File** pagina, selezionare **successivo** per usare le impostazioni predefinite.

    ![Sink formato pagina specifica](media/load-sap-bw-data/specify-sink-format.png)

11. Nel **le impostazioni** , espandere **le impostazioni delle prestazioni**. Immettere un valore per **Degree of parallelism copia** , ad esempio 5 per il caricamento da SAP BW in parallelo. Quindi selezionare **Avanti**.

    ![Configurare le impostazioni di copia](media/load-sap-bw-data/configure-copy-settings.png)

12. Nel **riepilogo** verificare le impostazioni. Quindi selezionare **Avanti**.

13. Nel **Deployment** pagina, selezionare **Monitor** per monitorare la pipeline.

    ![Pagina Distribuzione](media/load-sap-bw-data/deployment.png)

14. Si noti che il **Monitor** scheda sul lato sinistro della pagina viene selezionata automaticamente. Il **azioni** colonna include i collegamenti per visualizzare i dettagli di esecuzione di attività ed eseguire di nuovo la pipeline.

    ![Visualizzazione di monitoraggio della pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Per visualizzare le esecuzioni di attività che sono associati all'esecuzione della pipeline, selezionare **Visualizza esecuzioni attività** nel **azioni** colonna. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione esecuzioni di pipeline, selezionare la **pipeline** collegamento nella parte superiore. Selezionare **Aggiorna** per aggiornare l'elenco.

    ![Schermata Monitoraggio attività](media/load-sap-bw-data/activity-monitoring.png)

16. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare la **informazioni dettagliate** collegamento, che è presente un'icona a forma di occhiali seguita **azioni** nella visualizzazione Monitoraggio attività. Dettagli disponibili includono il volume dei dati copiati dall'origine al sink, velocità effettiva dei dati, i passaggi di esecuzione e la durata e le configurazioni utilizzate.

    ![Dettagli di Monitoraggio attività](media/load-sap-bw-data/activity-monitoring-details.png)

17. Per visualizzare il **ID richiesta massimo**, tornare alla visualizzazione e selezionare il monitoraggio dell'attività **Output** sotto **azioni**.

    ![Schermata di output di attività](media/load-sap-bw-data/activity-output.png)

    ![Visualizzazione dettagli di output attività](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>Eseguire una copia incrementale da SAP BW Open Hub

> [!TIP]
> Visualizzare [flusso di Hub aperto di SAP BW connector delta estrazione](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) per informazioni su come il connettore SAP BW Open Hub in Data Factory copia incrementale dei dati da SAP BW. Questo articolo consentono inoltre di comprendere la configurazione connettore di base.

A questo punto, è possibile continuare a configurare la copia incrementale di Hub aperto di SAP BW.

Copia incrementale utilizza un meccanismo "limite massimo" che si basa sul **request ID**. Tale ID viene generato automaticamente nell'istanza di destinazione SAP BW Open Hub dal DTP. Il diagramma seguente illustra questo flusso di lavoro:

![Diagramma di flusso del flusso di lavoro di copia incrementale](media/load-sap-bw-data/incremental-copy-workflow.png)

In data factory **attività iniziali** pagina, selezionare **creare una pipeline dal modello** per usare il modello predefinito.

1. Cercare **SAP BW** per trovare e selezionare il **Incremental copiare da SAP BW in Azure Data Lake Storage Gen2** modello. Questo modello copia i dati in Azure Data Lake Storage Gen2. È possibile usare un flusso di lavoro simile per copiare in altri tipi di sink.

2. Nella pagina principale del modello, selezionare o creare le connessioni di tre seguenti e quindi selezionare **usare questo modello** nell'angolo inferiore destro della finestra.

   - **Archiviazione Blob di Azure**: In questa procedura dettagliata, utilizziamo archiviazione Blob di Azure per archiviare il limite massimo, ovvero il *max copiati ID richiesta*.
   - **SAP BW Open Hub**: Questo è l'origine per copiare i dati. Fare riferimento alla procedura dettagliata completa-copia precedente per i dettagli della configurazione.
   - **Azure Data Lake Store Gen2**: Questo è il sink per copiare i dati. Fare riferimento alla procedura dettagliata completa-copia precedente per i dettagli della configurazione.

   ![Copia incrementale dal modello di SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Questo modello genera una pipeline con le tre attività seguenti e li rende concatenate in caso di esito positivo: *Lookup*, *copiare i dati*, e *Web*.

   Passare alla pipeline **parametri** scheda. Noterete che tutte le configurazioni che è necessario fornire.

   ![Copia incrementale dalla configurazione di SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Specificare il nome della tabella per copiare dati da Hub aperto.

   - **ADLSGen2SinkPath**: Specificare il percorso di destinazione Gen2 di archiviazione di Azure Data Lake per copiare i dati. Se il percorso non esiste, l'attività di copia di Data Factory crea un tracciato durante l'esecuzione.

   - **HighWatermarkBlobPath**: Specificare il percorso per archiviare il valore di limite massimo, ad esempio `container/path`.

   - **HighWatermarkBlobName**: Specificare il nome del blob per archiviare il valore del limite massimo, ad esempio `requestIdCache.txt`. Nell'archiviazione Blob, passare al percorso corrispondente del HighWatermarkBlobPath + HighWatermarkBlobName, ad esempio *container/path/requestIdCache.txt*. Creare un blob con contenuto 0.

      ![Contenuto BLOB](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: In questo modello, utilizziamo WebActivity per chiamare le App per la logica di Azure per impostare il valore di limite massimo in un BLOB. In alternativa, è possibile usare Database SQL di Azure per archiviarle. Usare un'attività stored procedure per aggiornare il valore.

      È innanzitutto necessario creare un'app per la logica, come illustrato nell'immagine seguente. Quindi, incollare il **URL POST HTTP**.

      ![Configurazione delle App per la logica](media/load-sap-bw-data/logic-app-config.png)

      1. Accedere al portale di Azure. Selezionare un nuovo **App per la logica** servizio. Selezionare **+ App per la logica vuota** per passare alla **progettazione App per la logica**.

      2. Creazione di un trigger **When an HTTP richiesta HHTP**. Specificare il corpo della richiesta HTTP come segue:

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

      3. Aggiungere un **crea blob** azione. Per **percorso cartella** e **il nome di Blob**, usare gli stessi valori configurati in precedenza nel **HighWatermarkBlobPath** e **HighWatermarkBlobName**.

      4. Selezionare **Salva**. Quindi, copiare il valore della **URL POST HTTP** da utilizzare nella pipeline di Data Factory.

4. Dopo aver specificato i parametri della pipeline di Data Factory, selezionare **Debug** > **fine** per richiamare un'esecuzione per convalidare la configurazione. In alternativa, selezionare **pubblica tutti** per pubblicare le modifiche e quindi selezionare **Trigger** avviare un'esecuzione.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurazioni di destinazione Hub aperto di SAP BW

Questa sezione illustra una configurazione del lato SAP BW per usare il connettore SAP BW Open Hub in Data Factory per copiare i dati.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurare estrazione delta in SAP BW

Se è necessario sia copia cronologico e copia incrementale o solo la copia incrementale, configurare estrazione delta in SAP BW.

1. Creare la destinazione Hub aperto. È possibile creare il OHD in SAP transazione RSA1, che crea automaticamente il processo di trasferimento dei dati e trasformazione necessarie. Usare le seguenti impostazioni:

   - **ObjectType**: È possibile usare qualsiasi tipo di oggetto. In questo caso, usiamo **InfoCube** come esempio.
   - **Tipo di destinazione**: Selezionare **tabella di Database**.
   - **Chiave della tabella**: Selezionare **tecniche chiave**.
   - **Estrazione**: Selezionare **mantenere i dati e inserire i record nella tabella**.

   ![Creare la finestra di dialogo di SAP BW OHD delta estrazione](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Creare la finestra di dialogo di SAP BW OHD delta2 estrazione](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   È possibile aumentare il numero di parallelo in esecuzione processi di lavoro SAP per il DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Pianificare il DTP nelle catene di processi.

   Un delta DTP per un cubo funziona solo se le righe necessarie non sono stati compressi. Assicurarsi che la compressione di cubo BW non è in esecuzione prima il DTP alla tabella di Hub aperto. Il modo più semplice per eseguire questa operazione è per integrare il DTP le catene di processi esistenti. Nell'esempio seguente, il DTP (per OHD) viene inserito la catena di processi tra il *Adjust* (rollup dell'aggregato) e *Collapse* passaggi (compressione cubo).

   ![Creare diagrammi di flusso catena processo SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurare l'estrazione full in SAP BW

Oltre all'estrazione delta, è possibile un'estrazione completa delle stesse InfoProvider BW SAP. Ciò vale in genere se si desidera copia completa, ma non incrementale, o si desidera [risincronizzare l'estrazione delta](#resync-delta-extraction).

Per la stessa OHD non è possibile avere più DTP. Pertanto, è necessario creare un OHD aggiuntivi prima dell'estrazione delta.

![Creazione di SAP BW OHD completo](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Per un caricamento completo OHD, scegliere opzioni diverse da per l'estrazione dei delta:

- In OHD: Impostare il **estrazione** possibilità **eliminare i dati e inserire i record**. In caso contrario, verranno estratti i dati più volte quando si ripete il DTP in una catena di processi BW.

- Nel DTP: Impostare **modalità di estrazione** al **completo**. È necessario modificare il DTP creato automaticamente da **Delta** al **completo** immediatamente dopo aver creato il OHD, come la seguente immagine illustra:

   ![Creazione di SAP BW OHD configurato per l'estrazione di "Completo" finestra di dialogo](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Nel connettore di Hub aperto BW della Data Factory: Disattivare **Exclude ultima richiesta**. In caso contrario, nulla verrà estratto.

È in genere eseguire manualmente il DTP completo. In alternativa, è possibile creare una catena di processi per il DTP completo. È in genere una catena separata che è indipendente dalle catene di processi esistenti. In entrambi i casi *assicurarsi che il DTP è stato completato prima di avviare l'estrazione usando Data Factory copia*. In caso contrario, verranno copiati solo i dati parziali.

### <a name="run-delta-extraction-the-first-time"></a>Eseguire l'estrazione delta la prima volta

Il primo estrazione delta è tecnicamente una *completo estrazione*. Per impostazione predefinita, il connettore SAP BW Open Hub esclude l'ultima richiesta quando copia i dati. Per l'estrazione delta prima, non i dati vengono estratti dall'attività di Data Factory copia fino a quando un successivo DTP genera dati differenziali nella tabella con un ID richiesta separata. Esistono due modi per evitare questo scenario:

- Disattivare la **Exclude ultima richiesta** opzione per l'estrazione delta prima. Assicurarsi che il primo delta DTP è stato completato prima di avviare l'estrazione delta la prima volta.
-  Utilizzare la procedura per la risincronizzazione l'estrazione delta, come descritto nella sezione successiva.

### <a name="resync-delta-extraction"></a>Risincronizzare l'estrazione delta

Gli scenari seguenti modificano i dati nei cubi di SAP BW, ma non vengono considerati dal delta DTP:

- Eliminazione selettiva di SAP BW (delle righe usando qualsiasi condizione di filtro)
- Eliminazione di richiesta di SAP BW (delle richieste non corrette)

Una destinazione Hub aperto SAP non è una destinazione dati regolamentati dal data mart (in tutti i pacchetti di supporto SAP BW poiché 2015). Pertanto, è possibile eliminare i dati da un cubo senza modificare i dati nel OHD. È quindi necessario risincronizzare i dati del cubo con Data Factory:

1. Eseguire un'estrazione completa in Data Factory (tramite un DTP completo in SAP).
2. Eliminare tutte le righe nella tabella di Hub aperto per il delta DTP.
3. Impostare lo stato del delta DTP al **recuperate**.

Al termine, tutti i successivi delta DTPs e le estrazioni delta di Data Factory funzionano come previsto.

Per impostare lo stato del delta DTP al **recuperate**, è possibile usare l'opzione seguente per eseguire manualmente il DTP delta:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul supporto di connettore di Hub aperto di SAP BW:

> [!div class="nextstepaction"]
>[Connettore SAP Business Warehouse Open Hub](connector-sap-business-warehouse-open-hub.md)
