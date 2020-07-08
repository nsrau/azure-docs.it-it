---
title: Estensione macchina virtuale Dependency Agent di Monitoraggio di Azure per Linux
description: Distribuire Dependency Agent di Monitoraggio di Azure in una macchina virtuale Linux usando un'estensione macchina virtuale.
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
ms.openlocfilehash: cf4653ea7e49404af9e6d46796a7b00d9373c6da
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996551"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Estensione macchina virtuale Dependency Agent di Monitoraggio di Azure per Linux

La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali ottiene i dati da Microsoft Dependency Agent. L'estensione macchina virtuale Dependency Agent VM di Azure per Linux è pubblicata e supportata da Microsoft. L'estensione installa Dependency Agent nelle macchine virtuali di Azure. Questo documento descrive in dettaglio le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione macchina virtuale Dependency Agent VM di Azure per Linux.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

È possibile eseguire l'estensione Dependency Agent VM di Azure per Linux nei sistemi operativi supportati, elencati nella sezione [Sistemi operativi supportati](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) dell'articolo sulla distribuzione di Monitoraggio di Azure per le macchine virtuali.

## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente visualizza lo schema dell'estensione Dependency Agent VM di Azure in una macchina virtuale Linux di Azure. 

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
| apiVersion | 01-01-2015 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. È possibile usare lo schema JSON descritto nella sezione precedente in un modello di Azure Resource Manager per eseguire l'estensione Dependency Agent VM di Azure durante la distribuzione di un modello di Azure Resource Manager.

Il modello JSON per un'estensione macchina virtuale può essere annidato nella risorsa della macchina virtuale. In alternativa, è possibile posizionarlo al livello radice o al livello superiore di un modello JSON di Resource Manager. Il posizionamento di JSON influisce sul valore del nome e tipo di risorsa. Per altre informazioni, vedere [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md) (Impostare il nome e il tipo per le risorse figlio).

L'esempio seguente presuppone che l'estensione Dependency Agent sia annidata nella risorsa della macchina virtuale. Quando si annida la risorsa di estensione, l'elemento JSON viene inserito nell'oggetto `"resources": []` della macchina virtuale.


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

Quando si inserisce il codice JSON dell'estensione nella radice del modello, il nome della risorsa include un riferimento alla macchina virtuale padre. Il tipo riflette la configurazione nidificata. 

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

È possibile usare l'interfaccia della riga di comando di Azure per distribuire l'estensione Dependency Agent VM in una macchina virtuale esistente.  

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

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente nell'interfaccia della riga di comando di Azure:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

L'output dell'esecuzione dell'estensione viene registrato nel file seguente:

```
/opt/microsoft/dependency-agent/log/install.log 
```

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi aspetto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow di Azure](https://azure.microsoft.com/support/forums/). È anche possibile registrare un evento imprevisto del supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
