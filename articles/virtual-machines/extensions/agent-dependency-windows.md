---
title: Azure Monitor Dependency virtual machine extension for Windows
description: Distribuire l'agente di dipendenza di Monitoraggio di Azure nella macchina virtuale Windows usando un'estensione della macchina virtuale.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250673"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Azure Monitor Dependency virtual machine extension for Windows

La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. L'estensione della macchina virtuale dell'agente di dipendenza della macchina virtuale delle macchine virtuali di Azure per Windows è pubblicata e supportata da Microsoft.The Azure VM Dependency agent virtual machine extension for Windows is published and supported by Microsoft. L'estensione installa l'agente di dipendenza nelle macchine virtuali di Azure.The extension installs the Dependency agent on Azure virtual machines. Questo documento descrive in dettaglio le piattaforme supportate, le configurazioni e le opzioni di distribuzione per l'estensione della macchina virtuale dell'agente di dipendenza delle macchine virtuali di Azure per Windows.This document details the supported platforms, configurations, and deployment options for the Azure VM Dependency agent virtual machine extension for Windows.

## <a name="operating-system"></a>Sistema operativo

L'estensione dell'agente di dipendenza della macchina virtuale di Azure per Windows può essere eseguita sui sistemi operativi supportati elencati nella sezione [Sistemi operativi supportati](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) dell'articolo Distribuzione di Monitoraggio di Azure per macchine virtuali.

## <a name="extension-schema"></a>Schema dell'estensione

The following JSON shows the schema for the Azure VM Dependency agent extension on an Azure Windows VM.

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

| Nome | Valore/Esempio |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Distribuzione del modello

È possibile distribuire le estensioni della macchina virtuale di Azure con i modelli di Azure Resource Manager.You can deploy the Azure VM extensions with Azure Resource Manager templates. È possibile usare lo schema JSON descritto nella sezione precedente in un modello di Azure Resource Manager per eseguire l'estensione dell'agente di dipendenza della macchina virtuale di Azure durante la distribuzione di un modello di Azure Resource Manager.You can use the JSON schema detailed in the previous section in an Azure Resource Manager template to run the Azure VM Dependency agent extension during an Azure Resource Manager template deployment.

Il codice JSON per un'estensione di macchina virtuale può essere annidato all'interno della risorsa della macchina virtuale. In alternativa, è possibile posizionarlo al livello principale o superiore di un modello JSON di Resource Manager.Or, you can place it at the root or top level of a Resource Manager JSON template. Il posizionamento di JSON influisce sul valore del nome e tipo di risorsa. Per ulteriori informazioni, consultate [Impostare il nome e](../../azure-resource-manager/templates/child-resource-name-type.md)il tipo per le risorse figlio.

Nell'esempio seguente si presuppone che l'estensione dell'agente di dipendenza sia annidata all'interno della risorsa della macchina virtuale. Quando si annida la risorsa di `"resources": []` estensione, il codice JSON viene inserito nell'oggetto della macchina virtuale.


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

Quando si inserisce l'estensione JSON nella radice del modello, il nome della risorsa include un riferimento alla macchina virtuale padre. Il tipo riflette la configurazione annidata.

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

È possibile `Set-AzVMExtension` usare il comando per distribuire l'estensione della macchina virtuale dell'agente di dipendenza in una macchina virtuale esistente. Prima di eseguire il comando, le configurazioni pubbliche e private devono essere archiviate in una tabella hash di PowerShell.Before you run the command, the public and private configurations need to be stored in a PowerShell hash table.

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

### <a name="troubleshoot"></a>Risolvere i problemi

I dati sullo stato delle distribuzioni di estensioni possono essere recuperati dal portale di Azure e tramite il modulo di Azure PowerShell.Data about the state of extension deployments can be retrieved from the Azure portal and by using the Azure PowerShell module. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente usando il modulo Azure PowerShell:To see the deployment state of extensions for a given VM, run the following command by using the Azure PowerShell module:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Supporto

Se è necessaria ulteriore assistenza in qualsiasi momento di questo articolo, è possibile contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto di Azure.Or, you can file an Azure support incident. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni su come usare il supporto di Azure, leggere le [domande frequenti sul supporto](https://azure.microsoft.com/support/faq/)di Microsoft Azure .
