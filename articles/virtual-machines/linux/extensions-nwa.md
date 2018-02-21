---
title: Estensione macchina virtuale Azure Network Watcher Agent per Linux | Microsoft Docs
description: Distribuire Network Watcher Agent in una macchina virtuale Linux usando un'estensione macchina virtuale.
services: virtual-machines-linux
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: fd160fc8cfc2e79ffa9492bb0ef4556bec5820d0
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Estensione macchina virtuale Network Watcher Agent per Linux

## <a name="overview"></a>Panoramica

[Azure Network Watcher](https://review.docs.microsoft.com/azure/network-watcher/) è un servizio di monitoraggio delle prestazioni di rete, diagnostica e analisi che consente di monitorare le reti di Azure. L'estensione macchina virtuale Network Watcher Agent è un requisito per alcune funzionalità di Network Watcher nelle macchine virtuali di Azure, tra cui l'acquisizione del traffico di rete su richiesta e altre funzionalità avanzate.

Questo documento descrive in dettaglio le piattaforme e le opzioni di distribuzione supportate per l'estensione macchina virtuale Network Watcher Agent per Linux.

## <a name="prerequisites"></a>prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione Network Watcher Agent può essere eseguita per le distribuzioni di Linux seguenti:

| Distribuzione | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS e 12.04 LTS |
| Debian | 7 e 8 |
| RedHat | 6.x e 7.x |
| Oracle Linux | 7x |
| SUSE | 11 e 12 |
| OpenSUSE | 7.0 |
| CentOS | 7.0 |

Si noti che CoreOS non è attualmente supportato.

### <a name="internet-connectivity"></a>Connettività Internet

Alcune delle funzionalità di Network Watcher Agent richiedono che la macchina virtuale di destinazione sia connessa a Internet. Se non vi è la possibilità di stabilire connessioni in uscita, alcune funzionalità di Network Watcher Agent potrebbero non funzionare correttamente o non essere disponibili. Per maggiori dettagli, vedere la [documentazione di Network Watcher](https://review.docs.microsoft.com/azure/network-watcher/).

## <a name="extension-schema"></a>Schema dell'estensione

Lo schema JSON seguente illustra lo schema dell'estensione Network Watcher Agent. Al momento, l'estensione non richiede né supporta impostazioni fornite dall'utente e si basa sulla configurazione predefinita.

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

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON descritto in dettaglio nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione Network Watcher Agent durante la distribuzione di un modello di Azure Resource Manager.

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure può essere usata per distribuire l'estensione macchina virtuale Network Watcher Agent in una macchina virtuale esistente.

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshooting"></a>risoluzione dei problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite l'interfaccia della riga di comando di Azure. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente nell'interfaccia della riga di comando di Azure.

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>Supporto

Per ricevere assistenza più approfondita su qualsiasi punto dell'articolo, fare riferimento alla documentazione su Network Watcher o contattare gli esperti di Azure nei [forum di Azure su MSDN e Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/en-us/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/en-us/support/faq/).
