---
title: Introduzione a ML Services in HDInsight - Azure
description: Informazioni sulla creazione di un cluster Apache Spark in HDInsight che include ML Services e sull'invio di uno script R nel cluster.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 0811f0e115545a37b6c570c436900a8548adae61
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006363"
---
# <a name="get-started-with-ml-services-on-azure-hdinsight"></a>Introduzione all'uso di ML Services in HDInsight

Azure HDInsight permette di creare un cluster ML Services. Con questa opzione, gli script R possono usare Spark e MapReduce per eseguire i calcoli distribuiti. In questo articolo si apprenderà come creare un cluster ML Service in HDInsight e come eseguire uno script R che illustra l'uso di Spark per i calcoli R distribuiti.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**: prima di iniziare questa esercitazione è necessario avere una sottoscrizione di Azure. Per altre informazioni, vedere il video su come [ottenere una versione di valutazione gratuita di Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un client Secure Shell (SSH)**: il client SSH viene usato per connettersi da remoto al cluster HDInsight e per eseguire i comandi direttamente sul cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Creare il cluster con il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **Crea una risorsa** > **Dati e analisi** > **HDInsight**.

3. In **Informazioni di base** immettere le informazioni seguenti.

    * **Nome del cluster**: nome del cluster HDInsight.
    * **Sottoscrizione**: selezionare la sottoscrizione da usare.
    * **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster**: account di accesso usato per il cluster su HTTPS. Queste credenziali vengono usate per accedere a servizi quali l'interfaccia utente Web di Ambari o l'API REST.
    * **Secure Shell (SSH) username** (Nome utente SSH): account di accesso usato per il cluster su SSH. Per impostazione predefinita, la password corrisponde alla password di accesso al cluster.
    * **Gruppo di risorse**: il gruppo di risorse nel quale viene creato il cluster.
    * **Posizione**: area di Azure in cui creare il cluster.

        ![Dettagli di base del cluster](./media/r-server-get-started/clustername.png)

4. Selezionare **Tipo di cluster**, quindi impostare i valori seguenti nella sezione **Configurazione cluster**:

    * **Tipo di cluster**: ML Services

    * **Sistema operativo**: Linux

    * **Versione**: ML Server 9.3 (HDI 3.6). Le note sulla versione per ML Server 9.3 sono disponibili in [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-machine-learning-server).

    * **R Studio Community Edition for ML Server**: questo IDE basato su browser viene installato per impostazione predefinita nel nodo perimetrale. Se si preferisce non installarlo, deselezionare la casella di controllo. Se si sceglie di installarlo, l'URL per l'accesso a RStudio Server è disponibile nel pannello delle applicazioni del portale per il cluster, dopo la creazione.

        ![Dettagli di base del cluster](./media/r-server-get-started/clustertypeconfig.png)

4. Dopo avere selezionato il tipo di cluster, usare il pulsante __Seleziona__ per impostare il tipo di cluster. Usare quindi il pulsante __Avanti__ per completare la configurazione di base.

5. Nella sezione **Archiviazione** selezionare o creare un account di archiviazione. Per la procedura illustrata in questo documento, non modificare i valori predefiniti degli altri campi della sezione. Usare il pulsante __Avanti__ per salvare la configurazione della risorsa di archiviazione.

    ![Configurare le impostazioni dell'account di archiviazione per HDInsight](./media/r-server-get-started/cluster-storage.png)

6. Nella sezione **Riepilogo** esaminare la configurazione del cluster. Usare i collegamenti __Modifica__ per cambiare eventuali impostazioni non corrette. Usare infine il pulsante __Crea__ per creare il cluster.

    ![Configurare le impostazioni dell'account di archiviazione per HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > La creazione del cluster può richiedere fino a 20 minuti.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Connettersi a RStudio Server

Se si sceglie di installare RStudio Server Community Edition come parte del cluster HDInsight, è possibile accedere a RStudio usando uno dei due metodi seguenti:

* **Opzione 1**: passare all'URL seguente (in cui **CLUSTERNAME** è il nome del cluster ML Services creato):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Opzione 2**: Aprire il cluster ML Services nel portale di Azure e in **Collegamenti rapidi** fare clic su **ML Services Dashboards** (Dashboard di ML Services).

     ![Configurare le impostazioni dell'account di archiviazione per HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    In **Dashboard del cluster** fare clic su **R Studio Server**.

    ![Configurare le impostazioni dell'account di archiviazione per HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Indipendentemente dal metodo usato, al primo accesso è necessario eseguire l'autenticazione due volte.  Al primo prompt di autenticazione specificare l'*ID utente* e la *password amministratore del cluster*. Al secondo prompt di autenticazione specificare l'*ID utente* e la *password SSH*. Per gli accessi successivi saranno necessarie solo le credenziali SSH.

Una volta connessi, la schermata dovrebbe avere un aspetto simile allo screenshot seguente:

![Connettersi a RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Eseguire un processo di esempio

È possibile inviare un processo usando funzioni ScaleR. Di seguito è riportato un esempio dei comandi usati per eseguire un processo:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Connettersi al nodo perimetrale del cluster

Questa sezione illustra come connettersi al nodo perimetrale di un cluster ML Services HDInsight tramite SSH. Per acquisire familiarità con l'uso di SSH, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Il comando SSH per connettersi al nodo perimetrale di un cluster ML Services è:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Per trovare il comando SSH per il cluster, dal portale di Azure fare clic sul nome del cluster, fare clic su **SSH + Account di accesso del cluster** e quindi selezionare il nodo perimetrale in **Nome host**. Questa operazione consente di visualizzare le informazioni sull'endpoint SSH per il nodo perimetrale.

![Immagine dell'endpoint SSH per il nodo perimetrale](./media/r-server-get-started/sshendpoint.png)

Se è stata usata una password per proteggere l'account utente SSH, viene richiesto di specificarla. Se è stata usata una chiave pubblica, può essere necessario usare il parametro `-i` per specificare la chiave privata corrispondente. Ad esempio: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Dopo aver stabilito la connessione, viene visualizzato un prompt simile al seguente:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Usare la console di R

1. Nella sessione SSH usare il comando seguente per avviare la console R:  

        R

2. Verrà visualizzato un output con la versione di ML Services e altre informazioni.
    
3. Dal prompt `>` è possibile immettere codice R. ML Services in HDInsight include pacchetti che permettono di interagire facilmente con Hadoop ed eseguire calcoli distribuiti. Ad esempio, usare il comando seguente per visualizzare la radice del file system predefinito per il cluster HDInsight:

        rxHadoopListFiles("/")

4. È anche possibile usare l'indirizzamento in stile WASB.

        rxHadoopListFiles("wasb:///")

5. Per chiudere la console di R, usare il comando seguente:

        quit()

## <a name="automated-cluster-creation"></a>Creazione automatizzata di cluster

È possibile automatizzare la creazione di un cluster ML Services per HDInsight usando l'SDK e PowerShell.

<!---* To create an ML Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).--->
* Per creare un cluster ML Services con .NET SDK, vedere [Creare cluster basati su Linux in HDInsight tramite .NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* Per creare un cluster ML Service con PowerShell, vedere l'articolo [Creare cluster in HDInsight tramite Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare un nuovo cluster ML Services in Azure HDInsight e sono state fornite le nozioni di base sull'uso della console di R da una sessione SSH. Gli articoli seguenti illustrano altre modalità di gestione e uso di ML Services in HDInsight:

* [Inviare processi da R Tools per Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Gestire cluster ML Services in Azure HDInsight](r-server-hdinsight-manage.md)
* [Rendere operativo un cluster ML Services in Azure HDInsight](r-server-operationalize.md)
* [Opzioni del contesto di calcolo per un cluster ML Services su HDInsight](r-server-compute-contexts.md)
* [Opzioni di Archiviazione di Azure per un cluster ML Services su HDInsight](r-server-storage.md)
