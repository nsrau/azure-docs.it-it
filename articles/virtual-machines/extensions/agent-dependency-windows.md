---
title: Estensione di macchina virtuale di Azure dipendenza del monitoraggio per Windows | Microsoft Docs
description: Distribuire l'agente di dipendenza di monitoraggio di Azure nella macchina virtuale Windows usando un'estensione macchina virtuale.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 34dd872db199a4c10e9f321457188b7f7642944d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120230"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Estensione di macchina virtuale di Azure dipendenza del monitoraggio per Windows

La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. L'estensione di macchina virtuale dell'agente di dipendenza di macchina virtuale di Azure per Windows è pubblicata e supportata da Microsoft. L'estensione installa l'agente di dipendenza in macchine virtuali di Azure. Questo documento illustra in dettaglio le piattaforme supportate, le configurazioni e opzioni di distribuzione per l'estensione di macchina virtuale dell'agente di dipendenza di macchina virtuale di Azure per Windows.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>Sistema operativo

L'estensione agente di dipendenza di macchina virtuale di Azure per Windows eseguibili in sistemi operativi supportati elencati nel [sistemi operativi supportati](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) sezione della finestra di monitoraggio di Azure per l'articolo di distribuzione di macchine virtuali.

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema per l'estensione dell'agente di dipendenza di macchina virtuale di Azure in una VM Windows di Azure.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Valori delle proprietà

| Name | Valore/esempio |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Distribuzione del modello

È possibile distribuire le estensioni della macchina virtuale di Azure con modelli Azure Resource Manager. È possibile utilizzare lo schema JSON indicato nella sezione precedente in un modello di Azure Resource Manager per eseguire l'estensione di agente di dipendenza di macchina virtuale di Azure durante la distribuzione di un modello di Azure Resource Manager.

Il codice JSON per un'estensione macchina virtuale può essere annidato all'interno della risorsa macchina virtuale. In alternativa, è possibile inserirlo nel livello radice o livello superiore di un modello JSON di Resource Manager. Il posizionamento di JSON influisce sul valore del nome e tipo di risorsa. Per altre informazioni, vedere [Set name and type for child resources](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources) (Impostare il nome e il tipo per le risorse figlio).

Nell'esempio seguente presuppone che l'estensione dell'agente di dipendenza sia annidata nella risorsa della macchina virtuale. Quando si nidifica la risorsa dell'estensione, il codice JSON viene inserito nel `"resources": []` oggetto della macchina virtuale.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Quando si posiziona l'estensione JSON nella radice del modello, il nome di risorsa include un riferimento alla macchina virtuale padre. Il tipo riflette la configurazione annidata.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>Distribuzione PowerShell

È possibile usare il `Set-AzVMExtension` comando per distribuire l'estensione di macchina virtuale dell'agente di dipendenza a una macchina virtuale esistente. Prima di eseguire il comando, le configurazioni pubbliche e private devono essere archiviati in una tabella hash di PowerShell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

Dati sullo stato delle distribuzioni dell'estensione possono essere recuperati dal portale di Azure e usando il modulo Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire il comando seguente usando il modulo Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile inviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni su come usare il supporto di Azure, vedere la [domande frequenti sul supporto di Microsoft Azure](https://azure.microsoft.com/support/faq/).
