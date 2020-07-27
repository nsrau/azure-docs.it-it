---
title: Usare endpoint privati per creare una pipeline di Azure Data Factory
description: Questa esercitazione offre istruzioni dettagliate per l'uso del portale di Azure per creare una data factory con una pipeline. La pipeline usa l'attività di copia per copiare i dati da un archivio BLOB di Azure a un database SQL di Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540529"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Copiare dati dall'archiviazione BLOB di Azure a un database SQL in sicurezza usando endpoint privati

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione viene creata una data factory con l'interfaccia utente di Azure Data Factory. **La pipeline in questa data factory copia i dati in sicurezza dall'archiviazione BLOB di Azure a un database SQL di Azure, consentendo l'accesso solo alle reti selezionate, usando endpoint privati nella [rete virtuale gestita di Azure Data Factory](managed-virtual-network-private-endpoint.md).** Il modello di configurazione di questa esercitazione si applica alla copia da un archivio dati basato su file a un archivio dati relazionale. Per un elenco degli archivi dati supportati come origini e sink, vedere la tabella degli [archivi dati supportati](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).

> [!NOTE]
>
> - Se non si ha familiarità con Data Factory, vedere [Introduzione ad Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

In questa esercitazione si segue questa procedura:

> * Creare una data factory
> * Creare una pipeline con un'attività di copia


## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**. Come archivio dati di *origine* si usa un archivio BLOB. Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) per informazioni su come crearne uno. **Assicurarsi che l'account di archiviazione consenta l'accesso solo da 'Reti selezionate'.** 
* **Database SQL di Azure**. Il database viene usato come archivio dati *sink*. Se non è disponibile un database SQL di Azure, vedere [Creare un database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) per crearne uno. **Assicurarsi che l'account di Database SQL di Azure consenta l'accesso solo da 'Reti selezionate'.** 

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare ora l'archivio BLOB di Azure e il database SQL per l'esercitazione seguendo questa procedura.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **emp.txt** sul disco:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Creare un contenitore denominato **adftutorial** nell'archivio BLOB. Creare una cartella denominata **input** in questo contenitore. Caricare quindi il file **emp.txt** nella cartella **input**. Usare il portale di Azure o strumenti come [Azure Storage Explorer](https://storageexplorer.com/) per eseguire queste attività.

#### <a name="create-a-sink-sql-table"></a>Creare una tabella SQL sink

1. Usare lo script SQL seguente per creare la tabella **dbo.emp** nel database SQL:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Consentire ai servizi di Azure di accedere a SQL Server. Assicurarsi che l'opzione **Consenti l'accesso a Servizi di Azure** sia impostata su **SÌ** per SQL Server in modo che Data Factory possa scrivere dati in SQL Server. Per verificare e attivare l'impostazione, passare a Server di Azure SQL > Panoramica > Imposta firewall server > impostare l'opzione **Consenti l'accesso a Servizi di Azure** su **SÌ**.

## <a name="create-a-data-factory"></a>Creare una data factory
In questo passaggio si crea una data factory e si avvia l'interfaccia utente di Data Factory per creare una pipeline nella data factory.

1. Aprire **Microsoft Edge** o **Google Chrome**. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.


2. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**.

3. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome**.

   Il nome della data factory di Azure deve essere *univoco a livello globale*. Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory. Ad esempio, nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](https://docs.microsoft.com/azure/data-factory/naming-rules).

4. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.

5. In **Gruppo di risorse** eseguire una di queste operazioni:

    a. Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa.

    b. Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse. 
         
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 

6. In **Versione** selezionare **V2**.

7. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dalla data factory possono trovarsi in altre aree.


8. Selezionare **Crea**.


9. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina della data factory.

10. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Creare un'istanza di Azure Integration Runtime nella rete virtuale gestita di Azure Data Factory
In questo passaggio si crea un'istanza di Azure Integration Runtime e si abilita la rete virtuale gestita.

1. Nel portale di Azure Data Factory passare a **Manage Hub** (Gestisci hub) e fare clic su **Nuovo** per creare una nuova istanza di Azure Integration Runtime.
   ![Creare un'istanza di Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Scegliere di creare un'istanza di Azure** Integration Runtime.
   ![Nuova istanza di Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Abilitare la **rete virtuale**.
   ![Nuova istanza di Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Selezionare **Crea**.

## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio si crea una pipeline con un'attività di copia nella data factory. L'attività copia i dati dall'archivio BLOB al database SQL. Nell'[esercitazione di avvio rapido](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) è stata creata una pipeline con questa procedura:

1. Creazione del servizio collegato.
1. Creazione dei set di dati di input e di output.
1. Creare una pipeline.

In questa esercitazione si crea inizialmente la pipeline, quindi si creano i servizi collegati e i set di dati quando risultano necessari per la configurazione della pipeline.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Creare una pipeline](./media/doc-common-process/get-started-page.png)
1. Nel riquadro **Proprietà** per la pipeline immettere **CopyPipeline** per **Nome** della pipeline.

1. Nella casella degli strumenti **Attività** espandere la categoria **Move & Transform** (Sposta e trasforma) e trascinare l'attività **Copia dati** dalla casella degli strumenti all'area di progettazione della pipeline. Specificare **CopyFromBlobToSql** per **Nome**.

    ![Attività di copia](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Configurare l'origine

>[!TIP]
>In questa esercitazione verrà usata la *chiave dell'account* come tipo di autenticazione per l'archivio dei dati di origine, ma è possibile scegliere altri metodi di autenticazione supportati: *URI di firma di accesso condiviso*,*Entità servizio* e *Identità gestita* se necessario. Per informazioni dettagliate, vedere le sezioni corrispondenti in [questo articolo](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties).
>Per archiviare in modo sicuro i segreti per gli archivi dati, è anche consigliabile usare il servizio Azure Key Vault. Per le spiegazioni dettagliate, vedere [questo articolo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault).

#### <a name="create-source-dataset-and-linked-service"></a>Creare il set di dati di origine e il servizio collegato

1. Passare alla scheda **Origine**. Selezionare **+ Nuovo** per creare un set di dati di origine.

1. Nella finestra di dialogo **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e quindi **Continua**. I dati di origine si trovano in un archivio BLOB, quindi come set di dati di origine si seleziona **Archiviazione BLOB di Azure**.

1. Nella finestra di dialogo **Select Format** (Seleziona formato) scegliere il tipo di formato dei dati e quindi fare clic su **Continua**.

1. Nella finestra di dialogo **Set Properties** (Imposta proprietà) immettere **SourceBlobDataset** come nome. Selezionare la casella di controllo **Prima riga come intestazione**. Selezionare **+ Nuovo** accanto alla casella di testo **Servizio collegato**.

1. Nella finestra **New Linked Service (Azure Blob Storage)** (Nuovo servizio collegato - Archivio BLOB di Azure) immettere **AzureStorageLinkedService** come nome e selezionare l'account di archiviazione nell'elenco **Nome account di archiviazione**. 

1. Assicurarsi di abilitare **Interactive Authoring** (Creazione interattiva). L'abilitazione potrebbe richiedere circa 1 minuto.

    ![Creazione interattiva](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Selezionare **Test connessione**. Il test dovrebbe avere esito negativo se l'account di archiviazione consente l'accesso solo da 'Reti selezionate' e richiede che Azure Data Factory crei un endpoint privato che deve essere approvato prima dell'uso. Nel messaggio di errore verrà visualizzato un collegamento che è possibile seguire per creare un **endpoint privato** gestito. *In alternativa, è possibile passare direttamente alla scheda Gestisci e seguire le istruzioni della [sezione successiva](#create-a-managed-private-endpoint) per creare un endpoint privato gestito*
> [!NOTE]
> La scheda Gestisci potrebbe non essere disponibile per tutte le istanze di data factory. Se non viene visualizzata, è comunque possibile accedere agli endpoint privati tramite la scheda '**Autore**'-->'**Connessioni**'-->'**Endpoint privato**'
1. Tenere aperta la finestra di dialogo e quindi passare all'account di archiviazione selezionato in precedenza.

1. Seguire le istruzioni riportate in [questa sezione](#approval-of-a-private-link-in-storage-account) per approvare il collegamento privato.

1. Tornare nella finestra di dialogo. Ripetere la procedura **Test connessione** e selezionare **Crea** per distribuire il servizio collegato.

1. Al termine della creazione del servizio collegato verrà visualizzata di nuovo la pagina **Set properties** (Imposta proprietà). Selezionare **Sfoglia** accanto a **Percorso file**.

1. Passare alla cartella **adftutorial/input**, selezionare il file **emp.txt** e quindi scegliere **OK**.

1. Selezionare **OK**. Si passerà automaticamente alla pagina della pipeline. Nella scheda **Origine** verificare che sia selezionato il set di dati **SourceBlobDataset**. Per visualizzare l'anteprima dei dati in questa pagina, selezionare **Anteprima dati**.

    ![Set di dati di origine](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato fatto clic sul collegamento ipertestuale durante il test della connessione, seguire il percorso seguente. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato in precedenza.

1. Passare alla scheda Gestisci.
> [!NOTE]
> La scheda Gestisci potrebbe non essere disponibile per tutte le istanze di data factory. Se non viene visualizzata, è comunque possibile accedere agli endpoint privati tramite la scheda '**Autore**'-->'**Connessioni**'-->'**Endpoint privato**'

1. Passare alla sezione Managed private endpoints (Endpoint privati gestiti).

1. Selezionare **+ Nuovo** in Managed private endpoints (Endpoint privati gestiti).

    ![Nuovo endpoint privato gestito](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selezionare il riquadro Archiviazione BLOB di Azure nell'elenco, quindi selezionare **Continua**.

1. Immettere il nome dell'account di archiviazione creato in precedenza.

1. Selezionare **Crea**.

1. Dopo aver atteso alcuni secondi, si noterà che il collegamento privato creato necessita di un'approvazione.

1. Selezionare l'endpoint privato creato in precedenza. Verrà visualizzato un collegamento ipertestuale, seguendo il quale sarà possibile approvare l'endpoint privato a livello di account di archiviazione.

    ![Endpoint privato gestito](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Approvazione di un collegamento privato nell'account di archiviazione
1. Nell'account di archiviazione passare a **Connessioni a endpoint privato** nella sezione Impostazioni.

1. Selezionare l'endpoint privato creato in precedenza, quindi selezionare **Approva**.

    ![Approvare un endpoint privato](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Aggiungere una descrizione e fare clic su **sì**
1. Tornare nella sezione **Managed private endpoints** (Gestisci endpoint privati) della scheda **Gestisci** di Azure Data Factory.
1. L'approvazione dell'endpoint privato verrà visualizzata nell'interfaccia utente di Azure Data Factory dopo circa 1-2 minuti.


### <a name="configure-sink"></a>Configurare il sink
>[!TIP]
>In questa esercitazione verrà usata l'*autenticazione SQL* come tipo di autenticazione per l'archivio dei dati di sink, ma è possibile scegliere altri metodi di autenticazione supportati: *Entità servizio* e *Identità gestita* se necessario. Per informazioni dettagliate, vedere le sezioni corrispondenti in [questo articolo](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties).
>Per archiviare in modo sicuro i segreti per gli archivi dati, è anche consigliabile usare il servizio Azure Key Vault. Per le spiegazioni dettagliate, vedere [questo articolo](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault).

#### <a name="create-sink-dataset-and-linked-service"></a>Creare un set di dati sink e un collegamento privato
1. Passare alla scheda **Sink** e selezionare **+ Nuovo** per creare un set di dati sink.

1. Nella finestra di dialogo **Nuovo set di dati** immettere "SQL" nella casella di ricerca per filtrare i connettori e selezionare **Database SQL di Azure** e quindi **Continua**. In questa esercitazione si copiano i dati in un database SQL.

1. Nella finestra di dialogo **Set Properties** (Imposta proprietà) immettere **OutputSqlDataset** come nome. Dall'elenco a discesa **Servizio collegato** selezionare **+ Nuovo**. È necessario associare un set di dati a un servizio collegato. Il servizio collegato contiene la stringa di connessione usata da Data Factory per connettersi al database SQL in fase di esecuzione. Il set di dati specifica il contenitore, la cartella e il file (facoltativo) in cui vengono copiati i dati.

1. Nella finestra di dialogo **New Linked Service (Azure SQL Database)** (Nuovo servizio collegato - Database SQL di Azure) seguire questa procedura:

    1. In **Nome** immettere **AzureSqlDatabaseLinkedService**.
    1. In **Nome server** selezionare l'istanza di SQL Server.
    1. Assicurarsi di abilitare **Interactive Authoring** (Creazione interattiva).
    1. In **Nome database** selezionare il database SQL.
    1. In **Nome utente** immettere il nome dell'utente.
    1. In **Password** immettere la password dell'utente.
    1. Selezionare **Test connessione**. Il test dovrebbe avere esito negativo se l'account di archiviazione consente l'accesso solo da 'reti selezionate' e richiede che Azure Data Factory crei un endpoint privato che deve essere approvato prima dell'uso. Nel messaggio di errore verrà visualizzato un collegamento che è possibile seguire per creare un **endpoint privato** gestito. *In alternativa, è possibile passare direttamente alla scheda Gestisci e seguire le istruzioni della sezione successiva per creare un endpoint privato gestito*
    1. Tenere aperta la finestra di dialogo e quindi passare al server SQL selezionato in precedenza.    
    1. Seguire le istruzioni riportate in [questa sezione](#approval-of-a-private-link-in-sql-server) per approvare il collegamento privato.
    1. Tornare nella finestra di dialogo. Ripetere la procedura **Test connessione** e selezionare **Crea** per distribuire il servizio collegato.

1. Si passerà automaticamente alla finestra di dialogo **Set Properties** (Imposta proprietà). In **Tabella** selezionare **[dbo].[emp]** . Selezionare **OK**.

1. Passare alla scheda con la pipeline e verificare che in **Sink Dataset** (Set di dati sink) sia selezionato **OutputSqlDataset**.

    ![Scheda con la pipeline](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

Facoltativamente, è possibile eseguire il mapping dello schema dell'origine allo schema corrispondente della destinazione seguendo le indicazioni riportate in [Mapping dello schema nell'attività di copia](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping).

#### <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato fatto clic sul collegamento ipertestuale durante il test della connessione, seguire il percorso seguente. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato in precedenza.

1. Passare alla scheda Gestisci.
1. Passare alla sezione Managed private endpoints (Endpoint privati gestiti).
1. Selezionare **+ Nuovo** in Managed private endpoints (Endpoint privati gestiti).

    ![Nuovo endpoint privato gestito](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selezionare il riquadro Database SQL di Azure nell'elenco, quindi selezionare **Continua**.
1. Immettere il nome del server SQL selezionato in precedenza.
1. Selezionare **Crea**.
1. Dopo aver atteso alcuni secondi, si noterà che il collegamento privato creato necessita di un'approvazione.
1. Selezionare l'endpoint privato creato in precedenza. Verrà visualizzato un collegamento ipertestuale, seguendo il quale sarà possibile approvare l'endpoint privato a livello di server SQL.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Approvazione di un collegamento privato nel server SQL
1. Nel server SQL passare a **Connessioni a endpoint privato** nella sezione Impostazioni.
1. Selezionare l'endpoint privato creato in precedenza, quindi selezionare **Approva**.
1. Aggiungere una descrizione e fare clic su **sì**.
1. Tornare nella sezione **Managed private endpoints** (Gestisci endpoint privati) della scheda **Gestisci** di Azure Data Factory.
1. Sono necessari circa 1-2 minuti prima che l'approvazione dell'endpoint privato venga visualizzata.

#### <a name="debug-and-publish-the-pipeline"></a>Eseguire il debug della pipeline e pubblicarla

È possibile eseguire il debug di una pipeline prima di pubblicare gli artefatti (servizi collegati, set di dati e pipeline) in Data Factory o nel proprio repository GIT di Azure Repos.

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra.
2. Quando è possibile eseguire correttamente la pipeline, nella barra degli strumenti superiore selezionare **Pubblica tutto**. Questa azione pubblica le entità create (set di dati e pipeline) in Data Factory.
3. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, fare clic su **Mostra notifiche** in alto a destra (pulsante con il campanello).


#### <a name="summary"></a>Summary
La pipeline di questo esempio copia i dati dall'archiviazione BLOB al database SQL di Azure usando un endpoint privato in una rete virtuale gestita. Si è appreso come:

> * Creare una data factory
> * Creare una pipeline con un'attività di copia

