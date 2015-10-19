<properties
	pageTitle="DataStax in Ubuntu con un modello di Gestione risorse | Microsoft Azure"
	description="Informazioni su come distribuire con facilità un nuovo cluster DataStax in macchine virtuali Ubuntu usando Azure PowerShell o l'interfaccia della riga di comando di Azure e un modello di Gestione risorse"
	services="virtual-machines"
	documentationCenter=""
	authors="scoriani"
	manager="timlt"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="scoriani"/>

# DataStax in Ubuntu con un modello di Gestione risorse

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In questo articolo viene illustrata la creazione di una risorsa con il modello di distribuzione di gestione delle risorse.

DataStax è una nota azienda leader nel settore dello sviluppo e della fornitura di soluzioni basate su Apache Cassandra, la tecnologia per database NoSQL distribuiti di livello aziendale, supportata in commercio e ampiamente riconosciuta per le caratteristiche di flessibilità, disponibilità e scalabilità per qualsiasi esigenza. DataStax offre versioni sia Enterprise (DSE) che Community (DSC). Offre inoltre funzionalità di elaborazione in memoria, di sicurezza a livello aziendale, di analisi rapida e potente integrata e funzioni di ricerca a livello aziendale.

Oltre alle funzionalità già disponibili in Azure Marketplace, ora è possibile distribuire facilmente un nuovo cluster DataStax nelle macchine virtuali Ubuntu usando un modello di Gestione risorse distribuito tramite [Azure PowerShell](../powershell-install-configure.md) o l'[interfaccia della riga di comando di Azure](../xplat-cli-install.md).

La topologia dei cluster appena distribuiti basati su questo modello è descritta nel diagramma seguente, sebbene sia possibile ottenere facilmente altre topologie, personalizzando il modello presentato in questo articolo:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

Mediante i parametri, è possibile definire il numero di nodi che verranno distribuiti nel nuovo cluster Apache Cassandra. Un'istanza del servizio DataStax Operations Center verrà inoltre distribuita in una macchina virtuale autonoma all'interno della stessa rete virtuale, offrendo la possibilità di monitorare lo stato del cluster e di tutti i singoli nodi, di aggiungere o rimuovere nodi e di eseguire tutte le attività amministrative correlate a tale cluster.

Una volta completata la distribuzione, è possibile accedere all'istanza della macchina virtuale di DataStax Operations Center usando l'indirizzo DNS configurato. La macchina virtuale dell'Operations Center ha la porta SSH 22 abilitata, ma anche la porta 8443 per la modalità HTTPS. L'indirizzo DNS per l'Operations Center includerà *dnsName* e *region* immessi come parametri, restituendo il formato `{dnsName}.{region}.cloudapp.azure.com`. Se è stata creata una distribuzione con il parametro *dnsName* impostato su "datastax" nell'area "West US", è possibile accedere alla macchina virtuale di DataStax Operations Center per la distribuzione all'indirizzo `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]Il certificato usato nella distribuzione è un certificato autofirmato che consente di creare un avviso visualizzato tramite il browser. È possibile attenersi alla procedura indicata nel sito Web [DataStax](http://www.datastax.com/) per la sostituzione del certificato con il proprio certificato SSL.

Prima di addentrarsi in ulteriori dettagli relativi a Gestione risorse di Azure e al modello usato per la distribuzione, assicurarsi che Azure PowerShell o l'interfaccia della riga di comando di Azure sia configurata correttamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Creare un cluster Cassandra basato su DataStax con un modello di Gestione risorse

Seguire questa procedura per creare un cluster Apache Cassandra, basato su DataStax, usando un modello di Gestione risorse dall'archivio dei modelli GitHub. Ogni passaggio includerà le indicazioni sia per Azure PowerShell che per l'interfaccia della riga di comando di Azure.

### Passaggio 1-a: Scaricare i file di modello usando Azure PowerShell

Creare una cartella locale per il modello JSON e gli altri file associati (ad esempio, C:\\Azure\\Templates\\DataStax).

Sostituire il nome di cartella della cartella locale ed eseguire questi comandi:

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### Passaggio 1-b: Scaricare i file di modello tramite l'interfaccia della riga di comando di Azure

Clonare l'intero repository dei modelli usando un client Git a propria scelta, ad esempio:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Una volta completata la clonazione, cercare la cartella **datastax-on-ubuntu** nella directory C:\\Azure\\Templates.

### Passaggio 2: (facoltativo) Comprendere i parametri del modello

Quando si distribuiscono soluzioni non semplici come un cluster Apache Cassandra basato su DataStax, è necessario specificare un set di parametri di configurazione per gestire un gran numero di impostazioni necessarie. Dichiarando i parametri nella definizione del modello, è possibile specificare i valori durante la distribuzione tramite un file esterno o nella riga di comando.

Nella sezione "parameters" nella parte superiore del file azuredeploy.json, si individuerà il set di parametri richiesti dal modello per configurare un cluster DataStax. Di seguito è riportato un esempio della sezione "parametri" dal file azuredeploy.json di questo modello:

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the storage account where the virtual machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the operations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
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
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

Ogni parametro include dettagli quali il tipo di dati e i valori consentiti. Ciò consente la convalida dei parametri passati durante l'esecuzione del modello in modalità interattiva (ad esempio, Azure PowerShell o l'interfaccia della riga di comando di Azure) e un'interfaccia utente di individuazione automatica che può essere compilata dinamicamente analizzando l'elenco dei parametri richiesti e le relative descrizioni.

### Passaggio 3-a: distribuire un cluster DataStax usando un modello tramite Azure PowerShell

Preparare un file dei parametri per la distribuzione creando un file JSON contenente i valori di runtime per tutti i parametri. Questo file verrà quindi passato al comando di distribuzione come singola entità. Se non si include un file dei parametri, in Azure PowerShell verranno usati tutti i valori predefiniti specificati nel modello, quindi verrà richiesto di inserire i valori rimanenti.

Di seguito viene riportato un set di parametri di esempio dal file azuredeploy-parameters.json.

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

Inserire un nome per la distribuzione Azure, un nome per il gruppo di risorse, il percorso di Azure e la cartella in cui è stato salvato il file di distribuzione JSON. Quindi eseguire i seguenti comandi:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Quando si esegue il comando **New-AzureResourceGroupDeployment**, verranno estratti i valori dei parametri dal file dei parametri JSON e verrà avviata l'esecuzione del modello di conseguenza. La definizione e l'uso di più file di parametri con gli ambienti diversi (test, produzione e così via) promuoveranno il riutilizzo e la semplificazione di soluzioni con più ambienti complesse.

Durante la distribuzione tenere presente che è necessario creare un nuovo account di archiviazione di Azure, pertanto il nome specificato come parametro per l'account di archiviazione deve essere univoco e deve soddisfare tutti i requisiti previsti per un account di archiviazione di Azure, ovvero deve essere costituito solo da lettere minuscole e numeri.

Durante e dopo la distribuzione, è possibile controllare tutte le richieste effettuate durante il provisioning, compresi gli errori che si sono verificati.

A tale scopo, visitare il [portale di Azure](https://portal.azure.com) ed effettuare le operazioni seguenti:

- Fare clic su **Sfoglia** nella barra di spostamento a sinistra, quindi scorrere verso il basso e fare clic su **Gruppi di risorse**.  
- Fare clic sul gruppo di risorse appena creato, per visualizzare il pannello "Gruppo di risorse".  
- Facendo clic sul grafico a barre "Eventi" nella parte relativa al **Monitoraggio** del pannello "Gruppo di risorse", sarà possibile visualizzare gli eventi per la distribuzione.
- Facendo clic su singoli eventi è possibile esaminare i dettagli di ogni operazione eseguita per conto del modello.

Dopo aver effettuato i test, per rimuovere il gruppo di risorse e tutte le relative risorse (l'account di archiviazione, la macchina virtuale e la rete virtuale), usare il comando seguente:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Passaggio 3-b: distribuire un cluster DataStax usando un modello tramite l'interfaccia della riga di comando di Azure

Per distribuire un cluster DataStax tramite l'interfaccia della riga di comando di Azure, è necessario innanzitutto creare un gruppo di risorse specificando un nome e un percorso:

	azure group create dsc "West US"

Passare il nome di questo gruppo di risorse, il percorso del file di modello JSON e il percorso del file dei parametri (vedere la sezione precedente di Azure PowerShell per i dettagli) nel comando seguente:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

È possibile controllare lo stato delle distribuzioni delle singole risorse usando il comando seguente:

	azure group deployment list dsc

## Panoramica della struttura del modello DataStax e dell'organizzazione dei file

Per progettare un modello di Gestione risorse efficace e riutilizzabile, è necessaria un'ulteriore valutazione per organizzare la serie di attività complesse e correlate necessarie durante la distribuzione di una soluzione complessa come DataStax. Sfruttando le funzionalità di ciclo delle risorse e di collegamento dei modelli di Gestione risorse, oltre all'esecuzione di script mediante estensioni correlate, è possibile implementare un approccio modulare riutilizzabile praticamente con qualsiasi distribuzione complessa basata su un modello.

Questa sezione illustra in dettaglio la struttura del file azuredeploy.json per il cluster DataStax.

### sezione "parametri"

Nella sezione “parametri” di azuredeploy.json vengono specificati i parametri modificabili usati in questo modello. Il file azuredeploy-parameters.json menzionato in precedenza viene usato per passare i valori nella sezione "parametri" di azuredeploy.json durante l'esecuzione del modello.

### sezione "variabili"

La sezione "variabili" Specifica le variabili che possono essere usate in questo modello. Contiene un numero di campi (tipi di dati o frammenti JSON) che verranno impostati su costanti o valori calcolati in fase di esecuzione. Di seguito è riportata la sezione "variables" per questo modello DataStax:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

Esaminando questo esempio più dettagliatamente, verranno individuati due diversi approcci. In questo primo frammento la variabile **osSettings** è un elemento JSON annidato contenente quattro coppie chiave-valore:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
In questo secondo frammento la variabile **scripts** è una matrice JSON in cui ogni elemento verrà calcolato in fase di runtime mediante una funzione del linguaggio del modello (concat) e il valore di un'altra variabile più le costanti di stringa:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### sezione "risorse"

La sezione "risorse" rappresenta la posizione in cui si svolgono la maggior parte delle operazioni. Analizzando attentamente questa sezione, è possibile identificare immediatamente due diversi casi. Il primo è un elemento definito di tipo `Microsoft.Resources/deployments` che fondamentalmente indica la chiamata di una distribuzione annidata all'interno di quella principale. Tramite l'elemento **templateLink** (e la proprietà Version correlata), è possibile specificare un file modello collegato che verrà richiamato passando un set di parametri come input, come mostrato in questo frammento:

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

Da questo primo esempio, è chiaro come il file azuredeploy.json in questo scenario sia stato organizzato come meccanismo di orchestrazione, che richiama un determinato numero di altri file. Ogni file è responsabile di parte delle attività di distribuzione necessarie.

In particolare, i seguenti modelli collegati verranno usati per la distribuzione:

-	**shared-resource.json**: contiene la definizione di tutte le risorse che verranno condivise durante la distribuzione. Come esempi considerare gli account di archiviazione usati per archiviare i dischi del sistema operativo di una macchina virtuale e le reti virtuali.
-	**opscenter-resources.json**: distribuisce una macchina virtuale OpsCenter e tutte le risorse correlate, inclusi un'interfaccia di rete e un indirizzo IP pubblico.
-	**opscenter-install-resources.json**: distribuisce l'estensione della macchina virtuale OpsCenter (script personalizzato per Linux) che richiamerà il file di script bash specifico (opscenter.sh) necessario per configurare il servizio OpsCenter all'interno della macchina virtuale.
-	**ephemeral-nodes-resources.json**: distribuisce tutte le macchine virtuali dei nodi del cluster e le risorse connesse (schede di rete, IP privati e così via). Questo modello consentirà inoltre di distribuire le estensioni della macchina virtuale (gli script personalizzati per Linux) e di richiamare uno script bash (dsenode.sh) per installare fisicamente parti del server Cassandra Apache su ciascun nodo.

Esaminiamo questo ultimo modello, poiché è uno dei più interessanti dal punto di vista dello sviluppo dei modelli. Un concetto importante da evidenziare è come un unico file di modello possa consentire la distribuzione di più copie di un singolo tipo di risorsa e per ogni istanza di impostare valori univoci per le impostazioni necessarie. Questo concetto è noto come **Resource Looping (ciclo delle risorse)**.

Quando il file ephemeral-nodes-resources.json viene richiamato dall'interno del file azuredeploy.json principale, viene fornito un parametro denominato *nodeCount* come parte dell'elenco di parametri. All'interno del modello figlio, *nodeCount* (numero di nodi da distribuire nel cluster ) verrà usato nell'elemento **copy** di ogni risorsa da distribuire in più copie, come mostrato nel frammento seguente. Per tutte le impostazioni in cui sono necessari valori univoci tra istanze diverse della risorsa distribuita, è possibile usare la funzione **copyindex()** per ottenere un valore numerico che indica l'indice corrente nella creazione di quel particolare ciclo di risorse. Nel frammento seguente è possibile vedere questo concetto applicato a più macchine virtuali da creare per i nodi del cluster DataStax:

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

Un altro concetto importante nella creazione delle risorse è la possibilità di specificare le dipendenze e le priorità tra risorse, come è possibile vedere nella matrice JSON **dependsOn**. In questo particolare modello, ogni nodo sarà collegato a un disco da 1 TB (vedere "dataDisks") che può essere usato per l'hosting dei backup e degli snapshot dell'istanza di Apache Cassandra.

I Dischi collegati sono formattati nell'ambito delle attività di preparazione del nodo attivate dall'esecuzione del file di script dsenode.sh. La prima riga di tale script, infatti, richiama un altro script:

	bash vm-disk-utils-0.1.sh

Il file vm-disk-utils-0.1.sh fa parte della cartella **shared\_scripts\\ubuntu** all'interno del repository GitHub azure-quickstart-templates e contiene funzioni molto utili per il montaggio, la formattazione e lo striping dei dischi. Queste funzioni possono essere usate in tutti i modelli del repository.

Un altro frammento interessante da esplorare è quello correlato alle estensioni delle macchine virtuali CustomScriptForLinux. Queste vengono installate come tipo di risorsa separato, con una dipendenza in ogni nodo del cluster (e l'istanza OpsCenter). Le estensioni si avvalgono dello stesso meccanismo di ciclo delle risorse descritto per le macchine virtuali:

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

Acquisendo familiarità con gli altri file inclusi in questa distribuzione, sarà possibile comprendere tutti i dettagli e le procedure consigliate necessarie per organizzare e gestire le strategie di distribuzione complesse per le soluzioni a più nodi, basate su qualsiasi tecnologia, sfruttando i modelli di Gestione risorse di Azure. Sebbene non obbligatorio, è un approccio consigliato per strutturare i file di modello come mostrato nel diagramma seguente:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

In pratica, questo approccio suggerisce di:

-	Definire il file del modello di base come un punto centrale di orchestrazione per tutte le attività di distribuzione specifiche, sfruttando il modello di collegamento per richiamare le esecuzioni del sottomodello.
-	Creare un file di modello specifico che consentirà di distribuire tutte le risorse condivise tra tutte le altre attività di distribuzione specifiche (account di archiviazione, configurazione di rete virtuale e così via). Questa funzione può essere riusata in modo intensivo tra distribuzioni che prevedono requisiti simili in termini di infrastruttura comune.
-	Includere modelli di risorsa facoltativi per requisiti specifici di una determinata risorsa.
-	Per i membri identici di un gruppo di risorse (nodi in un cluster e così via), creare modelli specifici che sfruttano il ciclo di risorse per poter distribuire più istanze con proprietà univoche.
-	Per tutte le attività di post distribuzione (installazione del prodotto, configurazioni e così via), usare estensioni di distribuzione di script e creare script specifici per ogni tecnologia.

Per altre informazioni, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

<!---HONumber=Oct15_HO2-->