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
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: 63ce3587a2c48e7e03503e50a548ed372f511e37
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082059"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Caricare dati da SAP Business Warehouse (BW) usando Azure Data Factory

Questo articolo illustra una procedura dettagliata su come usare Data Factory _caricare i dati da SAP Business Warehouse (BW) tramite Hub aperto in Azure Data Lake Storage Gen2_. È possibile seguire la procedura per copiare dati da altri [archivi dati sink supportati](copy-activity-overview.md#supported-data-stores-and-formats). 

> [!TIP]
> Fare riferimento a [articolo sul connettore SAP BW Open Hub](connector-sap-business-warehouse-open-hub.md) nella copia dei dati da SAP BW in generale, tra cui introduzione sul flusso di estrazione delta e integrazione di Hub aperto di SAP BW.

## <a name="prerequisites"></a>Prerequisiti

- **Azure Data Factory (ADF):** Se non si dispone di una data factory, seguire la "[creare una data factory](quickstart-create-data-factory-portal.md#create-a-data-factory)" sezione per crearne uno. 

- **SAP BW Open Hub destinazione (OHD) con tipo di destinazione "Tabella di Database".** Seguire [configurazioni dalla destinazione SAP BW Open Hub](#sap-bw-open-hub-destination-configurations) sezione per creare uno o per verificare se il OHD esistente è configurato correttamente per essere integrate con Azure Data factory.

- **Utente di SAP BW in uso deve avere le autorizzazioni seguenti:**

  - Autorizzazione per RFC e SAP BW.
  - Le autorizzazioni per il "**Execute**"Attività dell'oggetto di autorizzazione"**S_SDSAUTH**".

- **[Self-hosting Runtime di integrazione](concepts-integration-runtime.md#self-hosted-integration-runtime) con il connettore SAP .NET 3.0 sono necessari**. Di seguito sono le operazioni preliminari dettagliate che devono essere eseguite:

  1. Installare e registrare il runtime di integrazione Self-Hosted con versione > = 3.13 (descritto nella procedura riportata di seguito). 

  2. Scaricare il [a 64 bit SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) dal sito Web di SAP e installarlo nel computer di runtime di integrazione Self-Hosted.  Quando l'installazione, nella finestra "passaggi di configurazione facoltative". Assicurarsi di selezionare il "**installare gli assembly alla GAC**" opzione come illustrato nell'immagine seguente.

     ![Configurare SAP .NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>Copia completa di Hub aperto di SAP BW

Nel portale di Azure, passare alla data factory -> selezionare **crea e monitora** per avviare le UI ADF in una scheda separata. 

1. Nella pagina **Attività iniziali** selezionare **Copia dati** per avviare lo strumento Copia dati. 

2. Nel **proprietà** , specificare un nome per il **Task name** campo e selezionare **successivo**.

3. Nel **archivio dati di origine** pagina, fare clic su **+ Crea nuova connessione** -> selezionare **SAP BW Open Hub** dalla raccolta connettore -> selezionare **continua**. È possibile digitare "SAP" nella casella di ricerca per filtrare i connettori.

4. Nel **connessione specificare SAP BW Open Hub** pagina 

   ![Creare il servizio SAP BW Open Hub collegato](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Scegliere il **Connetti tramite Runtime di integrazione**: fare clic sull'elenco a discesa per selezionare un runtime di integrazione Self-Hosted esistente o crearne uno se non si dispone di runtime di integrazione Self-Hosted ancora configurato. 

      Per creare nuovi, fare clic su **+ nuovo** nell'elenco a discesa-> selezionare il tipo **self-hosted** -> specificare una **Name** e fare clic su **Avanti** -> scegliere **Installazione rapida** per installare nel computer corrente o seguire il **installazione manuale** passaggi non esiste.

      Come indicato nella [prerequisiti](#prerequisites), assicurarsi di avere anche le **connettore SAP .NET 3.0** installato nello stesso computer in cui è in esecuzione runtime di integrazione Self-Hosted.

   2. Specifica di SAP BW **nome Server**, **numero sistema**, **ID Client e** **lingua** (se diverso da EN), **nome utente**, e **Password**.

   3. Fare clic su **Connessione di test** per convalidare le impostazioni, quindi selezionare **Fine**.

   4. Si noterà che viene creata una nuova connessione. Selezionare **Avanti**.

5. Nel **destinazioni selezionare Hub aperto** pagina, selezionare le destinazioni di Hub aperto disponibili nella finestra di SAP BW e selezionare quello desiderato per copiare i dati, quindi fare clic su **successivo**.

   ![Selezionare la tabella di Hub aperto di SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Se necessario, specificare il filtro. Se la destinazione Hub aperto contiene solo i dati da una singola esecuzione del processo di trasferimento dei dati (DTP) con ID richiesta singola o si è certi che ha terminato il DTP e si vuole copiare tutti i dati, deselezionare il **escludere ultima richiesta**. È possibile ottenere informazioni di più sul modo in cui queste impostazioni si riferiscono alla propria configurazione di SAP BW in [configurazioni dalla destinazione SAP BW Open Hub](#sap-bw-open-hub-destination-configurations) sezione. Fare clic su **Validate** per controllare i dati restituiti, quindi selezionare **successivo**.

   ![Configurare il filtro di Hub aperto di SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Nel **archivio dati di destinazione** pagina, fare clic su **+ Crea nuova connessione**e quindi selezionare **Gen2 di archiviazione di Azure Data Lake**e selezionare **continua**.

8. Nel **connessione specificare archiviazione di Azure Data Lake** pagina 

   ![Creare il servizio Azure Data Lake Store Gen2 collegato](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selezionare l'account con supporto di Data Lake Storage Gen2 nell'elenco a discesa "Nome account di archiviazione".
   2. Selezionare **Fine** per creare la connessione. Quindi selezionare **Avanti**.

9. Nel **scegliere il file di output o la cartella** pagina, immettere "copyfromopenhub" come nome della cartella di output e selezionare **successivo**.

   ![Scegliere la cartella di output](media/load-sap-bw-data/choose-output-folder.png)

10. Nel **impostazione del formato File** pagina, selezionare **successivo** per usare le impostazioni predefinite.

    ![Specificare il formato di sink](media/load-sap-bw-data/specify-sink-format.png)

11. Nel **le impostazioni** , espandere il **le impostazioni delle prestazioni**e impostare **Degree of parallelism copia** , ad esempio 5 per caricare da SAP BW in parallelo. Fare clic su **Avanti**.

    ![Configurare le impostazioni di copia](media/load-sap-bw-data/configure-copy-settings.png)

12. Nel **Summary** pagina, esaminare le impostazioni e selezionare **successivo**.

13. Nel **Deployment** pagina, selezionare **Monitor** per monitorare la pipeline.

    ![Pagina Distribuzione](media/load-sap-bw-data/deployment.png)

14. Si noti che la scheda **Monitoraggio** a sinistra è selezionata automaticamente. La colonna **Azioni** contiene collegamenti per visualizzare i dettagli delle esecuzioni dell'attività ed eseguire di nuovo la pipeline:

    ![Monitoraggio delle pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. Dato che la pipeline contiene una sola attività (attività di copia), viene visualizzata una sola voce. Per tornare alla visualizzazione delle esecuzioni di pipeline, selezionare il collegamento **Pipeline** in alto. Selezionare **Aggiorna** per aggiornare l'elenco.

    ![Monitoraggio attività](media/load-sap-bw-data/activity-monitoring.png)

16. Per monitorare i dettagli di esecuzione per ogni attività di copia, selezionare il collegamento **Dettagli** (immagine degli occhiali) in **Azioni** nella visualizzazione di monitoraggio delle attività. È possibile monitorare dettagli come il volume dei dati copiati dall'origine al sink, la velocità effettiva dei dati, i passaggi di esecuzione con la durata corrispondente e le configurazioni usate:

    ![Dettagli di Monitoraggio attività](media/load-sap-bw-data/activity-monitoring-details.png)

17. Rivedere le **ID richiesta massimo** che viene copiato. Tornare alla visualizzazione Monitoraggio attività, scegliere il **Output** sotto **azioni**.

    ![Activity Output](media/load-sap-bw-data/activity-output.png)

    ![Dettagli di output dell'attività](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Copia incrementale di Hub aperto di SAP BW

> [!TIP]
> 
> Fare riferimento a [flusso di Hub aperto di SAP BW connector delta estrazione](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) per altre informazioni sul funzionamento delle attività di copia di Azure Data factory per copiare i dati incrementali da SAP BW.

A questo punto, è possibile continuare a configurare la copia incrementale di Hub aperto di SAP BW. 

La copia incrementale utilizza il meccanismo di limite massimo basato su richiesta ID generato automaticamente nell'istanza di destinazione SAP BW Open Hub da DTP. Il flusso di lavoro per questo approccio è raffigurato nel diagramma seguente:

![Flusso di lavoro di copia incrementale](media/load-sap-bw-data/incremental-copy-workflow.png)

L'UI di ADF **attività iniziali** pagina, selezionare **creare una pipeline**. 

1. Trascinare tre attività - **Web, copiare i dati e ricerca** : nell'area di disegno e apportare tali concatenate in caso di esito positivo. In questa procedura dettagliata, si userà il Blob di Azure per archiviare il limite massimo: ID richiesta copiato max. È anche possibile usare il database SQL per archiviarli e usare l'attività Stored Procedure anziché attività Web per aggiornarlo.

   ![Pipeline di copia incrementale](media/load-sap-bw-data/incremental-copy-pipeline.png)

2. Configurare attività di ricerca:

   1. Nell'attività di ricerca **le impostazioni** scheda, check **prima riga solo** opzione.

      ![Impostazioni di ricerca](media/load-sap-bw-data/lookup-settings.png)

   2. Configurare **set di dati di origine** come set di dati Blob, nelle **percorso File**punti al blob in cui si desidera archiviare l'ID richiesta copiato max come limite massimo e mantenere il formato come formato di testo.

      ![Impostazioni di set di dati BLOB](media/load-sap-bw-data/blob-dataset.png)

   3. Nel percorso del blob corrispondenti, creare un blob con contenuto 0.

      ![Contenuto BLOB](media/load-sap-bw-data/blob.png)

3. Configurare attività di copia: 

   1. Nelle **origine** scheda, configurare **set di dati di origine** come set di dati SAP BW Open Hub. 

   2. Modificare il **set di dati SAP BW Open Hub**:

      1. Nelle **parametri** scheda, impostare un parametro denominato "ID richiesta"
      2. Nelle **Connection** scheda, specificare un nome di tabella di Hub aperto, mantenere "Exclude ultimo ID richiesta" selezionata e impostare l'ID richiesta Base usare espressione (Aggiungi contenuto dinamico) `@dataset().requestId`.

   3. Tornare all'attività di copia **origine** scheda, configurare il valore "ID richiesta" per l'utilizzo di espressioni (Aggiungi contenuto dinamico) `@{activity('<look up activity name>').output.firstRow.Prop_0}`. Modificare di conseguenza la "per cercare il nome di attività" in questa espressione.

      ![Copiare le impostazioni di origine](media/load-sap-bw-data/copy-source.png)

4. Configurare l'attività Web: l'attività Web chiamerà App per la logica per archiviare ID massima della richiesta copiati nel blob.

   1. Vai al portale di Azure -> nuovo un **App per la logica** con uno **richiesta HTTP** e uno **crea blob** come indicato di seguito. Usare lo stesso file di blob configurato nell'origine dell'attività ricerca precedente. E copiare il **URL POST HTTP** che verrà usato nell'attività di web.

      ![Configurazione dell'App per la logica](media/load-sap-bw-data/logic-app-config.png)

   2. Tornare indietro per modificare il file ADF **attività Web** impostazioni come indicato di seguito. Configurare il corpo di espressione (Aggiungi contenuto dinamico) `{"sapOpenHubMaxRequestId":"@{activity('CopyFromSap').output.sapOpenHubMaxRequestId}"}`.

      ![Impostazioni di attività Web](media/load-sap-bw-data/web-activity-settings.png)

5. È possibile fare clic su **Debug** per convalidare la configurazione o selezionare **pubblica tutti** pubblicare tutte le modifiche, quindi fare clic su **Trigger** avviare un'esecuzione.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configurazioni di destinazione Hub aperto di SAP BW

Questa sezione illustra la configurazione necessaria sul lato SAP BW per poter usare il connettore SAP BW Open Hub in Azure Data factory per copiare i dati.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configurare estrazione delta in SAP BW

Se è necessario copiare cronologici e copia incrementale o solo la copia incrementale, configurare estrazione delta in SAP BW.

1. Creare la destinazione Hub aperto (OHD)

   È possibile creare il OHD in SAP transazione RSA1. Questo crea automaticamente la trasformazione necessarie e un processo di trasferimento dei dati (DTP). Usare le seguenti impostazioni:

   - Tipo di oggetto può essere qualsiasi. Qui usiamo InfoCube come esempio.
   - **Tipo di destinazione:** *Tabella di database*
   - **Chiave della tabella:** *Chiave tecnici*
   - **Estrazione:** *Mantenere i dati e inserire i record nella tabella*

   ![Creare l'estrazione di SAP BW OHD delta](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   È possibile aumentare il numero di parallelo in esecuzione processi di lavoro SAP per il DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Pianificare il DTP nelle catene di processi

   Un DTP Delta per un cubo funziona solo quando le necessarie righe non compressi ancora. Pertanto, è necessario assicurarsi che la compressione di cubo BW non è in esecuzione prima il DTP alla tabella di Hub aperto. Il modo più semplice per questo è l'integrazione di questo DTP nelle catene di processi esistenti. Nell'esempio seguente, il DTP (per OHD) viene inserito nella catena di processi tra il passaggio Adjust (Rollup dell'aggregato) e compressione (compressione cubo).

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configurare l'estrazione full in SAP BW

Oltre all'estrazione delta, è consigliabile avere un'estrazione completa del InfoProvider stesso. Ciò vale in genere se si desidera eseguire tutte le attività Copia senza necessità incrementale o si desidera [risincronizzare estrazione delta](#re-sync-delta-extraction).

Per la stessa OHD non deve essere più DTP. Pertanto, è necessario creare un OHD aggiuntive rispetto a estrazione delta.

![create-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Per un caricamento completo OHD, scegliere diverse opzioni di estrazione delta:

- In OHD: impostare l'opzione "Extraction" come "*Elimina dati di e inserire i record*". In caso contrario, verrebbero estratti i dati più volte quando ripetuto il DTP in una catena di processi BW.

- In DTP: impostare "Modalità di estrazione" come "*completo*". È necessario modificare il DTP creato automaticamente da Delta full solo dopo aver creato il OHD:

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Nel connettore di Hub aperto di Azure Data factory SAP BW: disattivare l'opzione "*Exclude ultima richiesta*". In caso contrario non sarebbe da estrarre. 

È in genere eseguire manualmente il DTP completo. O è anche possibile creare una catena di processi per il DTP completo - si tratterà in genere una catena di processi separata indipendente dalle catene di processi esistenti. In entrambi i casi, è necessario eseguire **assicurarsi che il DTP ha terminato prima di avviare l'estrazione usando ADF copy**, in caso contrario, verranno copiati solo dati parziali.

### <a name="run-delta-extraction-the-first-time"></a>Eseguire l'estrazione delta la prima volta

Il primo estrazione Delta è tecnicamente una **estrazione Full**. Nota dal connettore di Hub aperto di Azure Data factory SAP BW predefinita esclude l'ultima richiesta quando si copiano i dati. Nel caso di estrazione del delta per la prima volta, nell'attività di copia di Azure Data factory, sarà estratto Nessun dato fino a quando non c'è successive DTP genera i dati differenziali nella tabella con ID richiesta separato. Mentre, esistono due modi possibili per evitare questo scenario:

1. Disattivare l'opzione "Escludi ultima richiesta" per il primo Delta estrazione In questo caso che è necessario assicurarsi che il primo DTP Delta è stato completato prima di avviare l'estrazione Delta la prima volta
2. Utilizzare la procedura per eseguire nuovamente la sincronizzazione dell'estrazione differenziali come descritto di seguito.

### <a name="re-sync-delta-extraction"></a>Risincronizzare estrazione delta

Esistono alcuni scenari che modificano i dati in SAP BW cubi, ma non vengono considerati dal DTP Delta:

- Eliminazione selettiva di SAP BW (delle righe usando qualsiasi condizione di filtro)
- SAP BW richiesta di eliminazione (della richiesta non corretto)

Una destinazione Hub aperto SAP non è una destinazione dati regolamentati dal data mart (in tutti i SAP BW pacchetti di supporto rispetto all'anno 2015). Pertanto, è possibile eliminare i dati da un cubo senza modificare i dati nel OHD. In questo caso, è necessario risincronizzare i dati del cubo con i dati in Azure Data factory attenendosi alla procedura seguente:

1. Eseguire un'estrazione completo in Azure Data Factory (tramite un DTP completo in SAP)
2. Eliminare tutte le righe della tabella di Hub aperto per il DTP Delta
3. Impostare lo stato del DTP Delta su recuperate

Successivamente, tutte le successive DTPs Delta e le estrazioni Delta ADF funzionare come previsto.

È possibile impostare lo stato del DTP Delta a recuperate eseguendo il DTP Delta manualmente usando l'opzione seguente: "*Alcun trasferimento di dati. Stato delta nell'origine: Recuperate*".

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di connettore di Hub aperto di SAP BW: 

> [!div class="nextstepaction"]
>[Connettore SAP Business Warehouse Open Hub](connector-sap-business-warehouse-open-hub.md)
