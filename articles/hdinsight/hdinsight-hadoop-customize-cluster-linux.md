<properties
	pageTitle="Personalizzare cluster HDInsight mediante Azione di script | Microsoft Azure"
	description="Informazioni su come aggiungere componenti personalizzati in cluster HDInsight basati su Linux usando azioni di script. Le azioni di script sono script Bash eseguiti durante la creazione del cluster e possono essere usate per personalizzare la configurazione del cluster o aggiungere nuovi servizi o utilità, quali Hue, Solr o R."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="larryfr"/>

# Personalizzare cluster HDInsight basati su Linux tramite Azione script

[AZURE.INCLUDE [selettore](../../includes/hdinsight-create-windows-cluster-selector.md)]

HDInsight offre un'opzione di configurazione denominata **Azione di script** in grado di chiamare script personalizzati che definiscono la personalizzazione da eseguire nel cluster durante il processo di creazione. Questi script possono essere usati per installare altro software in un cluster o per modificare la configurazione delle applicazioni in un cluster.

> [AZURE.NOTE] Le informazioni contenute in questo articolo sono specifiche per i cluster HDInsight basati su Linux. Per una versione di questo articolo specifica per i cluster basati su Windows, vedere [Personalizzare cluster HDInsight basati su Windows tramite Azione script (Windows)](hdinsight-hadoop-customize-cluster.md).

## Azione di script nel processo di creazione di cluster

L'opzione Azione di script viene usata solo mentre è in corso il processo di creazione di un cluster. Il diagramma seguente illustra quando l'opzione Azione di script viene eseguita durante il processo di creazione:

![Personalizzazione di cluster HDInsight e fasi durante la creazione di un cluster][img-hdi-cluster-states]

Lo script viene eseguito durante la configurazione di HDInsight. In questa fase, lo script viene eseguito in parallelo su tutti i nodi specificati nel cluster e con privilegi a livello radice sui nodi.

> [AZURE.NOTE] Poiché durante l'esecuzione dello script si hanno privilegi a livello radice sui nodi del cluster, è possibile eseguire operazioni quali l'arresto e l'avvio dei servizi, inclusi quelli correlati ad Hadoop. Se si arrestano i servizi, è necessario assicurarsi che i servizi di Ambari e altri servizi correlati ad Hadoop siano attivi prima che termini l'esecuzione dello script. Questi servizi sono necessari per verificare correttamente l'integrità e lo stato del cluster durante la creazione.

Ogni cluster può accettare più azioni di script che vengono richiamate nell'ordine in cui sono specificate. Uno script può essere eseguito sui nodi head, sui nodi di lavoro o entrambi.

> [AZURE.IMPORTANT] Le azioni di script devono essere completate entro 60 minuti; in caso contrario si verifica un timeout. Durante il provisioning dei nodi, lo script viene eseguito contemporaneamente ad altri processi di installazione e configurazione. In caso di concorrenza per risorse come il tempo di CPU o la larghezza di banda di rete, lo script può richiedere più tempo per completare l'operazione rispetto al tempo che impiegherebbe in un ambiente di sviluppo.
> 
> Per ridurre al minimo il tempo necessario per eseguire lo script, evitare attività come il download e la compilazione di applicazioni dall'origine. Al contrario, precompilare l'applicazione e archiviare il file binario nell'archivio BLOB di Azure in modo che possa essere rapidamente scaricato nel cluster.


## Script di Azione di script di esempio

Gli script di Azione script possono essere usati dal portale di Azure, da Azure PowerShell o da HDInsight .NET SDK. In questo articolo viene illustrato come usare Azione di script dal portale. Per informazioni su come usare PowerShell e .NET SDK per usare Azione di script, esaminare gli esempi elencati nella tabella seguente.

HDInsight fornisce diversi script di esempio per installare i componenti seguenti nei cluster HDInsight:

Nome | Script
----- | -----
**Installare Hue.** | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Vedere [Installare e usare Hue in cluster HDInsight](hdinsight-hadoop-hue-linux.md).
**Installare Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh. Vedere [Installare e usare Spark in cluster Hadoop di HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Installare R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Vedere [Installare e usare R nei cluster Hadoop HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Installare Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Vedere [Installare e usare Solr in cluster HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Installare Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Vedere [Installare e usare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md).

## Usare un'azione di script dal portale di Azure

1. Avviare la creazione di un cluster come descritto in [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md#portal).

2. In __Configurazione facoltativa__ fare clic su **aggiungi azione di script** nel pannello **Azioni di script** per specificare i dettagli relativi all'azione di script, come illustrato di seguito:

	![Usare l'azione di script per personalizzare un cluster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png)

	| Proprietà | Valore |
	| -------- | ----- |
	| Nome | Specificare un nome per l'azione di script. |
	| URI script | Specificare l'URI dello script da richiamare per personalizzare il cluster. |
	| Head/Lavoro | Specificare i nodi (**Head**, **Lavoro** o **ZooKeeper**) su cui viene eseguito lo script di personalizzazione. |
	| Parametri | Specificare i parametri, se richiesti dallo script. |

	È possibile aggiungere altre azioni di script per installare più componenti nel cluster.

3. Fare clic su **Seleziona** per salvare la configurazione dell'azione di script e continuare con la creazione del cluster.

## Usare Azione di script dai modelli di Gestione risorse di Azure

In questa sezione si useranno i modelli di Gestione risorse di Azure (ARM) per creare un cluster HDInsight, oltre a un'azione di script per installare nel cluster componenti personalizzati (in questo esempio, R). Questa sezione include un modello ARM di esempio per creare un cluster con un'azione di script.

### Prima di iniziare

* Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
* Per istruzioni su come creare modelli ARM, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).
* Se Azure PowerShell non è stato usato in precedenza con Gestione risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).

### Creare cluster mediante l'azione di script

1. Copiare il modello seguente in un percorso di questo computer. Questo modello installa R nel nodo head e nei nodi di lavoro del cluster. È anche possibile verificare se il modello JSON è valido. Incollare il contenuto del modello in [JSONLint](http://jsonlint.com/), uno strumento di convalida JSON disponibile online.

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "username"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",
		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}

2. Avviare Azure PowerShell e accedere al proprio account Azure. Una volta specificate le credenziali, il comando restituisce le informazioni relative all'account.

		Add-AzureRmAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione che si desidera usare per la distribuzione.

		Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] È possibile usare `Get-AzureRmSubscription` per ottenere un elenco di tutte le sottoscrizioni associate all'account, con il relativo ID sottoscrizione.

5. Se non è presente un gruppo di risorse, crearne uno nuovo. Specificare il nome del gruppo di risorse e il percorso per la soluzione. Viene restituito un riepilogo del nuovo gruppo di risorse.

		New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando **New-AzureResourceGroupDeployment** e specificare i parametri necessari. I parametri includeranno un nome per la distribuzione, il nome del gruppo di risorse e il percorso o l'URL del modello creato. Passare anche gli eventuali altri parametri richiesti dal modello. In questo caso, l'azione di script per installare R nel cluster non richiede parametri.


		New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Verrà richiesto di fornire i valori per i parametri definiti nel modello.

7. Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. Se la distribuzione non riesce, è possibile usare i cmdlet seguenti per ottenere informazioni relative agli errori.

		Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

## Usare un'azione di script da Azure PowerShell

In questa sezione si userà il cmdlet [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) per richiamare script usando l'azione di script per personalizzare un cluster. Prima di procedere, assicurarsi di aver installato e configurato Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione di cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Eseguire la procedura seguente:

1. Aprire la console di Azure PowerShell e dichiarare le variabili seguenti:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionId = "<SubscriptionId>"		# ID of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
        $resourceGroupName = "<ResourceGroupName>"      # The resource group that the HDInsight cluster will be created in

2. Specificare i valori di configurazione, ad esempio i nodi del cluster e l'archivio predefinito da usare.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureRmSubscription -SubscriptionId $subscriptionId
		$config = New-AzureRmHDInsightClusterConfig
		$config.DefaultStorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccountKey=$storageAccountKey

3. Usare il cmdlet **Add-AzureRmHDInsightScriptAction** per richiamare lo script. L'esempio seguente usa uno script che installa R nel cluster:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION FOR HEADNODE AND WORKERNODE
		$config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
        $config = Add-AzureRmHDInsightScriptAction -Config $config -Name "Install R"  -NodeType WorkerNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

	Il cmdlet **Add-AzureRmHDInsightScriptAction** accetta i parametri seguenti:

	| Parametro | Definizione |
	| --------- | ---------- |
	| Config | Oggetto di configurazione a cui vengono aggiunte le informazioni dell'azione di script. |
	| Nome | Nome dell'azione di script. |
	| Tipo di nodo | Specifica il nodo su cui viene eseguito lo script di personalizzazione. I valori validi sono **HeadNode** (per l'installazione nel nodo head) o **WorkerNode** (per l'installazione in tutti i nodi dati) o **ZookeeperNode** (per l'installazione nel nodo zookeeper). |
	| Parametri | Parametri richiesti dallo script. |
	| Uri | Specifica l'URI per lo script eseguito. |

4. Impostare l'utente amministratore/HTTPS per il cluster:

        $httpCreds = get-credential
        
    Quando richiesto, immettere "admin" come nome e specificare una password.

5. Impostare le credenziali SSH:

        $sshCreds = get-credential
    
    Quando richiesto, immettere il nome utente e la password SSH. Se si desidera proteggere l'account SSH con un certificato anziché una password, usare una password vuota e impostare `$sshPublicKey` sul contenuto della chiave pubblica del certificato che si desidera usare. ad esempio:
    
        $sshPublicKey = Get-Content .\path\to\public.key -Raw
    
4. Al termine, creare il cluster:
        
        New-AzureRmHDInsightCluster -config $config -clustername $clusterName -DefaultStorageContainer $containerName -Location $location -ResourceGroupName $resourceGroupName -ClusterSizeInNodes $clusterNodes -HttpCredential $httpCreds -SshCredential $sshCreds -OSType Linux
    
    Se si usa una chiave pubblica per proteggere l'account SSH, è necessario anche specificare `-SshPublicKey $sshPublicKey` come parametro.

La creazione del cluster può richiedere alcuni minuti.

## Usare Azione di script da HDInsight .NET SDK

HDInsight .NET SDK fornisce librerie client che semplificano l'uso di HDInsight da un'applicazione .NET. Per un esempio di codice, vedere [Creare cluster basati su Linux in HDInsight tramite .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action).


## Risoluzione dei problemi

È possibile usare l'interfaccia utente Web Ambari per visualizzare le informazioni registrate dagli script durante la creazione del cluster. Se la creazione del cluster non è riuscita a causa di un errore nello script, tuttavia, i registri risultano disponibili anche nell'account di archiviazione predefinito associato al cluster. Questa sezione fornisce informazioni su come recuperare i registri usando entrambe le opzioni.

### Utilizzo dell'interfaccia utente Web Ambari

1. Nel browser passare a https://CLUSTERNAME.azurehdinsight.net. Sostituire CLUSTERNAME con il nome del cluster HDInsight.

	Quando richiesto, immettere il nome dell'account amministratore (admin) e la password per il cluster. Potrebbe essere necessario immettere di nuovo le credenziali di amministratore in un Web Form.

2. Nella barra in alto nella pagina fare clic sulla voce __ops__. Verrà visualizzato un elenco delle operazioni correnti e precedenti eseguite nel cluster tramite Ambari.

	![Barra nell'interfaccia utente di Ambari con selezionato ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. Trovare le voci con __run\_customscriptaction__ nella colonna __Operations__. Vengono create quando si eseguono le azioni di script.

	![Schermata delle operazioni](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Selezionare questa voce ed eseguire il drill-down dei collegamenti per visualizzare l'output di STDOUT e STDERR generato durante l'esecuzione dello script sul cluster.

### Accesso ai registri dall'account di archiviazione predefinito

Se la creazione del cluster non è riuscita a causa di un errore nell'azione di script, è possibile accedere ai registri dell'azione di script direttamente dall'account di archiviazione predefinito associato al cluster.

* I registri di archiviazione sono disponibili in `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`. 

	![Schermata delle operazioni](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

	In questo percorso i registri sono organizzati per nodi head, nodi di lavoro e nodi zookeeper. Alcuni esempi sono: * **Nodo head** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net` * **Nodo di lavoro** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net` * **Nodo zookeeper** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* Tutti i file stdout e stderr dell'host corrispondente vengono caricati nell'account di archiviazione. Per ogni azione di script esiste un file **output-*.txt** e uno **errors-*.txt**. Il file output-*.txt contiene informazioni relative all'URI dello script che è stato eseguito nell'host. Ad esempio

		'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* È possibile creare più volte un cluster dell'azione di script con lo stesso nome. In questo caso, è possibile distinguere i registri corrispondenti in base al nome della cartella della data. Ad esempio, la struttura di cartelle per un cluster (mycluster) creato in diverse date sarà:
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04`
	* `\STORAGE_ACOCUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se in uno stesso giorno si creano più cluster dell'azione di script con lo stesso nome, è possibile usare il prefisso univoco per identificare i file di registro corrispondenti.

* Se si crea un cluster alla fine del giorno, è possibile che i file di registro si estendano su due giorni. In questo caso, per lo stesso cluster vengono visualizzate due diverse cartelle della data.

* Il caricamento dei file di registro nel contenitore predefinito può richiedere fino a 5 minuti, soprattutto per i cluster di grandi dimensioni. Se si desidera accedere ai file di registro, quindi, è opportuno non eliminare immediatamente il cluster in caso di esito negativo di un'azione di script.


## Supporto per software open source usato nei cluster HDInsight

Il servizio Microsoft Azure HDInsight è una piattaforma flessibile che permette di creare applicazioni Big Data nel cloud usando un ecosistema di tecnologie open source basate su Hadoop. Microsoft Azure fornisce un livello di supporto generale per le tecnologie open source, come illustrato nella sezione relativa all'**ambito del supporto** del [sito Web Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/). Il servizio HDInsight fornisce un livello di supporto aggiuntivo per alcuni componenti, come illustrato di seguito.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

- **Componenti predefiniti** - Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. Questa categoria include ad esempio il gestore risorse YARN, il linguaggio di query Hive (HiveQL) e la libreria Mahout. L'elenco completo dei componenti del cluster è disponibile in [Novità delle versioni cluster di Hadoop incluse in HDInsight](hdinsight-component-versioning.md).

- **Componenti personalizzati** - Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

> [AZURE.WARNING] I componenti forniti con il cluster HDInsight sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.
>
> I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile ricorrere a molti siti di community, come il [forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/it-IT/home?forum=hdinsight) o [http://stackoverflow.com](http://stackoverflow.com). Per i progetti Apache sono inoltre disponibili siti specifici in [http://apache.org](http://apache.org), ad esempio [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui un componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. L'elenco seguente illustra i modi più comuni per usare i componenti personalizzati nei cluster HDInsight:

1. Invio di processi - È possibile inviare al cluster processi Hadoop o di altro tipo che eseguono o usano componenti personalizzati.

2. Personalizzazione del cluster - Durante la creazione di un cluster, è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.

3. Esempi - Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.

## Passaggi successivi

Per informazioni ed esempi sulla creazione e l'uso di script per personalizzare un cluster, vedere gli argomenti seguenti:

- [Sviluppare script di Azione di script per HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Installare e usare Spark nei cluster HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Installare e usare R nei cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fasi durante la creazione di un cluster"

<!---HONumber=AcomDC_0128_2016-->