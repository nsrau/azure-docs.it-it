---
title: Estensione macchina virtuale di Azure Network Watcher Agent per Linux
description: Distribuire Network Watcher Agent in una macchina virtuale Linux usando un'estensione macchina virtuale.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: 476241ad3d4077f2da0c513e3c989218701232ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81255754"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Estensione macchina virtuale Network Watcher Agent per Linux

## <a name="overview"></a>Panoramica

[Azure Network Watcher](/azure/network-watcher/) è un servizio di monitoraggio delle prestazioni di rete, diagnostica e analisi che consente di monitorare le reti di Azure. L'estensione della macchina virtuale dell'agente Network Watcher è un requisito per alcune funzioni di Network Watcher nelle macchine virtuali di Azure, come l'acquisizione del traffico di rete su richiesta e altre funzionalità avanzate.

Questo articolo descrive in dettaglio le piattaforme e le opzioni di distribuzione supportate per l'estensione macchina virtuale dell'agente Network Watcher per Linux. L'installazione dell'agente non causa l'interruzione, né richiede il riavvio della macchina virtuale. È possibile distribuire l'estensione in macchine virtuali da distribuire. Se la macchina virtuale viene distribuita da un servizio di Azure, vedere la documentazione relativa al servizio per determinare se consente l'installazione di estensioni nella macchina virtuale.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione agente Network Watcher può essere configurata nelle distribuzioni di Linux seguenti:

| Distribuzione | Versione |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 e 8 |
| Red Hat | 6 e 7 |
| Oracle Linux | 6.8+ e 7 |
| SuSE Linux Enterprise Server | 11 e 12 |
| OpenSUSE Leap | 42.3+ |
| CentOS | 6.5+ e 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Connettività Internet

Alcune delle funzionalità dell'agente Network Watcher richiedono che la macchina virtuale sia connessa a Internet. Se non è possibile stabilire connessioni in uscita, alcune funzionalità dell'agente Network Watcher potrebbero non funzionare correttamente o non essere disponibili. Per altre informazioni sulle funzionalità di Network Watcher che richiedono l'installazione dell'agente, vedere la [documentazione su Network Watcher](/azure/network-watcher/).

## <a name="extension-schema"></a>Schema dell'estensione

Lo schema JSON seguente illustra lo schema dell'estensione Network Watcher Agent. L'estensione non richiede né supporta eventuali impostazioni fornite dall'utente. L'estensione si basa sulla configurazione predefinita.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| tipo | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Distribuzione del modello

È possibile distribuire le estensioni della macchina virtuale di Azure con il modello di Azure Resource Manager. Per distribuire l'estensione agente Network Watcher, usare lo schema json nel modello.

## <a name="azure-classic-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando classica di Azure

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

L'esempio seguente consente di distribuire l'estensione macchina virtuale dell'agente Network Watcher in una macchina virtuale esistente distribuita tramite il modello di distribuzione classico:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

L'esempio seguente consente di distribuire l'estensione macchina virtuale dell'agente Network Watcher in una macchina virtuale esistente distribuita tramite il modello di Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshooting"></a>Risoluzione dei problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati dal portale di Azure o con l'interfaccia della riga di comando di Azure.

L'esempio seguente mostra lo stato di distribuzione dell'estensione NetworkWatcherAgentLinux per una macchina virtuale distribuita attraverso Gestione risorse, usando l'interfaccia della riga di comando di Azure:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Supporto

Per ulteriori informazioni in qualsiasi punto di questo articolo, è possibile fare riferimento alla documentazione di [Network Watcher](/azure/network-watcher/)oppure contattare gli esperti di Azure nei [Forum MSDN su Azure e stack overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).
