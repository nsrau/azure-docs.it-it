---
title: Estensione macchina virtuale Azure Network Watcher Agent per Linux | Microsoft Docs
description: Distribuire Network Watcher Agent in una macchina virtuale Linux usando un'estensione macchina virtuale.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: efd512395b49d3c274bb5aa409d1cbd527673659
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781888"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Estensione macchina virtuale Network Watcher Agent per Linux

## <a name="overview"></a>Panoramica

[Azure Network Watcher](/azure/network-watcher/) è un servizio di monitoraggio delle prestazioni di rete, diagnostica e analisi che consente di monitorare le reti di Azure. L'estensione della macchina virtuale dell'agente Network Watcher è un requisito per alcune funzioni di Network Watcher nelle macchine virtuali di Azure, come l'acquisizione del traffico di rete su richiesta e altre funzionalità avanzate.

Questo articolo descrive in dettaglio le piattaforme e le opzioni di distribuzione supportate per l'estensione macchina virtuale dell'agente Network Watcher per Linux. L'installazione dell'agente non causa l'interruzione, né richiede il riavvio della macchina virtuale. È possibile distribuire l'estensione in macchine virtuali da distribuire. Se la macchina virtuale viene distribuita da un servizio di Azure, vedere la documentazione relativa al servizio per determinare se consente l'installazione di estensioni nella macchina virtuale.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione agente Network Watcher può essere configurata nelle distribuzioni di Linux seguenti:

| Distribuzione | Version |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 e 8 |
| Red Hat | 6 e 7 |
| Oracle Linux | 6.8+ e 7 |
| SUSE Linux Enterprise Server | 11 e 12 |
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

| NOME | Valore/Esempio |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Distribuzione del modello

È possibile distribuire le estensioni della macchina virtuale di Azure con il modello di Azure Resource Manager. Per distribuire l'estensione agente Network Watcher, usare lo schema json nel modello.

## <a name="azure-cli-10-deployment"></a>Distribuzione con l'interfaccia della riga di comando di Azure 1.0

L'esempio seguente consente di distribuire l'estensione macchina virtuale dell'agente Network Watcher in una macchina virtuale esistente distribuita tramite il modello di distribuzione classico:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-20-deployment"></a>Distribuzione con l'interfaccia della riga di comando di Azure 2.0

L'esempio seguente consente di distribuire l'estensione macchina virtuale dell'agente Network Watcher in una macchina virtuale esistente distribuita tramite il modello di Resource Manager:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshooting"></a>risoluzione dei problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati dal portale di Azure o con l'interfaccia della riga di comando di Azure.

L'esempio seguente illustra lo stato della distribuzione delle estensioni per una macchina virtuale distribuita tramite il modello di distribuzione classica, usando l'interfaccia della riga di comando di Azure 1.0:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

L'esempio seguente illustra lo stato della distribuzione dell'estensione NetworkWatcherAgentLinux per una macchina virtuale distribuita tramite il modello di Resource Manager, usando l'interfaccia della riga di comando di Azure 2.0:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Supporto

Per ricevere assistenza più approfondita su qualsiasi punto dell'articolo, fare riferimento alla [documentazione su Network Watcher](/azure/network-watcher/) o contattare gli esperti di Azure nei forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).
