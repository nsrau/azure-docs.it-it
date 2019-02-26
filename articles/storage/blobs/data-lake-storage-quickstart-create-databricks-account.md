---
title: 'Avvio rapido: Analizzare i dati in Azure Data Lake Storage Gen2 con Azure Databricks | Microsoft Docs'
description: Informazioni su come eseguire un processo Spark in Azure Databricks usando il portale di Azure e un account di archiviazione di Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.openlocfilehash: 9d00819143d9a8fc38bfc09844d55f088e732b46
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453027"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Avvio rapido: Analizzare i dati in Azure Data Lake Storage Gen2 con Azure Databricks

Questa guida introduttiva illustra come eseguire un processo Apache Spark con Azure Databricks per effettuare analisi sui dati archiviati in un account di archiviazione per cui è abilitato Azure Data Lake Storage Gen2.

Nell'ambito del processo Spark si analizzeranno i dati delle sottoscrizioni di un canale radio per ottenere informazioni dettagliate sull'utilizzo gratuito/a pagamento in base ai dati demografici.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Creare un account di archiviazione Data Lake Gen2. Vedere [Avvio rapido: Creare un account di archiviazione di Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)

  Incollare il nome dell'account di archiviazione in un file di testo. Sarà presto necessario.

*  Creare un'entità servizio. Vedere [Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Mentre si completano le procedure descritte in tale articolo è necessario eseguire alcune operazioni specifiche.

   :heavy_check_mark: Quando si esegue la procedura descritta nella sezione [Assegnare l'applicazione a un ruolo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) dell'articolo, assicurarsi di assegnare il ruolo **Collaboratore ai dati del BLOB di archiviazione** all'entità servizio.

   > [!IMPORTANT]
   > Assicurarsi di assegnare il ruolo nell'ambito dell'account di archiviazione Data Lake Storage Gen2. È possibile assegnare un ruolo al gruppo di risorse padre o alla sottoscrizione, ma si riceveranno errori relativi alle autorizzazioni fino a quando tali assegnazioni di ruolo non si propagheranno all'account di archiviazione.

   :heavy_check_mark: Quando si esegue la procedura descritta nella sezione [Ottenere i valori per l'accesso](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) dell'articolo, incollare l'ID tenant, l'ID applicazione e i valori della chiave di autenticazione in un file di testo. Saranno necessari a breve.

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

In questa sezione viene creata un'area di lavoro di Azure Databricks usando il portale di Azure.

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks nel portale di Azure")

2. Nella pagina **Servizio Azure Databricks** specificare i valori per creare un'area di lavoro di Databricks.

    ![Creare un'area di lavoro di Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Creare un'area di lavoro di Azure Databricks")

    Specificare i valori seguenti:

    |Proprietà  |Descrizione  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks        |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti occidentali 2**. Se si preferisce, è possibile selezionare un'altra area pubblica.        |
    |**Piano tariffario**     |  Scegliere tra **Standard** e **Premium**. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**.

3. La creazione dell'area di lavoro richiede del tempo. Mentre viene creata, sul lato destro viene visualizzato il riquadro **Invio della distribuzione per Azure Databricks**. Per vedere il titolo, può essere necessario scorrere a destra nel dashboard. È presente anche un indicatore di stato nella parte superiore dello schermo. È possibile esaminare lo stato di avanzamento nelle due aree.

    ![Riquadro di distribuzione Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-deployment-tile.png "Riquadro di distribuzione Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Creare un cluster Spark in Databricks

1. Nel portale di Azure passare all'area di lavoro di Databricks creata e quindi selezionare **Avvia area di lavoro**.

2. Si verrà reindirizzati al portale di Azure Databricks. Nel portale selezionare **Nuovo** > **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks in Azure")

3. Nella pagina **New cluster** (Nuovo cluster) specificare i valori per creare un cluster.

    ![Creare il cluster Databricks Spark in Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Creare il cluster Databricks Spark in Azure")

    Accettare tutti gli altri valori predefiniti tranne i seguenti:

    * Immettere un nome per il cluster.
    * Creare un cluster con il runtime **5.1**.
    * Assicurarsi di selezionare la casella di controllo **Terminate after 120 minutes of inactivity** (Termina dopo 120 minuti di inattività). Specificare una durata in minuti per terminare il cluster, se questo non viene usato.

4. Selezionare **Crea cluster**. Quando il cluster è in esecuzione, è possibile collegare blocchi appunti al cluster ed eseguire processi Spark.

Per altre informazioni sulla creazione di cluster, vedere [Create a Spark cluster in Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html) (Creare un cluster Spark in Azure Databricks).

## <a name="create-storage-account-file-system"></a>Creare il file system per l'account di archiviazione

In questa sezione viene creato un notebook nell'area di lavoro di Azure Databricks e quindi vengono eseguiti frammenti di codice per configurare l'account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'area di lavoro di Azure Databricks creata e quindi selezionare **Launch Workspace** (Avvia l'area di lavoro).

2. Nel riquadro a sinistra selezionare **Workspace** (Area di lavoro). Nell'elenco a discesa **Workspace** (Area di lavoro) selezionare **Create (Crea)** > **Notebook**.

    ![Creare un blocco appunti in Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Creare un blocco appunti in Databricks")

3. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome per il notebook. Selezionare **Scala** come linguaggio e quindi selezionare il cluster Spark creato in precedenza.

    ![Creare un blocco appunti in Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Creare un blocco appunti in Databricks")

    Selezionare **Create**.

4. Copiare e incollare il blocco di codice seguente nella prima cella, ma non eseguirlo ancora.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```

    > [!NOTE]
    > Questo blocco di codice accede direttamente all'endpoint Data Lake Gen2 usando OAuth, ma è possibile connettere in altri modi l'area di lavoro di Databricks all'account di Data Lake Storage Gen2. È ad esempio possibile montare il file system usando OAuth oppure usare un accesso diretto con Chiave condivisa. <br>Per alcuni esempi di questi approcci, vedere l'articolo [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) sul sito Web di Azure Databricks.

5. In questo blocco di codice sostituire i valori segnaposto `storage-account-name`, `application-id`, `authentication-id` e `tenant-id` con i valori raccolti durante la creazione dell'entità servizio. Impostare il valore segnaposto `file-system-name` sul nome che si vuole assegnare al file system.

6. Premere **MAIUSC + INVIO** per eseguire il codice in questo blocco.

## <a name="ingest-sample-data"></a>Inserire dati di esempio

Prima di iniziare con questa sezione, è necessario completare i prerequisiti seguenti:

Immettere il codice seguente in una cella del notebook:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Nella cella, premere **MAIUSC+INVIO** per eseguire il codice.

In una nuova cella al di sotto di questa immettere il codice seguente, sostituendo i valori tra parentesi con gli stessi valori usati in precedenza:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Nella cella, premere **MAIUSC+INVIO** per eseguire il codice.

## <a name="run-a-spark-sql-job"></a>Eseguire un processo Spark SQL

Eseguire le attività seguenti per eseguire un processo Spark SQL sui dati.

1. Eseguire un'istruzione SQL per creare una tabella temporanea con i dati dal file di dati JSON di esempio **small_radio_json.json**. Nel frammento di codice seguente sostituire i valori segnaposto con il nome del file system e il nome dell'account di archiviazione. Usando il notebook creato in precedenza, incollare il frammento di codice in una nuova cella di codice del notebook e quindi premere MAIUSC+INVIO.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/<PATH>/small_radio_json.json"
    )
    ```

    Dopo il completamento del comando, tutti i dati presenti nel file JSON sono visualizzati in una tabella nel cluster Databricks.

    Il comando magic `%sql` del linguaggio consente di eseguire codice SQL dal blocco appunti anche se questo è di un altro tipo. Per altre informazioni, vedere [Mixing languages in a notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook) (Combinazione di linguaggi in un blocco appunti).

2. Si esaminerà ora uno snapshot dei dati JSON di esempio per comprendere meglio la query eseguita. Incollare il frammento di codice seguente in una cella di codice e premere **MAIUSC+INVIO**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. I risultati verranno visualizzati in una tabella simile a quella dello screenshot seguente (sono incluse solo alcune colonne):

    ![Dati JSON di esempio](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Dati JSON di esempio")

    Tra i vari dettagli, i dati di esempio acquisiscono il sesso del pubblico di un canale radio (nome colonna **gender**) e se l'abbonamento è gratuito o a pagamento (nome colonna **level**).

4. È ora possibile creare una rappresentazione visiva dei dati da visualizzare in base al sesso, con l'indicazione di quanti utenti usano un account gratuito e quanti sono abbonati a pagamento. Nella parte inferiore della tabella dei risultati fare clic sull'icona del **grafico a barre** e quindi su **Plot Options** (Opzioni grafico).

    ![Creare un grafico a barre](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Creare un grafico a barre")

5. In **Customize Plot** (Personalizza grafico) trascinare i valori come illustrato nello screenshot.

    ![Personalizzare il grafico a barre](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Personalizzare il grafico a barre")

    - Impostare **Keys** (Chiavi) su **gender**.
    - Impostare **Series groupings** (Raggruppamenti di serie) su **level**.
    - Impostare **Values** (Valori) su **level**.
    - Impostare **Aggregation** (Aggregazione) su **COUNT** (CONTEGGIO).

6. Fare clic su **Apply**.

7. L'output mostra la rappresentazione visiva illustrata nello screenshot seguente:

     ![Personalizzare il grafico a barre](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Personalizzare il grafico a barre")

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo avere completato l'articolo, è possibile terminare il cluster. Nell'area di lavoro di Azure Databricks selezionare **Cluster** e individuare il cluster da terminare. Posizionare il cursore del mouse sui puntini di sospensione sotto la colonna **Actions** (Azioni) e fare clic sull'icona **Terminate** (Termina).

![Arrestare un cluster Databricks](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Arrestare un cluster Databricks")

Se non viene terminato manualmente, il cluster si arresta automaticamente se è stata selezionata la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di attività) durante la creazione del cluster. Se si imposta questa opzione, il cluster si arresta dopo essere rimasto inattivo per il periodo di tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un cluster Spark in Azure Databricks ed è stato eseguito un processo Spark in un account di archiviazione con Data Lake Storage Gen2 abilitato. Per informazioni su come importare dati da altre origini dati in Azure Databricks, vedere anche [Spark data sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) (Origini dati di Spark). Passare all'articolo successivo per informazioni su come eseguire un'operazione ETL (estrazione, trasformazione e caricamento dati) tramite Azure Databricks.

> [!div class="nextstepaction"]
>[Estrarre, trasformare e caricare dati tramite Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)
