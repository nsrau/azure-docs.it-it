<properties
	pageTitle="DataStax Enterprise su Ubuntu con un modello di Gestione risorse"
	description="Ulteriori informazioni su come distribuire con facilità un nuovo cluster DataStax Enterprise in macchine virtuali Ubuntu utilizzando Azure PowerShell o l’interfaccia della riga di comando di Azure e un modello di Gestione risorse"
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>
<!-- In pageTitle, to follow corporate style (sentence-case caps), s/b lowercase "template", correct? This matches what is used later in article too. Also, precede first mention of "Azure" with "Microsoft" -->
<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# DataStax Enterprise su Ubuntu con un modello di Gestione risorse

DataStax è leader del settore nello sviluppo e nella fornitura di soluzioni basate su Apache Cassandra™ la tecnologia di database NoSQL distribuita, di livello enterprise supportata in commercio ampiamente riconosciuta per le caratteristiche di flessibilità, disponibilità e scalabilità per qualsiasi esigenza. DataStax offre versioni sia Enterprise (DSE) che Community (DSC). Oltre alle funzionalità offerte dalla versione Community, Datastax Enterprise è certificato per la produzione con funzionalità di elaborazione in memoria, protezione a livello aziendale, analisi integrata veloce e potente e gestione aziendale.

>[AZURE.NOTE]A differenza della versione Community, per distribuire DataStax Enterprise è necessario disporre di un account DataStax (nome utente e password) valido da passare come parametri durante la distribuzione del modello. Visitare il sito Web di [Datastax](http://www.datastax.com) per configurare l'account, se non è già stato configurato.

Oltre alle funzionalità già disponibili in Azure Marketplace, ora è possibile distribuire facilmente un nuovo cluster DataStax Enterprise nelle macchine virtuali Ubuntu utilizzando un modello di Gestione risorse distribuito tramite [Azure PowerShell](../powershell-install-configure.md) o l’[interfaccia della riga di comando di Azure](../xplat-cli.md).

La topologia dei cluster appena distribuiti basati su questo modello è descritta nel diagramma seguente, sebbene sia possibile ottenere facilmente altre topologie personalizzando il modello presentato in questo articolo:

![cluster-architecture](media/virtual-machines-datastax-enterprise-template/cluster-architecture.png)

Mediante i parametri, è possibile definire il numero di nodi che verranno distribuiti nel nuovo cluster Apache Cassandra. Un'istanza del servizio DataStax Operation Center verrà inoltre distribuita in una macchina virtuale autonoma all'interno della stessa rete virtuale, offrendo la possibilità di monitorare lo stato del cluster e di tutti i singoli nodi, aggiungere/rimuovere nodi e eseguire tutte le attività amministrative correlate a tale cluster.

Una volta completata la distribuzione, è possibile accedere all'istanza della macchina virtuale di Datastax Operations Center utilizzando l'indirizzo DNS configurato. La macchina virtuale dell’Operations Center ha la porta SSH 22 abilitata, ma anche la porta 8443 per la modalità HTTPS. L’indirizzo DNS per l’Operations Center includerà *dnsName* e *region* immessi come parametri, dando luogo al formato `{dnsName}.{region}.cloudapp.azure.com`. Se è stata creata una distribuzione con il parametro *dnsName* impostato su "datastax” nell’area "West US”, è possibile accedere alla macchina virtuale Datastax Operations Center per la distribuzione all’indirizzo `https://datastax.westus.cloudapp.azure.com:8443`.

> [AZURE.NOTE]Il certificato utilizzato nella distribuzione è un certificato autofirmato che consente di creare un avviso visualizzato tramite il browser. È possibile attenersi alla procedura indicata sul sito Web di [Datastax](http://www.datastax.com/) per la sostituzione del certificato con il proprio certificato SSL.

Prima di addentrarsi in ulteriori dettagli relativi a Gestione risorse di Azure e al modello utilizzato per la distribuzione, assicurarsi che Azure PowerShell o l’interfaccia della riga di comando di Azure sia configurata correttamente.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Creare un cluster Cassandra basato su Datastax Enterprise con un modello di Gestione risorse

Attenersi alla seguente procedura per creare un cluster Apache Cassandra, basato su DataStax Enterprise, utilizzando un modello di Gestione risorse dall'archivio dei modelli Github. Ogni passaggio includerà le indicazioni sia per Azure PowerShell che per l’interfaccia della riga di comando di Azure.

### Passaggio 1-a: Scaricare i file di modello tramite PowerShell

Creare una cartella locale per il modello JSON e gli altri file associati (ad esempio, C:\Azure\Templates\DataStax).

Sostituire il nome della cartella della cartella locale ed eseguire questi comandi:

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-enterprise/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

### Passaggio 1-b: Scaricare i file di modello tramite l'interfaccia della riga di comando di Azure

Clonare il repository dell'intero modello utilizzando un client git di propria scelta, come illustrato nell'esempio riportato di seguito.

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Una volta completata l’operazione, cercare la cartella datastax-enterprise nella directory C:\Azure\Templates.
<!--Wrapping name of folder in bold typeface is not corp style  -->
### Passaggio 2: (facoltativo) Comprendere i parametri del modello

Quando si distribuiscono soluzioni non semplici come un cluster Apache Cassandra basato su DataStax, è necessario specificare un set di parametri di configurazione per gestire un gran numero di impostazioni necessarie. Dichiarando i parametri nella definizione del modello, è possibile specificare i valori durante la distribuzione tramite un file esterno o nella riga di comando.

Nella sezione "parametri", nella parte superiore del file azuredeploy.json, è disponibile il set dei parametri richiesti dal modello per configurare un cluster Datastax Enterprise. Di seguito è riportato un esempio della sezione dei parametri dal file azuredeploy.json di questo modello:

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
			"datastaxUsername": {
				"type": "string",
				"metadata": {
					"description": "Your Datastax account username.  If you do not have one go to (datastax.com)"
				}
			},
			"datastaxPassword": {
				"type": "securestring",
				"metadata": {
					"description": "Your Datastax account password"
				}
			},
			"opsCenterAdminPassword": {
				"type": "securestring",
				"metadata": {
					"description": "Sets the operations center admin user password.  You will use this when you login to the operations center portal"
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

Ogni parametro include dettagli quali il tipo di dati e i valori consentiti. Ciò consente la convalida dei parametri passati durante l'esecuzione del modello in modalità interattiva (ad esempio, PowerShell o l’interfaccia della riga di comando di Azure) e un'interfaccia utente di individuazione automatica che può essere compilata dinamicamente analizzando l'elenco dei parametri richiesti e le relative descrizioni.

### Passaggio 3-a: Distribuzione di un cluster DataStax Enterprise con un modello tramite PowerShell

Preparare un file dei parametri per la distribuzione creando un file JSON con i valori di runtime per tutti i parametri. Questo file verrà quindi passato al comando di distribuzione come singola entità. Se non si include un file dei parametri, in PowerShell verranno usati tutti i valori predefiniti specificati nel modello, quindi verrà richiesto di inserire i valori rimanenti.

Di seguito viene fornito un set di parametri di esempio dal file azuredeploy-parameters.json.

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
		"datastaxUsername": {
			"value": "scorianidx"
		},
		"datastaxPassword": {
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

Inserire un nome per la distribuzione Azure, un nome per il gruppo di risorse, il percorso di Azure e la cartella in cui è stato salvato il file di distribuzione JSON. Eseguire quindi il set successivo di comandi.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Quando si esegue il comando **New-AzureResourceGroupDeployment**, verranno estratti i valori dei parametri dal file dei parametri JSON e verrà avviata l'esecuzione del modello di conseguenza. La definizione e l'uso di più file di parametri con ambienti diversi (ad esempio, test, produzione e così via) promuoveranno il riutilizzo e la semplificazione di soluzioni con più ambienti complesse.

Quando si effettua la distribuzione, tenere presente che è necessario creare un nuovo account di Archiviazione di Azure, in modo che il nome fornito come parametro di account di archiviazione sia univoco e soddisfi tutti i requisiti di un account di Archiviazione di Azure (solo lettere minuscole e numeri).

Durante e dopo la distribuzione, è possibile controllare tutte le richieste effettuate durante il provisioning, compresi gli errori che si sono verificati.

A tale scopo, visitare il [portale di Azure](https://portal.azure.com) ed effettuare le seguenti operazioni:

- Fare clic su "Sfoglia" nella barra di spostamento a sinistra, scorrere verso il basso e fare clic su "Gruppi di risorse".
- Dopo aver fatto clic sul gruppo di risorse appena creato, viene visualizzato il pannello "Gruppo di risorse".
- Facendo clic sul grafico a barre "Eventi" nella parte relativa al monitoraggio del pannello "Gruppo di risorse", sarà possibile visualizzare gli eventi per la distribuzione:
- Facendo clic sui singoli eventi è possibile esaminare i dettagli di ogni singola operazione eseguita per conto del modello.

Dopo aver effettuato i test, per rimuovere il gruppo di risorse e tutte le relative risorse (l'account di archiviazione, le macchine virtuali e la rete virtuale), utilizzare questo comando:

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Passaggio 3-b: Distribuzione di un cluster DataStax Enterprise con un modello tramite l’interfaccia della riga di comando di Azure

Per distribuire un cluster Datastax Enterprise tramite l’interfaccia della riga di comando di Azure, è necessario innanzitutto creare un gruppo di risorse specificando un nome e un percorso con il seguente comando:

	azure group create dsc "West US"

Passare il nome di questo gruppo di risorse, il percorso del file di modello JSON e il percorso del file dei parametri (vedere la sezione precedente di PowerShell per i dettagli) nel comando seguente:

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

È possibile controllare lo stato delle distribuzioni delle singole risorse con il comando seguente:

	azure group deployment list dsc

## Panoramica della struttura del modello Datastax Enterprise e dell’organizzazione dei file

Per progettare un modello di Gestione risorse efficace e riutilizzabile, è necessaria un’ulteriore valutazione per organizzare la serie di attività complesse e correlate necessarie durante la distribuzione di una soluzione complessa come DataStax Enterprise. Sfruttando le funzionalità di **collegamento dei modelli** e **ciclo delle risorse** ARM, oltre all'esecuzione di script tramite le estensioni correlate, è possibile implementare un approccio modulare che in teoria può essere riutilizzato con qualsiasi distribuzione complessa basata sui modelli.
<!-- In previous paragraph, we can't use bold typeface to show emphasis. You can use italic to denote emphasis. -->
In questo diagramma sono illustrate le relazioni tra tutti i file scaricati da GitHub per questa distribuzione:

![datastax-enterprise-files](media/virtual-machines-datastax-enterprise-template/datastax-enterprise-files.png)

In questa sezione viene fornita una descrizione della struttura del file azuredeploy.json per il cluster Datastax Enterprise.

### sezione "parametri"

Nella sezione “parametri” di azuredeploy.json vengono specificati i parametri modificabili utilizzati in questo modello. Il file azuredeploy-parameters.json citato in precedenza viene usato per passare i valori nella sezione "parametri" di azuredeploy.json durante l'esecuzione del modello.

### sezione "variabili"

La sezione "variabili" Specifica le variabili che possono essere usate in questo modello. Contiene un numero di campi (tipi di dati o frammenti JSON) che verranno impostati su costanti o valori calcolati in fase di esecuzione. Nell'esempio seguente viene illustrata la sezione "variabili" per il modello Datastax.

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/datastax-enterprise/",
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

Nell'esempio precedente, è possibile visualizzare due diversi approcci. In questo primo frammento, la variabile "osSettings" è un elemento JSON nidificato contenente 4 coppie chiave-valore:

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
In questo secondo frammento, la variabile "scripts" è una matrice JSON in cui i singoli elementi verranno calcolati in fase di esecuzione utilizzando una funzione del linguaggio del modello (concat) e il valore di un'altra variabile, più le costanti di stringa:

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

### sezione "risorse"

La sezione "risorse" rappresenta la posizione in cui si svolgono la maggior parte delle operazioni. Analizzando attentamente questa sezione, è possibile identificare immediatamente due diversi casi: il primo è un elemento definito di tipo `Microsoft.Resources/deployments`e essenzialmente ciò implica la chiamata di una distribuzione nidificata all'interno di quella principale. Tramite l'elemento "templateLink" (e la proprietà della versione correlata), è possibile specificare un file di modello collegato che verrà richiamato passando un set di parametri come input, come è possibile notare nel seguente esempio:

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

Da questo primo esempio è chiaro come il file azuredeploy.json in questo scenario sia stato organizzato come meccanismo di orchestrazione, che richiama un determinato numero di altri file di modello, ciascuno responsabile di parte delle attività di distribuzione necessarie.

In particolare, i seguenti modelli collegati verranno utilizzati per la distribuzione:
<!-- In list format, using bold typeface in the following manner is ok -->
-	**shared-resource.json**: contiene la definizione di tutte le risorse che verranno condivise durante la distribuzione. Esempi sono account di archiviazione utilizzati per archiviare i dischi del sistema operativo della macchina virtuale e le reti virtuali.
-	**opscenter resources.json**: consente di distribuire una macchina virtuale OpsCenter e tutte le relative risorse, inclusi un'interfaccia di rete e un indirizzo IP pubblico.
-	**opscenter-install-resources.json**: consente di distribuire l'estensione macchine virtuali OpsCenter (script personalizzato per Linux) che richiamerà il file di script bash specifico (opscenter.sh) necessario per configurare il servizio OpsCenter all'interno di tale macchina virtuale.
-	**ephemeral-nodes-resources.json**: consente di distribuire tutti i nodi del cluster delle macchine virtuali e le risorse connesse (ad esempio, schede di rete e indirizzi IP privati.). Questo modello consentirà inoltre di distribuire le estensioni della macchina virtuale (gli script personalizzati per Linux) e di richiamare uno script bash (dsenode.sh) per installare fisicamente parti del server Cassandra Apache su ciascun nodo.

Esaminiamo questo ultimo modello, poiché è uno dei più interessanti dal punto di vista dello sviluppo dei modelli. Un concetto importante da evidenziare è come un unico file di modello possa consentire la distribuzione di più copie di un singolo tipo di risorsa e per ogni istanza di impostare valori univoci per le impostazioni necessarie. Questo concetto è noto come Resource Looping (ciclo delle risorse).

Quando viene richiamato il file ephemeral-nodes-resources.json dall'interno del file azuredeploy.json principale, viene fornito un parametro denominato nodeCount come parte dell’elenco di parametri. All'interno del modello figlio, il parametro nodeCount (il numero di nodi da distribuire nel cluster) sarà utilizzato nell’elemento **"copy"** di ogni risorsa che deve essere distribuita in più copie, come evidenziato nel frammento riportato di seguito. Per tutte le impostazioni in cui sono necessari valori univoci tra istanze diverse della risorsa distribuita, è possibile utilizzare la funzione **copyindex()** per ottenere un valore numerico che indica l'indice corrente nella creazione di quel particolare ciclo di risorse. Nel frammento seguente è possibile osservare questo concetto applicato alla creazione di più macchine virtuali per i nodi del cluster Datastax Enterprise:

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

Un altro concetto importante nella creazione delle risorse è la possibilità di specificare le dipendenze e le priorità tra risorse, come è possibile vedere nella matrice JSON **dependsOn**. In questo particolare modello, ogni nodo sarà collegato a un disco da 1 TB (vedere "dataDisks") che può essere utilizzato per l'hosting dei backup e degli snapshot dell'istanza di Apache Cassandra.

I Dischi collegati sono formattati nell’ambito delle attività di preparazione del nodo attivate dall'esecuzione del file di script dsenode.sh. Nell'esempio seguente la prima riga dello script richiama un altro script.

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh fa parte della cartella shared_scripts\ubuntu all'interno del repository github azure-quickstart-tempates e contiene funzioni molto utili per il montaggio, la formattazione e lo striping dei dischi. che possono essere utilizzate in tutti i modelli del repository.

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

Acquisendo familiarità con gli altri file inclusi in questa distribuzione sarà in possibile comprendere tutti i dettagli e le procedure consigliate necessarie per organizzare e orchestrare le strategie di distribuzione complesse per le soluzioni con più nodi, basate su qualsiasi tecnologia, che sfruttano i modelli di Gestione risorse di Azure. Sebbene non obbligatorio, un approccio consigliato è quello di strutturare i file di modello come mostrato nel diagramma seguente:

![datastax-enterprise-template-structure](media/virtual-machines-datastax-enterprise-template/datastax-enterprise-template-structure.png)

Questo approccio suggerisce che è:

-	È necessario definire il file del modello di base come un punto centrale di orchestrazione per tutte le attività di distribuzione specifiche, sfruttando il modello di collegamento per richiamare le esecuzioni del sotto modello.
-	Bisogna inoltre creare un file di modello specifico che consentirà di distribuire tutte le risorse condivise tra tutte le altre attività di distribuzione specifiche (ad esempio gli account di archiviazione, configurazione di rete virtuale e così via). Questa funzione può essere riusata in modo intensivo tra distribuzioni che prevedono requisiti simili in termini di infrastruttura comune.
-	Includere modelli di risorsa facoltativi per requisiti specifici di una determinata risorsa.
-	Per i membri identici di un gruppo di risorse (ad esempio, nodi in un cluster e così via), creare modelli specifici che sfruttano il ciclo di risorse per poter distribuire più istanze con proprietà univoche
-	Per tutte le attività di post-distribuzione (ad esempio, installazione del prodotto, configurazioni e così via) usare estensioni di distribuzione di script e creare script specifici per ogni tecnologia.

Per altre informazioni, vedere il [linguaggio del modello di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!---HONumber=August15_HO6-->