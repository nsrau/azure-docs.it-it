<properties 
	pageTitle="Personalizzare cluster HDInsight mediante le azioni script | Microsoft Azure"
	description="Informazioni su come personalizzare cluster HDInsight tramite le azioni script."
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
	ms.date="08/21/2015"
	ms.author="larryfr"/>

# Personalizzare i cluster HDInsight mediante l'azione script

HDInsight offre un'opzione di configurazione denominata **Azione script** in grado di richiamare script personalizzati che definiscono la personalizzazione da apportare nel cluster durante il processo di provisioning. Questi script possono essere usati per installare altro software in un cluster o per modificare la configurazione delle applicazioni in un cluster.

> [AZURE.NOTE]Le informazioni contenute in questo articolo sono specifiche per i cluster HDInsight basati su Linux. Per una versione di questo articolo specifica per i cluster basati su Windows, vedere [Personalizzare i cluster HDInsight mediante l'azione script (Windows)](hdinsight-hadoop-customize-cluster-linux.md).

## Azione script nel processo di provisioning di cluster

Azione script viene usato solo mentre è in corso il processo di creazione di un cluster. Il diagramma seguente illustra quando Azione script viene eseguito durante il processo di provisioning:

![Personalizzazione di cluster HDInsight e fasi durante il provisioning di un cluster][img-hdi-cluster-states]

Lo script viene eseguito durante la configurazione di HDInsight. In questa fase, lo script viene eseguito in parallelo su tutti i nodi specificati nel cluster e con privilegi a livello radice sui nodi.

> [AZURE.NOTE]Poiché durante l'esecuzione dello script si hanno privilegi a livello radice sui nodi del cluster, è possibile eseguire operazioni quali l'arresto e l'avvio dei servizi, inclusi quelli correlati ad Hadoop. Se si arrestano i servizi, è necessario assicurarsi che i servizi di Ambari e altri servizi correlati ad Hadoop siano attivi prima che termini l'esecuzione dello script. Questi servizi sono necessari per verificare correttamente l'integrità e lo stato del cluster durante la creazione.

Ogni cluster può accettare più azioni script che vengono richiamate nell'ordine in cui sono specificate. Uno script può essere eseguito sui nodi head, sui nodi di lavoro o entrambi.

> [AZURE.IMPORTANT]Le azioni script devono essere completate entro 15 minuti; in caso contrario si verifica un timeout.

## Script di Azioni script di esempio

Gli script di Azione script possono essere usati dal portale di anteprima di Azure, da Azure PowerShell o da HDInsight .NET SDK. In questo articolo viene illustrato come utilizzare Azione di script dal portale. Per informazioni su come utilizzare PowerShell e .NET SDK per utilizzare Azione di script, esaminare gli esempi elencati nella tabella seguente.

HDInsight fornisce diversi script di esempio per installare i componenti seguenti nei cluster HDInsight:

Nome | Script
----- | -----
**Installare Hue**. | https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh. Vedere [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md).
**Installare Spark** | https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv01/spark-installer-v01.sh. Vedere [Installare e usare Spark nei cluster HDInsight](hdinsight-hadoop-spark-install-linux.md).
**Installare R** | https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh. Vedere [Installare e usare R nei cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md).
**Installare Solr** | https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh. Vedere [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md).
**Installare Giraph** | https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh. Vedere [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md).

##Usare un'azione script dal portale di anteprima di Azure

1. Avviare il provisioning di un cluster come descritto in [Effettuare il provisioning di un cluster con opzioni personalizzate](hdinsight-provision-clusters.md#portal). 

2. In __Configurazione facoltativa__ fare clic su **aggiungi azione script** nel pannello **Azioni script** per specificare i dettagli relativi all'azione script, come illustrato di seguito:

	![Usare l'azione script per personalizzare un cluster](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "Usare l'azione script per personalizzare un cluster")
	
	| Proprietà | Valore |
	| -------- | ----- |
	| Nome | Specificare un nome per l'azione script. |
	| URI script | Specificare l'URI dello script da richiamare per personalizzare il cluster. |
	| Head/ruolo di lavoro | Specificare i nodi (**Head**, **Lavoro** o **ZooKeeper**) su cui viene eseguito lo script di personalizzazione. |
	| Parametri | Specificare i parametri, se richiesti dallo script. |

	È possibile aggiungere altre azioni script per installare più componenti nel cluster.

3. Fare clic su **Seleziona** per salvare la configurazione dell'azione script e continuare con il provisioning del cluster.

##Usare un'azione script dai modelli di Gestione risorse di Azure

In questa sezione si useranno i modelli di Gestione risorse di Azure (ARM) per effettuare il provisioning di un cluster HDInsight, oltre a un'azione script per installare nel cluster i componenti personalizzati, in questo esempio R. Questa sezione include un modello ARM di esempio per effettuare il provisioning di un cluster con un'azione script.

### Prima di iniziare

* Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). 
* Per istruzioni su come creare modelli ARM, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md). 
* Se Azure PowerShell non è stato usato in precedenza con Gestione risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](powershell-azure-resource-manager).

### Effettuare il provisioning di un cluster con un'azione script

1. Copiare il modello seguente in un percorso di questo computer. Questo modello installa R nel nodo head e nei nodi di lavoro del cluster. È anche possibile verificare se il modello JSON è valido. Incollare il modello di contenuto in [JSONLint](http://jsonlint.com/), uno strumento di convalida JSON disponibile online.

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
		            "defaultValue": "hdiuser"
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

		Add-AzureAccount
	
		Id                             Type       ...
		--                             ----    
		someone@example.com            User       ...   

3. Se si hanno più sottoscrizioni, specificare l'ID sottoscrizione che si desidera usare per la distribuzione.

		Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

4. Passare al modulo Gestione risorse di Azure.

		Switch-AzureMode AzureResourceManager

5. Se non è presente un gruppo di risorse, crearne uno nuovo. Specificare il nome del gruppo di risorse e il percorso per la soluzione. Viene restituito un riepilogo del nuovo gruppo di risorse.

		New-AzureResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando **New-AzureResourceGroupDeployment** e specificare i parametri necessari. I parametri includeranno un nome per la distribuzione, il nome del gruppo di risorse e il percorso o l'URL del modello creato. Passare anche gli eventuali altri parametri richiesti dal modello. In questo caso, l'azione script per installare R nel cluster non richiede parametri.


		New-AzureResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	Verrà richiesto di fornire i valori per i parametri definiti nel modello.

7. Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ... 

8. Se la distribuzione non riesce, è possibile usare i cmdlet seguenti per ottenere informazioni relative agli errori.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed

	Per informazioni dettagliate sugli errori di distribuzione, usare il cmdlet seguente.

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed -DetailedOutput

##Usare un'azione script da Azure PowerShell

In questa sezione si usa il cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** per richiamare gli script usando l'azione script per personalizzare un cluster. Prima di procedere, assicurarsi di aver installato e configurato Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet PowerShell per HDInsight, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Eseguire la procedura seguente:

1. Aprire la console di Azure PowerShell e dichiarare le variabili seguenti:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"
	
2. Specificare i valori di configurazione, ad esempio i nodi del cluster e l'archivio predefinito da usare.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Usare il cmdlet **Add-AzureHDInsightScriptAction** per richiamare lo script. L'esempio seguente usa lo script per installare R nel cluster:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,WorkerNode,ZookeeperNode -Uri https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh


	Il cmdlet **Add-AzureHDInsightScriptAction** accetta i parametri seguenti:

	| Parametro | Definizione |
	| --------- | ---------- |
	| Config | Oggetto di configurazione a cui vengono aggiunte le informazioni dell'azione di script. |
	| Nome | Nome dell'azione di script. |
	| ClusterRoleCollection | Specifica i nodi in cui viene eseguito lo script di personalizzazione. I valori validi sono **HeadNode** (per l'installazione nel nodo head) o **WorkerNode** (per l'installazione in tutti i nodi dati) o **ZookeeperNode** (per l'installazione nel nodo zookeeper). È possibile usare uno o tutti i valori. |
	| Parametri | Parametri richiesti dallo script. |
	| Uri | Specifica l'URI per lo script eseguito. |
	
4. Infine, effettuare il provisioning del cluster:
	
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Quando richiesto, immettere le credenziali per il cluster. La creazione del cluster può richiedere alcuni minuti.

##Usare un'azione script da HDInsight .NET SDK

HDInsight .NET SDK fornisce librerie client che semplificano l'utilizzo di HDInsight da un'applicazione .NET. I passaggi seguenti illustrano come usare uno script per personalizzare un cluster da HDInsight .NET SDK.


> [AZURE.IMPORTANT]È necessario creare prima di tutto un certificato autofirmato, installarlo nella workstation e quindi caricarlo nella sottoscrizione di Azure. Per ottenere istruzioni, vedere [Creare un certificato autofirmato](http://go.microsoft.com/fwlink/?LinkId=511138).


###Creare un progetto di Visual Studio

1. Aprire Visual Studio 2013 o 2015.

2. Scegliere **Nuovo** dal menu **File** e quindi fare clic su **Progetto**.

3. In **Nuovo progetto** digitare o selezionare i valori seguenti:
	
	| Proprietà | Valore |
	| -------- | ----- |
	| Categoria | Templates/Visual C#/Windows |
	| Modello | Applicazione console |
	| Nome | ScriptActionCluster |

4. Fare clic su **OK** per creare il progetto.

5. Dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet**, quindi su **Console di Gestione pacchetti**.

6. Eseguire il comando seguente nella console per installare il pacchetto.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Questo comando aggiunge librerie .NET e riferimenti ad esse dal progetto corrente di Visual Studio.

7. In **Esplora soluzioni** fare doppio clic su **Program.cs** per aprirlo.

8. Aggiungere le istruzioni **using** seguenti all'inizio del file.

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Nella funzione **Main()** incollare il codice seguente e fornire i valori per le variabili:
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. Aggiungere il codice seguente alla funzione **Main()**. Questo codice richiama un'azione script. In questo esempio, lo script per installare R nel cluster:

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), null
            ));

11. Al termine, creare il cluster:

		client.CreateCluster(clusterInfo);

11. Salvare le modifiche all'applicazione e compilare la soluzione.

###Eseguire l'applicazione

Aprire una console di Azure PowerShell, passare al percorso in cui è stato salvato il progetto, spostarsi nella directory \\bin\\debug all'interno del progetto, quindi eseguire il comando seguente:

	.\ScriptActionCluster <cluster-name>

Specificare un nome per il cluster e premere INVIO per effettuare il provisioning di un cluster.

## Supporto per software open source usato nei cluster HDInsight

Il servizio Microsoft Azure HDInsight è una piattaforma flessibile che permette di creare applicazioni Big Data nel cloud usando un ecosistema di tecnologie open source basate su Hadoop. Microsoft Azure fornisce un livello di supporto generale per le tecnologie open source, come illustrato nella sezione **Ambito del supporto** del [sito Web Domande frequenti sul supporto tecnico di Azure](http://azure.microsoft.com/support/faq/). Il servizio HDInsight fornisce un livello di supporto aggiuntivo per alcuni componenti, come illustrato di seguito.

Nel servizio HDInsight sono disponibili due tipi di componenti open source:

- **Componenti predefiniti** - Questi componenti sono preinstallati nei cluster HDInsight e forniscono la funzionalità di base del cluster. Questa categoria include ad esempio il gestore risorse YARN, il linguaggio di query Hive (HiveQL) e la libreria Mahout. L'elenco completo dei componenti del cluster è disponibile in [Novità delle versioni cluster di Hadoop incluse in HDInsight](hdinsight-component-versioning.md).

- **Componenti personalizzati** - Un utente del cluster può installare o usare nel carico di lavoro qualsiasi componente disponibile nella community o creato da lui stesso.

I componenti predefiniti sono supportati in modo completo e il Supporto Microsoft contribuirà a isolare e risolvere i problemi correlati a questi componenti.

I componenti personalizzati ricevono supporto commercialmente ragionevole per semplificare la risoluzione dei problemi. È possibile che si ottenga la risoluzione dei problemi o che venga richiesto di usare i canali disponibili per le tecnologie open source, in cui è possibile ottenere supporto approfondito per la tecnologia specifica. È ad esempio possibile usare molti siti di community, quali:

* [forum MSDN forum per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)

* [Stack Overflow](https://stackoverflow.com)

Per i progetti Apache sono disponibili siti specifici in [Apache.org](https://apache.org); ad esempio, [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

Il servizio HDInsight permette di usare i componenti personalizzati in molti modi. Indipendentemente dal modo in cui un componente viene usato o installato nel cluster, verrà applicato lo stesso livello di supporto. L'elenco seguente illustra i modi più comuni per usare i componenti personalizzati nei cluster HDInsight:

1. Invio di processi - È possibile inviare al cluster processi Hadoop o di altro tipo che eseguono o usano componenti personalizzati.

2. Personalizzazione del cluster - Durante la creazione di un cluster, è possibile specificare impostazioni aggiuntive e componenti personalizzati, che verranno installati nei nodi del cluster.

3. Esempi - Microsoft e altri utenti possono fornire esempi relativi all'uso dei componenti personalizzati più diffusi nei cluster HDInsight. Questi esempi vengono forniti senza supporto.

##Risoluzione dei problemi

È possibile usare l'interfaccia utente Web Ambari per visualizzare le informazioni registrate dagli script durante il provisioning del cluster.

1. Nel browser passare a https://CLUSTERNAME.azurehdinsight.net. Sostituire CLUSTERNAME con il nome del cluster HDInsight.

	Quando richiesto, immettere il nome dell'account amministratore (admin) e la password per il cluster. Potrebbe essere necessario immettere di nuovo le credenziali di amministratore in un Web Form.
	
2. Nella barra in alto nella pagina fare clic sulla voce __ops__. Verrà visualizzato un elenco delle operazioni correnti e precedenti eseguite nel cluster tramite Ambari.

	![Barra nell'interfaccia utente di Ambari con selezionato ops](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)
	
3. Trovare le voci con __run\_customscriptaction__ nella colonna __Operations__. Vengono create quando si eseguono le azioni script.

	![Schermata della operazioni](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	Selezionare questa voce ed eseguire il drill-down dei collegamenti per visualizzare l'output di STDOUT e STDERR generato durante l'esecuzione dello script sul cluster.

## Passaggi successivi

Per informazioni ed esempi sulla creazione e l'uso di script per personalizzare un cluster, vedere gli argomenti seguenti:

- [Sviluppare script di Azione script per HDInsight](hdinsight-hadoop-script-actions-linux.md)
- [Installare e usare Spark nei cluster HDInsight](hdinsight-hadoop-spark-install-linux.md)
- [Installare e usare R nei cluster HDInsight](hdinsight-hadoop-r-scripts-linux.md)
- [Installare e usare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Installare e usare Giraph nei cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "Fasi durante il provisioning di un cluster"
 

<!---HONumber=September15_HO1-->