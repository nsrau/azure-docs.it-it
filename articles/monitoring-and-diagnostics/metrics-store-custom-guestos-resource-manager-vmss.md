---
title: Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per un set di scalabilità di macchine virtuali Windows
description: Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per un set di scalabilità di macchine virtuali Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 7b600bd699ce7f9e4a6c7cba1a41b6bdece16bf0
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343726"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per un set di scalabilità di macchine virtuali Windows

L'[estensione Diagnostica di Azure per Windows](azure-diagnostics.md) (WAD) di Monitoraggio di Azure consente di raccogliere le metriche e i log dal sistema operativo guest eseguito come parte di un cluster di macchine virtuali, di un servizio cloud o di Service Fabric.  L'estensione può inviare i dati di telemetria a molti percorsi diversi elencati nell'articolo indicato in precedenza.  

In questo articolo viene descritto il processo per inviare le metriche delle prestazioni del sistema operativo guest per un set di scalabilità di macchine virtuali Windows all'archivio dati di Monitoraggio di Azure. A partire dalla versione 1.11 di WAD, è possibile scrivere le metriche direttamente nell'archivio delle metriche di Monitoraggio di Azure in cui sono già state raccolte le metriche standard della piattaforma. L'archiviazione in questo percorso consente di accedere alle stesse azioni disponibili per le metriche della piattaforma.  Le azioni includono quasi in tempo reale gli avvisi, i grafici, il routing, l'accesso dall'API REST e altro ancora.  Le versioni precedenti dell'estensione WAD scrivono in Archiviazione di Azure, ma non nell'archivio dati di Monitoraggio di Azure.  

Se non si ha familiarità con i modelli di Resource Manager, vedere le [distribuzioni dei modelli](../azure-resource-manager/resource-group-overview.md)e la relativa struttura e sintassi.  

## <a name="pre-requisites"></a>Prerequisiti

- La sottoscrizione deve essere registrata con [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1). 

- È necessario aver installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) oppure è possibile usare [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurare Monitoraggio di Azure come sink dei dati 
L'estensione di Diagnostica di Azure usa una funzionalità denominata "sink di dati" per effettuare il routing delle metriche e dei log in percorsi diversi.  I passaggi seguenti illustrano come usare un modello di Resource Manager e PowerShell per distribuire una macchina virtuale usando il nuovo sink di dati "Monitoraggio di Azure". 

## <a name="author-resource-manager-template"></a>Creare un modello di Resource Manager 
Per questo esempio è possibile usare un modello di esempio disponibile pubblicamente. I modelli iniziali sono disponibili in https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale  

- **Azuredeploy.json** è un modello di Resource Manager preconfigurato per la distribuzione di un set di scalabilità di macchine virtuali

- **Azuredeploy.parameters.json** è un file di parametri in cui sono archiviate informazioni quali il nome utente e la password che si desidera impostare per la macchina virtuale. Durante la distribuzione, il modello di Resource Manager usa i parametri impostati in questo file. 

Scaricare e salvare entrambi i file in locale. 

###  <a name="modify-azuredeployparametersjson"></a>Modificare azuredeploy.parameters.json
Aprire il file *azuredeploy.parameters.json* 

- Fornire una **vmSKU** da distribuire (si consiglia Standard_D2_v3) 
- Specificare una **windowsOSVersion** desiderata per il set di scalabilità di macchine virtuali (si consiglia 2016-Datacenter) 
- Denominare la risorsa del set di scalabilità di macchine virtuali da distribuire usando una proprietà **vmssName**. Ad esempio, *VMSS-WAD-TEST*.    
- Specificare il numero di macchine virtuali che si desidera eseguire nel set di scalabilità di macchine virtuali usando la proprietà **instanceCount**
- Immettere i valori relativi a **adminUsername** e **adminPassword** per il set di scalabilità di macchine virtuali. Questi parametri vengono usati per l'accesso remoto alle macchine virtuali nel set di scalabilità. Questi parametri vengono usati per l'accesso remoto alla macchina virtuale. NON usare i parametri presenti in questo modello per evitare che la macchina virtuale sia soggetta a hijack. I bot eseguono la ricerca in Internet dei nomi utente e delle password archiviati nei repository Github pubblici. È probabile che stiano testando le macchine virtuali con queste impostazioni predefinite. 


###  <a name="modify-azuredeployjson"></a>Modificare azuredeploy.json
Aprire il file *azuredeploy.json* 

Aggiungere una variabile per conservare le informazioni sull'account di archiviazione nel modello di Resource Manager. È ancora necessario fornire un account di archiviazione come parte dell'installazione dell'estensione di diagnostica. Tutti i log e/o i contatori delle prestazioni specificati nel file di configurazione di diagnostica verranno scritti nell'account di archiviazione specificato oltre ad essere inviati all'archivio delle metriche di Monitoraggio di Azure. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
Trovare la definizione del Set di scalabilità di macchine virtuali nella sezione delle risorse e aggiungere la sezione "identità" alla configurazione. In questo modo Azure la assegnerà a un'identità di sistema. Questo passaggio garantisce che le macchine virtuali nel set di scalabilità possano emettere metriche guest su se stesse in Monitoraggio di Azure.  

```json
  { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
 ```

Nella risorsa del set di scalabilità di macchine virtuali trovare la sezione **virtualMachineProfile**. Aggiungere un nuovo profilo chiamato **extensionsProfile** per gestire le estensioni.  


In **extensionProfile** aggiungere una nuova estensione al modello, come illustrato nella sezione **VMSS-WAD-extension**.  Questa sezione è l'estensione relativa alle identità gestite per le risorse di Azure che garantisce che le metriche generate vengano accettate da Monitoraggio di Azure. Il campo **nome** può contenere un nome qualsiasi. 

Il codice seguente dell'estensione MSI aggiunge anche l'estensione e la configurazione di diagnostica come una risorsa di estensione alla risorsa del set di scalabilità di macchine virtuali. È possibile aggiungere o rimuovere i contatori delle prestazioni in base alle esigenze. 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identites for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Aggiungere un dependsOn per l'account di archiviazione, in modo da verificare che venga creato nell'ordine corretto. 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

Se non è già stato creato nel modello, creare un account di archiviazione.  
```json
"resources": [
  // add this code    
  {
     "type": "Microsoft.Storage/storageAccounts",
     "name": "[variables('storageAccountName')]",
     "apiVersion": "2015-05-01-preview",
     "location": "[resourceGroup().location]",
     "properties": {
       "accountType": "Standard_LRS"
      }
  },
  // end added code
  { 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Salvare e chiudere entrambi i file. 

## <a name="deploy-the-resource-manager-template"></a>Distribuire il modello di Resource Manager 

> [!NOTE]
> È necessario eseguire la versione 1.5 o successiva dell'estensione di Diagnostica di Azure E disporre di "autoUpgradeMinorVersion": proprietà impostata su *true* nel modello di Resource Manager.  Azure carica quindi l'estensione corretta all'avvio della macchina virtuale. Se il modello non dispone di queste impostazioni, modificarle e ridistribuire il modello. 


Per la distribuzione del modello di Resource Manager verrà usato Azure PowerShell.  

1. Avviare PowerShell 
1. Accedere ad Azure con `Login-AzureRmAccount`
1. Ottenere l'elenco delle sottoscrizioni usando `Get-AzureRmSubscription`
1. Impostare la sottoscrizione in cui verrà creata/aggiornata la macchina virtuale 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Creare un nuovo gruppo di risorse per la macchina virtuale in fase di distribuzione, eseguire il comando seguente 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Ricordarsi di usare un'area di Azure abilitata per le metriche personalizzate. Vedere 
 
1. Eseguire questi comandi per distribuire la macchina virtuale con  
   > [!NOTE] 
   > Se si desidera aggiornare un set di scalabilità esistente, aggiungere semplicemente *-Mode Incremental* alla fine del comando seguente. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Una volta completata la distribuzione, il set di scalabilità di macchine virtuali dovrebbe trovarsi nel portale di Azure ed emettere le metriche su Monitoraggio di Azure. 

   > [!NOTE] 
   > È possibile riscontrare errori in relazione al parametro vmSkuSize selezionato. In questo caso, tornare al file azuredeploy.json e aggiornare il valore predefinito del parametro vmSkuSize. In questo caso è consigliabile provare "Standard_DS1_v2"). 


## <a name="chart-your-metrics"></a>Tracciare il grafico delle metriche 

1. Accedere al portale di Azure 

1. Fare clic su **Monitoraggio** nel menu a sinistra 

1. Nella pagina Monitoraggio fare clic su **Metrica**. 

   ![Pagina delle metriche](./media/metrics-store-custom-rest-api/metrics.png) 

1. Modificare il periodo di aggregazione in **Ultimi 30 minuti**.  

1. Nell'elenco a discesa delle risorse selezionare il set di scalabilità di macchine virtuali appena creato.  

1. Nell'elenco a discesa degli spazi dei nomi selezionare **azure.vm.windows.guest** 

1. Nell'elenco a discesa delle metriche selezionare **Memoria\%Committed Bytes in Use** (Memoria\Byte di cui è stato eseguito il commit).  

È inoltre possibile scegliere di usare le dimensioni su questa metrica per tracciare il grafico di questa metrica per una particolare macchina virtuale nel set di scalabilità o per tracciare ogni macchina virtuale nel set di scalabilità. 



## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).

