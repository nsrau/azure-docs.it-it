---
title: "Ridimensionare set di scalabilità di macchine virtuali Windows | Microsoft Docs"
description: "Impostare il ridimensionamento automatico per un set di scalabilità di macchine virtuali Windows tramite Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 30d8a26f337c00390b1da51e85a0c01ea0d816e3
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017

---
# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>Ridimensionare automaticamente le macchine virtuali in un set di scalabilità di macchine virtuali
I set di scalabilità di macchine virtuali semplificano la distribuzione e la gestione di macchine virtuali identiche come un set. I set di scalabilità offrono un livello di calcolo scalabile e personalizzabile per applicazioni con iperscalabilità e supportano le immagini della piattaforma Windows, le immagini della piattaforma Linux, le immagini personalizzate e le estensioni. Per altre informazioni sui set di scalabilità, vedere [Set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

Questa esercitazione illustra come creare un set di scalabilità costituito da macchine virtuali Windows e come ridimensionare automaticamente le macchine virtuali nel set. Per creare il set di scalabilità e configurare il ridimensionamento, creare un modello di Azure Resource Manager e distribuirlo tramite Azure PowerShell. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md). Per altre informazioni sul ridimensionamento automatico dei set di scalabilità, vedere [Ridimensionamento automatico e set di scalabilità di macchine virtuali](virtual-machine-scale-sets-autoscale-overview.md).

In questo articolo verranno distribuite le risorse e le estensioni seguenti:

* Microsoft.Storage/storageAccounts
* Microsoft.Network/virtualNetworks
* Microsoft.Network/publicIPAddresses
* Microsoft.Network/loadBalancers
* Microsoft.Network/networkInterfaces
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.Insights.VMDiagnosticsSettings
* Microsoft.Insights/autoscaleSettings

Per altre informazioni sulle risorse di Resource Manager, vedere [Azure Resource Manager e distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="step-1-install-azure-powershell"></a>Passaggio 1: installare Azure PowerShell
Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere ad Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview) .

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>Passaggio 2: Creare un gruppo di risorse e un account di archiviazione
1. **Creare un gruppo di risorse** : tutte le risorse devono essere distribuite in un gruppo di risorse. Per creare un gruppo di risorse denominato [vmsstestrg1](https://msdn.microsoft.com/library/mt603739.aspx) , usare **New-AzureRmResourceGroup**.
2. **Creare un account di archiviazione** : questo è l'account di archiviazione in cui viene archiviato il modello. Usare [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) per creare un account di archiviazione denominato **vmsstestsa**.

## <a name="step-3-create-the-template"></a>Passaggio 3: Creare il modello
Un modello di Gestione risorse di Azure permette di distribuire e gestire le risorse di Azure insieme tramite una descrizione JSON delle risorse e dei parametri di distribuzione associati.

1. In un editor a scelta creare il file C:\VMSSTemplate.json e aggiungere la struttura JSON iniziale a supporto del modello.

    ```json
    {
      "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      },
      "variables": {
      },
      "resources": [
      ]
    }
    ```

2. I parametri non sono sempre obbligatori, ma forniscono un modo per immettere i valori quando il modello viene distribuito. Aggiungere questi parametri all'interno dell'elemento padre dei parametri aggiunto al modello.

    ```json   
    "vmName": { "type": "string" },
    "vmSSName": { "type": "string" },
    "instanceCount": { "type": "string" },
    "adminUsername": { "type": "string" },
    "adminPassword": { "type": "securestring" },
    "resourcePrefix": { "type": "string" }
    ```
   
    * Nome per la macchina virtuale separata usata per accedere alle macchine virtuali nel set di scalabilità.
    * Nome dell'account di archiviazione in cui viene archiviato il modello.
    * Numero di macchine virtuali da creare inizialmente nel set di scalabilità.
    * Nome e password dell'account amministratore nelle macchine virtuali.
    * Prefisso del nome per le risorse create per supportare il set di scalabilità.

3. Le variabili in un modello possono essere usate per specificare valori che possono subire modifiche frequenti o che devono essere creati da una combinazione di valori dei parametri. Aggiungere queste variabili all'interno dell'elemento padre delle variabili aggiunto al modello:

    ```json
    "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
    "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
    "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
    "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
    "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
    "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
    "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
      "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```
   
    * Nomi DNS che vengono usati dalle interfacce di rete.

        * Prefissi e nomi di indirizzi IP per la rete virtuale e le subnet.
        * Nomi e identificatori di rete virtuale, servizio di bilanciamento del carico e rete.
        * Nomi di account di archiviazione per gli account associati alle macchine virtuali nel set di scalabilità.
        * Impostazioni per l'estensione Diagnostica installata nelle macchine virtuali. Per altre informazioni sull'estensione Diagnostica, vedere [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante il modello di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

4. Aggiungere la risorsa account di archiviazione all'interno dell'elemento padre delle risorse aggiunto al modello. Questo modello usa un ciclo per creare cinque account di archiviazione consigliati in cui sono archiviati i dischi del sistema operativo e i dati di diagnostica. Questo set di account può supportare fino a 100 macchine virtuali in un set di scalabilità, ovvero il limite massimo corrente. A ogni account di archiviazione è assegnato un nome costituito da un indicatore di lettera definito nelle variabili in combinazione con il prefisso specificato nei parametri per il modello.

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
      "apiVersion": "2015-06-15",
      "copy": {
        "name": "storageLoop",
        "count": 5
      },
      "location": "[resourceGroup().location]",
      "properties": { "accountType": "Standard_LRS" }
    },
    ```

5. Aggiungere la risorsa rete virtuale. Per altre informazioni, vedere [Provider di risorse di rete](../virtual-network/resource-groups-networking.md).

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
        "subnets": [
          {
            "name": "subnet1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          }
        ]
      }
    },
    ```

6. Aggiungere le risorse indirizzo IP pubblico usate dal servizio di bilanciamento del carico e l'interfaccia di rete.

    ```json
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
    ```

7. Aggiungere la risorsa servizio di bilanciamento del carico usata dal set di scalabilità. Per altre informazioni, vedere [Supporto di Gestione risorse di Azure per il servizio di bilanciamento del carico](../load-balancer/load-balancer-arm.md).

    ```json   
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
                "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
              }
            }
          }
        ],
        "backendAddressPools": [ { "name": "bepool1" } ],
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
    ```

8. Aggiungere la risorsa interfaccia di rete usata dalla macchina virtuale separata. Poiché non è possibile accedere tramite un indirizzo IP pubblico alle macchine virtuali di un set di scalabilità, viene creata una macchina virtuale separata nella stessa rete virtuale che verrà usata per accedere in modalità remota alle macchine virtuali.

    ```json  
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
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
                "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
              }
            }
          }
        ]
      }
    },
    ```

9. Aggiungere la macchina virtuale separata nella stessa rete del set di scalabilità.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": { "vmSize": "Standard_A1" },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(parameters('resourcePrefix'), 'os1')]",
            "vhd": {
              "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"        
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    ```

10. Aggiungere la risorsa set di scalabilità di macchine virtuali e specificare l'estensione di Diagnostica installata in tutte le macchine virtuali nel set di scalabilità. Molte delle impostazioni per questa risorsa sono simili a quelle della risorsa macchina virtuale. Le differenze principali sono l'elemento capacity, che specifica il numero di macchine virtuali nel set di scalabilità, e l'elemento upgradePolicy, che specifica la modalità di esecuzione degli aggiornamenti delle macchine virtuali. Il set di scalabilità viene creato solo dopo che sono stati creati tutti gli account di archiviazione come specificato nell'elemento dependsOn.

    ```json
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "apiVersion": "2016-03-30",
      "name": "[parameters('vmSSName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "Standard_A1",
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
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
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
                "name": "networkconfig1",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
                      "name": "ip1",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
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
                    "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint": "https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. Aggiungere la risorsa autoscaleSettings che definisce la modalità di regolazione del set di scalabilità in base all'utilizzo dei processori delle macchine virtuali nel set di scalabilità.

    ```json
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
    ```

    Per questa esercitazione, i valori importanti sono i seguenti:
    
    * **metricName**  
    Questo valore corrisponde al contatore delle prestazioni definito nella variabile wadperfcounter. Con questa variabile, l'estensione Diagnostica raccoglie i dati del contatore **Processor(_Total)\% Processor Time**.
    
    * **metricResourceUri**  
    Questo valore è l'identificatore di risorsa del set di scalabilità di macchine virtuali.
    
    * **timeGrain**  
    Questo valore corrisponde alla granularità delle metriche che vengono raccolte. In questo modello il valore è impostato su un minuto.
    
    * **statistic**  
    Questo valore determina il modo in cui vengono combinate le metriche per consentire l'azione di ridimensionamento automatico. I valori possibili sono: Average, Min, Max. In questo modello viene raccolto l'utilizzo medio totale della CPU delle macchine virtuali.
    
    * **timeWindow**  
    Questo valore rappresenta l'intervallo di tempo in cui vengono raccolti i dati dell'istanza. Deve essere compreso tra 5 minuti e 12 ore.
    
    * **timeAggregation**  
    Questo valore definisce il modo in cui i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". I valori possibili sono: Average, Minimum, Maximum, Last, Total, Count.
    
    * **operator**  
    Questo valore indica l'operatore usato per confrontare i dati della metrica e la soglia. I valori possibili sono: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    
    * **threshold**  
    Questo valore indica l'attivazione dell'azione di ridimensionamento. In questo modello le macchine virtuali vengono aggiunte al set di scalabilità quando l'utilizzo medio della CPU tra le macchine nel set supera il 50%.
    
    * **direction**  
    Questo valore determina l'azione da eseguire quando viene raggiunto il valore di soglia. I valori possibili sono Increase o Decrease. In questo modello il numero di macchine virtuali nel set di scalabilità viene aumentato se la soglia supera il 50% nell'intervallo di tempo definito.
    
    * **type**  
    Questo valore indica il tipo di azione che deve verificarsi e deve essere impostato su ChangeCount.
    
    * **value**  
    Questo valore indica il numero di macchine virtuali che vengono aggiunte o rimosse dal set di scalabilità. Questo valore deve essere uguale o maggiore di 1. Il valore predefinito è 1. In questo modello il numero di macchine virtuali nel set di scalabilità aumenta di 1 quando viene raggiunta la soglia.
    
    * **cooldown**  
    Questo valore indica il tempo di attesa dopo l'ultima azione di ridimensionamento prima che venga eseguita l'azione successiva. Questo valore deve essere compreso tra un minuto e una settimana.

12. Salvare il file di modello.    

## <a name="step-4-upload-the-template-to-storage"></a>Passaggio 4: Caricare il modello nell'account di archiviazione
È possibile caricare il modello purché si conosca il nome e la chiave primaria dell'account di archiviazione creato nel passaggio 1.

1. Nella finestra di Microsoft Azure PowerShell impostare una variabile che specifichi il nome dell'account di archiviazione creato nel passaggio 1.
   
    ```powershell
    $storageAccountName = "vmstestsa"
    ```

2. Impostare una variabile che specifichi la chiave primaria dell'account di archiviazione.
   
    ```powershell
    $storageAccountKey = "<primary-account-key>"
    ```
   
   È possibile ottenere la chiave facendo clic sull'icona della chiave durante la visualizzazione della risorsa account di archiviazione nel portale di Azure.
3. Creare l'oggetto contesto dell'account di archiviazione usato per convalidare le operazioni con l'account di archiviazione.
   
    ```powershell
    $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    ```

4. Creare il contenitore per l'archiviazione del modello.

    ```powershell
    $containerName = "templates"
    New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob
    ```

5. Caricare il file di modello nel nuovo contenitore.

    ```powershell   
    $blobName = "VMSSTemplate.json"
    $fileName = "C:\" + $BlobName
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx
    ```

## <a name="step-5-deploy-the-template"></a>Passaggio 5: Distribuire il modello
Dopo aver creato il modello, è possibile avviare la distribuzione delle risorse. Per avviare il processo, usare questo comando:

```powershell
New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"
```

Dopo aver premuto INVIO, verrà richiesto di specificare i valori per le variabili assegnate. Specificare questi valori:

```powershell
vmName: vmsstestvm1
  vmSSName: vmsstest1
  instanceCount: 5
  adminUserName: vmadmin1
  adminPassword: VMpass1
  resourcePrefix: vmsstest
```

La distribuzione di tutte le risorse richiederà circa 15 minuti.

> [!NOTE]
> Per distribuire le risorse, è anche possibile usare il portale. Usare questo collegamento: "https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>"
> 
> 

## <a name="step-6-monitor-resources"></a>Passaggio 6: Monitorare le risorse
Per ottenere informazioni sui set di scalabilità di macchine virtuali, è possibile usare i metodi seguenti:

* Portale di Azure: attualmente è possibile ottenere una quantità limitata di informazioni tramite il portale.
* [Esplora risorse di Azure](https://resources.azure.com/) : si tratta dello strumento migliore per esaminare lo stato corrente del set di scalabilità. Seguire questo percorso per passare alla visualizzazione dell'istanza del set di scalabilità creato:
  
    sottoscrizioni > {sottoscrizioni dell'utente} > gruppi di risorse > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > macchine virtuali

* Azure PowerShell: per ottenere alcune informazioni, usare il comando seguente:

  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  ```
  
  Or
  
  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView
  ```

* Connettersi alla macchina virtuale separata esattamente come a qualsiasi altra macchina virtuale e quindi accedere in modalità remota alle macchine virtuali nel set di scalabilità per monitorare i singoli processi.

> [!NOTE]
> Nell'articolo [Set di scalabilità di macchine virtuali](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>Passaggio 7: Rimuovere le risorse
Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. È possibile eliminare il gruppo di risorse e tutte le relative risorse automaticamente.

  ```powershell
  Remove-AzureRmResourceGroup -Name vmsstestrg1
  ```

Se si vuole mantenere il gruppo di risorse, è possibile eliminare solo il set di scalabilità.

  ```powershell
  Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"
  ```

## <a name="next-steps"></a>Passaggi successivi
* Per gestire il set di scalabilità appena creato, usare le informazioni disponibili in [Gestire le macchine virtuali in un set di scalabilità di macchine virtuali](virtual-machine-scale-sets-windows-manage.md).
* Altre informazioni sull'aumento delle prestazioni sono disponibili in [Scalabilità automatica verticale con set di scalabilità di macchine virtuali](virtual-machine-scale-sets-vertical-scale-reprovision.md)
* Per alcuni esempi delle funzionalità di monitoraggio in Monitoraggio di Azure, vedere [Azure Monitor PowerShell quick start samples](../monitoring-and-diagnostics/insights-powershell-samples.md) (Esempi di avvio rapido di PowerShell per Monitoraggio di Azure).
* Per informazioni sulle funzionalità di notifica, vedere [Use autoscale actions to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) (Usare le azioni di ridimensionamento automatico per inviare notifiche di avviso tramite e-mail e webhook in Monitoraggio di Azure).
* Per informazioni, vedere [Use audit logs to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) (Usare i log di controllo per inviare notifiche di avviso tramite e-mail e webhook in Monitoraggio di Azure).


