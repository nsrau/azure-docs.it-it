<properties 
	pageTitle="Il modello di gestione risorse di farm di SharePoint a tre server" 
	description="Esaminare la struttura del modello di gestione risorse di Azure per la farm di SharePoint a tre server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Il modello di gestione risorse di farm di SharePoint a tre server

In questo argomento viene descritta la struttura del file di modello azuredeploy.json per la farm di SharePoint a tree server. È possibile visualizzare il contenuto di questo modello nel browser da [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json).

In alternativa, per esaminare una copia locale del file azuredeploy.json, specificare una cartella locale come posizione per il file e crearlo (ad esempio, C:\Azure\Templates\SharePointFarm). Inserire il nome della cartella ed eseguire questi comandi nel prompt dei comandi di Azure PowerShell.

	$folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"	
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

Aprire il modello azuredeploy.json in un editor di testo o in uno strumento a scelta. Di seguito vengono descritti la struttura del file di modello e lo scopo di ogni sezione.

## sezione "parametri"

La sezione "parametri" specifica i parametri utilizzati per inserire dati in questo modello. È possibile definire un massimo di 50 parametri. Di seguito è riportato un esempio di un parametro per l'indirizzo di Azure:

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## sezione "variabili"

La sezione "variabili" Specifica le variabili che possono essere utilizzate in questo modello. È possibile definire un massimo di 100 variabili. Di seguito sono riportati alcuni esempi:

	"LBFE": "LBFE",
	"LBBE": "LBBE",
	"RDPNAT": "RDP",
	"spWebNAT": "spWeb",
	"adSubnetName": "adSubnet",
	"sqlSubnetName": "sqlSubnet",
	"spSubnetName": "spSubnet",
	"adNicName": "adNic",
	"sqlNicName": "sqlNic",
	"spNicName": "spNic",

## sezione "risorse"

Il **"risorse"** sezione specifica le informazioni necessarie per distribuire le risorse per la farm di SharePoint in un gruppo di risorse. Un massimo di 250 risorse può essere definito e le dipendenze della risorsa possono essere solo definita 5 livelli.

In questa sezione sono disponibili le procedure seguenti:

### Microsoft.Storage/storageAccounts  

In questa sezione consente di creare un nuovo account di archiviazione per tutte le risorse disco rigido virtuale e disco per la farm. Ecco il codice JSON per l'account di archiviazione:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2015-05-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

Queste sezioni creare un set di indirizzi IP pubblici tramite il quale ogni macchina virtuale può essere raggiunto tramite Internet. Di seguito è fornito un esempio:

	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

In queste sezioni vengono creati tre set di disponibilità, uno per ogni livello della distribuzione:

- Controller di dominio Active Directory
- Cluster di SQL Server
- SharePoint Server

Di seguito è fornito un esempio:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

In questa sezione viene creata una rete virtuale solo cloud con tre subnet (una per ogni livello della distribuzione), in cui vengono posizionate le macchine virtuali. Ecco il codice JSON:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkAddressRange')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},


### Microsoft.Network/loadBalancers

Queste sezioni crea istanze del servizio di bilanciamento del carico per ogni macchina virtuale fornire NAT e filtro per il traffico in ingresso da Internet. Per ogni servizio di bilanciamento del carico, le impostazioni di configurare il front-end, back-end e le regole NAT in entrata. Ad esempio, esistono regole del traffico di Desktop remoto per ogni macchina virtuale e, per il server SharePoint, una regola per consentire il traffico web in ingresso (porta TCP 80) da Internet. Ecco un esempio per il server SharePoint:


	{
		"apiVersion": "2015-05-01-preview",
		"name": "[variables('spLBName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "[variables('LBFE')]",
					"properties": {
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "[variables('LBBE')]"
				}
			],
			"inboundNatRules": [
				{
					"name": "[variables('RDPNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": "[parameters('RDPPort')]",
						"backendPort": 3389,
						"enableFloatingIP": false
					}
				},
				{
					"name": "[variables('spWebNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": 80,
						"backendPort": 80,
						"enableFloatingIP": false
					}
				}
			]
		}
	},

### Microsoft.Network/networkInterfaces

In queste sezioni viene creata un'interfaccia di rete per ogni macchina virtuale e viene configurato un indirizzo IP statico per il controller di dominio. Ecco l'esempio per l'interfaccia di rete del controller di dominio:

	{
		"name": "[variables('adNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
		],
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress": "[parameters('adNicIPAddress')]",
						"subnet": {
							"id": "[variables('adSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id": "[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

In queste sezioni vengono create e configurate le tre macchine virtuali nella distribuzione.

Nella prima sezione viene creato e configurato il controller di dominio, che:

- Specifica account di archiviazione, set di disponibilità, interfaccia di rete e istanza del servizio di bilanciamento
- Aggiunge un altro disco
- Esegue uno script di PowerShell per configurare il controller di dominio

Ecco il codice JSON:

		{
			"apiVersion": "2015-05-01-preview",
			"type": "Microsoft.Compute/virtualMachines",
			"name": "[parameters('adVMName')]",
			"location": "[parameters('deploymentLocation')]",
			"dependsOn": [
				"[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
				"[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
				"[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
				"[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
			],
			"properties": {
				"hardwareProfile": {
					"vmSize": "[parameters('adVMSize')]"
				},
				"availabilitySet": {
					"id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
				},
				"osProfile": {
					"computername": "[parameters('adVMName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"storageProfile": {
					"imageReference": {
						"publisher": "[parameters('adImagePublisher')]",
						"offer": "[parameters('adImageOffer')]",
						"sku": "[parameters('adImageSKU')]",
						"version": "latest"
					},
					"osDisk": {
						"name": "osdisk",
						"vhd": {
							"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
						},
						"caching": "ReadWrite",
						"createOption": "FromImage"
					},
					"dataDisks": [
						{
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
							},
							"name": "[concat(parameters('adVMName'),'-data-disk1')]",
							"caching": "None",
							"createOption": "empty",
							"diskSizeGB": "[variables('adDataDiskSize')]",
							"lun": 0
						}
					]
				},
				"networkProfile": {
					"networkInterfaces": [
						{
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
						}
					]
				}
			},
			"resources": [
				{
					"type": "Microsoft.Compute/virtualMachines/extensions",
					"name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
					"apiVersion": "2015-05-01-preview",
					"location": "[parameters('deploymentLocation')]",
					"dependsOn": [
						"[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
					],
					"properties": {
						"publisher": "Microsoft.Powershell",
						"type": "DSC",
						"typeHandlerVersion": "1.7",
						"settings": {
							"ModulesUrl": "[variables('adModulesURL')]",
							"ConfigurationFunction": "[variables('adConfigurationFunction')]",
							"Properties": {
								"DomainName": "[parameters('domainName')]",
								"AdminCreds": {
									"UserName": "[parameters('adminUserName')]",
									"Password": "PrivateSettingsRef:AdminPassword"
								}
							}
						},
						"protectedSettings": {
							"Items": {
								"AdminPassword": "[parameters('adminPassword')]"
							}
						}
					}
				}
			]
		},

Una sezione aggiuntiva per il controller di dominio che inizia con **"nome": "UpdateVNetDNS"** consente di configurare il server DNS della rete virtuale per usare gli indirizzi IP statici del controller di dominio.

La sezione **"type": "Microsoft.Compute/virtualMachines"** successiva consente di creare le macchine virtuali di SQL Server nella distribuzione e:

- Specifica account di archiviazione, set di disponibilità, bilanciamento del carico, rete virtuale e interfaccia di rete
- Aggiunge un altro disco

Altre sezioni **"Microsoft.Compute/virtualMachines/extensions"** consentono di chiamare lo script di PowerShell per configurare SQL Server.

La sezione **"type": "Microsoft.Compute/virtualMachines"** successiva consente di creare la macchina virtuale di SharePoint nella distribuzione, specificando account di archiviazione, set di disponibilità, bilanciamento del carico, rete virtuale e interfaccia di rete. Un’altra sezione **"Microsoft.Compute/virtualMachines/extensions"** consente di chiamare uno script di PowerShell per configurare la farm di SharePoint.

Si noti l'organizzazione generale delle sottosezioni del **"risorse"** sezione del file JSON:

1.	Creare gli elementi dell'infrastruttura di Azure che sono necessari per il supporto di più macchine virtuali (un account di archiviazione, gli indirizzi IP pubblici, i set di disponibilità, una rete virtuale, interfacce di rete, istanze del servizio di bilanciamento del carico).
2.	Creare la macchina virtuale del controller di dominio, che utilizza gli elementi comuni e specifici creati in precedenza dell'infrastruttura di Azure, aggiunge un disco dati ed esegue uno script di PowerShell. Inoltre, aggiornare la rete virtuale per utilizzare l’indirizzo IP statico del controller di dominio.
3.	Creare la macchina virtuale di SQL Server, che utilizza gli elementi comuni e specifici creati in precedenza dell'infrastruttura di Azure creata per il controller di dominio, aggiunge dischi dati ed esegue uno script di PowerShell per configurare SQL Server.
4.	Creare la macchina virtuale di SharePoint server, che utilizza gli elementi comuni e specifici creati in precedenza dell'infrastruttura di Azure ed esegue uno script di PowerShell per configurare la farm di SharePoint.

Un modello JSON per compilare un'infrastruttura a più livelli in Azure deve seguire la stessa procedura:

1.	Creare il comune (account di archiviazione, rete virtuale), livello specifico (set di disponibilità) e macchina virtuale specifico (indirizzi IP pubblici, i set di disponibilità, le interfacce di rete e istanze del servizio di bilanciamento del carico) gli elementi dell'infrastruttura di Azure che sono necessari per la distribuzione.
2.	Per ogni livello dell'applicazione (ad esempio l'autenticazione web, database), creare e configurare i server in tale livello mediante comuni (account di archiviazione, rete virtuale), livello specifico (set di disponibilità) ed elementi specifici della macchina virtuale (indirizzi IP pubblici, interfacce di rete, istanze del servizio di bilanciamento del carico).

Per altre informazioni, vedere il [linguaggio del modello di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).

## Risorse aggiuntive

[Provider di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](../resource-group-overview.md)

[Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md)

[Macchine virtuali - Documentazione](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=58--> 