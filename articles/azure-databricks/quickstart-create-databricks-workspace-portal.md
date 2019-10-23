---
title: Eseguire un processo Spark in Azure Databricks mediante il portale di Azure
description: Questa guida introduttiva illustra come usare il portale di Azure per creare un'area di lavoro di Azure Databricks e un cluster Apache Spark e per eseguire un processo Spark.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: f7f8c94504eef1023939ed41aa6124baef354e39
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791578"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Avvio rapido: Eseguire un processo Spark in Azure Databricks mediante il portale di Azure

In questa guida di avvio rapido si usa il portale di Azure per creare un'area di lavoro di Azure Databricks con un cluster Apache Spark. Si esegue un processo nel cluster e si usano grafici personalizzati per produrre report in tempo reale da Boston Safety Data.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

> [!Note]
> Questa esercitazione non può essere eseguita usando una **sottoscrizione di valutazione gratuita di Azure**.
> Se l'utente ha un account gratuito, andare al proprio profilo e modificare la sottoscrizione a **con pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/). Quindi [rimuovere il limite di spesa](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) e [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) per le vCPU nell'area dell'utente. Quando si crea l'area di lavoro Azure Databricks, è possibile selezionare il piano tariffario **Versione di valutazione (Premium - Unità Databricks gratuite per 14 giorni)** per concedere l'accesso gratuito Premium per 14 giorni dell'area di lavoro alle Unità Databricks di Azure.

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

In questa sezione viene creata un'area di lavoro di Azure Databricks usando il portale di Azure.

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks nel portale di Azure")

2. Nella pagina **Servizio Azure Databricks** specificare i valori per creare un'area di lavoro di Databricks.

    ![Creare un'area di lavoro di Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Creare un'area di lavoro di Azure Databricks")

    Specificare i valori seguenti:
    
    |Proprietà  |Descrizione  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks        |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti occidentali 2**. Per le altre aree disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).        |
    |**Piano tariffario**     |  Scegliere tra **Standard**, **Premium** e **Versione di valutazione**. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |
    |**Rete virtuale**     |  Scegliere di distribuire un'area di lavoro di Databricks nella rete virtuale. Per altre informazioni, vedere [Distribuire Azure Databricks nella rete virtuale di Azure (inserimento in rete virtuale)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).        |

    Selezionare **Create** (Crea).

4. La creazione dell'area di lavoro richiede alcuni minuti, durante i quali è possibile visualizzare lo stato della distribuzione in **Notifiche**.

    ![Riquadro di distribuzione di Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Riquadro di distribuzione di Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Creare un cluster Spark in Databricks

> [!NOTE]
> Per usare un account gratuito per creare il cluster Azure Databricks, prima di creare il cluster, passare al profilo personale e impostare la sottoscrizione per il **pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/).

1. Nel portale di Azure passare all'area di lavoro di Databricks creata e quindi fare clic su **Avvia area di lavoro**.

2. Si verrà reindirizzati al portale di Azure Databricks. Nel portale fare clic su **Nuovo cluster**.

    ![Databricks in Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks in Azure")

3. Nella pagina **New cluster** (Nuovo cluster) specificare i valori per creare un cluster.

    ![Creare un cluster di Databricks Spark in Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Creare un cluster di Databricks Spark in Azure")

    Accettare tutti gli altri valori predefiniti tranne i seguenti:

   * Immettere un nome per il cluster.
   * Per questo articolo, creare un cluster con il runtime **5.3**.
   * Assicurarsi di selezionare la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di attività). Specificare una durata in minuti per terminare il cluster, se questo non viene usato.
    
     Selezionare **Crea cluster**. Quando il cluster è in esecuzione, è possibile collegare blocchi appunti al cluster ed eseguire processi Spark.

Per altre informazioni sulla creazione di cluster, vedere [Create a Spark cluster in Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html) (Creare un cluster Spark in Azure Databricks).

## <a name="run-a-spark-sql-job"></a>Eseguire un processo Spark SQL

Eseguire le attività seguenti per creare un blocco appunti in Databricks, configurare il blocco appunti per la lettura dei dati da set di dati Open di Azure e quindi eseguire un processo Spark SQL sui dati.

1. Nel riquadro sinistro selezionare **Azure Databricks**. In **Attività comuni** fare clic su **Nuovo blocco appunti**.

    ![Creare un notebook in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Creare un notebook in Databricks")

2. Nella finestra di dialogo **Crea blocco appunti** immettere un nome, selezionare **Python** come linguaggio e selezionare il cluster Spark creato in precedenza.

    ![Creare un notebook in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Creare un notebook in Databricks")

    Selezionare **Create** (Crea).

3. In questo passaggio creare un dataframe Spark con i dati relativi alla sicurezza di Boston (Boston Safety Data) da [Set di dati Open di Azure](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks) e usare SQL per eseguire query sui dati.

   Il comando seguente consente di impostare le informazioni di accesso alle risorse di archiviazione di Azure. Incollare questo codice PySpark nella prima cella e usare **MAIUSC+INVIO** per eseguire il codice.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Il comando seguente consente a Spark di leggere dall'archivio BLOB in remoto. Incollare questo codice PySpark nella cella successiva e usare **MAIUSC+INVIO** per eseguire il codice.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Il comando seguente consente di creare un dataframe. Incollare questo codice PySpark nella cella successiva e usare **MAIUSC+INVIO** per eseguire il codice.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Eseguire un'istruzione SQL per restituire le prime 10 righe di dati dalla vista temporanea denominata **source**. Incollare questo codice PySpark nella cella successiva e usare **MAIUSC+INVIO** per eseguire il codice.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. I risultati verranno visualizzati in una tabella simile a quella dello screenshot seguente (sono incluse solo alcune colonne):

    ![Dati di esempio](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Dati JSON di esempio")

6. È ora possibile creare una rappresentazione visiva di questi dati per mostrare quanti eventi correlati alla sicurezza vengono segnalati usando Citizens Connect App e City Worker App invece di altre origini. Nella parte inferiore dell'output in formato tabella fare clic sull'icona del **grafico a barre** e quindi su **Opzioni tracciato**.

    ![Creare un grafico a barre](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Creare un grafico a barre")

8. In **Customize Plot** (Personalizza grafico) trascinare i valori come illustrato nello screenshot.

    ![Personalizzare un grafico a torta](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Personalizzare un grafico a barre")

   * Impostare **Chiavi** su **source**.
   * Impostare **Valori** su **<\id>** .
   * Impostare **Aggregation** (Aggregazione) su **COUNT** (CONTEGGIO).
   * Impostare il **tipo di visualizzazione** su **Grafico a torta**.

     Fare clic su **Apply**.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver finito l'articolo è possibile terminare il cluster. A questo scopo, nel riquadro sinistro dell'area di lavoro di Azure Databricks fare clic su **Clusters** (Cluster). Per il cluster che si vuole terminare, posizionare il cursore sui puntini di sospensione sotto la colonna **Actions** (Azioni) e fare clic sull'icona **Terminate** (Termina).

![Arrestare un cluster di Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Arrestare un cluster di Databricks")

Se non viene terminato manualmente, il cluster si arresterà automaticamente se è stata selezionata la casella di controllo **Terminate after \_\_ minutes of inactivity** (Termina dopo \_\_ minuti di attività) durante la creazione del cluster. In tal caso, il cluster viene automaticamente arrestato se è rimasto inattivo per il tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un cluster Spark in Azure Databricks ed è stato eseguito un processo Spark usando dati provenienti da set di dati Open di Azure. Per informazioni su come importare dati da altre origini dati in Azure Databricks, vedere anche [Spark data sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) (Origini dati di Spark). Passare all'articolo successivo per informazioni su come eseguire un'operazione ETL (estrazione, trasformazione e caricamento dati) tramite Azure Databricks.

> [!div class="nextstepaction"]
>[Estrarre, trasformare e caricare dati tramite Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
