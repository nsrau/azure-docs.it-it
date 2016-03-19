
<properties
	pageTitle="Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight | Microsoft Azure"
	description="Informazioni sullo sviluppo di programmi MapReduce per la creazione di flussi Hadoop in C# e sulla distribuzione in Azure HDInsight."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="jgao"/>



# Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight

In Hadoop è disponibile un'API di flusso per MapReduce che consente di scrivere funzioni di mapping e riduzione in linguaggi diversi da Java. Questa esercitazione illustra in modo dettagliato la creazione di un programma C# per il conteggio delle parole, che conta le occorrenze di una parola specifica nei dati di input forniti. La figura seguente mostra in che modo il framework MapReduce esegue un conteggio delle parole:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

> [AZURE.NOTE] La procedura descritta in questo articolo è valida solo per i cluster Azure HDInsight basati su Windows. Per un esempio di flusso per cluster HDInsight basati su Linux, vedere [Sviluppare programmi per la creazione di flussi Python per HDInsight](hdinsight-hadoop-streaming-python.md).

Questa esercitazione illustra come:

- Sviluppare programmi MapReduce per la creazione di flussi Hadoop mediante C# 
- Eseguire il processo MapReduce in Azure HDInsight
- Recuperare i risultati del processo MapReduce

###Prerequisiti

Prima di iniziare questa esercitazione, è necessario eseguire le operazioni seguenti:

- Una workstation con [Azure PowerShell][powershell-install] e [Microsoft Visual Studio](https://www.visualstudio.com/).
- Ottenere una sottoscrizione di Azure. Per istruzioni, vedere [Opzioni di acquisto][azure-purchase-options], [Offerte per i membri][azure-member-offers] oppure [Versione di valutazione gratuita][azure-free-trial].


##Sviluppare un programma per la creazione di flussi Hadoop in C&#35 per il conteggio delle parole;

La soluzione per il conteggio delle parole include due progetti di applicazione console: l'applicazione per il mapping (mapper) e quella per la riduzione (reducer). L'applicazione mapper trasmette in flusso ogni parola nella console e l'applicazione reducer conta il numero di parole trasmesse in flusso da un documento. Sia mapper sia reducer leggono i caratteri, riga per riga, dal flusso di input standard (stdin) e scrivono nel flusso di output standard (stdout).

**Per creare il programma mapper**

1. Aprire Visual Studio e creare un'applicazione console C# denominata **WordCountMapper**.
2. In Esplora soluzioni rinominare **Program.cs** in **WordCountMapper.cs**. Fare clic su **Sì** per confermare l'assegnazione del nuovo nome a tutti i riferimenti.
3. Sostituire il codice in WordCountMapper.cs con quanto segue:

        using System;
        using System.IO;

        namespace WordCountMapper
        {
            class WordCountMapper
            {
                static void Main(string[] args)
                {
                    if (args.Length > 0)
                    {
                        Console.SetIn(new StreamReader(args[0]));
                    }

                    string line;
                    string[] words;

                    while ((line = Console.ReadLine()) != null)
                    {
                        words = line.Split(' ');

                        foreach (string word in words)
                            Console.WriteLine(word.ToLower());
                    }
                }
            }
        }

4. Compilare la soluzione e verificare che non siano presenti errori di compilazione.

**Per creare il programma reducer**

1. Aggiungere un'altra applicazione console C# alla soluzione denominata **WordCountReducer**". Percorso|C:\\Tutorials\\WordCount
2. In Esplora soluzioni rinominare **Program.cs** in **WordCountReducer.cs**. Fare clic su **Sì** per confermare l'assegnazione del nuovo nome a tutti i riferimenti.
3. Sostituire il codice in WordCountReducer.cs con quanto segue:

        using System;
        using System.IO;

        namespace WordCountReducer
        {
            class WordCountReducer
            {
                static void Main(string[] args)
                {
                    string word, lastWord = null;
                    int count = 0;

                    if (args.Length > 0)
                    {
                        Console.SetIn(new StreamReader(args[0]));
                    }

                    while ((word = Console.ReadLine()) != null)
                    {
                        if (word != lastWord)
                        {
                            if (lastWord != null)
                                Console.WriteLine("{0}[{1}]", lastWord, count);

                            count = 1;
                            lastWord = word;
                        }
                        else
                        {
                            count += 1;
                        }
                    }
                    Console.WriteLine(count);
                }
            }
        }

4. Compilare la soluzione e verificare che non siano presenti errori di compilazione.

Si otterranno i file eseguibili dei programmi di mapping e riduzione.

- ..\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
- ..\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe


##Caricare dati nell'archivio BLOB di Azure
Azure HDInsight utilizza l'archivio BLOB di Azure come file system predefinito. È possibile configurare un cluster HDInsight per l'utilizzo di archiviazione BLOB aggiuntiva per i file di dati. In questa sezione verrà creato un account di archiviazione di Azure e i file di dati verranno caricati nell'archivio BLOB. I file di dati sono file con estensione txt disponibili nella directory %hadoop\_home%\\share\\doc\\hadoop\\common.


**Per creare un account di archiviazione e un contenitore**

1. Aprire Azure PowerShell.
2. Impostare le variabili, quindi eseguire i comandi:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Eseguire i comandi seguenti per creare un account di archiviazione e un contenitore di archiviazione BLOB nell'account:

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Eseguire i comandi seguenti per verificare l'account e il contenitore di archiviazione:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**Per caricare i file di dati**

1. Nella finestra di Azure PowerShell impostare i valori per la cartella locale e quella di destinazione:

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Si noti che la cartella di file di origine locale è **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** e la cartella di destinazione è **WordCount/Input**. Il percorso di origine è il percorso dei file txt nell'emulatore HDInsight. La destinazione è la struttura di cartelle che sarà rispecchiata nel contenitore BLOB di Azure.

3. Eseguire i comandi seguenti per ottenere un elenco dei file con estensione txt disponibili nella cartella di file di origine:

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

5. Eseguire il frammento seguente per copiare i file:

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Eseguire il comando seguente per ottenere un elenco dei file caricati:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**Per caricare le applicazioni per il conteggio delle parole**

1. Nella finestra di Azure PowerShell impostare le variabili seguenti:

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Si noti che la cartella di destinazione è **WordCount/Apps**, che è la struttura che sarà rispecchiata nel contenitore BLOB di Azure.

2. Eseguire i comandi seguenti per copiare le applicazioni:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. Eseguire il comando seguente per ottenere un elenco dei file caricati:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	Verranno elencati entrambi i file dell'applicazione.


##Eseguire il processo MapReduce in Azure HDInsight

Questa sezione fornisce uno script di Azure PowerShell che esegue tutte le attività correlate all'esecuzione di un processo MapReduce. L'elenco di attività include:

1. Provisioning di un cluster HDInsight

	1. Creazione di un account di archiviazione che verrà usato come file system predefinito del cluster HDInsight
	2. Creazione di un contenitore di archiviazione BLOB
	3. Creare un cluster HDInsight

2. Invio del processo MapReduce

	1. Creazione di una definizione del processo MapReduce di streaming
	2. Invio di un processo MapReduce
	3. Attesa del completamento del processo
	4. Visualizzazione degli errori standard
	5. Visualizzazione dell'output standard

3. Eliminazione del cluster

	1. Eliminazione del cluster HDInsight
	2. Eliminazione dell'account di archiviazione usato come file system predefinito del cluster HDInsight


**Per eseguire lo script di Azure PowerShell**

1. Aprire il Blocco note.
2. Copiare e incollare il codice seguente:

		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### Prefix to cluster, Storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data storage account location
		$clusterNodes = 1

		$clusterName = $stringPrefix + "hdicluster"

		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

		Select-AzureSubscription $subscriptionName

		#====== CREATE A STORAGE ACCOUNT ======
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

		#====== CREATE A BLOB STORAGE CONTAINER ======
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

		New-AzureStorageContainer -Name $containerName_Default -Context $destContext

		#====== CREATE AN HDINSIGHT CLUSTER ======
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

		Select-AzureSubscription $subscriptionName
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config

		#====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

		#====== RUN A STREAMING MAPREDUCE JOB ======
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600

		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

		#====== DELETE THE HDINSIGHT CLUSTER ======
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		Remove-AzureHDInsightCluster -Name $clusterName

		#====== DELETE THE STORAGE ACCOUNT ======
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Impostare le prime quattro variabili nello script. La variabile **$stringPrefix** verrà usata per aggiungere la stringa specificata come prefisso del nome del cluster HDInsight, del nome dell'account di archiviazione e del nome del contenitore di archiviazione BLOB. Poiché la lunghezza di questi nomi deve essere compresa tra 3 e 24 caratteri, assicurarsi che la stringa specificata e i nomi usati dallo script non superino, una volta combinati, il limite di caratteri fissato per il nome. Per **$stringPrefix** è necessario usare tutte lettere minuscole.

	Le variabili **$storageAccountName\_Data** e **$containerName\_Data** corrispondono all'account e al contenitore di archiviazione già creati nei passaggi precedenti, quindi è necessario specificarne i nomi. Vengono usati per archiviare i file di dati e le applicazioni. La variabile **$location** deve corrispondere alla posizione dell'account di archiviazione dei dati.

4. Esaminare le variabili rimanenti.
5. Salvare il file di script.
6. Aprire Azure PowerShell.
7. Eseguire il comando seguente per impostare i criteri di esecuzione su RemoteSigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Quando richiesto, immettere nome utente e password per il cluster HDInsight. Assicurarsi che la password sia composta da almeno 10 caratteri, inclusi una lettera maiuscola, una lettera minuscola, un numero e un carattere speciale. Se non si vuole che venga richiesta l'immissione delle credenziali, vedere [Utilizzo di password, stringhe sicure e credenziali in Windows PowerShell][powershell-PSCredential].

Per un esempio di HDInsight .NET SDK sull'invio di processi per la creazione di flussi Hadoop, vedere [Inviare processi Hadoop a livello di codice][hdinsight-submit-jobs].


##Recupero dell'output del processo MapReduce
In questa sezione viene illustrato come scaricare e visualizzare l'output. Per informazioni sulla visualizzazione dei risultati in Excel, vedere [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver][hdinsight-ODBC] e [Connettere Excel a HDInsight mediante Power Query][hdinsight-power-query].


**Per recuperare l'output**

1. Aprire la finestra di Azure PowerShell.
2. Impostare i valori e quindi eseguire i comandi:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"

3. Per creare un oggetto contesto di Archiviazione di Azure, eseguire i comandi seguenti:

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4. Eseguire i comandi seguenti per scaricare e visualizzare l'output:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"



##Passaggi successivi
In questa esercitazione si è appreso come sviluppare un processo MapReduce di creazione di flussi Hadoop, come testare l'applicazione in HDInsight Emulator e come scrivere uno script di Azure PowerShell per effettuare il provisioning di un cluster HDInsight ed eseguire un processo MapReduce nel cluster. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione ad Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Introduzione a HDInsight Emulator][hdinsight-get-started-emulator]
- [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]
- [Usare l'archivio BLOB di Azure con HDInsight][hdinsight-storage]
- [Amministrare HDInsight tramite Azure PowerShell][hdinsight-admin-powershell]
- [Caricare dati in HDInsight][hdinsight-upload-data]
- [Usare Hive con HDInsight][hdinsight-use-hive]
- [Usare Pig con HDInsight][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "Flusso dell'applicazione per il conteggio di parole MapReduce"

<!---HONumber=AcomDC_0218_2016-->