<properties
	pageTitle="DataStax sul modello di gestione risorse Ubuntu"
	description="Ulteriori informazioni su come distribuire con facilità un nuovo cluster DataStax in macchine virtuali Ubuntu utilizzando Azure PowerShell o l’interfaccia della riga di comando di Azure e un modello di gestione delle risorse"
	services="multiple"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# DataStax sul modello di gestione risorse Ubuntu

DataStax è un leader del settore nello sviluppo e nella fornitura di soluzioni commerciali, tra cui la soluzione Cassandra Apache ™ riservata alle grandi aziende, la tecnologia di database NoSQL distribuita open source ampiamente riconosciuta per essere flessibile, sempre attiva e potenzialmente scalabile per qualsiasi esigenza. DataStax è disponibile nelle varianti Enterprise (DSE) e Community (DSC), offre funzionalità di elaborazione in memoria, di sicurezza a livello aziendale, di analisi rapida e potente integrata e funzioni di ricerca a livello aziendale.

Oltre a ciò che era già disponibile in Azure Marketplace, ora è possibile distribuire facilmente un nuovo cluster DataStax nelle macchine virtuali Ubuntu utilizzando Azure PowerShell o l’interfaccia della riga di comando di Azure e un modello di Gestione risorse.

Appena distribuito cluster basati su questo modello ha la topologia descritta nel diagramma seguente, anche se possono essere ottenuti facilmente altre topologie personalizzando presentato approccio:

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

Essenzialmente, tramite un parametro, è possibile definire il numero di nodi che verranno distribuiti nel nuovo cluster Cassandra Apache e, oltre a ciò, un'istanza del servizio DataStax Operation Center verrà inoltre distribuita in una macchina virtuale autonoma all'interno della stessa rete virtuale, offrendo la possibilità di monitorare lo stato del cluster e di tutti i singoli nodi, aggiungere o rimuovere nodi e di eseguire tutte le attività amministrative correlate a tale cluster.

Una volta completata la distribuzione è possibile accedere all'istanza della macchina virtuale di Datastax Operations Center utilizzando l'indirizzo DNS configurato. La macchina virtuale dell’Operations Center ha la porta SSH 22 abilitata, ma anche la porta 8443 per la modalità HTTPS. L’indirizzo DNS per l’Operations Center includerà il nome DNS e la regione immessi come parametri durante la creazione di una distribuzione basata su questo modello nel formato `{dnsName}.{region}.cloudapp.azure.com`. Se è stata creata una distribuzione con il `dnsName` parametro impostato su "datastax" nella regione "West US", sarà possibile accedere alla macchina virtuale Datastax Operations Center per la distribuzione all’indirizzo `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]Il certificato utilizzato nella distribuzione è un certificato autofirmato che consente di creare un avviso visualizzato tramite il browser. È possibile attenersi alla procedura indicata sul sito Web di [Datastax](http://www.datastax.com/) per la sostituzione del certificato con il proprio certificato SSL.

Prima di addentrarsi in ulteriori dettagli relativi a Gestione risorse di Azure e al modello utilizzato per la distribuzione, assicurarsi che Microsoft Azure, PowerShell e l’interfaccia grafica della riga di comando di Microsoft Azure siano configurati e pronti all’uso.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Creare un cluster Cassandra basato su DataStax con un modello di Gestione risorse e Azure PowerShell

Attenersi alla seguente procedura per creare un cluster Cassandra, basato su DataStax, utilizzando un modello di Gestione risorse nell'archivio dei modelli Github con Microsoft Azure PowerShell.

### Passaggio 1: scaricare il file JSON per il modello e altri file.

Designare una cartella locale come posizione per i file di modello JSON e gli altri file (ad esempio, C:\\Azure\\Templates\\DataStax).

Sostituire il nome della cartella, quindi eseguire questi comandi.

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

In alternativa, è inoltre possibile clonare il repository dei modelli utilizzando un client git di propria scelta, ad esempio:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Al termine, cercare la cartella datastax-on-ubuntu nel percorso C:\\Azure\\Templates.

### Passaggio 2: acquisire familiarità (facoltativo) con i parametri del modello.

Quando si distribuiscono soluzioni non semplici come un cluster Cassandra Apache basato su DataStax, è necessario specificare un set di parametri di configurazione per gestire un numero di impostazioni necessarie. Dichiarando i parametri nella definizione del modello, è possibile specificare i valori durante l'esecuzione della distribuzione tramite un file esterno o dalla riga di comando.

Cercando la sezione "parameters" nella parte superiore del file azuredeploy.json, si individuerà il set di parametri richiesti dal modello per configurare un cluster DataStax. Di seguito è riportato un esempio di tale sezione nel modello azuredeploy.json:

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
				"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the opserations center public IP"
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

Con la descrizione dei parametri obbligatori, inclusi dettagli quali tipi di dati, valori consentiti e così via, è evidente che questa sezione sarà utile per qualsiasi attività di convalida relativa ai valori dei parametri passati al momento dell'esecuzione del modello in modalità interattiva (ad esempio, PowerShell o l’interfaccia della riga di comando di Azure), ma anche per qualsiasi interfaccia utente di individuazione automatica che può essere compilata dinamicamente analizzando l'elenco dei parametri obbligatori e la relativa descrizione.

### Passaggio 3: distribuire il nuovo cluster DataStax con il modello.

Preparazione di un file di parametro per la distribuzione si intende creare un file JSON che contenga i valori di runtime di tutti i parametri che verranno quindi passati come una singola entità per il comando di distribuzione.

Di seguito è riportato un esempio che è possibile trovare nel file azuredeploy-parameters.json:

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

Inserire un nome per la distribuzione di Microsoft Azure, il nome gruppo di risorse, la posizione di Microsoft Azure, la cartella del file JSON salvato, quindi eseguire questi comandi:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Quando si esegue il comando **New-AzureResourceGroupDeployment**, verranno estratti i valori dei parametri dal file JSON e verrà avviata l'esecuzione del modello di conseguenza. La definizione e l’uso di più file di parametri con gli ambienti diversi (ad esempio, test, produzione e così via) promuoveranno il riutilizzo e la semplificazione di soluzioni con più ambienti complesse.

Quando si effettua la distribuzione, tenere presente che è necessario creare un nuovo account di archiviazione di Azure in modo che il nome fornito come parametro di account di archiviazione sia univoco e soddisfi tutti i requisiti di un account di archiviazione Azure.

Durante e dopo la distribuzione, è possibile controllare tutte le richieste effettuate durante il provisioning, compresi gli errori che si sono verificati.

A tale scopo, visitare il [portale di Azure](https://portal.azure.com) ed effettuare le seguenti operazioni:

- Fare clic su "Sfoglia" nella barra di spostamento a sinistra, scorrere verso il basso e fare clic su "Gruppi di risorse".  
- Facendo clic sul gruppo di risorse appena creato, viene visualizzato il blade "Gruppo di risorse".  
- Facendo clic sul grafico a barre "Eventi" nella parte relativa al "Monitoraggio" del blade "Gruppo di risorse", sarà possibile visualizzare gli eventi per la distribuzione:
- Facendo clic su singoli eventi è possibile esaminare i dettagli di ogni singola operazione eseguita per conto del modello più in basso

Dopo aver effettuato i test, per rimuovere il gruppo di risorse e tutte le relative risorse (l'account di archiviazione, le macchine virtuali e la rete virtuale), utilizzare questo comando:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Passaggio 3-b: distribuire un cluster DataStax con un modello di Gestione risorse mediante l’interfaccia della riga di comando di Azure

Funzionalmente equivalente con l'approccio di PowerShell riportato sopra, la distribuzione di un cluster Cassandra Apache tramite l’interfaccia della riga di comando di Azure richiede innanzitutto la creazione di un gruppo di risorse specificandone il nome e il percorso:

	azure group create dsc "West US"

E, successivamente, richiamando una creazione di distribuzione e passando un nome di gruppo di risorse, il file dei parametri e il modello effettivo come illustrato di seguito:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

È inoltre possibile controllare lo stato di singole distribuzioni richiamando il comando seguente:

	azure group deployment list dsc

## Presentazione della struttura dei modelli e dell'organizzazione dei modelli create per distribuire DataStax su Ubuntu

Per creare un approccio efficace e riutilizzabile alla progettazione di modelli di Gestione risorse, è necessaria un’ulteriore valutazione per organizzare la serie di attività complesse e correlate necessarie durante la distribuzione di una soluzione complessa come DataStax. Sfruttando le funzionalità di **collegamento dei modelli**, **ciclo delle risorse** oltre all'esecuzione di script tramite le estensioni relative, è possibile implementare un approccio modulare che può essere riutilizzato con potenzialmente qualsiasi distribuzione complessa basata su modelli.

In questo diagramma sono illustrate le relazioni tra tutti i file scaricati da GitHub per la distribuzione:

![datastax-files](media/virtual-machines-datastax-template/datastax-files.png)

È stato già menzionato il ruolo del file **azuredeploy parameters.json**, che verrà utilizzato per passare un set di valori di parametro specificati durante l'esecuzione del modello, ma la parte principale di questo approccio di distribuzione è contenuta nel file **azuredeploy.json**. Ignorando la sezione parameters, come descritto in precedenza in questo documento, la sezione seguente è rappresentata da **"variables"**. Essenzialmente contiene un determinato numero di campi (tipi di dati o frammenti JSON) che verranno impostati su costanti o valori calcolati in fase di esecuzione, come viene descritto nell'esempio seguente:

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/trentmswanson/azure-quickstart-templates/master/datastax-on-ubuntu/",
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

Esaminando questo esempio più dettagliatamente, verranno individuati due diversi approcci. In questo primo frammento, la variabile variabile "osSettings" verrà impostata su un elemento JSON nidificato contenente 4 coppie chiave-valore:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
In questo secondo frammento invece, la variabile "scripts" è una matrice JSON in cui i singoli elementi verranno calcolati in fase di esecuzione utilizzando una funzione del linguaggio del modello (concat) e il valore di un'altra variabile più le costanti di stringa:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

La sezione **"resources"** rappresenta la posizione in cui si svolgono la maggior parte delle operazioni. Analizzando attentamente questa sezione, è possibile identificare immediatamente due diversi casi: il primo è un elemento definito di tipo `Microsoft.Resources/deployments` essenzialmente ciò implica la chiamata di una distribuzione nidificata all'interno di quella principale. Tramite l’elemento `templateLink` (e la proprietà della versione correlata), è possibile specificare un file di modello collegato che verrà richiamato passando un set di parametri come input, come è possibile notare in questo frammento:

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

Da questo primo esempio è chiaro come il file **azuredeploy.json** in questo scenario sia stato organizzato come meccanismo di orchestrazione, che richiama un determinato dumero di altri file di modello, ciascuno responsabile della parte delle attività di distribuzione necessarie.

In particolare, i seguenti modelli collegati verranno utilizzati per la distribuzione:

-	**shared-resource.json**:  contiene la definizione di tutte le risorse che verranno condivisi durante la distribuzione. Esempio: account di archiviazione utilizzati per archiviare i dischi del sistema operativo della macchina virtuale o in reti virtuali.
-	**opscenter-resources.json**: consente di distribuire la macchina virtuale dell’Operations Center e tutte le relative risorse, come interfaccia di rete, indirizzo IP pubblico e così via.
-	**opscenter-install-resources.json**: consente di distribuire estensioni della macchina virtuale dell’Operations Center (script personalizzato per Linux) che richiamerà il file di script bash specifico (**opscenter.sh**) richiesto per preparare il servizio Operations Center all'interno di tale macchina virtuale.
-	**ephemeral-nodes-resources.json**: consente di distribuire tutti i nodi del cluster di macchine virtuali e le risorse connesse (ad esempio, schede di rete, indirizzi IP privati e così via). Questo modello consentirà inoltre di distribuire le estensioni della macchina virtuale (gli script personalizzati per Linux) e di richiamare uno script bash (**dsenode.sh**) per installare fisicamente parti del server Cassandra Apache su ciascun nodo.

Si consiglia di eseguire il drill-down in questo ultimo modello, poiché è uno dei più interessanti da una prospettiva di sviluppo dei modelli. Un concetto importante da evidenziare è come un unico file di modello possa consentire la distribuzione di più copie di un singolo tipo di risorsa e per ogni istanza di impostare valori univoci per le impostazioni necessarie. Questo concetto è noto come **Ciclo delle risorse o Resource Looping**.

Quando viene richiamato il file **ephemeral-nodes-resources.json** dall'interno del file **azuredeploy.json** principale, in realtà, un parametro denominato **nodeCount** viene fornito all’interno dell’elenco di parametri. All'interno del modello figlio, tale parametro verrà utilizzato (numero di nodi per la distribuzione del cluster) all'interno dell’elemento **"copy"** di ogni risorsa che deve essere distribuita in più copie, come mostrato nel frammento riportato di seguito. Per tutte le impostazioni di cui è necessario specificare valori univoci tra istanze diverse della risorsa distribuita, è possibile utilizzare la funzione **copyindex()** per ottenere un valore numerico che indica l'indice corrente nel ciclo di creazione delle risorse specifico. Nel frammento seguente è possibile visualizzare questo concetto applicato alla creazione di più macchine virtuali per i nodi del cluster:

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

Un altro concetto importante nella creazione di risorse è la possibilità di specificare le dipendenze e le priorità tra risorse, come si noterà nella matriced JSON **dependsOn**. In questo particolare modello, per ogni nodo sarà inoltre necessario un disco collegato di 1 TB (vedere `dataDisks`) che può essere utilizzato per l'hosting di backup e di snapshot dall'istanza Cassandra Apache.

I Dischi collegati sono formattati nell’ambito delle attività di preparazione del nodo attivate dall'esecuzione del file di script **dsenode.sh**. La prima riga di script infatti richiama un altro script:

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh fa parte della cartella **shared_scripts\\ubuntu** all'interno dell'archivio github azure-quickstart-tempates e contiene funzioni molto utili per il montaggio, la formattazione e lo striping del disco, che possono essere riutilizzate ogni volta che si presenti la necessità di eseguire attività simili nell’ambito della creazione del modello.

Un altro frammento interessante da esplorare, è quello correlato alle estensioni della macchina virtuale CustomScriptForLinux. Questi strumenti vengono installati come un tipo di risorsa separato, con una dipendenza in ogni nodo del cluster e nell'istanza Operations Center sfruttando lo stesso meccanismo della risorsa di ciclo descritto per le macchine virtuali:

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

Tutti gli altri file di modello necessari per la distribuzione sono invece per la creazione di singole istanze di tutte le risorse necessarie, pertanto possono essere considerati una versione di semplice del file **ephemeral-nodes-resources.json**.

Acquisendo familiarità con gli altri file inclusi in questa distribuzione, sarà in possibile comprendere tutti i dettagli e le procedure consigliate necessarie per organizzare e orchestrare le strategie di distribuzione complesse per le soluzioni con più nodi, basate su qualsiasi tecnologia e che sfruttano i modelli di Gestione risorse di Azure. Sebbene non obbligatorio, è un approccio consigliato per strutturare i file di modello come mostrato nel diagramma seguente:

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

In pratica, questo approccio suggerisce di:

-	Definire il file del modello di base come un punto centrale di orchestrazione per tutte le attività di distribuzione specifiche, sfruttando il modello di collegamento per richiamare le esecuzioni del sottomodello
-	Creare un modello specifico di file che verranno distribuiti a tutte le risorse condivise tra tutte le altre attività di distribuzione specifiche (ad esempio gli account di archiviazione, configurazione di rete virtuale e così via). Questa funzione può essere riutilizzata in modo intensivo tra distribuzioni che prevedono requisiti simili in termini di infrastruttura comune.
-	Includere modelli di risorsa facoltativi per requisiti specifici di una determinata risorsa
-	Per i membri identici di un gruppo di risorse (nodi in un cluster e così via), creare modelli specifici che sfruttano il ciclo di risorse per poter distribuire più istanze con proprietà univoche
-	Per tutte le attività di post-distribuzione (ad esempio, installazione del prodotto, configurazioni e così via) utilizzare estensioni di distribuzione di script e creare script specifici per ogni tecnologia

Per ulteriori informazioni, vedere il [Linguaggio del modello di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!--HONumber=52-->
