---
title: Estensione macchina virtuale agente Network Watcher per Windows | Documentazione Microsoft
description: Distribuire l'agente Network Watcher in una macchina virtuale Windows usando un'estensione macchina virtuale.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 6e02f5a5b42da9c99a08782903cdc05ee32ec9d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743327"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Estensione macchina virtuale agente Network Watcher per Windows

## <a name="overview"></a>Panoramica

[Network Watcher di Azure](../../network-watcher/network-watcher-monitoring-overview.md) è un servizio di monitoraggio, diagnostica e analisi delle prestazioni di rete che consente di monitorare le reti di Azure. L'estensione della macchina virtuale dell'agente Network Watcher è un requisito per l'acquisizione del traffico di rete su richiesta e altre funzionalità avanzate nelle macchine virtuali di Azure.


Questo documento descrive in dettaglio le piattaforme e le opzioni di distribuzione supportate per l'estensione macchina virtuale agente Network Watcher per Windows. L'installazione dell'agente non causa l'interruzione, né richiede il riavvio della macchina virtuale. È possibile distribuire l'estensione in macchine virtuali da distribuire. Se la macchina virtuale viene distribuita da un servizio di Azure, vedere la documentazione relativa al servizio per determinare se consente l'installazione di estensioni nella macchina virtuale.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione agente Network Watcher per Windows può essere eseguita in Windows Server 2008 R2, 2012, 2012 R2 e 2016. Nano Server non è supportato in questo momento.

### <a name="internet-connectivity"></a>Connettività Internet

Alcune delle funzionalità di Network Watcher Agent richiedono che la macchina virtuale di destinazione sia connessa a Internet. Senza la possibilità di stabilire connessioni in uscita, l'agente Network Watcher non sarà in grado di caricare le acquisizioni dei pacchetti nell'account di archiviazione. Per maggiori dettagli, vedere la [documentazione di Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schema dell'estensione

Lo schema JSON seguente illustra lo schema dell'estensione Network Watcher Agent. L'estensione non richiede né supporta impostazioni fornite dall'utente e si basa sulla configurazione predefinita.

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

| NOME | Valore/Esempio |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. È possibile usare lo schema JSON descritto nella sezione precedente in un modello di Azure Resource Manager per eseguire l'estensione agente Network Watcher durante la distribuzione di un modello di Azure Resource Manager.

## <a name="powershell-deployment"></a>Distribuzione PowerShell

Usare il comando `Set-AzVMExtension` per distribuire l'estensione macchina virtuale dell'agente Network Watcher in una macchina virtuale esistente:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Risoluzione dei problemi e supporto

### <a name="troubleshooting"></a>risoluzione dei problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e da PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una determinata macchina virtuale, eseguire il comando seguente tramite il modulo di Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Supporto

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, fare riferimento alla documentazione Guida per l'utente di Network Watcher o contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
