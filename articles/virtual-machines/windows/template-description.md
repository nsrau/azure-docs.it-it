---
title: Macchine virtuali in un modello di Azure Resource Manager | Microsoft Azure
description: Altre informazioni su come definire la risorsa della macchina virtuale in un modello di Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: davidmu
ms.openlocfilehash: 9c0039987ec28601c9338d2b94633c38c31e01f8
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Macchine virtuali in un modello di Azure Resource Manager

Questo articolo descrive gli aspetti di un modello di Azure Resource Manager che si applicano alle macchine virtuali. L'articolo descrive un modello completo per la creazione di una macchina virtuale; a tale scopo sono necessarie definizioni di risorse per gli account di archiviazione, le interfacce di rete, gli indirizzi IP pubblici e le reti virtuali. Per altre informazioni su come queste risorse possono essere definite insieme, vedere [Procedura dettagliata per un modello di Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Sono disponibili numerosi [modelli nella raccolta](https://azure.microsoft.com/documentation/templates/?term=VM) che includono la risorsa di VM. Di seguito sono descritti solo alcuni elementi che possono essere inclusi in un modello.

Questo esempio mostra una sezione di risorse tipica di un modello per la creazione di un numero specificato di VM:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
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
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Questo esempio si basa su un account di archiviazione creato in precedenza. È possibile creare l'account di archiviazione mediante la distribuzione dal modello. L'esempio si basa anche su un'interfaccia di rete e le risorse dipendenti definite nel modello. Queste risorse non vengono visualizzate nell'esempio.
>
>

## <a name="api-version"></a>Versione dell'API

Quando si distribuiscono risorse usando un modello, è necessario specificare una versione dell'API da usare. L'esempio illustra l'uso di questa risorsa di macchina virtuale usando l'elemento apiVersion:

```
"apiVersion": "2016-04-30-preview",
```

La versione dell'API specificata nel modello influisce sulle proprietà che è possibile definire nel modello. In generale, è opportuno selezionare la versione più recente dell'API durante la creazione di modelli. Per i modelli esistenti, è possibile decidere se si desidera continuare a usare una versione precedente dell'API o aggiornare il modello alla versione più recente per sfruttare i vantaggi delle nuove funzionalità.

Per ottenere le versioni dell'API più aggiornate:

- API REST: [Elencare tutti i provider di risorse](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)
- PowerShell: [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider)
- Interfaccia della riga di comando di Azure 2.0: [az provider show](https://docs.microsoft.com/cli/azure/provider#az_provider_show)

## <a name="parameters-and-variables"></a>Parametri e variabili

I [parametri](../../resource-group-authoring-templates.md) semplificano la specifica di valori per il modello quando viene eseguito. Nell'esempio viene usata questa sezione dei parametri:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

Quando si distribuisce il modello di esempio, si immettono valori per il nome e la password dell'account amministratore in ogni VM e il numero di VM da creare. È possibile scegliere di specificare i valori di parametri in un file separato gestito con il modello o fornire i valori quando viene richiesto.

Le [variabili](../../resource-group-authoring-templates.md) semplificano la configurazione di valori nel modello usati ripetutamente o che possono cambiare nel tempo. L'esempio usa questa sezione delle variabili:

```
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

Quando si distribuisce il modello di esempio, vengono usati valori di variabili per il nome e l'identificatore dell'account di archiviazione creato in precedenza. Le variabili consentono anche di fornire le impostazioni per l'estensione di diagnostica. Usare le [procedure consigliate per la creazione di Azure Resource Manager](../../resource-manager-template-best-practices.md) per decidere come si vuole organizzare i parametri e le variabili del modello.

## <a name="resource-loops"></a>cicli di risorse

Quando sono necessarie più macchine virtuali per l'applicazione, è possibile usare un elemento di copia in un modello. Questo elemento facoltativo esegue il ciclo di creazione del numero di VM specificato come parametro:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Si noti anche nell'esempio che l'indice di ciclo viene usato quando si specificano alcuni valori per la risorsa. Ad esempio, se è stato immesso un numero di istanze pari a tre, i nomi dei dischi del sistema operativo sono myOSDisk1, myOSDisk2 e myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Questo esempio usa dischi gestiti per le macchine virtuali.
>
>

Tenere presente che la creazione di un ciclo per una risorsa nel modello potrebbe richiedere di usare il ciclo quando si crea o si accede ad altre risorse. Ad esempio, più VM non possono usare la stessa interfaccia di rete; pertanto se esegue il ciclo di creazione di tre VM, il modello deve anche eseguire il ciclo di creazione di tre interfacce di rete. Quando si assegna un'interfaccia di rete a una VM, l'indice di ciclo viene usato per la relativa identificazione:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Dipendenze

Il corretto funzionamento della maggior parte delle risorse dipende dalle altre risorse. Le macchine virtuali deve essere associate a una rete virtuale e a tale scopo è necessaria un'interfaccia di rete. L'elemento [dependsOn](../../resource-group-define-dependencies.md) viene usato per verificare che l'interfaccia di rete sia pronta per essere usata prima che vengano create le VM:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager consente di distribuire in parallelo le risorse la cui distribuzione non dipende da un'altra risorsa. Prestare attenzione quando si impostano le dipendenze in quanto si può rallentare inavvertitamente la distribuzione, specificando dipendenze non necessarie. Le dipendenze possono concatenare più risorse. Ad esempio, l'interfaccia di rete dipende dall'indirizzo IP pubblico e dalle risorse della rete virtuale.

Come è possibile stabilire se è necessaria una dipendenza? Esaminare i valori impostati nel modello. Una dipendenza è necessaria se un elemento nella definizione di risorsa della macchina virtuale punta a un'altra risorsa distribuita nello stesso modello. Ad esempio, la macchina virtuale di esempio definisce un profilo di rete:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Per impostare questa proprietà, è necessario che esista l'interfaccia di rete. Pertanto, è necessaria una dipendenza. È inoltre necessario impostare una dipendenza quando viene definita una risorsa (figlio) all'interno di un'altra risorsa (padre). Ad esempio, le estensioni dello script personalizzate e le impostazioni di diagnostica vengono entrambe definite come risorse figlio della macchina virtuale. Non possono essere create fino a quando non esiste la macchina virtuale. Pertanto, entrambe le risorse sono contrassegnate come dipendenti nella macchina virtuale.

## <a name="profiles"></a>Profili

Quando si definisce una risorsa di macchina virtuale, vengono usati diversi elementi di profilo. Alcuni sono necessari e alcuni sono facoltativi. Ad esempio, sono necessari gli elementi hardwareProfile, osProfile, storageProfile e networkProfile, ma diagnosticsProfile è facoltativo. Questi profili definiscono impostazioni, ad esempio:
   
- [dimensione](sizes.md)
- [nome](/architecture/best-practices/naming-conventions) e credenziali
- disco e [impostazioni del sistema operativo](cli-ps-findimage.md)
- [interfaccia di rete](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- diagnostica di avvio

## <a name="disks-and-images"></a>Dischi e immagini
   
In Azure i file del disco rigido virtuale possono rappresentare [dischi o immagini](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Quando il sistema operativo in un file di disco rigido virtuale è specializzato per essere una VM specifica, vi viene fatto riferimento come disco. Quando il sistema operativo in un file di disco rigido virtuale è generalizzato per essere usato per creare più VM, viene considerato un'immagine.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Creare nuove macchine virtuali e nuovi dischi da un'immagine della piattaforma

Quando si crea una VM, è necessario decidere quale sistema operativo usare. L'elemento imageReference viene usato per definire il sistema operativo di una nuova VM. L'esempio illustra una definizione per un sistema operativo Windows Server:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Se si vuole creare un sistema operativo Linux, è possibile usare questa definizione:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Le impostazioni di configurazione per il disco del sistema operativo vengono assegnate con l'elemento osDisk. L'esempio definisce un nuovo disco gestito con la modalità di memorizzazione nella cache impostata su **ReadWrite** e con il disco creato da un'[immagine della piattaforma](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Creare nuove macchine virtuali da dischi gestiti esistenti

Se si vuole creare macchine virtuali da dischi esistenti, rimuovere gli elementi imageReference e osProfile e definire le impostazioni del disco:

```
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Creare nuove macchine virtuali da un'immagine gestita

Se si vuole creare una macchina virtuale da un'immagine gestita, cambiare l'elemento imageReference e definire queste impostazioni del disco:

```
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Collegare i dischi dei dati

È facoltativamente possibile aggiungere dischi di dati alle VM. Il [numero di dischi](sizes.md) dipende dalle dimensioni del disco del sistema operativo in uso. Con le dimensioni delle VM impostate su Standard_DS1_v2, il numero massimo di dischi dati che possono essere aggiunti è due. Nell'esempio viene aggiunto un disco dati gestito a ogni VM:

```
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Estensioni

Sebbene siano una risorsa separata, le [estensioni](extensions-features.md) sono strettamente legate alle VM. Le estensioni possono essere aggiunte come risorsa figlio della VM o come risorsa separata. L'esempio illustra l'aggiunta dell'[estensione Diagnostica](extensions-diagnostics-template.md) alle VM:

```
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

Questa risorsa di estensione usa la variabile storageName e le variabili di diagnostica per fornire i valori. Se si vuole modificare i dati raccolti da questa estensione, è possibile aggiungere altri contatori delle prestazioni alla variabile wadperfcounters. È possibile anche scegliere di inserire i dati di diagnostica in un account di archiviazione diverso rispetto a quello in cui sono archiviati i dischi della VM.

Sono disponibili numerose estensioni che è possibile installare in una VM, ma la più utile è probabilmente l'[estensione dello script personalizzata](extensions-customscript.md). Nell'esempio, al primo avvio in ogni VM viene eseguito uno script PowerShell denominato start.ps1:

```
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

Lo script start.ps1 può eseguire molte attività di configurazione. Ad esempio, i dischi di dati aggiunti alle VM nell'esempio non vengono inizializzati; è possibile usare uno script personalizzato per inizializzarli. Se si devono eseguire più attività di avvio, è possibile usare il file start.ps1 per chiamare altri script PowerShell in Archiviazione di Azure. L'esempio usa PowerShell, ma è possibile usare qualsiasi metodo di scripting disponibile nel sistema operativo in uso.

È possibile visualizzare lo stato delle estensioni installate dalle impostazioni Estensioni nel portale:

![Recuperare lo stato dell'estensione](./media/template-description/virtual-machines-show-extensions.png)

È possibile anche ottenere informazioni sull'estensione tramite il comando **Get-AzureRmVMExtension** di PowerShell, il comando **vm extension get** dell'interfaccia della riga di comando di Azure 2.0 oppure l'API REST **Get extension information**.

## <a name="deployments"></a>Distribuzioni

Quando si distribuisce un modello, Azure tiene traccia delle risorse distribuite come gruppo e assegna automaticamente un nome a questo gruppo distribuito. Il nome della distribuzione corrisponde a quello del modello.

Per conoscere lo stato delle risorse nella distribuzione, è possibile usare il pannello Gruppo di risorse nel portale di Azure:

![Ottenere informazioni sulla distribuzione](./media/template-description/virtual-machines-deployment-info.png)
    
Non è un problema usare lo stesso modello per creare risorse o per aggiornare le risorse esistenti. Quando si usano comandi per distribuire i modelli, si ha la possibilità di indicare la [modalità](../../resource-group-template-deploy.md) da usare. La modalità può essere impostata su **Completa** o **Incrementale**. Gli aggiornamenti incrementali sono il valore predefinito. Prestare attenzione quando si usa la modalità **Completa** perché è possibile eliminare accidentalmente le risorse. Quando si imposta la modalità su **Completa**, Resource Manager elimina tutte le risorse nel gruppo di risorse che non sono presenti nel modello.

## <a name="next-steps"></a>Passaggi successivi

- È possibile creare un modello personalizzato usando le informazioni presenti in [Creazione di modelli di Azure Resource Manager](../../resource-group-authoring-templates.md).
- Distribuire il modello creato usando [Creare una macchina virtuale Windows con un modello di Resource Manager](ps-template.md).
- Per informazioni su come gestire le macchine virtuali create, vedere [Creare e gestire macchine virtuali di Windows con il modulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
