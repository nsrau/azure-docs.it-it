<properties
	pageTitle="Spark sul modello di Gestione risorse Ubuntu | Microsoft Azure"
	description="Come distribuire con facilità un nuovo cluster Spark nelle macchine virtuali Ubuntu usando Azure PowerShell o l'interfaccia della riga di comando di Azure e un modello di Gestione risorse"
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# Spark su Ubuntu con un modello di Gestione risorse

Apache Spark è un motore ad alta velocità per l'elaborazione dati su larga scala. Spark dispone di un motore di esecuzione DAG avanzato che supporta il flusso dati ciclico e l'elaborazione in memoria e può accedere a diverse fonti di dati, tra cui HDFS, Spark, HBase e S3.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Questo articolo illustra la distribuzione di una risorsa con il modello di distribuzione Gestione risorse. Non è possibile distribuire questa risorsa con il modello di distribuzione classico.


Oltre a essere eseguito sugli strumenti di gestione del cluster Mesos e YARN, Spark fornisce una semplice modalità di distribuzione autonoma. In questa esercitazione verrà illustrato come utilizzare un modello di Gestione risorse di Azure di esempio per distribuire un cluster Spark su macchine virtuali Ubuntu tramite [Azure PowerShell](../powershell-install-configure.md) o l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

Questo modello consente di distribuire un cluster Spark in macchine virtuali Ubuntu. Fornisce inoltre l'account di archiviazione, la rete virtuale, il set di disponibilità, gli indirizzi IP pubblici e le interfacce di rete necessari per l'installazione. Il cluster Spark viene creato dietro una subnet, pertanto non è presente alcun IP pubblico di accesso al cluster. Nell'ambito del processo di distribuzione, è possibile distribuire anche un "jumpbox" opzionale. Si tratta di una macchina virtuale Ubuntu distribuita anch'essa nella subnet, ma che *espone* un indirizzo IP pubblico con una porta SSH aperta alla quale è possibile connettersi. Tramite il "jumpbox" è possibile stabilire una connessione SSH con tutte le macchine virtuali Spark nella subnet.

Questo modello utilizza il concetto di "dimensione t-shirt" per specificare una configurazione "piccola", "media" o "grande" del cluster Spark. Se il linguaggio del modello supporta un ridimensionamento dei modelli più dinamico, è possibile specificare invece il numero di nodi master del cluster Spark, nonché i nodi slave, le dimensioni della macchina virtuale e così via. Per ora, è possibile visualizzare le dimensioni della macchina virtuale e il numero di master e slave definiti nel file azuredeploy.json nelle variabili **tshirtSizeS**, **tshirtSizeM** e **tshirtSizeL**:

- S: 1 master, 1 slave
- M: 1 master, 4 slave
- L: 1 master, 6 slave

La topologia dei cluster appena distribuiti basati su questo modello è descritta nel diagramma seguente, sebbene sia possibile ottenere facilmente altre topologie, personalizzando il modello presentato in questo articolo:

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

Come illustrato nell'immagine riportata in precedenza, la topologia di distribuzione è costituita dagli elementi seguenti:

-	Un nuovo account di archiviazione che ospita il disco del sistema operativo delle macchine virtuali appena create.
-	Una rete virtuale con una subnet. Viene effettuato il provisioning di tutte le macchine virtuali create dal modello all'interno di questa rete virtuale.
-	Un set di disponibilità per i nodi master e slave.
-	Un nodo principale nel set di disponibilità appena creato.
-	Quattro nodi slave in esecuzione nella stessa subnet virtuale e nello stesso set di disponibilità del nodo master.
-	Una macchina virtuale "jumpbox" presente nella stessa rete virtuale e nella stessa subnet con cui è possibile accedere al cluster.

Spark 3.0.0 è la versione predefinita e può essere modificato con qualsiasi file binario pre-esistente disponibile nel repository di Spark. Nello script è inoltre presente una disposizione per rimuovere dalla compilazione i commenti correlati all'origine. Verrà assegnato un indirizzo IP statico per ciascun nodo Spark master: 10.0.0.10. Verrà assegnato un indirizzo IP statico per ciascun nodo Spark slave per risolvere il problema relativo alla limitazione attuale di non poter comporre in modo dinamico un elenco di indirizzi IP dall'interno del modello. (Per impostazione predefinita, al primo nodo verrà assegnato l'indirizzo IP privato 10.0.0.30, al secondo nodo verrà assegnato 10.0.0.31 e così via.) Per verificare che non siano presenti errori di distribuzione, andare al nuovo portale di Azure e selezionare **Gruppo di risorse** > **Ultima distribuzione** > **Verifica dettagli operazione**.

Prima di addentrarsi in ulteriori dettagli relativi a Gestione risorse di Azure e al modello utilizzato per la distribuzione, assicurarsi che Azure PowerShell o l’interfaccia della riga di comando di Azure sia configurata correttamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Creazione di un cluster Spark utilizzando un modello di Gestione risorse

Seguire questi passaggi per creare un cluster Spark utilizzando un modello di Gestione risorse dal repository dei modelli GitHub tramite Azure PowerShell o l'interfaccia della riga di comando di Azure.

### Passaggio 1-a: Scaricare i file di modello utilizzando Azure PowerShell

Creare una cartella locale per il modello JSON e gli altri file associati (ad esempio, C:\\Azure\\Templates\\Spark).

Sostituire il nome di cartella della cartella locale ed eseguire questi comandi:

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### Passaggio 1-b: Scaricare i file di modello tramite l'interfaccia della riga di comando di Azure

Clonare l’intero repository dei modelli utilizzando un client Git a propria scelta, ad esempio:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Una volta completata la clonazione, cercare la cartella **spark-on-ubuntu** nella directory C:\\Azure\\Templates.

### Passaggio 2: (facoltativo) Comprendere i parametri del modello

Quando si crea un cluster Spark utilizzando un modello, è necessario specificare un set di parametri di configurazione per gestire un numero di impostazioni necessarie. Dichiarando tali parametri nella definizione del modello, è possibile specificare i valori durante l'esecuzione della distribuzione tramite un file esterno o da una riga di comando.

Nella sezione "parametri" nella parte superiore del file azuredeploy.json, si individuerà il set di parametri richiesti dal modello per configurare un cluster Spark. Di seguito è riportato un esempio della sezione "parametri" dal file azuredeploy.json di questo modello:

```json
"parameters": {
	"adminUsername": {
		"type": "string",
		"metadata": {
			"Description": "Administrator user name used when provisioning virtual machines"
		}
	},
	"adminPassword": {
		"type": "securestring",
		"metadata": {
			"Description": "Administrator password used when provisioning virtual machines"
		}
	},
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

Ogni parametro include dettagli quali il tipo di dati e i valori consentiti. Ciò consente la convalida dei parametri passati durante l'esecuzione del modello in modalità interattiva (ad esempio, Azure PowerShell o l’interfaccia della riga di comando di Azure) e un'interfaccia utente di individuazione automatica che può essere compilata dinamicamente analizzando l'elenco dei parametri richiesti e le relative descrizioni.

### Passaggio 3-a: distribuire un cluster Spark utilizzando un modello tramite Azure PowerShell

Preparare un file dei parametri per la distribuzione creando un file JSON con i valori di runtime per tutti i parametri. Questo file verrà quindi passato al comando di distribuzione come singola entità. Se non si include un file dei parametri, in Azure PowerShell verranno utilizzati tutti i valori predefiniti specificati nel modello, quindi verrà richiesto di inserire i valori rimanenti.

Di seguito viene riportato un set di parametri di esempio dal file azuredeploy-parameters.json. Sarà necessario fornire valori validi per i parametri **storageAccountName**, **adminUsername** e **adminPassword**, oltre a eventuali personalizzazioni per gli altri parametri:

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
  },
  "subnetName": {
    "value": "Subnet1"
  },
  "addressPrefix": {
    "value": "10.0.0.0/16"
  },
  "subnetPrefix": {
    "value": "10.0.0.0/24"
  },
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
Inserire un nome per la distribuzione Azure, un nome per il gruppo di risorse, il percorso di Azure e la cartella in cui è stato salvato il file di distribuzione JSON. Quindi eseguire i seguenti comandi:

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]**$RGName** deve essere univoco all'interno della sottoscrizione.

Quando si esegue il comando **New-AzureResourceGroupDeployment**, verranno estratti i valori dei parametri dal file dei parametri JSON e verrà avviata l'esecuzione del modello di conseguenza. La definizione e l'uso di più file di parametri con gli ambienti diversi (test, produzione e così via) promuoveranno il riutilizzo e la semplificazione di soluzioni con più ambienti complesse.

Quando si effettua la distribuzione, tenere presente che è necessario creare un nuovo account di Archiviazione di Azure, in modo che il nome fornito come parametro di account di archiviazione sia univoco e soddisfi tutti i requisiti di un account di Archiviazione di Azure (solo lettere minuscole e numeri).

Durante la distribuzione, verrà visualizzata una schermata simile alla seguente:

```powershell
PS C:\> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

Durante e dopo la distribuzione, è possibile controllare tutte le richieste effettuate durante il provisioning, compresi gli errori che si sono verificati.

A tale scopo, visitare il [portale di Azure](https://portal.azure.com) ed effettuare le seguenti operazioni:

- Fare clic su **Sfoglia** nella barra di spostamento a sinistra, quindi scorrere verso il basso e fare clic su **Gruppi di risorse**.
- Fare clic sul gruppo di risorse appena creato, per visualizzare il pannello "Gruppo di risorse".
- Facendo clic sul grafico a barre **Eventi** nella parte relativa al **Monitoraggio** del pannello "Gruppo di risorse", sarà possibile visualizzare gli eventi per la distribuzione:
- Facendo clic su singoli eventi, è possibile esaminare i dettagli di ogni singola operazione eseguita per conto del modello.

![portal-events](media/virtual-machines-spark-template/portal-events.png)

Dopo aver effettuato i test, per rimuovere il gruppo di risorse e tutte le relative risorse (l'account di archiviazione, la macchina virtuale e la rete virtuale), usare il comando seguente:

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### Passaggio 3-b: distribuire un cluster Spark utilizzando un modello tramite l'interfaccia della riga di comando di Azure

Per distribuire un cluster Spark tramite l'interfaccia della riga di comando di Azure, è necessario innanzitutto creare un gruppo di risorse specificando un nome e un percorso:

	azure group create SparkResourceGroup "West US"

Passare il nome di questo gruppo di risorse, il percorso del file di modello JSON e il percorso del file dei parametri (vedere la sezione precedente di Azure PowerShell per i dettagli) nel comando seguente:

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

È possibile controllare lo stato delle distribuzioni delle singole risorse utilizzando il comando seguente:

	azure group deployment list SparkResourceGroup

## Panoramica della struttura del modello Spark e dell'organizzazione dei file

Per progettare un modello di Gestione risorse efficace e riusabile, è necessaria un'ulteriore valutazione per organizzare la serie di attività complesse e correlate necessarie durante la distribuzione di una soluzione complessa come Spark. Sfruttando le funzionalità di collegamento dei modelli e ciclo delle risorse di Gestione risorse, oltre all'esecuzione di script tramite le estensioni correlate, è possibile implementare un approccio modulare che in teoria può essere riutilizzato con qualsiasi distribuzione complessa basata sui modelli.

In questo diagramma sono illustrate le relazioni tra tutti i file scaricati da GitHub per questa distribuzione:

![spark-files](media/virtual-machines-spark-template/spark-files.png)

In questa sezione viene fornita una descrizione della struttura del file azuredeploy.json per il cluster Spark.

Se non è ancora stata scaricata una copia del file di modello, specificare una cartella locale come posizione per il file e crearlo (ad esempio, C:\\Azure\\Templates\\Spark). Sostituire il nome della cartella, quindi eseguire questi comandi.

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Aprire il modello azuredeploy.json in un editor di testo o in uno strumento a scelta. Di seguito vengono descritti la struttura del file di modello e lo scopo di ogni sezione. In alternativa, è possibile visualizzare il contenuto di questo modello nel browser da [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json).

### sezione "parametri"

Nella sezione “parametri” di azuredeploy.json vengono specificati i parametri modificabili utilizzati in questo modello. Il file azuredeploy-parameters.json menzionato in precedenza viene utilizzato per passare i valori nella sezione "parametri" di azuredeploy.json durante l'esecuzione del modello.

Di seguito è riportato un esempio di un parametro per la "taglia":

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

> [AZURE.NOTE]È possibile specificare **defaultValue** e **allowedValues**.

### sezione "variabili"

La sezione "variabili" Specifica le variabili che possono essere usate in questo modello. Contiene un numero di campi (tipi di dati o frammenti JSON) che verranno impostati su costanti o valori calcolati in fase di esecuzione. Di seguito sono riportati alcuni esempi che spaziano da semplici a più complessi:

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

**vmStorageAccountContainerName** è un esempio di variabile nome/valore semplice. **vnetID** è un esempio di variabile calcolata in fase di esecuzione utilizzando le funzioni **resourceId** e **parameters**. Il valore delle variabili **numberOfMasterInstances** e **vmSize** viene calcolato in fase di esecuzione utilizzando le funzioni **concat**, **variables** e **parameters**.

Se si desidera personalizzare le dimensioni della distribuzione del cluster Spark, è possibile quindi modificare le proprietà delle variabili **tshirtSizeS**, **tshirtSizeM** e **tshirtSizeL** nel modello azuredeploy.json.

Altre informazioni relative al linguaggio del modello sono disponibili sul sito MSDN in [Linguaggio del modello di Gestione risorse di Azure](../resource-group-authoring-templates.md).


### sezione "risorse"

La sezione "risorse" rappresenta la posizione in cui si svolgono la maggior parte delle operazioni. Analizzando attentamente questa sezione, è possibile identificare immediatamente due diversi casi. Il primo è un elemento definito di tipo `Microsoft.Resources/deployments` che richiama essenzialmente una distribuzione nidificata all'interno di quella principale. Il secondo è la proprietà **templateLink** (e la proprietà correlata **contentVersion**) che rende possibile specificare un file di modello correlato che verrà richiamato, passando una serie di parametri come input. Tutto questo può essere osservato in questo frammento di modello:

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

Da questo primo esempio, è chiaro come il file azuredeploy.json in questo scenario sia stato organizzato come meccanismo di orchestrazione, che richiama un determinato numero di altri file. Ogni file è responsabile di parte delle attività di distribuzione necessarie.

In particolare, i seguenti modelli collegati verranno usati per la distribuzione:

-	**shared-resource.json**: contiene la definizione di tutte le risorse che verranno condivise durante la distribuzione. Esempi di account di archiviazione utilizzati per archiviare i dischi del sistema operativo della macchina virtuale e le reti virtuali.
-	**jumpbox-resources-enabled.json**: consente di distribuire la macchina virtuale "jumpbox" e tutte le risorse correlate, come l'interfaccia di rete, l'indirizzo IP pubblico e l'endpoint di input usato per la connessione SSH all'ambiente.

Dopo aver richiamato questi due modelli, azuredeploy.json esegue il provisioning di tutte le macchine virtuali dei nodi del cluster Spark e delle risorse collegate (schede di rete, IP privati e così via). Questo modello consentirà inoltre di distribuire estensioni delle macchine virtuali (gli script personalizzati per Linux) e di richiamare uno script bash (spark-cluster-install.sh) per installare fisicamente e configurare Spark in ciascun nodo.

Si passerà ora a esaminare *come* quest'ultimo modello, azuredeploy.json, viene utilizzato, in quanto è uno dei più interessanti dal punto di vista dello sviluppo dei modelli. Un concetto importante da evidenziare è come un unico file di modello possa consentire la distribuzione di più copie di un singolo tipo di risorsa, e per ogni istanza di impostare valori univoci per le impostazioni necessarie. Questo concetto è noto come **Resource Looping (ciclo delle risorse)**.

Una risorsa che utilizza l'elemento **copy** verrà copiata automaticamente per il numero di volte specificato nel parametro **count** dell'elemento **copy**. Per tutte le impostazioni di cui è necessario specificare valori univoci tra istanze diverse della risorsa distribuita, è possibile utilizzare la funzione **copyindex()** per ottenere un valore numerico che indica l'indice corrente nella creazione del ciclo di risorse specifico. Nel frammento seguente di azuredeploy.json, è possibile osservare questo concetto applicato a più schede di rete, macchine virtuali ed estensioni di macchine virtuali create per il cluster Spark:

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
				"subnet": {
					"id": "[variables('subnetRef')]"
				}
			}
		}
		]
	}
},
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Compute/virtualMachines",
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

Un altro concetto importante nella creazione delle risorse è la possibilità di specificare le dipendenze e le priorità tra risorse, come è possibile vedere nella matrice JSON **dependsOn**. In questo particolare modello, è possibile osservare come i nodi del cluster Spark dipendano dalle risorse condivise e dalle risorse **networkInterfaces** create in precedenza.

Un altro frammento interessante da esplorare è quello correlato alle estensioni di macchina virtuale **CustomScriptForLinux**, Queste vengono installate come tipo di risorsa separato, con una dipendenza in ogni nodo del cluster. In questo caso, l'estensione viene utilizzata per installare e configurare Spark in ciascun nodo della macchina virtuale. Si esaminerà ora un frammento del modello azuredeploy.json che utilizza queste estensioni:

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

L'estensione relativa alle risorse dei nodi master e slave esegue comandi diversi, definiti nella proprietà **commandToExecute ** e specifici del processo di provisioning.

Se si osserva il frammento JSON dell'estensione della macchina virtuale più recente, si noterà che questa risorsa dipende dalla risorsa della macchina virtuale e dalla relativa interfaccia di rete. Ciò indica che la distribuzione di queste due risorse deve essere già stata eseguita prima del provisioning e dell'esecuzione di questa estensione della macchina virtuale. Inoltre è necessario utilizzare la funzione **copyindex()** per ripetere questo passaggio per ciascuna macchina virtuale slave.

Acquisendo familiarità con gli altri file inclusi in questa distribuzione sarà possibile comprendere tutti i dettagli e le procedure consigliate necessarie per organizzare e orchestrare le strategie di distribuzione complesse per le soluzioni con più nodi, basate su qualsiasi tecnologia, che sfruttano i modelli di Gestione risorse di Azure. Sebbene non obbligatorio, è un approccio consigliato per strutturare i file di modello come mostrato nel diagramma seguente:

![spark-template-structure](media/virtual-machines-spark-template/spark-template-structure.png)

In pratica, questo approccio suggerisce di:

-	Definire il file del modello di base come un punto centrale di orchestrazione per tutte le attività di distribuzione specifiche, sfruttando il modello di collegamento per richiamare le esecuzioni del sottomodello.
-	Creare un file di modello specifico che consentirà di distribuire tutte le risorse condivise tra tutte le altre attività di distribuzione specifiche (account di archiviazione, configurazione di rete virtuale e così via). Questa funzione può essere riusata in modo intensivo tra distribuzioni che prevedono requisiti simili in termini di infrastruttura comune.
-	Includere modelli di risorsa facoltativi per requisiti specifici di una determinata risorsa.
-	Per i membri identici di un gruppo di risorse (nodi in un cluster e così via), creare modelli specifici che sfruttano il ciclo di risorse per poter distribuire più istanze con proprietà univoche.
-	Per tutte le attività di post distribuzione (installazione del prodotto, configurazioni e così via), usare estensioni di distribuzione di script e creare script specifici per ogni tecnologia.

Per altre informazioni, vedere il [linguaggio del modello di Gestione risorse di Azure](../resource-group-authoring-templates.md).

## Passaggi successivi

Acquisire altre informazioni sulla [distribuzione di un modello](../resource-group-template-deploy.md).

Scoprire altri [framework di applicazioni](virtual-machines-app-frameworks.md).

[Risoluzione dei problemi relativi alle distribuzioni dei modelli](resource-group-deploy-debug.md).

<!---HONumber=Oct15_HO1-->