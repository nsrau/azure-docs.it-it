<properties
	pageTitle="Ridimensionare set di scalabilità di macchine virtuali Linux | Microsoft Azure"
	description="Impostare il ridimensionamento automatico per un set di scalabilità di macchine virtuali Linux tramite l'interfaccia della riga di comando di Azure"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="davidmu"/>

# Ridimensionare automaticamente macchine virtuali Linux in un set di scalabilità di macchine virtuali

I set di scalabilità di macchine virtuali semplificano la distribuzione e la gestione di macchine virtuali identiche come un set. I set di scalabilità offrono un livello di calcolo scalabile e personalizzabile per applicazioni con iperscalabilità e supportano le immagini della piattaforma Windows, le immagini della piattaforma Linux, le immagini personalizzate e le estensioni. Per altre informazioni, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

Questa esercitazione illustra come creare un set di scalabilità per macchine virtuali Linux con la versione più recente di Linux Ubuntu e come ridimensionare automaticamente le macchine virtuali del set. Per eseguire questa operazione è necessario creare un modello di Azure Resource Manager e distribuirlo tramite l'interfaccia della riga di comando di Azure. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md). Per altre informazioni sul ridimensionamento automatico dei set di scalabilità, vedere [Ridimensionamento automatico e set di scalabilità di macchine virtuali](virtual-machine-scale-sets-autoscale-overview.md).

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

Per altre informazioni sulle risorse di Gestione risorse, vedere [Provider di calcolo, rete e archiviazione in Gestione risorse di Azure](../virtual-machines/virtual-machines-linux-compare-deployment-models.md).

Prima di iniziare con i passaggi illustrati in questa esercitazione, [installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

## Passaggio 1: Creare un gruppo di risorse e un account di archiviazione

1. **Accedere a Microsoft Azure**: nell'interfaccia della riga di comando (bash, terminale, prompt dei comandi) assicurarsi che sia attiva la modalità Resource Manager digitando `azure config mode arm`, quindi [accedere con l'ID aziendale o dell'istituto di istruzione](../xplat-cli-connect.md#use-the-log-in-method) digitando `azure login` e seguendo le istruzioni per un'esperienza di accesso interattivo all'account Azure.

	> [AZURE.NOTE] Se si ha un ID aziendale o dell'istituto di istruzione e si è certi che l'autenticazione a due fattori non è abilitata, è possibile usare `azure login -u` con l'ID aziendale o dell'istituto di istruzione per eseguire l'accesso senza una sessione interattiva. Se non si ha un ID aziendale o dell'istituto di istruzione, è possibile [crearne uno dall'account Microsoft personale](../virtual-machines/resource-group-create-work-id-from-personal.md).

2. **Creare un gruppo di risorse**: tutte le risorse devono essere distribuite in un gruppo di risorse. Per questa esercitazione assegnare al gruppo di risorse il nome **vmsstest1**.

        azure group create vmsstestrg1 centralus

3. **Distribuire un account di archiviazione nel nuovo gruppo di risorse**: questa esercitazione usa diversi account di archiviazione per semplificare la creazione del set di scalabilità di macchine virtuali. Creare un account di archiviazione denominato **vmsstestsa**. Mantenere aperta la finestra dell'interfaccia dei comandi per i passaggi successivi in questa esercitazione.

        azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa

## Passaggio 2: Creare il modello
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

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "vmSize": "Standard_A0",
        "imagePublisher": "Canonical",
        "imageOffer": "UbuntuServer",
        "imageVersion": "15.10",
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
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
        "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor\\PercentProcessorTime" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU percentage guest OS" locale="it-IT"/></PerformanceCounterConfiguration></PerformanceCounters>",
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
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": {
            "accountType": "[variables('storageAccountType')]"
          }
        },

5. Aggiungere la risorsa rete virtuale. Per altre informazioni, vedere [Provider di risorse di rete](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
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
          "apiVersion": "2016-03-30",
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
          "apiVersion": "2016-03-30",
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
          "apiVersion": "2015-06-15",
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
          "apiVersion": "2016-03-30",
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
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
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
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'sa.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'osdisk1.vhd')]"
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
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
              ],
              "sku": {
                "name": "[variables('vmSize')]",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              }
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), 'a.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'g.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'm.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 's.blob.core.windows.net/vmss')]",
                        "[concat('https://', parameters('resourcePrefix'), 'y.blob.core.windows.net/vmss')]"
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
                        "name":"LinuxDiagnostic",
                        "properties": {
                          "publisher":"Microsoft.OSTCExtensions",
                          "type":"LinuxDiagnostic",
                          "typeHandlerVersion":"2.1",
                          "autoUpgradeMinorVersion":false,
                          "settings": {
                            "xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount":"[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName":"[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey":"[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint":"https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11.	Aggiungere la risorsa autoscaleSettings che definisce la modalità di regolazione del set di scalabilità in base all'uso dei processori delle macchine virtuali nel set.
        
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
                          "metricName": "\\Processor\\PercentProcessorTime",
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
    
    - **metricName**: corrisponde al contatore delle prestazioni definito nella variabile wadperfcounter. Con questa variabile, l'estensione Diagnostica raccoglie i dati del contatore **Processor\\PercentProcessorTime**.
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

## Passaggio 3: Caricare il modello nell'account di archiviazione

È possibile caricare il modello dall'interfaccia della riga di comando purché si conosca il nome dell'account e la chiave primaria dell'account di archiviazione creato nel passaggio 1.

1. Nell'interfaccia della riga di comando (Bash, terminale, prompt dei comandi), eseguire questi comandi per impostare le variabili di ambiente necessarie per accedere all'account di archiviazione:

		export AZURE_STORAGE_ACCOUNT={account_name}
		export AZURE_STORAGE_ACCESS_KEY={key}

	È possibile ottenere la chiave facendo clic sull'icona della chiave durante la visualizzazione della risorsa account di archiviazione nel portale di Azure. Se si usa il prompt dei comandi di Windows, digitare **set** anziché export.

2. Creare il contenitore per archiviare il modello:

		azure storage container create -p Blob templates

3. Caricare il file di modello nel nuovo contenitore.

		azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json

## Passaggio 4: Distribuire il modello

Dopo aver creato il modello, è possibile avviare la distribuzione delle risorse. Per avviare il processo, usare questo comando:

	azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1

Dopo aver premuto INVIO, verrà richiesto di specificare i valori per le variabili assegnate. Specificare questi valori:

	vmName: vmsstestvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: VMpass1
	resourcePrefix: vmsstest

La distribuzione di tutte le risorse richiederà circa 15 minuti.

>[AZURE.NOTE]Per distribuire le risorse, è anche possibile usare il portale. A tale scopo, fare clic su questo collegamento: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## Passaggio 5: Monitorare le risorse

Per ottenere informazioni sui set di scalabilità di macchine virtuali, è possibile usare i metodi seguenti:

 - Portale di Azure: attualmente è possibile ottenere una quantità limitata di informazioni tramite il portale.
 - [Esplora risorse di Azure](https://resources.azure.com/): si tratta dello strumento migliore per esplorare lo stato del set di scalabilità. Seguire questo percorso per passare alla visualizzazione dell'istanza del set di scalabilità creato:

		subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Interfaccia della riga di comando di Azure. Usare questo comando per ottenere alcune informazioni:

		azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1

 - Connettersi alla macchina virtuale jumpbox esattamente come a qualsiasi altra macchina virtuale e quindi accedere in modalità remota alle macchine virtuali nel set di scalabilità per monitorare i singoli processi.

>[AZURE.NOTE]Nell'articolo [Set di scalabilità di macchine virtuali](https://msdn.microsoft.com/library/mt589023.aspx) è disponibile un'API REST completa per ottenere informazioni sui set di scalabilità.

## Passaggio 6: Rimuovere le risorse

Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. È possibile eliminare il gruppo di risorse, in modo che vengano eliminate automaticamente tutte le risorse di tale gruppo.

		azure group delete vmsstestrg1

## Passaggi successivi

- Per alcuni esempi delle funzionalità di monitoraggio di Azure Insights, vedere [Esempi di avvio rapido dell'interfaccia della riga di comando multipiattaforma di Azure Insights](../azure-portal/insights-cli-samples.md).
- Per informazioni sulle funzionalità di notifica, vedere [Usare le azioni di scalabilità automatica per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](../azure-portal/insights-autoscale-to-webhook-email.md) e [Usare i log di controllo per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](../azure-portal/insights-auditlog-to-webhook-email.md).
- Vedere il modello di [scalabilità automatica di un set di scalabilità di macchine virtuali che esegue un'app Ubuntu/Apache/PHP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) che consente di impostare uno stack LAMP per applicare la funzionalità di ridimensionamento automatico dei set di scalabilità di macchine virtuali.

<!---HONumber=AcomDC_0615_2016-->