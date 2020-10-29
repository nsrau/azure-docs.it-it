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
ms.openlocfilehash: c08dd1b5b2f90e874f36c6cf01c4cc5f5ae74d17
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636256"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Copiare dati dall'archiviazione BLOB di Azure a un database SQL in sicurezza usando endpoint privati

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questa esercitazione viene creata una data factory con l'interfaccia utente di Azure Data Factory. *La pipeline in questa data factory copia i dati in sicurezza dall'archiviazione BLOB di Azure a un database SQL di Azure, consentendo l'accesso solo alle reti selezionate, usando endpoint privati nella [rete virtuale gestita di Azure Data Factory](managed-virtual-network-private-endpoint.md).* Il modello di configurazione di questa esercitazione si applica alla copia da un archivio dati basato su file a un archivio dati relazionale. Per un elenco degli archivi dati supportati come origini e sink, vedere la tabella [Archivi dati e formati supportati](./copy-activity-overview.md).

> [!NOTE]
> Se non si ha familiarità con Data Factory, vedere [Introduzione ad Azure Data Factory](./introduction.md).

In questa esercitazione vengono completati i passaggi seguenti:

* Creare una data factory.
* Creare una pipeline con un'attività di copia.


## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure** . Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure** . Come archivio dati di *origine* si usa un archivio BLOB. Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal) per informazioni su come crearne uno. *Assicurarsi che l'account di archiviazione consenta l'accesso solo da reti selezionate.* 
* **Database SQL di Azure** . Il database viene usato come archivio dati *sink* . Se non è disponibile un database SQL di Azure, vedere [Creare un database SQL](../azure-sql/database/single-database-create-quickstart.md) per crearne uno. *Assicurarsi che l'account del database SQL di Azure consenta l'accesso solo da reti selezionate.* 

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare ora l'archivio BLOB di Azure e il database SQL per l'esercitazione seguendo questa procedura.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

1. Aprire il Blocco note. Copiare il testo seguente e salvarlo come file **emp.txt** sul disco:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Creare un contenitore denominato **adftutorial** nell'archivio BLOB. Creare una cartella denominata **input** in questo contenitore. Caricare quindi il file **emp.txt** nella cartella **input** . Usare il portale di Azure o strumenti come [Azure Storage Explorer](https://storageexplorer.com/) per eseguire queste attività.

#### <a name="create-a-sink-sql-table"></a>Creare una tabella SQL sink

Usare lo script SQL seguente per creare la tabella **dbo.emp** nel database SQL:

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

## <a name="create-a-data-factory"></a>Creare una data factory
In questo passaggio si crea una data factory e si avvia l'interfaccia utente di Data Factory per creare una pipeline nella data factory.

1. Aprire Microsoft Edge o Google Chrome. L'interfaccia utente di Data Factory è attualmente supportata solo nei Web browser Microsoft Edge e Google Chrome.

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory** .

1. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** in **Nome** .

   Il nome della data factory di Azure deve essere *univoco a livello globale* . Se viene visualizzato un messaggio di errore relativo al valore del nome, immettere un nome diverso per la data factory (ad esempio, nomeADFTutorialDataFactory). Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere [Azure Data factory - Regole di denominazione](./naming-rules.md).

1. Selezionare la **sottoscrizione** di Azure in cui creare la data factory.

1. In **Gruppo di risorse** eseguire una di queste operazioni:

    - Selezionare **Usa esistente** e scegliere un gruppo di risorse esistente dall'elenco a discesa.
    - Selezionare **Crea nuovo** e immettere un nome per il gruppo di risorse. 
     
    Per informazioni sui gruppi di risorse, vedere l'articolo su come [usare gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/overview.md). 

1. In **Versione** selezionare **V2** .

1. In **Località** selezionare una località per la data factory. Nell'elenco a discesa vengono visualizzate solo le località supportate. Gli archivi dati (ad esempio, Archiviazione di Azure e il database SQL) e le risorse di calcolo (ad esempio, Azure HDInsight) usati dalla data factory possono trovarsi in altre aree.

1. Selezionare **Crea** .

1. Al termine della creazione, la relativa notifica verrà visualizzata nel centro notifiche. Selezionare **Vai alla risorsa** per passare alla pagina **Data Factory** .

1. Selezionare **Crea e monitora** per avviare l'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Creare un runtime di integrazione di Azure nella rete virtuale gestita di Data Factory
In questo passaggio si crea un runtime di integrazione di Azure e si abilita la rete virtuale gestita di Data Factory.

1. Nel portale di Data Factory passare a **Gestisci** e selezionare **Nuovo** per creare un nuovo runtime di integrazione di Azure.

   ![Screenshot che illustra la creazione di un nuovo runtime di integrazione di Azure.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Scegliere di creare un runtime di integrazione di **Azure** .

   ![Screenshot che mostra un nuovo runtime di integrazione di Azure.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. In **Configurazione della rete virtuale (anteprima)** selezionare **Abilita** .

   ![Screenshot che mostra l'abilitazione di un nuovo runtime di integrazione di Azure.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Selezionare **Crea** .

## <a name="create-a-pipeline"></a>Creare una pipeline
In questo passaggio si crea una pipeline con un'attività di copia nella data factory. L'attività copia i dati dall'archivio BLOB al database SQL. Nell'[esercitazione di avvio rapido](./quickstart-create-data-factory-portal.md) è stata creata una pipeline con questa procedura:

1. Creazione del servizio collegato.
1. Creazione dei set di dati di input e di output.
1. Creare una pipeline.

In questa esercitazione si inizia creando una pipeline. quindi si creano i servizi collegati e i set di dati quando risultano necessari per la configurazione della pipeline.

1. Nella pagina **Attività iniziali** selezionare **Create pipeline** (Crea pipeline).

   ![Screenshot che mostra la creazione di una pipeline.](./media/doc-common-process/get-started-page.png)
1. Nel riquadro Proprietà per la pipeline immettere **CopyPipeline** per il nome della pipeline.

1. Nella casella degli strumenti **Attività** espandere la categoria **Move and Transform** (Sposta e trasforma) e trascinare l'attività **Copia dati** dalla casella degli strumenti all'area di progettazione della pipeline. Immettere **CopyFromBlobToSql** per il nome.

    ![Screenshot che mostra l'attività di copia.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Configurare un'origine

>[!TIP]
>In questa esercitazione si usa **Chiave dell'account** come tipo di autenticazione per l'archivio dati di origine. È anche possibile scegliere altri metodi di autenticazione supportati, ad esempio **URI SAS** , **Entità servizio** e **Identità gestita** se necessario. Per altre informazioni, vedere le sezioni corrispondenti in [Copiare e trasformare i dati in archiviazione BLOB di Azure con Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>Per archiviare in modo sicuro i segreti per gli archivi dati, è anche consigliabile usare Azure Key Vault. Per altre informazioni e illustrazioni, vedere [Archiviare le credenziali in Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Creare il set di dati di origine e il servizio collegato

1. Passare alla scheda **Origine** . Selezionare **+ Nuovo** per creare un set di dati di origine.

1. Nella finestra di dialogo **Nuovo set di dati** selezionare **Archiviazione BLOB di Azure** e quindi **Continua** . I dati di origine si trovano in un archivio BLOB, quindi come set di dati di origine si seleziona **Archiviazione BLOB di Azure** .

1. Nella finestra di dialogo **Selezionare il formato** selezionare il tipo di formato dei dati e quindi fare clic su **Continua** .

1. Nella finestra di dialogo **Imposta proprietà** immettere **SourceBlobDataset** in **Nome** . Selezionare la casella di controllo **Prima riga come intestazione** . Selezionare **+ Nuovo** accanto alla casella di testo **Servizio collegato** .

1. Nella finestra **Nuovo servizio collegato - Archiviazione BLOB di Azure** immettere **AzureStorageLinkedService** in **Nome** e selezionare l'account di archiviazione nell'elenco **Nome account di archiviazione** . 

1. Assicurarsi di abilitare **Creazione interattiva** . L'abilitazione potrebbe richiedere circa un minuto.

    ![Screenshot che mostra la creazione interattiva.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Selezionare **Test connessione** . L'operazione dovrebbe avere esito negativo se l'account di archiviazione consente l'accesso solo da **Reti selezionate** e richiede che Data Factory crei un endpoint privato che deve essere approvato prima dell'uso. Nel messaggio di errore verrà visualizzato un collegamento che è possibile seguire per creare un endpoint privato gestito. In alternativa, è possibile passare direttamente alla scheda **Gestisci** e seguire le istruzioni della [sezione successiva](#create-a-managed-private-endpoint) per creare un endpoint privato gestito.

   > [!NOTE]
   > La scheda **Gestisci** potrebbe non essere disponibile per tutte le istanze di data factory. Se non viene visualizzata, è possibile accedere agli endpoint privati selezionando **Autore** > **Connessioni**  > **Endpoint privato** .
1. Tenere aperta la finestra di dialogo e quindi passare all'account di archiviazione.

1. Seguire le istruzioni riportate in [questa sezione](#approval-of-a-private-link-in-a-storage-account) per approvare il collegamento privato.

1. Tornare nella finestra di dialogo. Selezionare di nuovo **Test connessione** e selezionare **Crea** per distribuire il servizio collegato.

1. Al termine della creazione del servizio collegato si tornerà alla pagina **Imposta proprietà** . Selezionare **Sfoglia** accanto a **Percorso file** .

1. Passare alla cartella **adftutorial/input** , selezionare il file **emp.txt** e quindi selezionare **OK** .

1. Selezionare **OK** . Si passerà automaticamente alla pagina della pipeline. Nella scheda **Origine** verificare che sia selezionato il set di dati **SourceBlobDataset** . Per visualizzare l'anteprima dei dati in questa pagina, selezionare **Anteprima dati** .

    ![Screenshot che mostra il set di dati di origine.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato selezionato il collegamento ipertestuale durante il test della connessione, seguire il percorso. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato.

1. Passare alla scheda **Gestisci** .

   > [!NOTE]
   > La scheda **Gestisci** potrebbe non essere disponibile per tutte le istanze di Data Factory. Se non viene visualizzata, è possibile accedere agli endpoint privati selezionando **Autore** > **Connessioni**  > **Endpoint privato** .

1. Passare alla sezione **Managed private endpoints** (Endpoint privati gestiti).

1. Selezionare **+ Nuovo** in **Managed private endpoints** (Endpoint privati gestiti).

    ![Screenshot che mostra il pulsante Nuovo in Managed private endpoints (Endpoint privati gestiti).](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selezionare il riquadro **Archiviazione BLOB di Azure** nell'elenco e quindi selezionare **Continua** .

1. Immettere il nome dell'account di archiviazione creato.

1. Selezionare **Crea** .

1. Dopo alcuni secondi si noterà che il collegamento privato creato necessita dell'approvazione.

1. Selezionare l'endpoint privato creato. Verrà visualizzato un collegamento ipertestuale, seguendo il quale sarà possibile approvare l'endpoint privato a livello di account di archiviazione.

    ![Screenshot che mostra il riquadro Managed private endpoint (Endpoint privato gestito).](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Approvazione di un collegamento privato in un account di archiviazione
1. Nell'account di archiviazione passare a **Connessioni a endpoint privato** nella sezione **Impostazioni** .

1. Selezionare la casella di controllo per l'endpoint privato creato e selezionare **Approva** .

    ![Screenshot che mostra il pulsante Approva per l'endpoint privato.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Aggiungere una descrizione e selezionare **sì** .
1. Tornare nella sezione **Managed private endpoints** (Gestisci endpoint privati) della scheda **Gestisci** in Data Factory.
1. Dopo un paio di minuti dovrebbe essere visualizzata l'approvazione dell'endpoint privato nell'interfaccia utente di Data Factory.


### <a name="configure-a-sink"></a>Configurare un sink
>[!TIP]
>In questa esercitazione si userà **Autenticazione SQL** come tipo di autenticazione per l'archivio dati sink. È anche possibile scegliere altri metodi di autenticazione supportati, ad esempio **Entità servizio** e **Identità gestita** se necessario. Per altre informazioni, vedere le sezioni corrispondenti in [Copiare e trasformare i dati nel database SQL di Azure con Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>Per archiviare in modo sicuro i segreti per gli archivi dati, è anche consigliabile usare Azure Key Vault. Per altre informazioni e illustrazioni, vedere [Archiviare le credenziali in Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Creare un set di dati sink e un collegamento privato
1. Passare alla scheda **Sink** e selezionare **+ Nuovo** per creare un set di dati sink.

1. Nella finestra di dialogo **Nuovo set di dati** immettere **SQL** nella casella di ricerca per filtrare i connettori. Selezionare **Database SQL di Azure** e quindi selezionare **Continua** . In questa esercitazione si copiano i dati in un database SQL.

1. Nella finestra di dialogo **Imposta proprietà** immettere **OutputSqlDataset** in **Nome** . Nell'elenco a discesa **Servizio collegato** selezionare **+ Nuovo** . È necessario associare un set di dati a un servizio collegato. Il servizio collegato contiene la stringa di connessione usata da Data Factory per connettersi al database SQL in fase di esecuzione. Il set di dati specifica il contenitore, la cartella e il file (facoltativo) in cui vengono copiati i dati.

1. Nella finestra di dialogo **Nuovo servizio collegato - Database SQL di Azure** seguire questa procedura:

    1. In **Nome** immettere **AzureSqlDatabaseLinkedService** .
    1. In **Nome server** selezionare l'istanza di SQL Server.
    1. Assicurarsi di abilitare **Creazione interattiva** .
    1. In **Nome database** selezionare il database SQL.
    1. In **Nome utente** immettere il nome dell'utente.
    1. In **Password** immettere la password dell'utente.
    1. Selezionare **Test connessione** . Il test dovrebbe avere esito negativo perché il server SQL consente l'accesso solo da **Reti selezionate** e richiede che Data Factory crei un endpoint privato che deve essere approvato prima dell'uso. Nel messaggio di errore verrà visualizzato un collegamento che è possibile seguire per creare un endpoint privato gestito. In alternativa, è possibile passare direttamente alla scheda **Gestisci** e seguire le istruzioni della sezione successiva per creare un endpoint privato gestito.
    1. Tenere aperta la finestra di dialogo e quindi passare al server SQL selezionato.
    1. Seguire le istruzioni riportate in [questa sezione](#approval-of-a-private-link-in-sql-server) per approvare il collegamento privato.
    1. Tornare nella finestra di dialogo. Selezionare di nuovo **Test connessione** e selezionare **Crea** per distribuire il servizio collegato.

1. Si passerà automaticamente alla finestra di dialogo **Imposta proprietà** . In **Tabella** selezionare **[dbo].[emp]** . Selezionare **OK** .

1. Passare alla scheda con la pipeline e verificare che in **Sink Dataset** (Set di dati sink) sia selezionato **OutputSqlDataset** .

    ![Screenshot che mostra la scheda Pipeline.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Facoltativamente, è possibile eseguire il mapping dello schema dell'origine allo schema corrispondente della destinazione seguendo le indicazioni riportate in [Mapping dello schema nell'attività di copia](./copy-activity-schema-and-type-mapping.md).

#### <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato selezionato il collegamento ipertestuale durante il test della connessione, seguire il percorso. A questo punto è necessario creare un endpoint privato gestito che verrà connesso al servizio collegato creato.

1. Passare alla scheda **Gestisci** .
1. Passare alla sezione **Managed private endpoints** (Endpoint privati gestiti).
1. Selezionare **+ Nuovo** in **Managed private endpoints** (Endpoint privati gestiti).

    ![Screenshot che mostra il pulsante Nuovo in Managed private endpoints (Endpoint privati gestiti).](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selezionare il riquadro **Database SQL di Azure** nell'elenco e quindi selezionare **Continua** .
1. Immettere il nome del server SQL selezionato.
1. Selezionare **Crea** .
1. Dopo alcuni secondi si noterà che il collegamento privato creato necessita dell'approvazione.
1. Selezionare l'endpoint privato creato. Verrà visualizzato un collegamento ipertestuale, seguendo il quale sarà possibile approvare l'endpoint privato a livello di server SQL.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Approvazione di un collegamento privato nel server SQL
1. Nel server SQL passare a **Connessioni a endpoint privato** nella sezione **Impostazioni** .
1. Selezionare la casella di controllo per l'endpoint privato creato e selezionare **Approva** .
1. Aggiungere una descrizione e selezionare **sì** .
1. Tornare nella sezione **Managed private endpoints** (Gestisci endpoint privati) della scheda **Gestisci** in Data Factory.
1. L'approvazione per l'endpoint privato dovrebbe essere visualizzata nel giro di un paio di minuti.

#### <a name="debug-and-publish-the-pipeline"></a>Eseguire il debug della pipeline e pubblicarla

È possibile eseguire il debug di una pipeline prima di pubblicare gli artefatti (servizi collegati, set di dati e pipeline) in Data Factory o nel proprio repository GIT di Azure Repos.

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra.
1. Quando è possibile eseguire correttamente la pipeline, nella barra degli strumenti superiore selezionare **Pubblica tutto** . Questa azione pubblica le entità create (set di dati e pipeline) in Data Factory.
1. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita** . Per visualizzare i messaggi di notifica, selezionare **Mostra notifiche** nell'angolo superiore destro (pulsante a forma di campanello).


#### <a name="summary"></a>Riepilogo
La pipeline in questo esempio copia i dati dall'archiviazione BLOB al database SQL usando un endpoint privato in una rete virtuale gestita di Data Factory. Si è appreso come:

* Creare una data factory.
* Creare una pipeline con un'attività di copia.