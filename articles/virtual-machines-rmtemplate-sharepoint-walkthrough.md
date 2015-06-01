<properties 
	pageTitle="Il modello di gestione risorse di Farm di SharePoint a disponibilità elevata" 
	description="Esaminare la struttura del modello di gestione risorse di Azure per la farm di SharePoint a disponibilità elevata." 
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

# Il modello di gestione risorse di Farm di SharePoint a disponibilità elevata

In questo argomento viene descritta la struttura del file di modello azuredeploy.json per la farm di SharePoint a disponibilità elevata.

## sezione "parametri"

I "parametri" Specifica i parametri utilizzati per inserire dati in questo modello. È possibile definire un massimo di 50 parametri. Di seguito è riportato un esempio di un parametro per l'indirizzo di Azure:

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

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## sezione "risorse"

Il **"risorse"** sezione specifica le informazioni necessarie per distribuire le risorse per la farm di SharePoint in un gruppo di risorse. Un massimo di 250 risorse può essere definito e le dipendenze della risorsa possono essere solo definita 5 livelli.

In questa sezione sono disponibili le procedure seguenti:

### Microsoft.Storage/storageAccounts  

In questa sezione consente di creare un nuovo account di archiviazione per tutte le risorse disco rigido virtuale e disco per la farm. Ecco il codice JSON per l'account di archiviazione:

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

Queste sezioni creare un set di indirizzi IP pubblici tramite il quale ogni macchina virtuale può essere raggiunto tramite Internet. Di seguito è fornito un esempio:

	{
		"apiVersion": "2014-12-01-preview",
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

Queste sezioni creare quattro set di disponibilità, uno per ogni livello della distribuzione:

- Controller di dominio Active Directory
- Cluster di SQL Server
- Server livello applicazione
- Server di livello Web

Di seguito è fornito un esempio:

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

Questa sezione viene creata una rete virtuale solo cloud con quattro subnet (uno per ogni livello della distribuzione), in cui vengono posizionate le macchine virtuali. Ecco il codice JSON:

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

Queste sezioni crea istanze del servizio di bilanciamento del carico per ogni macchina virtuale fornire NAT e filtro per il traffico in ingresso da Internet. Per ogni servizio di bilanciamento del carico, le impostazioni di configurare il front-end, back-end e le regole NAT in entrata. Ad esempio, esistono regole del traffico di Desktop remoto per ogni macchina virtuale e una regola per consentire il traffico web in ingresso (porta TCP 80) da Internet per i server di livello web. Ecco un esempio per il server di livello web:

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

Queste sezioni creare un'interfaccia di rete per ogni macchina virtuale e di configurare gli indirizzi IP statici per i controller di dominio. Ecco l'esempio per l'interfaccia di rete per il controller di dominio primario:

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
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

Queste sezioni creare e configurare le macchine virtuali nove nella distribuzione.

Le prime due sezioni principali crea e configura i controller di dominio nella distribuzione. Ogni sezione:

- Specifica l'account di archiviazione, set di disponibilità, interfaccia di rete e istanza del servizio di bilanciamento del carico per ogni macchina virtuale controller di dominio
- Aggiunge un disco aggiuntivo per ogni macchina virtuale controller di dominio
- Esegue lo script di PowerShell per configurare le macchine virtuali come controller di dominio

Ecco un esempio per il controller di dominio primario:

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
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


Una sezione aggiuntiva dopo ogni controller di dominio inizia con **"nome": "UpdateVNetDNS"** consente di configurare i server DNS della rete virtuale per utilizzare gli indirizzi IP statici di due controller di dominio.

I successivi tre **"type": "Microsoft.Compute/virtualMachines"** sezioni creare le macchine virtuali del cluster SQL Server nella distribuzione. Ogni sezione:

- Specifica l'account di archiviazione, set di disponibilità, bilanciamento del carico, rete virtuale e interfaccia di rete per ogni macchina virtuale
- Aggiunge due dischi aggiuntivi per ogni computer SQL server

Ulteriori **"Microsoft.Compute/virtualMachines/extensions"** sezioni chiamare lo script di PowerShell per configurare le macchine virtuali di cluster SQL Server, il cluster di SQL server e abilitare i gruppi di disponibilità AlwaysOn.

Gli altri quattro **"type": "Microsoft.Compute/virtualMachines"** sezioni creare macchine virtuali farm di SharePoint nella distribuzione. Ogni sezione specifica l'account di archiviazione, set di disponibilità, bilanciamento del carico, rete virtuale e interfaccia di rete per ogni macchina virtuale.

Ulteriori **"Microsoft.Compute/virtualMachines/extensions"** sezioni chiamare gli script di PowerShell per configurare il server SharePoint come una farm di SharePoint.

Si noti l'organizzazione generale delle sottosezioni del **"risorse"** sezione del file JSON:

1.	Creare gli elementi dell'infrastruttura di Azure che sono necessari per il supporto di più macchine virtuali (un account di archiviazione, gli indirizzi IP pubblici, i set di disponibilità, una rete virtuale, interfacce di rete, istanze del servizio di bilanciamento del carico).
2.	Creare il dominio controller macchine virtuali, utilizzare gli elementi comuni e specifici creato in precedenza dell'infrastruttura di Azure, aggiungere dischi dati ed eseguire gli script di PowerShell. Inoltre, aggiornare la rete virtuale per utilizzare gli indirizzi IP statici dei controller di dominio.
3.	Creare le macchine virtuali del cluster SQL Server che utilizzano gli elementi comuni e specifici creato in precedenza dell'infrastruttura di Azure creato per i controller di dominio, aggiungere dischi dati ed eseguire gli script di PowerShell per configurare il cluster e i gruppi di disponibilità AlwaysOn di SQL Server.
4.	Creare le macchine virtuali di SharePoint server, che consente di utilizzare gli elementi comuni e specifici creato in precedenza dell'infrastruttura di Azure, aggiungere dischi dati ed eseguire gli script di PowerShell per configurare la farm di SharePoint.

Un modello JSON per compilare un'infrastruttura a più livelli in Azure deve seguire la stessa procedura:

1.	Creare il comune (account di archiviazione, rete virtuale), livello specifico (set di disponibilità) e macchina virtuale specifico (indirizzi IP pubblici, i set di disponibilità, le interfacce di rete e istanze del servizio di bilanciamento del carico) gli elementi dell'infrastruttura di Azure che sono necessari per la distribuzione.
2.	Per ogni livello dell'applicazione (ad esempio l'autenticazione web, database), creare e configurare i server in tale livello mediante comuni (account di archiviazione, rete virtuale), livello specifico (set di disponibilità) ed elementi specifici della macchina virtuale (indirizzi IP pubblici, interfacce di rete, istanze del servizio di bilanciamento del carico).

Per altre informazioni, vedere il [linguaggio del modello di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn835138.aspx).


## Risorse aggiuntive

[Provider di calcolo, rete e archiviazione in Gestione risorse di Microsoft Azure](virtual-machines-azurerm-versus-azuresm.md)

[Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md)

[Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md)

[Macchine virtuali - Documentazione](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->
