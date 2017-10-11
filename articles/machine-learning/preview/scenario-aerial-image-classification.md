---
title: Classificazione delle immagini aeree | Microsoft Docs
description: Fornisce istruzioni per uno scenario reale sulla classificazione delle immagini aeree
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 09/15/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 43b124fc3eb72adc5d299b218c9e16ec83d1a240
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="aerial-image-classification"></a>Classificazione delle immagini aeree

Questo esempio illustra come usare Azure Machine Learning Workbench per coordinare l'addestramento distribuito e l'operazionalizzazione di modelli di classificazione delle immagini. Si usa il pacchetto [Microsoft Machine Learning for Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) per definire le caratteristiche delle immagini usando modelli CNTK pre-addestrati e per addestrare i classificatori usando le caratteristiche derivate. Quindi si applicano i modelli addestrati in parallelo ad ampi set di immagini nel cloud. Questi passaggi sono eseguiti in un cluster [Azure HDInsight Spark](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/) e ciò consente di ridimensionare la velocità di addestramento e operazionalizzazione aggiungendo o rimuovendo nodi di lavoro.

La forma di transfer learning descritta presenta vantaggi importanti rispetto alla ripetizione dell'addestramento o all'ottimizzazione di una rete neurale profonda: non richiede il calcolo GPU, è intrinsecamente veloce e scalabile in modo arbitrario e inoltre richiede meno parametri. Questo metodo è pertanto un'ottima scelta quando sono disponibili pochi campioni di addestramento, come accade spesso i per casi d'uso personalizzati. Molti utenti riferiscono che il transfer learning genera modelli ad alte prestazioni, consentendo di evitare le reti neurali profonde addestrate da zero con un costo maggiore.

Il cluster Spark utilizzato in questo esempio ha 40 nodi di lavoro e costa circa $ 40 per ora di funzionamento. Per completare questa procedura dettagliata sono necessarie circa due ore. Al termine, seguire le istruzioni di pulizia per rimuovere le risorse che sono state create e interrompere gli addebiti.

## <a name="link-to-the-gallery-github-repository"></a>Collegamento al repository GitHub della raccolta

Il repository GitHub pubblico per questo scenario reale contiene tutti i materiali, inclusi esempi di codice necessari per questo esempio:

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Descrizione del caso d'uso

In questo scenario si addestrano modelli di machine learning per classificare il tipo di terreno mostrato nelle immagini aeree di tracciati di 224 x 224 metri. I modelli di classificazione degli utilizzi del suolo possono essere usati per tenere traccia dell'urbanizzazione, la deforestazione, la perdita di paludi e altre tendenze ambientali principali usando immagini aeree raccolte periodicamente. Sono stati preparati set di immagini di addestramento e convalida riguardanti gli Stati Uniti. Programma Nazionale delle Immagini Agricole ed etichette degli utilizzi del suolo pubblicate dagli Stati Uniti. Database nazionale della copertura del suolo. Le immagini di esempio in ogni classe di utilizzo del suolo sono illustrate di seguito:

![Aree di esempio per ogni etichetta di utilizzo del suolo](media/scenario-aerial-image-classification/example-labels.PNG)

Dopo l'addestramento e la convalida del modello di classificazione, lo si applicherà alle immagini aeree che riguardano la Contea di Middlesex, MA, sede del New England Research & Development (NERD) Center di Microsoft, per illustrare come questi modelli possono essere usati per analizzare le tendenze dello sviluppo urbano.

Per produrre un classificatore di immagini usando il transfer learning, i data scientist spesso costruiscono più modelli con una gamma di metodi e selezionano il modello con le massime prestazioni. Azure Machine Learning Workbench può aiutare i data scientist a coordinare l'addestramento fra diversi ambienti di calcolo, a tenere traccia e confrontare le prestazioni di più modelli e ad applicare un modello scelto a set di dati di grandi dimensioni nel cloud.

## <a name="scenario-structure"></a>Struttura dello scenario

In questo esempio i dati di immagine e i modelli pre-addestrati sono collocati in un account di archiviazione di Azure. Un cluster Azure HDInsight Spark legge questi file e costruisce un modello di classificazione delle immagini tramite MMLSpark. Il modello addestrato e le sue previsioni vengono quindi scritti nell'account di archiviazione, dove possono essere analizzati e visualizzati da un notebook Jupyter eseguito localmente. Azure Machine Learning Workbench coordina l'esecuzione remota degli script nel cluster Spark. Tiene inoltre traccia delle metriche di accuratezza per più modelli addestrati con metodi diversi, consentendo all'utente di selezionare il modello con le massime prestazioni.

![Schema per lo scenario reale di classificazione delle immagini aeree](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Le [istruzioni passo per passo](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/) iniziano con la creazione e la preparazione di un account di archiviazione di Azure e di un cluster Spark, incluso il trasferimento dei dati e l'installazione della dipendenza. Quindi descrivono come avviare i processi di addestramento e confrontare le prestazioni dei modelli risultanti. Infine illustrano come applicare un modello scelto a un set di immagini di grandi dimensioni nel cluster Spark e analizzare i risultati predittivi in locale.


## <a name="set-up-the-execution-environment"></a>Configurare l'ambiente di esecuzione

Le istruzioni seguenti consentono di eseguire il processo di configurazione dell'ambiente di esecuzione per questo esempio.

### <a name="prerequisites"></a>Prerequisiti
- Un [account di Azure](https://azure.microsoft.com/en-us/free/) (sono disponibili account di prova gratuiti)
    - Questo esempio crea un cluster HDInsight Spark con 40 nodi di lavoro (168 core in totale). Assicurarsi che l'account disponga di sufficienti core disponibili controllando la scheda "Utilizzo + quote" per la sottoscrizione nel portale di Azure.
    - Se si dispone di meno core, è possibile modificare il modello del cluster HDInsight per ridurre il numero di ruoli di lavoro di cui eseguire il provisioning. Le istruzioni per come procedere sono disponibili nella sezione "Creare il cluster HDInsight Spark".
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - Seguire la [Guida introduttiva all'installazione e alla creazione](quickstart-installation.md) per installare Azure Machine Learning Workbench e creare gli account di sperimentazione e di gestione dei modelli.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), un'utilità gratuita per il coordinamento del trasferimento dei file fra account di archiviazione di Azure
    - Verificare che la cartella contenente il file eseguibile AzCopy sia nella variabile di ambiente PATH del sistema (le istruzioni su come modificare le variabili di ambiente sono disponibili [qui](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp)). Questo esempio è stato testato in un PC Windows 10; sarà possibile eseguirlo da qualsiasi computer Windows, tra cui le macchine virtuali per data science di Azure. Potrebbero essere necessarie piccole modifiche (ad esempio modifiche a filepaths) durante l'esecuzione di questo esempio in macOS.

### <a name="set-up-azure-resources"></a>Configurare le risorse di Azure

Questo esempio richiede un cluster HDInsight Spark e un account di archiviazione di Azure per ospitare i file rilevanti. Seguire queste istruzioni per creare queste risorse in un nuovo gruppo di risorse di Azure:

#### <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Progetti** fare clic sul segno **+** e selezionare **Nuovo progetto**
3.  Nel riquadro **Crea nuovo progetto** specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Cerca modelli di progetto** digitare "Classificazione immagini aeree" e selezionare il modello
5.  Fare clic su **Crea**
 
#### <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

1. Dal progetto Azure Machine Learning Workbench aprire l'interfaccia della riga di comando facendo clic su File -> Apri prompt dei comandi.
1. Dall'interfaccia della riga di comando accedere al proprio account di Azure usando il comando seguente:

    ````
    az login
    ```

    You are asked to visit a URL and type in a provided temporary code; the website requests your Azure account credentials.
    
1. When login is complete, return to the CLI and run the following command to determine which Azure subscriptions are available to your Azure account:

    ```
    az account list
    ```

    This command lists all subscriptions associated with your Azure account. Find the ID of the subscription you would like to use. Write the subscription ID where indicated in the following command, then set the active subscription by executing the command:

    ```
    az account set --subscription [subscription ID]
    ```

1. The Azure resources created in this example are stored together in an Azure resource group. Choose a unique resource group name and write it where indicated, then execute both commands to create the Azure resource group:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### Create the storage account

We now create the storage account that hosts project files that must be accessed by the HDInsight Spark.

1. Choose a unique storage account name and write it where indicated in the following `set` command, then create an Azure storage account by executing both commands:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Issue the following command to list the storage account keys:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Record the value of `key1` as the storage key in the following command, then run the command to store the value.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. In your favorite text editor, load the `settings.cfg` file from the Azure Machine Learning Workbench project's "Code" subdirectory, and insert the storage account name and key as indicated. Save and close the `settings.cfg` file.
1. If you have not already done so, download and install the [AzCopy](http://aka.ms/downloadazcopy) utility. Ensure that the AzCopy executable is on your system path by typing "AzCopy" and pressing Enter to show its documentation.
1. Issue the following commands to copy all of the sample data, pretrained models, and model training scripts to the appropriate locations in your storage account:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Expect file transfer to take up to 20 minutes. While you wait, you can proceed to the following section. You may need to open another Command Line Interface through Workbench and redefine the temporary variables there.

#### Create the HDInsight Spark cluster

Our recommended method to create an HDInsight cluster uses the HDInsight Spark cluster Resource Manager template included in the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project.

1. The HDInsight Spark cluster template is the "template.json" file under the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project. By default, the template creates a Spark cluster with 40 worker nodes. If you must adjust that number, open the template in your favorite text editor and replace all instances of "40" with the worker node number of your choice.
    - You may encounter out-of-memory errors if the number of worker nodes you choose is small. To combat memory errors, you may run the training and operationalization scripts on a subset of the available data as described later in this document.
2. Choose a unique name and password for the HDInsight cluster and write them where indicated in the following command. Then create the cluster by issuing the following command:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Your cluster's deployment may take up to 30 minutes (including provisioning and script action execution).

### Prepare the Azure Machine Learning Workbench execution environment

#### Register the HDInsight cluster as an Azure Machine Learning Workbench compute target

Once HDInsight cluster creation is complete, register the cluster as a compute target for your project as follows:

1.  Issue the following command from the Azure Machine Learning Command Line Interface:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    This command adds two files, `myhdi.runconfig` and `myhdi.compute`, to your project's `aml_config` folder.

1. Open the `myhdi.compute` file in your favorite text editor. Modify the `yarnDeployMode: cluster` line to read `yarnDeployMode: client`, then save and close the file.
1. Run the following command to prepare your environment for use:
   ```
   az ml experiment prepare -c myhdi
   ```

#### Install local dependencies

Open a CLI from Azure Machine Learning Workbench and install dependencies needed for local execution by issuing the following command:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## Data acquisition and understanding

This scenario uses publicly available aerial imagery data from the [National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) at 1-meter resolution. We have generated sets of 224 pixel x 224 pixel PNG files cropped from the original NAIP data and sorted according to land use labels from the [National Land Cover Database](https://www.mrlc.gov/nlcd2011.php). A sample image with label "Developed" is shown at full size:

![A sample tile of developed land](media/scenario-aerial-image-classification/sample-tile-developed.png)

Class-balanced sets of ~44k and 11k images are used for model training and validation, respectively. We demonstrate model deployment on a ~67k image set tiling Middlesex County, MA -- home of Microsoft's New England Research and Development (NERD) center. For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Location of Middlesex County, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

During setup, the aerial image sets used in this example were transferred to the storage account that you created. The training, validation, and operationalization images are all 224 pixel x 224 pixel PNG files at a resolution of one pixel per square meter. The training and validation images have been organized into subfolders based on their land use label. (The land use labels of the operationalization images are unknown and in many cases ambiguous; some of these images contain multiple land types.) For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

To view example images in your Azure storage account (optional):
1. Log in to the [Azure portal](https://portal.azure.com).
1. Search for the name of your storage account in the search bar along the top of your screen. Click on your storage account in the search results.
2. Click on the "Blobs" link in the storage account's main pane.
3. Click on the container named "train." You should see a list of directories named according to land use.
4. Click on any of these directories to load the list of images it contains.
5. Click on any image and download it to view the image.
6. If desired, click on the containers named "test" and "middlesexma2016" to view their contents as well.

## Modeling

### Training models with MMLSpark
The `run_mmlspark.py` script in the "Code\02_Modeling" subfolder of the Workbench project is used to train an [MMLSpark](https://github.com/Azure/mmlspark) model for image classification. The script first featurizes the training set images using an image classifier DNN pretrained on the ImageNet dataset (either AlexNet or an 18-layer ResNet). The script then uses the featurized images to train an MMLSpark model (either a random forest or a logistic regression model) to classify the images. The test image set is then featurized and scored with the trained model. The accuracy of the model's predictions on the test set is calculated and logged to Azure Machine Learning Workbench's run history feature. Finally, the trained MMLSpark model and its predictions on the test set are saved to blob storage.

Select a unique output model name for your trained model, a pretrained model type, and an MMLSpark model type. Write your selections where indicated in the following command template, then begin retraining by executing the command from an Azure ML Command Line Interface:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

An additional `--sample_frac` parameter can be used to train and test the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of trained model accuracy. For more information on this and other parameters, run `python Code\02_Modeling\run_mmlspark.py -h`.

Users are encouraged to run this script several times with different input parameters. The performance of the resulting models can then be compared in Azure Machine Learning Workbench's Run History feature.

### Comparing model performance using the Workbench Run History feature

After you have executed two or more training runs of either type, navigate to the Run History feature in Workbench by clicking the clock icon along the left-hand menu bar. Select `run_mmlspark.py` from the list of scripts at left. A pane loads comparing the test set accuracy for all runs. To see more detail, scroll down and click on the name of an individual run.

## Deployment

To apply one of your trained models to aerial images tiling Middlesex County, MA using remote execution on HDInsight, insert your desired model's name into the following command and execute it:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

An additional `--sample_frac` parameter can be used to operationalize the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of prediction completeness. For more information on this and other parameters, run `python Code\03_Deployment\batch_score_spark -h`.

This script writes the model's predictions to your storage account. The predictions can be examined as described in the next section.

## Visualization

The "Model prediction analysis" Jupyter notebook in the "Code\04_Result_Analysis" subfolder of the Workbench project visualizes a model's predictions. Load and run the notebook as follows:
1. Open the project in Workbench and click on the folder ("Files") icon along the left-hand menu to load the directory listing.
2. Navigate to the "Code\04_Result_Analysis" subfolder and click on the notebook named "Model prediction analysis." A preview rendering of the notebook should be displayed.
3. Click "Start Notebook Server" to load the notebook.
4. In the first cell, enter the name of the model whose results you would like to analyze where indicated.
5. Click on "Cell -> Run All" to execute all cells in the notebook.
6. Read along with the notebook to learn more about the analyses and visualizations it presents.

## Cleanup
When you have completed the example, we recommend that you delete all of the resources you have created by executing the following command from the Azure Command Line Interface:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## References

- [The Embarrassingly Parallel Image Classification repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Describes dataset construction from freely available imagery and labels
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub repository
   - Contains additional examples of model training and evaluation with MMLSpark

## Conclusions

Azure Machine Learning Workbench helps data scientists easily deploy their code on remote compute targets. In this example, local code was deployed for remote execution on an HDInsight cluster. Azure Machine Learning Workbench's run history feature tracked the performance of multiple models and helped us identify the most accurate model. Workbench's Jupyter notebooks feature helped visualize our models' predictions in an interactive, graphical environment.

## Next steps
To dive deeper into this example:
- In Azure Machine Learning Workbench's Run History feature, click the gear symbols to select which graphs and metrics are displayed.
- Examine the sample scripts for statements calling the `run_logger`. Check that you understand how each metric is being recorded.
- Examine the sample scripts for statements calling the `blob_service`. Check that you understand how trained models and predictions are stored and retrieved from the cloud.
- Explore the contents of the containers created in your blob storage account. Ensure that you understand which script or command is responsible for creating each group of files.
- Modify the training script to train a different MMLSpark model type or to change the model hyperparameters. Use the run history feature to determine whether your changes increased or decreased the model's accuracy.

