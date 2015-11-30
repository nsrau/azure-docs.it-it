<properties
	pageTitle="Modello di Gestione risorse Redis Cluster"
	description="Informazioni su come distribuire con facilità un nuovo cluster Redis nelle macchine virtuali Ubuntu utilizzando Azure PowerShell o l’interfaccia della riga di comando di Azure e un modello di Gestione risorse"
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# Cluster Redis con un modello di Gestione risorse

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.


Redis è una cache e un archivio chiave-valore open-source in cui le chiavi possono contenere strutture di dati quali stringhe, hash, elenchi, insiemi e insiemi ordinati. Redis supporta una serie di operazioni atomiche su questi tipi di dati. Con il rilascio di Redis versione 3.0, Redis Cluster è ora disponibile nella versione stabile più recente di Redis. Redis Cluster è un'implementazione distribuita di Redis in cui i dati sono partizionati automaticamente tra più nodi Redis, con la possibilità di continuare le operazioni quando si verificano errori in un subset di nodi.

Cache Redis di Microsoft Azure è un Servizio cache Redis dedicato, gestito da Microsoft, ma non tutti i clienti di Microsoft Azure desiderano utilizzare Cache Redis di Azure. Alcuni desiderano mantenere la cache Redis dietro una subnet all'interno delle proprie distribuzioni di Azure, mentre altri trovano più comodo lasciare i propri server Redis in hosting nelle macchine virtuali Linux, per poter sfruttare appieno tutte le funzionalità di Redis.

In questa esercitazione verrà illustrato l'utilizzo di un modello di Gestione risorse di Azure di esempio per distribuire Redis Cluster nelle VM di Ubuntu all'interno di una subnet in un gruppo di risorse di Microsoft Azure. Oltre a Redis 3.0 Cluster, questo modello supporta anche la distribuzione di Redis 2.8 con Redis Sentinel. Si noti che in questa esercitazione verrà trattata solo l'implementazione di Redis 3.0 Cluster.

Il cluster Redis viene creato dietro una subnet, in modo da non disporre di alcun IP pubblico di accesso a Redis Cluster. Come parte della distribuzione, può essere distribuito un "jumpbox" facoltativo. Si tratta di una macchina virtuale Ubuntu distribuita anch’essa nella subnet, ma che *espone* un indirizzo IP pubblico con una porta SSH aperta con la quale è possibile stabilire una connessione SSH. Tramite il "jumpbox", è possibile stabilire una connessione SSH con tutte le macchine virtuali Redis nella subnet.

Questo modello utilizza il concetto di "taglia" per specificare una configurazione "piccola", "media" o "grande" di Redis Cluster. Quando il linguaggio del modello gestione risorse di Azure supporta il ridimensionamento dei modelli più dinamico, è possibile specificare invece il numero di nodi master Redis Cluster, nodi slave, dimensioni della VM e così via. Per ora, è possibile visualizzare le dimensioni della VM e il numero di master e slave definiti nel file azuredeploy.json nelle variabili `tshirtSizeSmall`, `tshirtSizeMedium`, and `tshirtSizeLarge`.

Con il modello Redis Cluster per la taglia "medium" viene creata questa configurazione:

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

Prima di addentrarsi in ulteriori dettagli relativi a Gestione risorse di Azure e al modello utilizzato per la distribuzione, assicurarsi che Azure PowerShell o l’interfaccia della riga di comando di Azure sia configurata correttamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Distribuire un cluster Redis utilizzando un modello di Gestione risorse

Attenersi alla seguente procedura per creare un cluster Redis utilizzando un modello di Gestione risorse dall'archivio dei modelli GitHub. Ogni passaggio includerà le indicazioni sia per Azure PowerShell che per l’interfaccia della riga di comando di Azure.

### Passaggio 1-a: Scaricare i file di modello utilizzando Azure PowerShell

Creare una cartella locale per il modello JSON e gli altri file associati (ad esempio, C:\\Azure\\Templates\\RedisCluster).

Sostituire il nome di cartella della cartella locale ed eseguire questi comandi:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### Passaggio 1-b: Scaricare i file di modello tramite l'interfaccia della riga di comando di Azure

Clonare l’intero repository dei modelli utilizzando un client Git a propria scelta, ad esempio:

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

Una volta completata l’operazione, cercare la cartella **redis-high-availability** nella directory C:\\Azure\\Templates.

### Passaggio 2: (facoltativo) Comprendere i parametri del modello

Quando si crea un cluster Redis utilizzando un modello, è necessario specificare un set di parametri di configurazione. Per visualizzare i parametri che è necessario specificare per il modello in un file JSON locale prima di eseguire il comando per creare il cluster Redis, aprire il file JSON in un editor di testo o in uno strumento a scelta.

Cercare la sezione "parametri" nella parte superiore del file, nella quale è elencato il set di parametri necessari al modello per configurare il cluster Redis. Questa è la sezione "parameters" per il modello azuredeploy.json:

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
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
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
		"defaultValue": "redisSubnet1",
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
	"nodeAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "Disabled",
		"allowedValues": [
			"Enabled",
			"Disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

Ogni parametro include dettagli quali il tipo di dati e i valori consentiti. Ciò consente la convalida dei parametri passati durante l'esecuzione del modello in modalità interattiva (ad esempio, Azure PowerShell o l’interfaccia della riga di comando di Azure) e un'interfaccia utente di individuazione automatica che può essere compilata dinamicamente analizzando l'elenco dei parametri richiesti e le relative descrizioni.

### Passaggio 3-a: Distribuire un cluster Redis utilizzando un modello tramite Azure PowerShell

Preparare un file dei parametri per la distribuzione creando un file JSON con i valori di runtime per tutti i parametri. Questo file verrà quindi passato al comando di distribuzione come singola entità. Se non si include un file dei parametri, in Azure PowerShell verranno utilizzati tutti i valori predefiniti specificati nel modello, quindi verrà richiesto di inserire i valori rimanenti.

Di seguito è riportato un esempio che è possibile trovare nel file azuredeploy-parameters.json. Si noti che è necessario fornire valori validi per i parametri `storageAccountName`, `adminUsername` e `adminPassword`, più eventuali personalizzazioni per gli altri parametri:

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
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
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]Il parametro `storageAccountName` deve essere un nome account di Archiviazione univoco, non esistente, che soddisfi i requisiti di denominazione per un account di Archiviazione di Microsoft Azure (solo numeri e lettere minuscole). Questo account di Archiviazione verrà creato come parte del processo di distribuzione.

Inserire un nome per la distribuzione Azure, un nome per il gruppo di risorse, il percorso di Azure e la cartella in cui sono stati salvati i file di JSON. Quindi eseguire i seguenti comandi:

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName` deve essere univoco all'interno della sottoscrizione.

Quando si esegue il comando **New-AzureResourceGroupDeployment**, verranno estratti i valori dei parametri dal file dei parametri JSON (azuredeploy-parameters.json) e verrà avviata l'esecuzione del modello di conseguenza. La definizione e l'uso di più file di parametri con gli ambienti diversi (test, produzione e così via) promuoveranno il riutilizzo e la semplificazione di soluzioni con più ambienti complesse.

Quando si effettua la distribuzione, tenere presente che è necessario creare un nuovo account di Archiviazione di Azure, in modo che il nome fornito come parametro di account di archiviazione sia univoco e soddisfi tutti i requisiti di un account di Archiviazione di Azure (solo lettere minuscole e numeri).

Durante la distribuzione, verrà visualizzata una schermata simile alla seguente:

	PS C:\> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

Durante e dopo la distribuzione, è possibile controllare tutte le richieste effettuate durante il provisioning, compresi gli errori che si sono verificati.

A tale scopo, visitare il [portale di anteprima di Azure](https://portal.azure.com) ed effettuare le seguenti operazioni:

- Nella barra di spostamento a sinistra, fare clic su **Sfoglia**, scorrere verso il basso e fare clic su **Gruppi di risorse**.
- Selezionare il gruppo di risorse appena creato, per visualizzare il pannello "Gruppo di risorse".
- Nella sezione relativa al **Monitoraggio**, selezionare il grafico a barre "Eventi". Verranno visualizzati gli eventi per la distribuzione.
- Facendo clic su singoli eventi è possibile esaminare i dettagli di ogni operazione eseguita per conto del modello.

Se, dopo aver effettuato i test, è necessario rimuovere il gruppo di risorse e tutte le relative risorse (l'account di Archiviazione, la macchina virtuale e la rete virtuale), utilizzare questo comando:

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### Passaggio 3-b: Distribuire un cluster Redis utilizzando un modello tramite l’interfaccia della riga di comando di Azure

Per distribuire un cluster Redis tramite l’interfaccia della riga di comando di Azure, è necessario innanzitutto creare un gruppo di risorse specificando un nome e un percorso:

```powershell
azure group create TestRG "West US"
```

Passare il nome di questo gruppo di risorse, il percorso del file di modello JSON e il percorso del file dei parametri (vedere la sezione precedente di Azure PowerShell per i dettagli) nel comando seguente:

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

È possibile controllare lo stato delle distribuzioni delle singole risorse utilizzando il comando seguente:

```powershell
azure group deployment list TestRG
```

## Panoramica della struttura del modello di cluster Redis e dell’organizzazione dei file

Per creare un approccio efficace e riutilizzabile alla progettazione di modelli di Gestione risorse, è necessaria un’ulteriore valutazione per organizzare la serie di attività complesse e correlate necessarie durante la distribuzione di una soluzione complessa come Redis Cluster. Sfruttando le funzionalità di collegamento del modello di gestione risorse, ciclo delle risorse e di esecuzione di script tramite le estensioni correlate, è possibile implementare un approccio modulare che può essere riutilizzato con potenzialmente qualsiasi distribuzione complessa basata su modelli.

In questo diagramma sono illustrate le relazioni tra tutti i file scaricati da GitHub per la distribuzione:

![redis-files](media/virtual-machines-redis-template/redis-files.png)

In questa sezione viene fornita una descrizione della struttura del file azuredeploy.json per il cluster Redis.

Se non è stata scaricata una copia del file di modello, specificare una cartella locale come posizione per il file e crearlo (ad esempio, C:\\Azure\\Templates\\RedisCluster). Sostituire il nome della cartella, quindi eseguire questi comandi.

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Aprire il modello azuredeploy.json in un editor di testo o in uno strumento a scelta. Di seguito vengono descritti la struttura del file di modello e lo scopo di ogni sezione. In alternativa, è possibile visualizzare il contenuto di questo modello nel browser da [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json).

### sezione "parametri"

È stato già menzionato il ruolo del file azuredeploy-parameters.json, che verrà utilizzato per passare un set di valori di parametro specificati durante l'esecuzione del modello. Nella sezione "parametri" di azuredeploy.json vengono specificati i parametri utilizzati per immettere i dati in questo modello.

Di seguito è riportato un esempio di un parametro per la "taglia":

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]Si noti che è possibile specificare `defaultValue` e `allowedValues`.

### sezione "variabili"

La sezione "variabili" Specifica le variabili che possono essere utilizzate in questo modello. Contiene, di base, un numero di campi (tipi di dati o frammenti JSON) che verranno impostati su costanti o valori calcolati in fase di esecuzione. Di seguito sono riportati alcuni esempi che spaziano da semplici a più complessi:

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

Le variabili `vmStorageAccountContainerName` e `vmStorageAccountDomain` sono esempi di nome semplice/variabili di valore. `vnetID` è un esempio di una variabile calcolata in fase di esecuzione mediante le funzioni `resourceId` e `parameters`. `machineSettings` si basa su questi concetti e inoltre nidifica l’oggetto JSON `osImageReference` nella variabile `machineSettings`. `vmScripts` contiene una matrice JSON, `scriptsToDownload`, che viene calcolata in fase di esecuzione mediante le funzioni `concat` e `variables`.

Se si desidera personalizzare le dimensioni della distribuzione del Redis Cluster, è possibile dunque modificare le proprietà delle variabili `tshirtSizeSmall`, `tshirtSizeMedium` e `tshirtSizeLarge` nel modello azuredeploy.json.

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

Nota: le proprietà `totalMemberCountExcludingLast` e `totalMemberCount` sono necessarie perché il linguaggio del modello non dispone attualmente di operazioni "matematiche".

Ulteriori informazioni relative al linguaggio del modello sono reperibili sul sito MSDN in [Linguaggio del modello di gestione delle risorse di Azure](../resource-group-authoring-templates.md).

### sezione "risorse"

La sezione "risorse" rappresenta la posizione in cui si svolgono la maggior parte delle operazioni. Analizzando attentamente questa sezione, è possibile identificare immediatamente due diversi casi. Il primo è un elemento definito di tipo `Microsoft.Resources/deployments` che richiama essenzialmente una distribuzione nidificata all'interno di quella principale. Il secondo è la proprietà `templateLink` (e la proprietà correlata `contentVersion`) che rende possibile specificare un file di modello correlato che verrà richiamato, passando una serie di parametri come input. Tutto questo può essere osservato in questo frammento di modello:

```json
{
    "name": "shared-resources",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('sharedTemplateUrl')]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            }
        }
    }
},
```

Da questo primo esempio è chiaro come il file azuredeploy.json in questo scenario sia stato organizzato come meccanismo di orchestrazione, che richiama un determinato numero di altri file. Ogni file è responsabile di parte delle attività di distribuzione necessarie.

In particolare, i seguenti modelli collegati verranno usati per la distribuzione:

- **shared-resource.json**: contiene la definizione di tutte le risorse che verranno condivise durante la distribuzione. Ne sono esempio gli account di Archiviazione utilizzati per archiviare i dischi del sistema operativo della VM, le reti virtuali e i set di disponibilità.
- **jumpbox-resources.json**: consente di distribuire la macchina virtuale “jumpbox” e tutte le risorse correlate, come interfaccia di rete, indirizzo IP pubblico e l’endpoint di input utilizzato per la connessione SSH all’ambiente.
- **node-resources.json**: consente di distribuire tutte le VM dei nodi Redis Cluster e le risorse collegate (ad esempio, schede di rete, IP privati e così via). Questo modello consente inoltre di distribuire le estensioni della VM (gli script personalizzati per Linux) e di richiamare uno script bash per installare fisicamente e configurare Redis in ciascun nodo. Lo script per la richiamata viene passato a questo modello nel `machineSettings` parametro della `commandToExecute` proprietà. È possibile distribuire e creare script in parallelo di tutti i nodi del Redis Cluster tranne uno. Un nodo deve essere conservato fino alla fine poiché la configurazione di Redis Cluster può essere eseguita su un solo nodo, e deve essere eseguita dopo che in tutti i nodi è in esecuzione il server Redis. Questo è il motivo per cui lo script da eseguire viene passato a questo modello. L'ultimo nodo deve eseguire uno script leggermente diverso che non solo consentirà di installare il server Redis, ma anche di configurare il cluster Redis.

Esaminiamo *come* viene utilizzato quest’ultimo modello, node-resources.json, in quanto è uno dei più interessanti dal punto di vista dello sviluppo dei modelli. Un concetto importante da evidenziare è come un unico file di modello possa consentire la distribuzione di più copie di un singolo tipo di risorsa, e per ogni istanza di impostare valori univoci per le impostazioni necessarie. Questo concetto è noto come **Resource Looping (ciclo delle risorse)**.

Quando si richiama node-resources.json dall’interno del file azuredeploy.json principale, la richiamata viene eseguita dall’interno di una risorsa che utilizza l’elemento `copy` per creare un ciclo di ordinamenti. Una risorsa che utilizza l’elemento `copy` verrà copiata automaticamente per il numero di volte specificato nel parametro `count` dell’elemento `copy`. Per tutte le impostazioni di cui è necessario specificare valori univoci tra istanze diverse della risorsa distribuita, è possibile utilizzare la funzione **copyindex()** per ottenere un valore numerico che indica l'indice corrente nella creazione del ciclo di risorse specifico. Nel frammento seguente di azuredeploy.json è possibile osservare questo concetto applicato alla creazione di più VM per i nodi Redis Cluster:

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
    },
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "[variables('clusterSpec').vmTemplate]",
            "contentVersion": "1.0.0.0"
        },
        "parameters": {
            "commonSettings": {
                "value": "[variables('commonSettings')]"
            },
            "storageSettings": {
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

Un altro concetto importante nella creazione delle risorse è la possibilità di specificare le dipendenze e le priorità tra risorse, come è possibile vedere nella matrice JSON `dependsOn`. In questo particolare modello, è possibile vedere che i nodi Cluster Redis dipendono dalle risorse condivise create in precedenza.

Come accennato precedentemente, l'ultimo nodo deve attendere per il provisioning fino a quando non è stato eseguito il provisioning di tutti gli altri cluster Redis con il server Redis in esecuzione su di essi. Questa operazione viene eseguita in azuredeploy.json con una risorsa denominata `lastnode-resources` che dipende dal ciclo `copy` denominato `memberNodesLoop` dal frammento di modello precedente. Dopo il completamento del provisioning di `memberNodesLoop`, è possibile eseguire il provisioning di `lastnode-resources`:

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[variables('clusterSpec').vmTemplate]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"commonSettings": {
				"value": "[variables('commonSettings')]"
			},
			"storageSettings": {
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

Si noti come la risorsa `lastnode-resources` passi uno script `machineSettings.commandToExecute` leggermente diverso al modello collegato. Questo è il motivo per il quale per l'ultimo nodo, oltre al server Redis installato, è necessario chiamare uno script per configurare Redis Cluster (che deve essere eseguito solo una volta dopo che tutti i server Redis sono operativi).

Un altro frammento interessante da esplorare è quello correlato alle `CustomScriptForLinux` estensioni della VM. Queste vengono installate come tipo di risorsa separato, con una dipendenza in ogni nodo del cluster. In questo caso, questa viene utilizzata per installare e configurare Redis in ogni nodo della VM. Esaminiamo un frammento del modello node-resources.json in cui vengono utilizzate le estensioni:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

È possibile vedere che questa risorsa dipende dalla VM della risorsa già distribuita (`Microsoft.Compute/virtualMachines/vmMember<X>`, nella quale `<X>` è il parametro `machineSettings.machineIndex`, che è l’indice della VM passato a questo script tramite la funzione **copyindex()**).

Acquisendo familiarità con gli altri file inclusi in questa distribuzione sarà possibile comprendere tutti i dettagli e le procedure consigliate necessarie per organizzare e orchestrare le strategie di distribuzione complesse per le soluzioni con più nodi, basate su qualsiasi tecnologia, che sfruttano i modelli di Gestione risorse di Azure. Sebbene non obbligatorio, è un approccio consigliato per strutturare i file di modello come mostrato nel diagramma seguente:

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

In pratica, questo approccio suggerisce di:

- Definire il file del modello di base come un punto centrale di orchestrazione per tutte le attività di distribuzione specifiche, sfruttando il modello di collegamento per richiamare le esecuzioni del sottomodello.
- Creare un file di modello specifico che consentirà di distribuire tutte le risorse condivise tra tutte le altre attività di distribuzione specifiche (account di Archiviazione, configurazione di rete virtuale e così via). Questa funzione può essere riusata in modo intensivo tra distribuzioni che prevedono requisiti simili in termini di infrastruttura comune.
- Includere modelli di risorsa facoltativi per requisiti specifici di una determinata risorsa.
- Per i membri identici di un gruppo di risorse (nodi in un cluster e così via), creare modelli specifici che sfruttano il ciclo di risorse per poter distribuire più istanze con proprietà univoche.
- Per tutte le attività di post distribuzione (installazione del prodotto, configurazioni e così via), usare estensioni di distribuzione di script e creare script specifici per ogni tecnologia.

Per altre informazioni, vedere il [linguaggio del modello di Gestione risorse di Azure](../resource-group-authoring-templates.md).

<!---HONumber=Nov15_HO4-->