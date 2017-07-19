---
title: Caricare terabyte di dati in SQL Data Warehouse | Documentazione Microsoft
description: Illustra come caricare 1 TB di dati in Azure SQL Data Warehouse in meno di 15 minuti con Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: c1485205f49dae28adbddbf679fc120a6e52bff6
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017


---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Caricare 1 TB di dati in Azure SQL Data Warehouse in meno di 15 minuti con Data Factory
[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) è un database basato su cloud, con possibilità di aumentare il numero di istanze, che può elaborare volumi massivi di dati relazionali e non relazionali.  Basato sull'architettura di elaborazione parallela massiva (MPP, Massively Parallel Processing), SQL Data Warehouse è ottimizzato per i carichi di lavoro dei data warehouse dell'organizzazione.  Offre l'elasticità del cloud con la flessibilità per ridimensionare la capacità di archiviazione e di calcolo in modo indipendente.

L'uso di Azure SQL Data Warehouse è ora più semplice dell'uso di **Azure Data Factory**.  Azure Data Factory è un servizio di integrazione di dati basato su cloud completamente gestito, che può essere usato per popolare un'istanza di SQL Data Warehouse con i dati del sistema esistente e che consente di risparmiare tempo prezioso durante la valutazione di SQL Data Warehouse e la creazione di soluzioni di analisi. Di seguito sono elencati i vantaggi principali del caricamento di dati in Azure SQL Data Warehouse mediante Azure Data Factory:

* **Semplicità di configurazione**: procedura guidata intuitiva in 5 passaggi, senza necessità di script.
* **Supporto completo per archivi dati**: supporto integrato per una vasta gamma di archivi dati locali e basati su cloud.
* **Sicurezza e conformità**: i dati vengono trasferiti tramite HTTPS o ExpressRoute e la presenza di un servizio globale garantisce che i dati non superino mai il confine geografico.
* **Prestazioni ineguagliabili tramite Polybase**: Polybase rappresenta il modo più efficiente per spostare dati in Azure SQL Data Warehouse. Mediante la funzionalità di gestione temporanea dei BLOB è possibile ottenere velocità di carico elevate da tutti i tipi di archivi dati oltre all'archivio BLOB di Azure, supportato da Polybase per impostazione predefinita.

Questo articolo illustra come usare la Copia guidata di Data Factory per caricare 1 TB di dati da Archiviazione BLOB di Azure ad Azure SQL Data Warehouse in meno di 15 minuti a una velocità effettiva di oltre 1,2 GBps.

Questo articolo include istruzioni dettagliate per spostare dati in Azure SQL Data Warehouse tramite la Copia guidata.

> [!NOTE]
>  Per informazioni generali sulle funzionalità di Data Factory per lo spostamento di dati da e verso Azure SQL Data Warehouse, vedere [Spostare dati da e verso Azure SQL Data Warehouse mediante Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md).
>
> È anche possibile creare pipeline usando il portale di Azure, Visual Studio, PowerShell e così via. Per una procedura con istruzioni dettagliate sull'uso dell'attività di copia in Azure Data Factory, vedere l'esercitazione [Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .  
>
>

## <a name="prerequisites"></a>Prerequisiti
* Archivio BLOB di Azure: questo esperimento usa l'archivio BLOB di Azure (GRS) per l'archiviazione di set di dati di test TPC-H.  Se non si ha un account di archiviazione di Azure, vedere l'articolo relativo alla [creazione di un account di archiviazione](../storage/storage-create-storage-account.md#create-a-storage-account).
* Dati [TPC-H](http://www.tpc.org/tpch/): si userà TPC-H come set di dati di test.  A tale scopo, è necessario usare `dbgen` dal toolkit TPC-H, che consente di generare il set di dati.  È possibile scaricare il codice sorgente per `dbgen` dagli [strumenti TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) e compilarlo autonomamente oppure scaricare il file binario compilato da [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Eseguire dbgen.exe con i comandi seguenti per generare un file flat da 1 TB per la tabella `lineitem` distribuito tra 10 file:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Copiare ora i file generati nel BLOB di Azure.  Per informazioni su come eseguire questa operazione usando ADF Copy, vedere [Spostare i dati da e nel file system locale usando Azure Data Factory](data-factory-onprem-file-system-connector.md).    
* Azure SQL Data Warehouse: in questo esperimento i dati vengono caricati nell'istanza di Azure SQL Data Warehouse creata con 6000 DWU (Data Warehouse Units)

    Per informazioni su come creare un database di SQL Data Warehouse, vedere [Create an Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) (Creare un'istanza di Azure SQL Data Warehouse).  Per ottenere le migliori prestazioni di caricamento possibili in SQL Data Warehouse usando Polybase, si è scelto il numero massimo di DWU consentito nell'impostazione delle prestazioni, ovvero 6000 DWU.

  > [!NOTE]
  > Durante il caricamento dal BLOB di Azure, le prestazioni di caricamento dei dati sono direttamente proporzionali al numero di DWU configurate in SQL Data Warehouse:
  >
  > Il caricamento di 1 TB in un'istanza di SQL Data Warehouse con 1.000 DWU richiede 87 minuti (alla velocità effettiva di circa 200 MBps) Il caricamento di 1 TB in un'istanza di SQL Data Warehouse con 2.000 DWU richiede 46 minuti (alla velocità effettiva di circa 380 MBps) Il caricamento di 1 TB in un'istanza di SQL Data Warehouse con 6.000 DWU richiede 14 minuti (alla velocità effettiva di circa 1,2 GBps)
  >
  >

    Per creare un'istanza di SQL Data Warehouse con 6000 DWU, spostare il dispositivo di scorrimento delle prestazioni verso destra:

    ![Dispositivo di scorrimento delle prestazioni](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Se un database esistente non è configurato con 6000 DWU, è possibile aumentarne la capacità tramite il portale di Azure.  Passare al database nel portale di Azure e individuare il pulsante **Ridimensiona** nel pannello **Panoramica** illustrato nella figura seguente:

    ![Pulsante Ridimensiona](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Fare clic sul pulsante **Ridimensiona** per aprire il pannello seguente, spostare il dispositivo di scorrimento fino al valore massimo e fare clic sul pulsante **Salva**.

    ![Finestra di dialogo Ridimensiona](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    In questo esperimento i dati vengono caricati in Azure SQL Data Warehouse mediante la classe di risorse `xlargerc`.

    Per ottenere la velocità effettiva migliore possibile, è necessario eseguire la copia tramite un utente di SQL Data Warehouse appartenente alla classe di risorse `xlargerc`.  Per eseguire questa operazione, seguire la procedura descritta in [Esempio di modifica della classe di risorse di un utente](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example).  
* Creare lo schema della tabella di destinazione nel database di Azure SQL Data Warehouse eseguendo l'istruzione DDL seguente:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
Dopo aver completato i passaggi preliminari necessari, è ora possibile configurare l'attività di copia mediante la Copia guidata.

## <a name="launch-copy-wizard"></a>Avviare la Copia guidata
1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **+ NUOVO** nell'angolo in alto a sinistra e quindi su **Intelligence e analisi** e su **Data Factory**.
3. Nel pannello **Nuova data factory** :

   1. Immettere **LoadIntoSQLDWDataFactory** come **nome**.
       È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato un errore simile a **Nome "LoadIntoSQLDWDataFactory" per la data factory non disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteLoadIntoSQLDWDataFactory) e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md) .  
   2. Selezionare la **sottoscrizione**di Azure.
   3. In Gruppo di risorse eseguire una di queste operazioni:
      1. Selezionare **Usa esistente** per scegliere un gruppo di risorse esistente.
      2. Selezionare **Crea nuovo** per immettere un nome per un gruppo di risorse.
   4. Selezionare una **località** per la data factory.
   5. Selezionare la casella di controllo **Aggiungi al dashboard** nella parte inferiore del pannello.  
   6. Fare clic su **Crea**.
4. Al termine della creazione verrà visualizzato il pannello **Data factory**, come illustrato nell'immagine seguente:

   ![Home page di Data factory](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Nella home page di Data Factory fare clic sul riquadro **Copia dati** per avviare la **Copy Wizard** (Copia guidata).

   > [!NOTE]
   > Se il Web browser è bloccato su "Concessione autorizzazioni in corso...", disabilitare/deselezionare l'impostazione **Block third party cookies and site data** (Blocca cookie e dati del sito di terze parti) oppure lasciarla abilitata e creare un'eccezione per **login.microsoftonline.com** e quindi provare di nuovo ad avviare la procedura guidata.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Passaggio 1: Configurare una pianificazione di caricamento dei dati
Il primo passaggio consiste nel configurare la pianificazione di caricamento dei dati.  

Nella pagina **Proprietà** :

1. Immettere **CopyFromBlobToAzureSqlDataWarehouse** per **Nome attività**
2. Selezionare l'opzione **Esegui una volta**.   
3. Fare clic su **Avanti**.  

    ![Copia guidata: pagina delle proprietà](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Passaggio 2: Configurare l'origine
Questa sezione illustra i passaggi per configurare l'origine: BLOB di Azure contenente i file delle voci TPC-H da 1 TB.

1. Selezionare **Archivio BLOB di Azure** come archivio dati e fare clic su **Avanti**.

    ![Copia guidata: selezionare la pagina di origine](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Immettere le informazioni di connessione per l'account di archiviazione BLOB di Azure e fare clic su **Avanti**.

    ![Copia guidata: informazioni sulla connessione di origine](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Scegliere la **cartella** contenente i file della voce TPC-H e fare clic su **Avanti**.

    ![Copia guidata: selezionare la cartella di input](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Facendo clic su **Avanti** le impostazioni del formato di file vengono rilevate automaticamente.  Verificare che il delimitatore di colonna sia "|" anziché la virgola predefinita ",".  Dopo aver visualizzato l'anteprima dei dati fare clic su **Avanti**.

    ![Copia guidata: impostazioni di formattazione file](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Passaggio 3: Configurare la destinazione
Questa sezione illustra come configurare la destinazione: tabella `lineitem` nel database di Azure SQL Data Warehouse.

1. Selezionare **Azure SQL Data Warehouse** come archivio di destinazione e fare clic su **Avanti**.

    ![Copia guidata: selezionare l'archivio dati di destinazione](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Immettere le informazioni di connessione per Azure SQL Data Warehouse.  Assicurarsi di specificare l'utente membro del ruolo `xlargerc` (per informazioni dettagliate, vedere la sezione **Prerequisiti**), quindi fare clic su **Avanti**.

    ![Copia guidata: informazioni sulla connessione di destinazione](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Scegliere la tabella di destinazione e fare clic su **Avanti**.

    ![Copia guidata: pagina del mapping della tabella](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Nella pagina Mapping dello schema, lasciare deselezionata l'opzione "Apply column mapping" (Applica mapping di colonne) e fare clic su **Avanti**.

## <a name="step-4-performance-settings"></a>Passaggio 4: Impostazioni relative alle prestazioni

L'opzione **Allow polybase** (Consenti Polybase) è selezionata per impostazione predefinita.  Fare clic su **Avanti**.

![Copia guidata: pagina del mapping dello schema](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Passaggio 5: Distribuire e monitorare i risultati del caricamento
1. Fare clic sul pulsante **Fine** per eseguire la distribuzione.

    ![Copia guidata: pagina di riepilogo](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Al termine della distribuzione, fare clic su `Click here to monitor copy pipeline` per monitorare l'avanzamento dell'esecuzione della copia. Scegliere la pipeline di copia creata nell'elenco **Activity Windows** (Finestre attività).

    ![Copia guidata: pagina di riepilogo](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    È possibile visualizzare i dettagli relativi all'esecuzione della copia in **Activity Window Explorer** (Esplora attività) nel riquadro di destra, inclusi il volume dei dati letti dall'origine e scritti nella destinazione, la durata e la velocità effettiva media dell'esecuzione.

    Come si può notare dalla schermata seguente, la copia di 1 TB dall'archivio BLOB di Azure a SQL Data Warehouse ha richiesto 14 minuti, raggiungendo di fatto la velocità effettiva di 1,22 Gbps.

    ![Copia guidata: finestra di dialogo operazione completata](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Procedure consigliate
Di seguito sono elencate alcune procedure consigliate per l'esecuzione del database di Azure SQL Data Warehouse:

* Usare una classe di risorse di dimensioni maggiori durante il caricamento in un INDICE COLUMNSTORE CLUSTER.
* Per join più efficienti, è consigliabile usare la distribuzione hash da una colonna di selezione anziché la distribuzione round robin predefinita.
* Per una maggiore velocità di carico, è consigliabile usare un heap per i dati temporanei.
* Creare statistiche al termine del caricamento di Azure SQL Data Warehouse.

Per informazioni dettagliate, vedere [Procedure consigliate per Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi
* [Data Factory Copy Wizard](data-factory-copy-wizard.md) (Copia guidata di Data Factory): questo articolo include informazioni dettagliate sulla Copia guidata.
* [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md): questo articolo include la guida all'ottimizzazione e alle misurazioni delle prestazioni di riferimento.

