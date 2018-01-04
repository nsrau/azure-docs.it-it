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
ms.openlocfilehash: 68855e0070916dc672914fbc8ca3587a5d3c25f6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Estensione macchina virtuale agente Network Watcher per Windows

## <a name="overview"></a>Panoramica

[Azure Watcher di rete](../../network-watcher/network-watcher-monitoring-overview.md) è delle prestazioni di monitoraggio, diagnostica e analitica servizio di rete che consente il monitoraggio di reti di Azure. L'estensione della macchina virtuale dell'agente di controllo di rete è un requisito per l'acquisizione del traffico di rete su richiesta e altre funzionalità avanzate in macchine virtuali di Azure.


Questo documento descrive in dettaglio le piattaforme e le opzioni di distribuzione supportate per l'estensione macchina virtuale agente Network Watcher per Windows.

## <a name="prerequisites"></a>Prerequisiti

### <a name="operating-system"></a>Sistema operativo

L'estensione agente Network Watcher per Windows può essere eseguita in Windows Server 2008 R2, 2012, 2012 R2 e 2016. Nano Server non è supportato.

### <a name="internet-connectivity"></a>Connettività Internet

Alcune delle funzionalità di Network Watcher Agent richiedono che la macchina virtuale di destinazione sia connessa a Internet. Senza la possibilità di stabilire connessioni in uscita, l'agente di controllo di rete non sarà in grado di caricare le acquisizioni di pacchetti per l'account di archiviazione. Per maggiori dettagli, vedere la [documentazione di Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schema dell'estensione

Lo schema JSON seguente illustra lo schema dell'estensione Network Watcher Agent. L'estensione non richiede, né supporta, le impostazioni fornite dall'utente e si basa sulla configurazione predefinita.

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

È possibile distribuire le estensioni VM di Azure con modelli di gestione risorse di Azure. È possibile utilizzare lo schema JSON dettagliato nella sezione precedente in un modello di gestione risorse di Azure per eseguire l'estensione dell'agente di controllo di rete durante la distribuzione di un modello di gestione risorse di Azure.

## <a name="powershell-deployment"></a>Distribuzione PowerShell

Utilizzare il `Set-AzureRmVMExtension` comando per distribuire l'estensione della macchina virtuale dell'agente di controllo di rete a una macchina virtuale esistente:

```powershell
Set-AzureRmVMExtension `
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

È possibile recuperare i dati sullo stato delle distribuzioni di estensione dal portale di Azure e PowerShell. Per visualizzare lo stato di distribuzione di estensioni per una macchina virtuale specificata, eseguire il comando seguente con il modulo PowerShell di Azure:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

L'output dell'esecuzione dell'estensione viene registrato nei file presenti nella directory seguente:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Supporto

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, fare riferimento alla documentazione Guida per l'utente di Network Watcher o contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/en-us/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/en-us/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/en-us/support/faq/).
