---
title: 'Guida introduttiva: Eseguire un processo Spark in Azure Databricks tramite il portale di Azure | Microsoft Docs'
description: Questa guida introduttiva illustra come usare il portale di Azure per creare un'area di lavoro di Azure Databricks e un cluster Apache Spark e per eseguire un processo Spark.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/09/2018
ms.author: nitinme
ms.custom: mvc
ms.openlocfilehash: 9eff06934eefa44db94de3d01be470ca69a2d88c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Guida introduttiva: Eseguire un processo Spark in Azure Databricks mediante il portale di Azure

Questa guida introduttiva illustra come creare un'area di lavoro di Azure Databricks e un cluster Apache Spark all'interno di tale area di lavoro. Viene infine spiegato come eseguire un processo Spark nel cluster Databricks. Per altre informazioni su Azure Databricks, vedere [Informazioni su Azure Databricks](what-is-azure-databricks.md).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-databricks-workspace"></a>Creare un'area di lavoro di Databricks

In questa sezione viene creata un'area di lavoro di Azure Databricks usando il portale di Azure. 

1. Nel portale di Azure fare clic su **Crea una risorsa**, su **Dati e analisi** e quindi su **Azure Databricks (anteprima)**. 

    ![Databricks nel portale di Azure](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks nel portale di Azure")

2. In **Azure Databricks (anteprima)** fare clic su **Crea**.

3. Nella pagina **Servizio Azure Databricks** specificare i valori per creare un'area di lavoro di Databricks.

    ![Creare un'area di lavoro di Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Creare un'area di lavoro di Azure Databricks")

    Specificare i valori seguenti: 
     
    |Proprietà  |DESCRIZIONE  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks        |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti orientali 2**. Per le altre aree disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).        |
    |**Piano tariffario**     |  Scegliere tra **Standard** e **Premium**. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**.

4. La creazione dell'account richiede alcuni minuti, durante i quali il portale visualizza il riquadro **Invio della distribuzione per Azure Databricks** a destra. Potrebbe essere necessario scorrere verso destra nel dashboard per visualizzare il riquadro. È presente anche un indicatore di stato nella parte superiore della schermata. È possibile esaminare lo stato di avanzamento nelle due aree.

    ![Riquadro di distribuzione Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Riquadro di distribuzione Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Creare un cluster Spark in Databricks

1. Nel portale di Azure passare all'area di lavoro di Databricks creata e quindi fare clic su **Avvia area di lavoro**.

2. Si verrà reindirizzati al portale di Azure Databricks. Nel portale fare clic su **Cluster**.

    ![Databricks in Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks in Azure")

3. Nella pagina **New cluster** (Nuovo cluster) specificare i valori per creare un cluster.

    ![Creare il cluster Databricks Spark in Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Creare il cluster Databricks Spark in Azure")

    * Immettere un nome per il cluster.
    * Per questo articolo creare un cluster con il runtime **4.0**. 
    * Assicurarsi di selezionare la casella di controllo **Terminate after ____ minutes of inactivity** (Termina dopo ___ minuti di attività). Specificare una durata in minuti per terminare il cluster, se questo non viene usato.
    * Accettare tutti gli altri valori predefiniti. 
    * Fare clic su **Create cluster** (Crea cluster). Quando il cluster è in esecuzione, è possibile collegare blocchi appunti al cluster ed eseguire processi Spark.

Per altre informazioni sulla creazione di cluster, vedere [Create a Spark cluster in Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html) (Creare un cluster Spark in Azure Databricks).

## <a name="run-a-spark-sql-job"></a>Eseguire un processo Spark SQL

Prima di iniziare con questa sezione, è necessario completare i prerequisiti seguenti:

* [Creare un account di Archiviazione di Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Scaricare un file JSON di esempio [da GitHub](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json). 
* Caricare il file JSON di esempio nell'account di archiviazione di Azure creato. Per caricare file, è possibile usare [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Eseguire le operazioni seguenti per creare un blocco appunti in Databricks, configurare il blocco appunti per la lettura dei dati da un account di archiviazione BLOB di Azure e quindi eseguire un processo Spark SQL sui dati.

1. Nel riquadro sinistro fare clic su **Area di lavoro**. Nell'elenco a discesa **Area di lavoro** fare clic su **Crea** e quindi su **Notebook** (Blocco appunti).

    ![Creare un blocco appunti in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Creare un blocco appunti in Databricks")

2. Nella finestra di dialogo **Create Notebook** (Crea blocco appunti) immettere un nome, selezionare **Scala** come linguaggio e selezionare il cluster Spark creato in precedenza.

    ![Creare un blocco appunti in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Creare un blocco appunti in Databricks")

    Fare clic su **Crea**.

3. In questo passaggio associare l'account di archiviazione di Azure con il cluster Databricks Spark. Esistono due modi per eseguire questa associazione. È possibile montare l'account di archiviazione di Azure in Databricks FileSystem (DBFS) oppure accedere direttamente all'account di Archiviazione di Azure dall'applicazione creata.  

    > [!IMPORTANT]
    >Questo articolo usa l'**approccio che prevede il montaggio della risorsa di archiviazione con DBFS**. Questo approccio assicura che la risorsa di archiviazione montata venga associata a FileSystem del cluster. Qualsiasi applicazione che accede al cluster può quindi usare anche la risorsa di archiviazione associata. L'approccio con accesso diretto è limitato all'applicazione in cui viene configurato l'accesso.
    >
    > Per usare l'approccio con montaggio, è necessario creare un cluster Spark con il runtime di Databricks versione **4.0**, selezionato in questo articolo.

    Nel frammento di codice seguente sostituire `{YOUR CONTAINER NAME}`, `{YOUR STORAGE ACCOUNT NAME}` e `{YOUR STORAGE ACCOUNT ACCESS KEY}` con i valori appropriati per l'account di archiviazione di Azure. Incollare il frammento in una cella vuota del blocco appunti e quindi premere MAIUSC+INVIO per eseguire la cella di codice.

    * **Montare l'account di archiviazione con DBFS (opzione consigliata)**. In questo frammento di codice il percorso dell'account di archiviazione di Azure viene montato in `/mnt/mypath`. In occasione di tutti gli accessi futuri all'account di archiviazione di Azure non sarà quindi necessario fornire il percorso completo. È sufficiente usare `/mnt/mypath`.

          dbutils.fs.mount(
            source = "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/",
            mountPoint = "/mnt/mypath",
            extraConfigs = Map("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net" -> "{YOUR STORAGE ACCOUNT ACCESS KEY}"))

    * **Accedere direttamente all'account di archiviazione**

          spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")

    Per istruzioni su come recuperare la chiave dell'account di archiviazione, vedere [Gestire le chiavi di accesso alle risorse di archiviazione](../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    > [!NOTE]
    > È anche possibile usare Azure Data Lake Store con un cluster Spark in Azure Databricks. Per istruzioni, vedere [Use Data Lake Store with Azure Databricks](https://go.microsoft.com/fwlink/?linkid=864084) (Usare Data Lake Store con Azure Databricks).

4. Eseguire un'istruzione SQL per creare una tabella temporanea con i dati dal file di dati JSON di esempio **small_radio_json.json**. Nel frammento di codice seguente sostituire i valori dei segnaposto con il nome del contenitore e il nome dell'account di archiviazione. Incollare il frammento in una cella di codice del blocco appunti e quindi premere MAIUSC+INVIO. Nel frammento di codice `path` indica il percorso del file JSON di esempio caricato nell'account di archiviazione di Azure.

    ```sql
    %sql 
    DROP TABLE IF EXISTS radio_sample_data
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path "/mnt/mypath/small_radio_json.json"
    )
    ```

    Dopo il completamento del comando, tutti i dati presenti nel file JSON sono visualizzati in una tabella nel cluster Databricks.

    Il comando magic `%sql` del linguaggio consente di eseguire codice SQL dal blocco appunti anche se questo è di un altro tipo. Per altre informazioni, vedere [Mixing languages in a notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook) (Combinazione di linguaggi in un blocco appunti).

5. Esaminiamo ora uno snapshot dei dati JSON di esempio per comprendere meglio la query eseguita. Incollare il frammento di codice seguente in una cella di codice e premere **MAIUSC+INVIO**.

    ```sql
    %sql 
    SELECT * from radio_sample_data
    ```

6. I risultati verranno visualizzati in una tabella simile a quella dello screenshot seguente (sono incluse solo alcune colonne):

    ![Dati JSON di esempio](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Dati JSON di esempio")

    Tra i vari dettagli, i dati di esempio acquisiscono il sesso del pubblico di un canale radio (nome colonna **gender**) e se l'abbonamento è gratuito o a pagamento (nome colonna **level**).

7. È ora possibile creare una rappresentazione visiva dei dati da visualizzare in base al sesso, con l'indicazione di quanti utenti usano un account gratuito e quanti sono abbonati a pagamento. Nella parte inferiore della tabella dei risultati fare clic sull'icona del **grafico a barre** e quindi su **Plot Options** (Opzioni grafico).

    ![Creare un grafico a barre](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Creare un grafico a barre")

8. In **Customize Plot** (Personalizza grafico) trascinare i valori come illustrato nello screenshot.

    ![Personalizzare il grafico a barre](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Personalizzare il grafico a barre")

    * Impostare **Keys** (Chiavi) su **gender**.
    * Impostare **Series groupings** (Raggruppamenti di serie) su **level**.
    * Impostare **Values** (Valori) su **level**.
    * Impostare **Aggregation** (Aggregazione) su **COUNT** (CONTEGGIO).

    Fare clic su **Apply**.

9. L'output mostra la rappresentazione visiva illustrata nello screenshot seguente:

     ![Personalizzare il grafico a barre](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "Personalizzare il grafico a barre")

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver finito l'articolo è possibile terminare il cluster. A questo scopo, nel riquadro sinistro dell'area di lavoro di Azure Databricks fare clic su **Clusters** (Cluster). Per il cluster che si vuole terminare, posizionare il cursore sui puntini di sospensione sotto la colonna **Actions** (Azioni) e fare clic sull'icona **Terminate** (Termina).

![Arrestare un cluster Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Arrestare un cluster Databricks")

Se non viene terminato manualmente, il cluster si arresterà automaticamente se è stata selezionata la casella di controllo **Terminate after __ minutes of inactivity** (Termina dopo ___ minuti di attività) durante la creazione del cluster. In tal caso, il cluster verrà automaticamente arrestato se è rimasto inattivo per il tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un cluster Spark in Azure Databricks ed è stato eseguito un processo Spark usando i dati nelle risorse di archiviazione di Azure. Per informazioni su come importare dati da altre origini dati in Azure Databricks, vedere anche [Spark data sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) (Origini dati di Spark). Passare all'articolo successivo per informazioni su come trasmettere dati in Azure Databricks tramite Hub eventi.

> [!div class="nextstepaction"]
>[Trasmettere dati in Azure Databricks tramite Hub eventi](databricks-stream-from-eventhubs.md)
