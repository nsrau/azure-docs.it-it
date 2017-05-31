---
title: Estensione script personalizzata di Azure per Windows | Microsoft Docs
description: "Automatizzare le attività di configurazione delle macchine virtuali Windows usando l&quot;estensione script personalizzata"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 957fb7bf1bd646ae5e4d27350502bdf14b0f00c3
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="custom-script-extension-for-windows"></a>Estensione Script personalizzato per Windows

L'estensione script personalizzata scarica ed esegue script sulle macchine virtuali di Azure. Questa estensione è utile per la configurazione post-distribuzione, l'installazione di software o qualsiasi altra attività di configurazione o gestione. Gli script possono essere scaricati dall'archiviazione di Azure o da GitHub, oppure possono essere forniti al portale di Azure durante il runtime dell'estensione. L'estensione script personalizzata è integrabile nei modelli di Azure Resource Manager e può essere eseguita anche tramite l'interfaccia della riga di comando di Azure, PowerShell, il portale di Azure o l'API REST di Macchine virtuali di Azure.

Questo documento descrive come usare l'estensione di script personalizzata con il modulo Azure PowerShell e i modelli di Azure Resource Manager e inoltre illustra i passaggi per la risoluzione dei problemi nei sistemi Windows.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione di script personalizzata per Windows può essere eseguita in Windows Server 2008 R2, 2012, 2012 R2 e 2016.

### <a name="script-location"></a>Percorso dello script

Lo script deve essere archiviato nell'archiviazione di Azure o in un altro percorso accessibile tramite un URL valido.

### <a name="internet-connectivity"></a>Connettività Internet

Per distribuire l'estensione di script personalizzata per Windows, è necessario che la macchina virtuale di destinazione sia connessa a Internet. 

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema dell'estensione di script personalizzata. L'estensione richiede un percorso dello script (archiviazione di Azure o altro percorso con un URL valido) e un comando da eseguire. Se si usa l'archiviazione di Azure come origine dello script, sono necessari un nome e una chiave di account. Questi elementi devono essere trattati come dati sensibili ed essere specificati nella configurazione protetta dell'estensione. I dati della configurazione protetta dell'estensione macchina virtuale di Azure vengono crittografati, per essere poi decrittografati solo nella macchina virtuale di destinazione.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| type | Estensioni |
| typeHandlerVersion | 1.8 |
| fileUris (es.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (es.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName (es.) | examplestorageacct |
| storageAccountKey (es.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

**Nota**: questi nomi di proprietà fanno distinzione tra maiuscole e minuscole. Usare i nomi come sono riportati sopra per evitare problemi di distribuzione.

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON indicato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione di script personalizzata durante la distribuzione di un modello di Azure Resource Manager. Un modello di esempio che include l'estensione script personalizzata è disponibile su [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Distribuzione PowerShell

Il comando `Set-AzureRmVMCustomScriptExtension` consente di aggiungere l'estensione di script personalizzata a una macchina virtuale esistente. Per ulteriori informazioni, vedere [Set-AzureRmVMCustomScriptExtension](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite il modulo Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente nella macchina virtuale di destinazione.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

I file specificati vengono scaricati nella directory seguente nella macchina virtuale di destinazione.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
dove `<n>` è un numero intero decimale che può variare nelle diverse esecuzioni dell'estensione.  Il valore `1.*` corrisponde al valore effettivo attuale `typeHandlerVersion` dell'estensione.  Ad esempio, la directory effettiva potrebbe essere `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Quando si esegue il comando `commandToExecute`, nell'estensione sarà impostata questa directory (ad esempio `...\Downloads\2`) come directory di lavoro attuale. In questo modo viene abilitato l'uso di percorsi relativi per individuare i file scaricati tramite la proprietà `fileURIs`. Nella tabella seguente sono riportati alcuni esempi.

Poiché il percorso di download assoluto può variare nel tempo, quando è possibile è preferibile optare per percorsi relativi di script/file nella stringa `commandToExecute`. Ad esempio:
```json
    "commandToExecute": "powershell.exe . . . -File './scripts/myscript.ps1'"
```

Le informazioni sul percorso dopo il primo segmento URI vengono mantenute per i file scaricati tramite l'elenco delle proprietà `fileUris`.  Come illustrato nella tabella riportata di seguito, per i file scaricati viene eseguito il mapping nelle sottodirectory di download per riflettere la struttura dei valori `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Esempi di file scaricati

| URI in fileUris | Percorso relativo scaricato | Percorso assoluto scaricato * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Come in precedenza, i percorsi assoluti delle directory cambieranno nella durata della macchina virtuale ma non all'interno di una singola esecuzione dell'estensione CustomScript.

### <a name="support"></a>Supporto

Per ricevere maggiore assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum di Azure su MSDN e Stack Overflow].(https://azure.microsoft.com/en-us/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/en-us/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/en-us/support/faq/).

