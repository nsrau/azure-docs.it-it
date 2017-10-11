---
title: Estensione macchina virtuale agente Network Watcher per Windows | Documentazione Microsoft
description: Distribuire l'agente Network Watcher in una macchina virtuale Windows usando un'estensione macchina virtuale.
services: virtual-machines-windows
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: b8d6a998bc86337b286a3434f44f762cca9b7e68
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Estensione macchina virtuale agente Network Watcher per Windows

## <a name="overview"></a>Panoramica

[Azure Network Watcher](https://review.docs.microsoft.com/en-us/azure/network-watcher/) è un servizio di monitoraggio delle prestazioni di rete, diagnostica e analisi che consente di monitorare le reti di Azure. L'estensione macchina virtuale Network Watcher Agent è un requisito per alcune funzionalità di Network Watcher nelle macchine virtuali di Azure, Include l'acquisizione del traffico di rete su richiesta e altre funzionalità avanzate.

Questo documento descrive in dettaglio le piattaforme e le opzioni di distribuzione supportate per l'estensione macchina virtuale agente Network Watcher per Windows.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione agente Network Watcher per Windows può essere eseguita in Windows Server 2008 R2, 2012, 2012 R2 e 2016. Si noti che il Nano Server non è supportato in questo momento.

### <a name="internet-connectivity"></a>Connettività Internet

Alcune delle funzionalità di Network Watcher Agent richiedono che la macchina virtuale di destinazione sia connessa a Internet. Se non vi è la possibilità di stabilire connessioni in uscita, alcune funzionalità di Network Watcher Agent potrebbero non funzionare correttamente o non essere disponibili. Per maggiori dettagli, vedere la [documentazione di Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

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
        "type": "NetworkWatcherAgentWindows",
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
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. Lo schema JSON indicato nella sezione precedente può essere usato in un modello di Azure Resource Manager per eseguire l'estensione agente Network Watcher durante la distribuzione di un modello di Azure Resource Manager.

## <a name="powershell-deployment"></a>Distribuzione PowerShell

Il comando `Set-AzureRmVMExtension` consente di distribuire l'estensione macchina virtuale agente Network Watcher a una macchina virtuale esistente.

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup1" `
                       -Location "WestUS" `
                       -VMName "myVM1" `
                       -Name "networkWatcherAgent" `
                       -Publisher "Microsoft.Azure.NetworkWatcher" `
                       -Type "NetworkWatcherAgentWindows" `
                       -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshooting"></a>Risoluzione dei problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite il modulo Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una determinata VM, eseguire questo comando nel modulo Azure PowerShell.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Supporto

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, fare riferimento alla documentazione Guida per l'utente di Network Watcher o contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/en-us/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/en-us/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/en-us/support/faq/).
