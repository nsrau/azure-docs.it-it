---
title: Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per una macchina virtuale Windows
description: Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per una macchina virtuale Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 4ed911766a14dd35ea662326a5d50df11cf81698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984074"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per una macchina virtuale Windows

L'[estensione Diagnostica di Azure per Windows](azure-diagnostics.md) (WAD) di Monitoraggio di Azure consente di raccogliere le metriche e i log dal sistema operativo guest eseguito come parte di un cluster di macchine virtuali, di un servizio cloud o di Service Fabric.  L'estensione può inviare i dati di telemetria a molti percorsi diversi elencati nell'articolo indicato in precedenza.  

In questo articolo viene descritto il processo per inviare le metriche delle prestazioni del sistema operativo guest per una macchina virtuale Windows all'archivio dati di Monitoraggio di Azure. A partire dalla versione 1.11 di WAD, è possibile scrivere le metriche direttamente nell'archivio delle metriche di Monitoraggio di Azure in cui sono già state raccolte le metriche standard della piattaforma. L'archiviazione in questo percorso consente di accedere alle stesse azioni disponibili per le metriche della piattaforma.  Le azioni includono quasi in tempo reale gli avvisi, i grafici, il routing, l'accesso dall'API REST e altro ancora.  Le versioni precedenti dell'estensione WAD scrivono in Archiviazione di Azure, ma non nell'archivio dati di Monitoraggio di Azure.   

Se non si ha familiarità con i modelli di Resource Manager, vedere le [distribuzioni dei modelli](../azure-resource-manager/resource-group-overview.md) e la struttura e la sintassi correlate.  

## <a name="pre-requisites"></a>Prerequisiti

- La sottoscrizione deve essere registrata con [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1). 

- È necessario aver installato [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) oppure è possibile usare [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md). 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurare Monitoraggio di Azure come sink dei dati 
L'estensione di Diagnostica di Azure usa una funzionalità denominata "sink di dati" per effettuare il routing delle metriche e dei log in percorsi diversi.  I passaggi seguenti illustrano come usare un modello di Resource Manager e PowerShell per distribuire una macchina virtuale usando il nuovo sink di dati "Monitoraggio di Azure". 

## <a name="author-resource-manager-template"></a>Creare un modello di Resource Manager 
Per questo esempio è possibile usare un modello di esempio disponibile pubblicamente. I modelli iniziali sono disponibili in https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows 

- **Azuredeploy.json** è un modello di Resource Manager preconfigurato per la distribuzione di una macchina virtuale. 

- **Azuredeploy.parameters.json** è un file di parametri in cui sono archiviate informazioni quali il nome utente e la password che si desidera impostare per la macchina virtuale. Durante la distribuzione, il modello di Resource Manager usa i parametri impostati in questo file. 

Scaricare e salvare entrambi i file in locale. 

###  <a name="modify-azuredeployparametersjson"></a>Modificare azuredeploy.parameters.json
Aprire il file *azuredeploy.parameters.json* 

1. Immettere i valori relativi a *adminUsername* e *adminPassword* per la macchina virtuale. Questi parametri vengono usati per l'accesso remoto alla macchina virtuale. NON usare i parametri presenti in questo modello per evitare che la macchina virtuale sia soggetta a hijack. I bot eseguono la ricerca in Internet dei nomi utente e delle password archiviati nei repository Github pubblici. È probabile che stiano testando le macchine virtuali con queste impostazioni predefinite.  

1. Creare un nome DNS univoco per la VM.  

### <a name="modify-azuredeployjson"></a>Modificare azuredeploy.json

Aprire il file *azuredeploy.json* 

Aggiungere un ID account di archiviazione alla sezione **variables** del modello dopo la voce relativa a **storageAccountName**.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

Aggiungere questa estensione di identità del servizio gestita al modello nella parte superiore della sezione "resources".  L'estensione garantisce che Monitoraggio di Azure accetti le metriche generate.  

```json
//Find this code 
"resources": [
// Add this code directly below
    { 
        "type": "Microsoft.Compute/virtualMachines/extensions", 
        "name": "WADExtensionSetup", 
        "apiVersion": "2015-05-01-preview", 
        "location": "[resourceGroup().location]", 
        "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ], 
        "properties": { 
            "publisher": "Microsoft.ManagedIdentity", 
            "type": "ManagedIdentityExtensionForWindows", 
            "typeHandlerVersion": "1.0", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "port": 50342 
            } 
        } 
    }, 
```

Aggiungere la configurazione "identity" alla risorsa della VM per assicurarsi che Azure assegni all'estensione dell'identità del servizio gestita un'identità di sistema. Con questo passaggio, la VM può generare metriche guest su se stessa in Monitoraggio di Azure 

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{ 
    "apiVersion": "2017-03-30", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[variables('vmName')]", 
    "location": "[resourceGroup().location]", 
    // add these 3 lines below
    "identity": {  
    "type": "SystemAssigned" 
    }, 
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {   
    ...
```

Aggiungere la configurazione seguente per abilitare l'estensione di diagnostica in una macchina virtuale Windows.  Per una semplice macchina virtuale basata su Resource Manager, è possibile aggiungere la configurazione dell'estensione alla matrice di risorse per la macchina virtuale. La riga "sink": "AzMonSink" e il corrispondente "SinksConfig" più avanti nella sezione consentono all'estensione di generare le metriche direttamente in Monitoraggio di Azure. È possibile aggiungere o rimuovere i contatori delle prestazioni in base alle esigenze.  


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": { 
    "bootDiagnostics": { 
    "enabled": true, 
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]" 
    } 
} 
}, 
//Start of section to add 
"resources": [        
{ 
            "type": "extensions", 
            "name": "Microsoft.Insights.VMDiagnosticsSettings", 
            "apiVersion": "2015-05-01-preview", 
            "location": "[resourceGroup().location]", 
            "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" 
            ], 
            "properties": { 
            "publisher": "Microsoft.Azure.Diagnostics", 
            "type": "IaaSDiagnostics", 
            "typeHandlerVersion": "1.4", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "WadCfg": { 
                "DiagnosticMonitorConfiguration": { 
    "overallQuotaInMB": 4096, 
    "DiagnosticInfrastructureLogs": { 
                    "scheduledTransferLogLevelFilter": "Error" 
        }, 
                    "Directories": { 
                    "scheduledTransferPeriod": "PT1M", 
    "IISLogs": { 
                        "containerName": "wad-iis-logfiles" 
                    }, 
                    "FailedRequestLogs": { 
                        "containerName": "wad-failedrequestlogs" 
                    } 
                    }, 
                    "PerformanceCounters": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "sinks": "AzMonSink", 
                    "PerformanceCounterConfiguration": [ 
                        { 
                        "counterSpecifier": "\\Memory\\Available Bytes", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\Committed Bytes", 
                        "sampleRate": "PT15S" 
                        } 
                    ] 
                    }, 
                    "WindowsEventLog": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "DataSource": [ 
                        { 
                        "name": "Application!*" 
                        } 
                    ] 
                    }, 
                    "Logs": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "scheduledTransferLogLevelFilter": "Error" 
                    } 
                }, 
                "SinksConfig": { 
                    "Sink": [ 
                    { 
                        "name" : "AzMonSink", 
                        "AzureMonitor" : {} 
                    } 
                    ] 
                } 
                }, 
                "StorageAccount": "[variables('storageAccountName')]" 
            }, 
            "protectedSettings": { 
                "storageAccountName": "[variables('storageAccountName')]", 
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]", 
                "storageAccountEndPoint": "https://core.windows.net/" 
            } 
            } 
        } 
        ] 
//End of section to add 
```


Salvare e chiudere entrambi i file 
 

## <a name="deploy-the-resource-manager-template"></a>Distribuire il modello di Resource Manager 

> [!NOTE]
> È necessario eseguire la versione 1.5 o successiva dell'estensione di Diagnostica di Azure E la proprietà "autoUpgradeMinorVersion" deve essere impostata su 'true' nel modello di Resource Manager.  Azure carica quindi l'estensione corretta all'avvio della macchina virtuale. Se il modello non dispone di queste impostazioni, modificarle e ridistribuire il modello. 


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
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > Ricordarsi di [usare un'area di Azure abilitata per le metriche personalizzate](metrics-custom-overview.md). 
 
1. Eseguire questi comandi per distribuire la macchina virtuale con  
   > [!NOTE] 
   > Se si desidera aggiornare una VM esistente, aggiungere semplicemente *-Mode Incremental* alla fine del comando seguente. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. Una volta completata la distribuzione, la VM dovrebbe trovarsi nel portale di Azure e generare le metriche in Monitoraggio di Azure. 

   > [!NOTE] 
   > È possibile riscontrare errori in relazione al parametro vmSkuSize selezionato. In questo caso, tornare al file azuredeploy.json e aggiornare il valore predefinito del parametro vmSkuSize. In questo caso è consigliabile provare "Standard_DS1_v2"). 

## <a name="chart-your-metrics"></a>Tracciare il grafico delle metriche 

1. Accedere al portale di Azure 

1. Fare clic su **Monitoraggio** nel menu a sinistra 

1. Nella pagina Monitoraggio fare clic su **Metriche**. 

   ![Pagina delle metriche](./media/metrics-store-custom-rest-api/metrics.png) 

1. Modificare il periodo di aggregazione in **Ultimi 30 minuti**.  

1. Nell'elenco a discesa della risorsa selezionare la macchina virtuale appena creata. Se non si è modificato il nome nel modello, deve essere *SimpleWinVM2*.  

1. Nell'elenco a discesa degli spazi dei nomi selezionare **azure.vm.windows.guest** 

1. Nell'elenco a discesa delle metriche selezionare **Memoria\%Committed Bytes in Use** (Memoria\Byte di cui è stato eseguito il commit).  
 

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).