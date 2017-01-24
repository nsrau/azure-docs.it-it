---
title: "Ridimensionare set di scalabilità di macchine virtuali Linux | Microsoft Docs"
description: "Impostare il ridimensionamento automatico per un set di scalabilità di macchine virtuali Linux tramite l&quot;interfaccia della riga di comando di Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 655bb950ad21ab2df0e88db52b8f54b89d35f871
ms.openlocfilehash: d095814e32b7354419d454d0d7bd3df772b6f01e


---
# <a name="automatically-scale-linux-machines-in-a-virtual-machine-scale-set"></a>Ridimensionare automaticamente macchine virtuali Linux in un set di scalabilità di macchine virtuali
I set di scalabilità di macchine virtuali semplificano la distribuzione e la gestione di macchine virtuali identiche come un set. I set di scalabilità offrono un livello di calcolo scalabile e personalizzabile per applicazioni con iperscalabilità e supportano le immagini della piattaforma Windows, le immagini della piattaforma Linux, le immagini personalizzate e le estensioni. Per altre informazioni, vedere [Panoramica dei set di scalabilità di macchine virtuali](virtual-machine-scale-sets-overview.md).

Questa esercitazione illustra come creare un set di scalabilità per macchine virtuali Linux con la versione più recente di Linux Ubuntu. L'esercitazione mostra inoltre come ridimensionare automaticamente le macchine nel set. Per creare il set di scalabilità e configurare il ridimensionamento, creare un modello di Azure Resource Manager e distribuirlo tramite l'interfaccia della riga di comando di Azure. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md). Per altre informazioni sul ridimensionamento automatico dei set di scalabilità, vedere [Ridimensionamento automatico e set di scalabilità di macchine virtuali](virtual-machine-scale-sets-autoscale-overview.md).

In questa esercitazione verranno distribuite le risorse e le estensioni seguenti:

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

Prima di iniziare con i passaggi illustrati in questa esercitazione, [installare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

## <a name="step-1-create-a-resource-group-and-a-storage-account"></a>Passaggio 1: Creare un gruppo di risorse e un account di archiviazione
1. **Accedere a Microsoft Azure** : nell'interfaccia della riga di comando (Bash, terminale, prompt dei comandi) passare alla modalità Gestione risorse e quindi [accedere con l'ID aziendale o dell'istituto di istruzione](../xplat-cli-connect.md#scenario-1-azure-login-with-interactive-login). Seguire le istruzioni per un'esperienza di accesso interattivo al proprio account Azure.
   
        azure config mode arm
   
        azure login
   
   > [!NOTE]
   > Se si ha un ID aziendale o dell'istituto di istruzione e l'autenticazione a due fattori non è abilitata, usare `azure login -u` con l'ID per eseguire l'accesso senza una sessione interattiva. Se non si ha un'identità aziendale o dell'istituto di istruzione, è possibile [crearne una dall'account Microsoft personale](../virtual-machines/virtual-machines-linux-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   > 
   > 
2. **Creare un gruppo di risorse** : tutte le risorse devono essere distribuite in un gruppo di risorse. Per questa esercitazione, assegnare al gruppo di risorse il nome **vmsstest1**.
   
        azure group create vmsstestrg1 centralus
3. **Distribuire un account di archiviazione nel nuovo gruppo di risorse** : nell'account di archiviazione viene archiviato il modello. Creare un account di archiviazione denominato **vmsstestsa**.
   
        azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa

## <a name="step-2-create-the-template"></a>Passaggio 2: Creare il modello
Un modello di Gestione risorse di Azure permette di distribuire e gestire le risorse di Azure insieme tramite una descrizione JSON delle risorse e dei parametri di distribuzione associati.

1. In un editor a scelta creare il file VMSSTemplate.json e aggiungere la struttura JSON iniziale a supporto del modello.
   
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
2. I parametri non sono sempre obbligatori, ma forniscono un modo per immettere i valori quando il modello viene distribuito. Aggiungere questi parametri all'interno dell'elemento padre dei parametri aggiunto al modello.
   
        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }
   
   * Nome per la macchina virtuale separata usata per accedere alle macchine virtuali nel set di scalabilità.
   * Nome per l'account di archiviazione in cui è archiviato il modello.
   * Numero di istanze di macchine virtuali da creare inizialmente nel set di scalabilità.
   * Nome e password dell'account amministratore nelle macchine virtuali.
   * Prefisso del nome per le risorse create per supportare il set di scalabilità.
3. Le variabili in un modello possono essere usate per specificare valori che possono subire modifiche frequenti o che devono essere creati da una combinazione di valori dei parametri. Aggiungere queste variabili all'interno dell'elemento padre delle variabili aggiunto al modello:
   
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
        "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor\\PercentProcessorTime\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU percentage guest OS\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
   
   * Nomi DNS che vengono usati dalle interfacce di rete.
   * Prefissi e nomi di indirizzi IP per la rete virtuale e le subnet.
   * Nomi e identificatori di rete virtuale, servizio di bilanciamento del carico e rete.
   * Nomi di account di archiviazione per gli account associati alle macchine virtuali nel set di scalabilità.
   * Impostazioni per l'estensione Diagnostica installata nelle macchine virtuali. Per altre informazioni sull'estensione Diagnostica, vedere [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante il modello di Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. Aggiungere la risorsa account di archiviazione all'interno dell'elemento padre delle risorse aggiunto al modello. Questo modello usa un ciclo per creare cinque account di archiviazione consigliati in cui sono archiviati i dischi del sistema operativo e i dati di diagnostica. Questo set di account può supportare fino a 100 macchine virtuali in un set di scalabilità, ovvero il limite massimo corrente. A ogni account di archiviazione è assegnato un nome costituito da un indicatore di lettera definito nelle variabili in combinazione con il suffisso specificato nei parametri per il modello.
   
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
5. Aggiungere la risorsa rete virtuale. Per altre informazioni, vedere [Provider di risorse di rete](../virtual-network/resource-groups-networking.md).
   
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
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
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
                  "backendPort": 22
                }
              }
            ]
          }
        },
8. Aggiungere la risorsa interfaccia di rete usata dalla macchina virtuale separata. Poiché non è possibile accedere tramite un indirizzo IP pubblico alle macchine virtuali di un set di scalabilità, viene creata una macchina virtuale separata nella stessa rete virtuale che verrà usata per accedere in modalità remota alle macchine virtuali.
   
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
9. Aggiungere la macchina virtuale separata nella stessa rete del set di scalabilità.
   
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
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "14.04.4-LTS",
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
10. Aggiungere la risorsa set di scalabilità di macchine virtuali e specificare l'estensione di Diagnostica installata in tutte le macchine virtuali nel set di scalabilità. Molte delle impostazioni per questa risorsa sono simili a quelle della risorsa macchina virtuale. Le differenze principali sono l'elemento capacity, che specifica il numero di macchine virtuali nel set di scalabilità, e l'elemento upgradePolicy, che specifica la modalità di esecuzione degli aggiornamenti delle macchine virtuali. Il set di scalabilità viene creato solo dopo che sono stati creati tutti gli account di archiviazione come specificato nell'elemento dependsOn.
    
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
                     "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0],'.blob.core.windows.net/vmss')]",
                     "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1],'.blob.core.windows.net/vmss')]",
                     "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2],'.blob.core.windows.net/vmss')]",
                     "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3],'.blob.core.windows.net/vmss')]",
                     "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4],'.blob.core.windows.net/vmss')]"
                   ],
                   "name": "vmssosdisk",
                   "caching": "ReadOnly",
                   "createOption": "FromImage"
                 },
                 "imageReference": {
                   "publisher": "Canonical",
                   "offer": "UbuntuServer",
                   "sku": "14.04.4-LTS",
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
11. Aggiungere la risorsa autoscaleSettings che definisce la modalità di regolazione del set di scalabilità in base all'uso dei processori delle macchine virtuali nel set.
    
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
    
    * **metricName** : questo valore corrisponde al contatore delle prestazioni definito nella variabile wadperfcounter. Con questa variabile, l'estensione Diagnostica raccoglie i dati del contatore **Processor\PercentProcessorTime**.
    * **metricResourceUri** : questo valore è l'identificatore di risorsa del set di scalabilità di macchine virtuali.
    * **timeGrain** : questo valore corrisponde alla granularità delle metriche che vengono raccolte. In questo modello il valore è impostato su un minuto.
    * **statistic** : questo valore determina il modo in cui vengono combinate le metriche per consentire l'azione di ridimensionamento automatico. I valori possibili sono: Average, Min, Max. In questo modello viene raccolto l'utilizzo medio totale della CPU delle macchine virtuali.
    * **timeWindow** : questo valore è l'intervallo di tempo in cui vengono raccolti i dati dell'istanza. Deve essere compreso tra 5 minuti e 12 ore.
    * **timeAggregation** : questo valore definisce il modo in cui i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". I valori possibili sono: Average, Minimum, Maximum, Last, Total, Count.
    * **operator** : questo valore indica l'operatore usato per confrontare i dati della metrica e la soglia. I valori possibili sono: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    * **threshold** : questo valore attiva l'azione di ridimensionamento. In questo modello le macchine virtuali vengono aggiunte al set di scalabilità quando l'utilizzo medio della CPU tra le macchine nel set supera il 50%.
    * **direction** : questo valore determina l'azione da eseguire quando viene raggiunto il valore di soglia. I valori possibili sono Increase o Decrease. In questo modello il numero di macchine virtuali nel set di scalabilità viene aumentato se la soglia supera il 50% nell'intervallo di tempo definito.
    * **type** : questo valore indica il tipo di azione che deve verificarsi e deve essere impostato su ChangeCount.
    * **value** : questo valore indica il numero di macchine virtuali che vengono aggiunte o rimosse dal set di scalabilità. Questo valore deve essere uguale o maggiore di 1. Il valore predefinito è 1. In questo modello il numero di macchine virtuali nel set di scalabilità aumenta di 1 quando viene raggiunta la soglia.
    * **cooldown** : questo valore indica il tempo di attesa dopo l'ultima azione di ridimensionamento prima che venga eseguita l'azione successiva. Questo valore deve essere compreso tra un minuto e una settimana.
12. Salvare il file di modello.    

## <a name="step-3-upload-the-template-to-storage"></a>Passaggio 3: Caricare il modello nell'account di archiviazione
È possibile caricare il modello purché si conosca il nome e la chiave primaria dell'account di archiviazione creato nel passaggio 1.

1. Nell'interfaccia della riga di comando (Bash, terminale, prompt dei comandi), eseguire questi comandi per impostare le variabili di ambiente necessarie per accedere all'account di archiviazione:
   
        export AZURE_STORAGE_ACCOUNT={account_name}
        export AZURE_STORAGE_ACCESS_KEY={key}
   
    È possibile ottenere la chiave facendo clic sull'icona della chiave durante la visualizzazione della risorsa account di archiviazione nel portale di Azure. Se si usa il prompt dei comandi di Windows, digitare **set** anziché export.
2. Creare il contenitore per l'archiviazione del modello.
   
        azure storage container create -p Blob templates
3. Caricare il file di modello nel nuovo contenitore.
   
        azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json

## <a name="step-4-deploy-the-template"></a>Passaggio 4: Distribuire il modello
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

> [!NOTE]
> Per distribuire le risorse, è anche possibile usare il portale. Usare questo collegamento: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>
> 
> 

## <a name="step-5-monitor-resources"></a>Passaggio 5: Monitorare le risorse
Per ottenere informazioni sui set di scalabilità di macchine virtuali, è possibile usare i metodi seguenti:

* Portale di Azure: attualmente è possibile ottenere una quantità limitata di informazioni tramite il portale.
* [Esplora risorse di Azure](https://resources.azure.com/) : si tratta dello strumento migliore per esaminare lo stato corrente del set di scalabilità. Seguire questo percorso per passare alla visualizzazione dell'istanza del set di scalabilità creato:
  
       subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines
* Interfaccia della riga di comando di Azure. Usare questo comando per ottenere alcune informazioni:
  
       azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1
* Connettersi alla macchina virtuale jumpbox esattamente come a qualsiasi altra macchina virtuale e quindi accedere in modalità remota alle macchine virtuali nel set di scalabilità per monitorare i singoli processi.

> [!NOTE]
> Nell'articolo relativo ai [set di scalabilità di macchine virtuali](https://msdn.microsoft.com/library/mt589023.aspx)è disponibile un'API REST completa per ottenere informazioni sui set di scalabilità.
> 
> 

## <a name="step-6-remove-the-resources"></a>Passaggio 6: Rimuovere le risorse
Poiché vengono applicati addebiti per le risorse usate in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie. Non è necessario eliminare separatamente ogni risorsa da un gruppo di risorse. È possibile eliminare il gruppo di risorse e tutte le relative risorse automaticamente.

        azure group delete vmsstestrg1

## <a name="next-steps"></a>Passaggi successivi
* Per alcuni esempi delle funzionalità di monitoraggio in Monitoraggio di Azure, vedere [Azure Monitor Cross-platform CLI quick start samples](../monitoring-and-diagnostics/insights-cli-samples.md) (Esempi di avvio rapido dell'interfaccia della riga di comando multipiattaforma di Monitoraggio di Azure).
* Per informazioni sulle funzionalità di notifica, vedere [Use autoscale actions to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) (Usare le azioni di scalabilità automatica per inviare notifiche di avviso tramite e-mail e webhook in Monitoraggio di Azure).
* Per informazioni, vedere [Use audit logs to send email and webhook alert notifications in Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) (Usare i log di controllo per inviare notifiche di avviso tramite e-mail e webhook in Monitoraggio di Azure).
* Vedere il modello di [scalabilità automatica di un set di scalabilità di macchine virtuali che esegue un'app Ubuntu/Apache/PHP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) che consente di impostare uno stack LAMP per applicare la funzionalità di ridimensionamento automatico dei set di scalabilità di macchine virtuali.




<!--HONumber=Nov16_HO4-->


