<properties
	pageTitle="Ridimensionare set di scalabilità di macchine virtuali Windows | Microsoft Azure"
	description="Impostare il ridimensionamento automatico per un set di scalabilità di macchine virtuali Windows tramite Azure PowerShell"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="davidmu"/>

# Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali

I set di scalabilità di macchine virtuali semplificano la distribuzione e la gestione di macchine virtuali identiche come un set. I set di scalabilità offrono un livello di calcolo scalabile e personalizzabile per applicazioni con iperscalabilità e supportano le immagini della piattaforma Windows, le immagini della piattaforma Linux, le immagini personalizzate e le estensioni. Per altre informazioni sui set di scalabilità, vedere [Set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

Questa esercitazione illustra come creare un set di scalabilità di macchine virtuali costituito da macchine virtuali Windows e come ridimensionare automaticamente le macchine virtuali nel set. Per eseguire questa operazione, sarà necessario creare un modello di Gestione risorse di Azure e distribuirlo tramite Azure PowerShell. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md). Per altre informazioni sul ridimensionamento automatico dei set di scalabilità, vedere [Ridimensionamento automatico e set di scalabilità di macchine virtuali](virtual-machine-scale-sets-autoscale-overview.md).

In questa esercitazione verranno distribuite le risorse e le estensioni seguenti:

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Per altre informazioni sulle risorse di Gestione risorse, vedere [Provider di calcolo, rete e archiviazione in Gestione risorse di Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md).

Il modello creato in questa esercitazione è simile a un modello disponibile nella raccolta modelli. Per altre informazioni, vedere [Distribuire un semplice set di scalabilità di macchine virtuali con macchine virtuali Windows e un jumpbox](https://azure.microsoft.com/documentation/templates/201-vmss-windows-jumpbox/).

## Passaggio 1: installare Azure PowerShell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Passaggio 2: Creare un gruppo di risorse e un account di archiviazione

1. **Creare un gruppo di risorse**: tutte le risorse devono essere distribuite in un gruppo di risorse. Per questa esercitazione, assegnare al gruppo di risorse il nome **vmsstestrg1**. Vedere [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx).

2. **Distribuire un account di archiviazione nel nuovo gruppo di risorse**: questa esercitazione usa diversi account di archiviazione per semplificare la creazione del set di scalabilità di macchine virtuali. Usare [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) per creare un account di archiviazione denominato **vmsstestsa**. Tenere aperta la finestra di Azure PowerShell per eseguire le procedure illustrate più avanti in questa esercitazione.

## Passaggio 3: Creare il modello
Un modello di Gestione risorse di Azure permette di distribuire e gestire le risorse di Azure insieme tramite una descrizione JSON delle risorse e dei parametri di distribuzione associati.

1. In un editor a scelta creare il file C:\\VMSSTemplate.json e aggiungere la struttura JSON iniziale a supporto del modello.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          }
          "variables": {
          }
          "resources": [
          ]
        }

2. I parametri non sono sempre obbligatori, ma semplificano la gestione dei modelli. Consentono di specificare i valori per il modello, descrivere il tipo del valore, indicare il valore predefinito, se necessario, ed eventualmente i valori consentiti del parametro. Aggiungere questi parametri all'interno dell'elemento padre dei parametri aggiunto al modello.

        "vmName": {
          "type": "string"
        },
        "vmSSName": {
          "type": "string"
        },
        "instanceCount": {
          "type": "string"
        },
        "adminUsername": {
          "type": "string"
        },
        "adminPassword": {
          "type": "securestring"
        },
        "resourcePrefix": {
          "type": "string"
        }
        
    - Nome per la macchina virtuale separata usata per accedere alle macchine virtuali nel set di scalabilità.
    - Nome per l'account di archiviazione in cui è archiviato il modello.
    - Numero di istanze di macchine virtuali da creare inizialmente nel set di scalabilità.
    - Nome e password dell'account amministratore nelle macchine virtuali.
    - Un prefisso per le risorse create nel gruppo di risorse.
    
3. Le variabili in un modello possono essere usate per specificare valori che possono subire modifiche frequenti o che devono essere creati da una combinazione di valori dei parametri. Aggiungere queste variabili all'interno dell'elemento padre delle variabili aggiunto al modello:

        "apiVersion": "2016-03-30"
        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
        "vmSize": "Standard_A0",
        "imagePublisher": "MicrosoftWindowsServer",
        "imageOffer": "WindowsServer",
        "imageVersion": "2012-R2-Datacenter",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
        "nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
        "publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountType": "Standard_LRS",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Processor Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU utilization" locale="it-IT"/></PerformanceCounterConfiguration>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

    - Nomi DNS che vengono usati dalle interfacce di rete.
	- Dimensioni delle macchine virtuali usate nel set di scalabilità. Per altre informazioni sulle dimensioni di macchine virtuali, vedere [Dimensioni delle macchine virtuali](../virtual-machines/virtual-machines-size-specs.md).
	- Informazioni sulle immagini della piattaforma per la definizione del sistema operativo che verrà eseguito nelle macchine virtuali nel set di scalabilità. Per altre informazioni su come selezionare le immagini, vedere [Esplorare e selezionare immagini di macchine virtuali di Azure con Windows PowerShell e l'interfaccia della riga di comando di Azure](../virtual-machines/resource-groups-vm-searching.md).
	- Prefissi e nomi di indirizzi IP per la rete virtuale e le subnet.
	- Nomi e identificatori di rete virtuale, servizio di bilanciamento del carico e rete.
	- Nomi di account di archiviazione per gli account associati alle macchine virtuali nel set di scalabilità.
	- Impostazioni per l'estensione Diagnostica installata nelle macchine virtuali. Per altre informazioni sull'estensione Diagnostica, vedere [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Gestione risorse di Azure](../virtual-machines/virtual-machines-extensions-diagnostics-windows-template.md).
    
4. Aggiungere la risorsa account di archiviazione all'interno dell'elemento padre delle risorse aggiunto al modello. Questo modello usa un ciclo per creare 5 account di archiviazione consigliati in cui sono archiviati i dischi del sistema operativo e dati di diagnostica. Questo set di account può supportare fino a 100 macchine virtuali in un set di scalabilità, ovvero il limite massimo corrente. A ogni account di archiviazione è assegnato un nome costituito da un indicatore di lettera definito nelle variabili in combinazione con il suffisso specificato nei parametri per il modello.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(variables('resourcePrefix'), parameters('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
          "name": "storageLoop",
          "count": 5
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "[variables('storageAccountType')]"
        }
      }

5. Aggiungere la risorsa rete virtuale. Per altre informazioni, vedere [Provider di risorse di rete](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[variables('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[variables('subnetName')]",
                "properties": {
                  "addressPrefix": "[variables('subnetPrefix')]"
                }
              }
            ]
          }
        },

6. Aggiungere le risorse indirizzo IP pubblico usate dal servizio di bilanciamento del carico e l'interfaccia di rete.

        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Aggiungere la risorsa servizio di bilanciamento del carico usata dal set di scalabilità. Per altre informazioni, vedere [Supporto di Gestione risorse di Azure per il servizio di bilanciamento del carico](../load-balancer/load-balancer-arm.md).

        {
          "apiVersion": "[variables('apiVersion')]",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[variables('publicIPAddressID1')]"
                  }
                }
              }
            ],
            "backendAddressPools": [
              {
                "name": "bepool1"
              }
            ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 3389
                }
              }
            ]
          }
        },

8. Aggiungere la risorsa interfaccia di rete usata dalla macchina virtuale separata. Poiché non è possibile accedere direttamente alle macchine virtuali di un set di scalabilità tramite un indirizzo IP pubblico, viene creata una macchina virtuale separata nella stessa rete virtuale del set di scalabilità, che verrà usata per accedere in modalità remota alle macchine virtuali nel set.

        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName1')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
                  }
                }
              }
            ]
          }
        },

9. Aggiungere la macchina virtuale separata nella stessa rete del set di scalabilità.

        {
          "apiVersion": "[variables('apiVersion')]",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
          ],
          "properties": {
            "hardwareProfile": {
              "vmSize": "[variables('vmSize')]"
            },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "[variables('imagePublisher')]",
                "offer": "[variables('imageOffer')]",
                "sku": "[variables('imageVersion')]",
                "version": "latest"
              },
              "osDisk": {
                "name": "osdisk1",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'saa.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'osdisk1.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"        
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
                }
              ]
            }
          }
        },

10.	Aggiungere la risorsa set di scalabilità di macchine virtuali e specificare l'estensione Diagnostica installata in tutte le macchine virtuali nel set di scalabilità. Molte delle impostazioni per questa risorsa sono simili a quelle della risorsa macchina virtuale. Le differenze principali sono l'aggiunta dell'elemento capacity, che specifica il numero di macchine virtuali da inizializzare nel set di scalabilità, e di upgradePolicy, che specifica la modalità di esecuzione degli aggiornamenti delle macchine virtuali nel set di scalabilità. Il set di scalabilità viene creato solo dopo che sono stati creati tutti gli account di archiviazione come specificato nell'elemento dependsOn.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "[variables('apiVersion')]",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "[variables('vmSize')]",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), 'saa.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'sag.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'sam.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'sas.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'say.blob.core.windows.net/vmss')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "[variables('imagePublisher')]",
                      "offer": "[variables('imageOffer')]",
                      "sku": "[variables('imageVersion')]",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "[variables('nicName2')]",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                        "properties": {
                          "publisher": "Microsoft.Azure.Diagnostics",
                          "type": "IaaSDiagnostics",
                          "typeHandlerVersion": "1.5",
                          "autoUpgradeMinorVersion": true,
                          "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11.	Aggiungere la risorsa autoscaleSettings che definisce la modalità di regolazione del set di scalabilità in base all'utilizzo dei processori delle macchine virtuali nel set di scalabilità.

            {
              "type": "Microsoft.Insights/autoscaleSettings",
              "apiVersion": "2015-04-01",
              "name": "[concat(parameters('resourcePrefix'),'as1')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              ],
              "properties": {
                "enabled": true,
                "name": "[concat(parameters('resourcePrefix'),'as1')]",
                "profiles": [
                  {
                    "name": "Profile1",
                    "capacity": {
                      "minimum": "1",
                      "maximum": "10",
                      "default": "1"
                    },
                    "rules": [
                      {
                        "metricTrigger": {
                          "metricName": "\\Processor(_Total)\\% Processor Time",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    Per questa esercitazione, i valori importanti sono i seguenti:

    - **metricName**: corrisponde al contatore delle prestazioni definito nella variabile wadperfcounter. Con questa variabile, l'estensione Diagnostica raccoglie i dati del contatore **Processor(\_Total)\\% Processor Time**.
- **metricResourceUri**: si tratta dell'identificatore di risorsa del set di scalabilità di macchine virtuali.
    - **timeGrain**: corrisponde alla granularità delle metriche che vengono raccolte. In questo modello il valore è impostato su 1 minuto.
    - **statistic**: determina il modo in cui vengono combinate le metriche per consentire l'azione di ridimensionamento automatico. I valori possibili sono: Average, Min, Max. In questo modello si vuole visualizzare il valore dell'utilizzo medio di CPU totale tra le macchine virtuali nel set di scalabilità.
    - **timeWindow**: si tratta dell'intervallo di tempo in cui dati dell' istanza vengono raccolti. Deve essere compreso tra 5 minuti e 12 ore.
    - **timeAggregation**: definisce il modo in cui i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". I valori possibili sono: Average, Minimum, Maximum, Last, Total, Count.
    - **operator**: operatore usato per confrontare i dati della metrica e la soglia. I valori possibili sono: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    - **threshold**: indica il valore che attiva l'azione di ridimensionamento. In questo modello le macchine virtuali vengono aggiunte al set di scalabilità quando l'utilizzo medio della CPU tra le macchine nel set supera il 50%.
    - **direction**: determina l'azione da eseguire quando viene raggiunto il valore di soglia. I valori possibili sono Increase o Decrease. In questo modello il numero di macchine virtuali nel set di scalabilità viene aumentato se la soglia supera il 50% nell'intervallo di tempo definito.
    - **type**: indica il tipo di azione che deve verificarsi e deve essere impostato su ChangeCount.
    - **value**: indica il numero di macchine virtuali che vengono aggiunte o rimosse dal set di scalabilità. Questo valore deve essere uguale o maggiore di 1. Il valore predefinito è 1. In questo modello il numero di macchine virtuali nel set di scalabilità aumenta di 1 quando viene raggiunta la soglia.
    - **cooldown**: indica il tempo di attesa dopo l'ultima azione di ridimensionamento prima che venga eseguita l'azione successiva. Questo valore deve essere compreso tra 1 minuto e 1 settimana.

12.	Salvare il file di modello.

## Passaggio 4: Caricare il modello nell'account di archiviazione

È possibile caricare il modello dalla finestra di Microsoft Azure PowerShell, purché si conosca il nome dell'account e la chiave primaria dell'account di archiviazione creato nel passaggio 1.

1.	Nella finestra di Microsoft Azure PowerShell impostare una variabile che specifichi il nome dell'account di archiviazione distribuito nel passaggio 1.

            $StorageAccountName = "vmstestsa"

2.	Impostare una variabile che specifichi la chiave primaria dell'account di archiviazione.

            $StorageAccountKey = "<primary-account-key>"

	È possibile ottenere la chiave facendo clic sull'icona della chiave durante la visualizzazione della risorsa account di archiviazione nel portale di Azure.

3.	Creare l'oggetto contesto dell'account di archiviazione usato per convalidare le operazioni con l'account di archiviazione.

            $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

4.	Creare un nuovo contenitore di modelli in cui archiviare il modello creato.

            $ContainerName = "templates"
            New-AzureStorageContainer -Name $ContainerName -Context $ctx  -Permission Blob

5.	Caricare il file di modello nel nuovo contenitore.

            $BlobName = "VMSSTemplate.json"
            $fileName = "C:" + $BlobName
            Set-AzureStorageBlobContent -File $fileName -Container $ContainerName -Blob  $BlobName -Context $ctx

## Passaggio 5: Distribuire il modello

Dopo aver creato il modello, è possibile avviare la distribuzione delle risorse. Per avviare il processo, usare questo comando:

    New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

Dopo aver premuto INVIO, verrà richiesto di specificare i valori per le variabili assegnate. Specificare questi valori:

    vmName: vmsstestvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: VMpass1
	resourcePrefix: vmsstest

La distribuzione di tutte le risorse richiederà circa 15 minuti.

>[AZURE.NOTE] Per distribuire le risorse, è anche possibile usare il portale. A tale scopo, usare questo collegamento: "https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>"

## Passaggio 6: Monitorare le risorse

Per ottenere informazioni sui set di scalabilità di macchine virtuali, è possibile usare i metodi seguenti:

 - Portale di Azure: attualmente è possibile ottenere una quantità limitata di informazioni tramite il portale.
 - [Esplora risorse di Azure](https://resources.azure.com/): si tratta dello strumento migliore per esplorare lo stato del set di scalabilità. Seguire questo percorso per passare alla visualizzazione dell'istanza del set di scalabilità creato:

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell: per ottenere alcune informazioni, usare il comando seguente:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or
        
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

 - Connettersi alla macchina virtuale jumpbox esattamente come a qualsiasi altra macchina virtuale e quindi accedere in modalità remota alle macchine virtuali nel set di scalabilità per monitorare i singoli processi.

>[AZURE.NOTE] Nell'articolo [Set di scalabilità di macchine virtuali](https://msdn.microsoft.com/library/mt589023.aspx) è disponibile un'API REST completa per ottenere informazioni sui set di scalabilità

## Passaggio 7: Rimuovere le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. È possibile eliminare il gruppo di risorse, in modo che vengano eliminate automaticamente tutte le risorse di tale gruppo.

	Remove-AzureRmResourceGroup -Name vmsstestrg1

Se si vuole mantenere il gruppo di risorse, è possibile eliminare solo il set di scalabilità.

	Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"
    
## Passaggi successivi

- Gestire il set di scalabilità appena creato usando le informazioni contenute in [Gestire macchine virtuali in un set di scalabilità di macchine virtuali](virtual-machine-scale-sets-windows-manage.md).
- Altre informazioni sulla scalabilità verticale sono disponibili in [Ridimensionamento automatico verticale con set di scalabilità di macchine virtuali](virtual-machine-scale-sets-vertical-scale-reprovision.md)

<!---HONumber=AcomDC_0504_2016-->