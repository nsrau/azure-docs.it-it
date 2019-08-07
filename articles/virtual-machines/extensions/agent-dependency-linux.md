---
title: Estensione macchina virtuale dipendenza monitoraggio di Azure per Linux | Microsoft Docs
description: Distribuire l'agente di dipendenza di monitoraggio di Azure in una macchina virtuale Linux usando un'estensione macchina virtuale.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: b78bea88149d05067cf849000fef48f7b4dc5815
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774397"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Estensione della macchina virtuale per le dipendenze di monitoraggio di Azure per Linux

La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. L'estensione macchina virtuale dell'agente di dipendenza VM di Azure per Linux è pubblicata e supportata da Microsoft. L'estensione installa Dependency Agent nelle macchine virtuali di Azure. Questo documento descrive in dettaglio le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione macchina virtuale dell'agente di dipendenza VM di Azure per Linux.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione Azure VM Dependency Agent per Linux può essere eseguita nei sistemi operativi supportati elencati nella sezione [sistemi operativi supportati](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) dell'articolo sulla distribuzione di monitoraggio di Azure per le macchine virtuali.

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema dell'estensione dell'agente di dipendenza delle macchine virtuali di Azure in una VM Linux di Azure. 

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

| NOME | Valore/esempio |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. È possibile usare lo schema JSON descritto in dettaglio nella sezione precedente di un modello di Azure Resource Manager per eseguire l'estensione dell'agente di dipendenza delle macchine virtuali di Azure durante una distribuzione del modello di Azure Resource Manager.

Il codice JSON per un'estensione della macchina virtuale può essere annidato all'interno della risorsa della macchina virtuale. In alternativa, è possibile posizionarlo alla radice o al livello superiore di un modello di Gestione risorse JSON. Il posizionamento di JSON influisce sul valore del nome e tipo di risorsa. Per altre informazioni, vedere [Set name and type for child resources](../../azure-resource-manager/child-resource-name-type.md) (Impostare il nome e il tipo per le risorse figlio).

Nell'esempio seguente si presuppone che l'estensione dell'agente di dipendenza sia annidata all'interno della risorsa della macchina virtuale. Quando si nidifica la risorsa di estensione, il codice JSON viene inserito `"resources": []` nell'oggetto della macchina virtuale.


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

Quando si inserisce l'estensione JSON alla radice del modello, il nome della risorsa include un riferimento alla macchina virtuale padre. Il tipo riflette la configurazione nidificata. 

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

È possibile usare l'interfaccia della riga di comando di Azure per distribuire l'estensione VM di Dependency Agent in una macchina virtuale esistente.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati dal portale di Azure e usando l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente usando l'interfaccia della riga di comando di Azure:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Supporto

Per ulteriori informazioni in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni su come usare il supporto di Azure, leggere le [domande frequenti sul supporto Microsoft Azure](https://azure.microsoft.com/support/faq/).
