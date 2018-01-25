---
title: Trasformare i dati con Spark in Azure Data Factory | Microsoft Docs
description: "Questa esercitazione offre istruzioni dettagliate per la trasformazione dei dati tramite l'attività Spark in Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: c2ec6706c92f229bb05ad9a19246c6ffe5f615c9
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Trasformare dati nel cloud usando l'attività Spark in Azure Data Factory
In questa esercitazione si usa il portale di Azure per creare una pipeline di Data Factory che trasforma i dati con un'attività Spark e un servizio collegato HDInsight su richiesta. In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory. 
> * Creare una pipeline con un'attività Spark
> * Attivare un'esecuzione della pipeline
> * Monitorare l'esecuzione della pipeline.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti
* **Account di archiviazione di Azure**. Creare uno script Python e un file di input, quindi caricarli nell'archivio di Azure. L'output del programma Spark viene archiviato in questo account di archiviazione. Il cluster Spark su richiesta usa lo stesso account di archiviazione come risorsa di archiviazione primaria.  
* **Azure PowerShell**. Seguire le istruzioni in [How to install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Come installare e configurare Azure PowerShell).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Caricare lo script Python nell'account di archiviazione BLOB
1. Creare un file Python denominato **WordCount_Spark.py** con il contenuto seguente: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Sostituire **&lt;storageAccountName&gt;** con il nome del proprio account di archiviazione di Azure. Salvare quindi il file. 
3. Nell'Archivio BLOB di Azure creare un contenitore denominato **adftutorial**, se non esiste. 
4. Creare una cartella denominata **spark**.
5. Creare una sottocartella denominata **script** nella cartella **spark**. 
6. Caricare il file **WordCount_Spark.py** nella sottocartella **script**. 


### <a name="upload-the-input-file"></a>Caricare il file di input
1. Creare un file denominato **minecraftstory.txt** con del testo. Il programma Spark conta il numero di parole in questo testo. 
2. Creare una sottocartella denominata `inputfiles` nella cartella `spark`. 
3. Caricare il file `minecraftstory.txt` nella sottocartella `inputfiles`. 

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Scegliere **Nuovo** dal menu a sinistra, fare clic su **Dati e analisi** e quindi fare clic su **Data factory**. 
   
   ![Nuovo->DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** immettere **ADFTutorialDataFactory** per **Nome**. 
      
     ![Pagina Nuova data factory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente per il campo Nome, modificare il nome della data factory, ad esempio nomeutenteADFTutorialDataFactory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'articolo [Data Factory - Regole di denominazione](naming-rules.md).
  
     ![Il nome non è disponibile - Errore](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
4. Per il **gruppo di risorse**, eseguire una di queste operazioni:
     
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
      Alcuni dei passaggi di questa guida introduttiva presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selezionare **V2 (anteprima)** per **Versione**.
5. Selezionare la **località** per la data factory. Data Factory V2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.
6. Selezionare **Aggiungi al dashboard**.     
7. Fare clic su **Crea**.
8. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. Al termine della creazione verrà visualizzata la pagina **Data factory**, come illustrato nell'immagine.
   
    ![Home page di Data factory](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. Fare clic sul riquadro **Crea e monitora** per avviare l'applicazione dell'interfaccia utente di Data Factory in una scheda separata.

## <a name="create-linked-services"></a>Creare servizi collegati
In questa sezione vengono creati due servizi collegati: 
    
- Un **servizio collegato Archiviazione di Azure** che collega un account di archiviazione di Azure alla data factory. Questo archivio viene usato dal cluster HDInsight su richiesta. Contiene anche lo script Spark da eseguire. 
- Un **servizio collegato HDInsight su richiesta**. Azure Data Factory crea automaticamente un cluster HDInsight, esegue il programma Spark ed elimina il cluster HDInsight dopo che è rimasto inattivo per un periodo di tempo preconfigurato. 

### <a name="create-an-azure-storage-linked-service"></a>Creare un servizio collegato Archiviazione di Azure

1. Nella pagina **Attività iniziali** passare alla scheda **Modifica** nel pannello a sinistra, come mostrato nell'immagine seguente: 

    ![Riquadro Create pipeline (Crea pipeline)](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Fare clic su **Connessioni** nella parte inferiore della finestra e quindi su **+ Nuovo**. 

    ![Pulsante per una nuova connessione](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Archiviazione BLOB di Azure** e fare clic su **Continua**. 

    ![Selezionare Archiviazione BLOB di Azure](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Selezionare il **nome dell'account di archiviazione di Azure** e fare clic su **Salva**. 

    ![Specificare l'account di archiviazione BLOB](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Creare un servizio collegato HDInsight su richiesta

1. Fare di nuovo clic sul pulsante **+ Nuovo** per creare un altro servizio collegato. 
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) selezionare **Azure HDInsight** e fare clic su **Continua**. 

    ![Selezionare Azure HDInsight](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. Nella finestra **New Linked Service** (Nuovo servizio collegato) seguire questa procedura: 

    1. Immettere **AzureHDInsightLinkedService** per **Nome**.
    2. Verificare che per **Tipo** sia selezionata l'opzione **On-demand HDInsight** (HDInsight su richiesta).
    3. Selezionare **AzureStorage1** per **Azure Storage Linked Service** (Servizio collegato Archiviazione di Azure). Questo servizio collegato è stato creato in precedenza. Se è stato usato un nome diverso, specificare il nome corretto per questo campo. 
    4. Selezionare **spark** per **Tipo di cluster**.
    5. Immettere l'**ID dell'entità servizio** autorizzata a creare un cluster HDInsight. Questa entità servizio deve essere un membro del ruolo Collaboratore della sottoscrizione o del gruppo di risorse in cui viene creato il cluster. Vedere come [creare un'applicazione e un'entità servizio di Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) per dettagli.
    6. Immettere la **chiave dell'entità servizio**. 
    7. Selezionare lo stesso gruppo di risorse usato durante la creazione della data factory per **Gruppo di risorse**. Il cluster Spark viene creato in questo gruppo di risorse. 
    8. Espandere **Tipo di sistema operativo**.
    9. Immettere un **nome** per l'**utente** del cluster. 
    10. Immettere la **password** dell'utente. 
    11. Fare clic su **Save**. 

        ![Impostazioni servizio collegato HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight applica un limite al numero totale di core che è possibile usare in ogni area di Azure supportata. Per il servizio collegato HDInsight su richiesta, il cluster HDInsight verrà creato nello stesso percorso dell'archivio di Azure usato come risorsa di archiviazione primaria. Assicurarsi che siano disponibili sufficienti quote di core per la creazione del cluster. Per altre informazioni, vedere [Configurare i cluster di HDInsight con Hadoop, Spark, Kafka e altro ancora](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Creare una pipeline

2. Fare clic sul pulsante + (segno più) e quindi scegliere **Pipeline** dal menu.

    ![Menu per nuova pipeline](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. Nella casella degli strumenti **Attività** espandere **HDInsight** e trascinare l'attività **Spark** dalla casella degli strumenti **Attività** all'area di progettazione della pipeline. 

    ![Trascinare l'attività Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. Nelle proprietà della finestra dell'attività **Spark** in basso seguire questa procedura: 

    1. Passare alla scheda **HDI Cluster** (Cluster HDInsight).
    2. Selezionare il servizio **AzureHDInsightLinkedService** creato nel passaggio precedente. 
        
    ![Specificare il servizio collegato HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. Passare alla scheda **Script/Jar** (Script/JAR) e seguire questa procedura: 

    1. Selezionare **AzureStorage1** per **Job Linked Service** (Servizio collegato processo).
    2. Fare clic su **Sfoglia risorsa di archiviazione**. 
    3. Passare alla cartella **adftutorial/spark/script**, selezionare **WordCount_Spark.py** e fare clic su **Fine**.      

    ![Specificare lo script Spark](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. Per convalidare la pipeline, fare clic sul pulsante **Convalida** sulla barra degli strumenti. Fare clic sul pulsante **freccia destra** (>>) per chiudere la finestra di convalida. 
    
    ![Pulsante Convalida](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. Fare clic su **Pubblica**. L'interfaccia utente di Data Factory pubblicherà le entità (servizi collegati e pipeline) nel servizio Azure Data Factory. 
    
    ![Pulsante Publish](./media/tutorial-transform-data-spark-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Attivare un'esecuzione della pipeline
Fare clic su **Trigger** sulla barra degli strumenti e quindi su **Trigger Now** (Attiva adesso). 

![Trigger now (Attiva adesso)](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Passare alla scheda **Monitoraggio**. Verificare che venga visualizzata un'esecuzione della pipeline. La creazione di un cluster Spark richiede circa 20 minuti. 

    ![Monitorare le esecuzioni di pipeline](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. Fare periodicamente clic su **Aggiorna** per controllare lo stato dell'esecuzione della pipeline. 

    ![Stato dell'esecuzione della pipeline](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, fare clic sull'azione **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna Azioni. È possibile tornare alla visualizzazione delle esecuzioni di pipeline facendo clic sul collegamento **Pipeline** in alto.

    ![Visualizzazione delle esecuzioni di attività](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verificare l'output
Verificare che il file di output sia stato creato nella cartella spark/otuputfiles/wordcount del contenitore adftutorial. 

![Verificare l'output](./media/tutorial-transform-data-spark-portal/verity-output.png)

Il file dovrebbe contenere ogni parola del file di testo di input e il numero di occorrenze della parola nel file. Ad esempio:  

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio trasforma i dati usando un'attività Spark e un servizio collegato HDInsight su richiesta. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory. 
> * Creare una pipeline con un'attività Spark
> * Attivare un'esecuzione della pipeline
> * Monitorare l'esecuzione della pipeline.

Passare alla prossima esercitazione per informazioni su come trasformare i dati tramite l'esecuzione di uno script Hive in un cluster Azure HDInsight che si trova in una rete virtuale. 

> [!div class="nextstepaction"]
> [Esercitazione: Trasformare dati usando Hive nella rete virtuale di Azure](tutorial-transform-data-hive-virtual-network-portal.md).





