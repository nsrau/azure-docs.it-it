---
title: Azure Monitor Dependency virtual machine extension for Linux
description: Distribuire l'agente di dipendenza di Monitoraggio di Azure nella macchina virtuale Linux usando un'estensione della macchina virtuale.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254040"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Azure Monitor Dependency virtual machine extension for Linux

La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. L'estensione della macchina virtuale dell'agente di dipendenza della dipendenza di Azure per Linux è pubblicata e supportata da Microsoft.The Azure VM Dependency agent virtual machine extension for Linux is published and supported by Microsoft. L'estensione installa l'agente di dipendenza nelle macchine virtuali di Azure.The extension installs the Dependency agent on Azure virtual machines. Questo documento descrive in dettaglio le piattaforme supportate, le configurazioni e le opzioni di distribuzione per l'estensione della macchina virtuale dell'agente di dipendenza delle macchine virtuali di Azure per Linux.This document details the supported platforms, configurations, and deployment options for the Azure VM Dependency agent virtual machine extension for Linux.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione dell'agente di dipendenza della macchina virtuale di Azure per Linux può essere eseguita nei sistemi operativi supportati elencati nella sezione [Sistemi operativi supportati](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) dell'articolo Distribuzione di Monitoraggio di Azure per macchine virtuali.

## <a name="extension-schema"></a>Schema dell'estensione

The following JSON shows the schema for the Azure VM Dependency agent extension on an Azure Linux VM. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
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
        "type": "DependencyAgentLinux",
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
| type | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. È possibile usare lo schema JSON descritto nella sezione precedente in un modello di Azure Resource Manager per eseguire l'estensione dell'agente di dipendenza della macchina virtuale di Azure durante la distribuzione di un modello di Azure Resource Manager.You can use the JSON schema detailed in the previous section in an Azure Resource Manager template to run the Azure VM Dependency agent extension during an Azure Resource Manager template deployment.

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
        "type": "DependencyAgentLinux",
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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per distribuire l'estensione della macchina virtuale dell'agente di dipendenza in una macchina virtuale esistente.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere i problemi

I dati sullo stato delle distribuzioni di estensioni possono essere recuperati dal portale di Azure e tramite l'interfaccia della riga di comando di Azure.Data about the state of extension deployments can be retrieved from the Azure portal and by using the Azure CLI. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente usando l'interfaccia della riga di comando di Azure:To see the deployment state of extensions for a given VM, run the following command by using the Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Supporto

Per altre informazioni in qualsiasi momento di questo articolo, contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto di Azure.Or, you can file an Azure support incident. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni su come usare il supporto di Azure, leggere le [domande frequenti sul supporto](https://azure.microsoft.com/support/faq/)di Microsoft Azure .
