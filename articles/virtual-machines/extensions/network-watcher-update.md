---
title: Aggiornare l'estensione Network Watcher alla versione più recente
description: Informazioni su come aggiornare l'estensione Azure Network Watcher alla versione più recente.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: e367c348364d03cec6914c99e7ff112803fc58f6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132432"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Aggiornare l'estensione Network Watcher alla versione più recente

## <a name="overview"></a>Panoramica

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) è un servizio di monitoraggio, diagnostica e analisi delle prestazioni di rete che monitora le reti di Azure. L'estensione della macchina virtuale (VM) agente Network Watcher è un requisito per l'acquisizione del traffico di rete su richiesta e l'uso di altre funzionalità avanzate nelle VM di Azure. L'estensione Network Watcher viene utilizzata da funzionalità quali monitoraggio connessione, monitoraggio connessione (anteprima), risoluzione dei problemi di connessione e acquisizione pacchetti.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'estensione Network Watcher sia installata nella macchina virtuale.

## <a name="latest-version"></a>Versione più recente

È attualmente disponibile la versione più recente dell'estensione Network Watcher `1.4.1654.1` .

## <a name="update-your-extension"></a>Aggiornare l'estensione

Per aggiornare l'estensione, è necessario essere a conoscenza della versione dell'estensione.

### <a name="check-your-extension-version"></a>Controllare la versione dell'estensione

È possibile controllare la versione dell'estensione usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

#### <a name="usetheazureportal"></a>Usare il portale di Azure

1. Passare al riquadro **estensioni** della macchina virtuale nella portale di Azure.
1. Selezionare l'estensione **AzureNetworkWatcher** per visualizzare il riquadro dei dettagli.
1. Individuare il numero di versione nel campo **versione** .  

#### <a name="use-the-azure-cli"></a>Usare l'interfaccia della riga di comando di Azure

Eseguire il comando seguente da un prompt dell'interfaccia della riga di comando di Azure:

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Individuare l'estensione AzureNetworkWatcher nell'output. Identificare il numero di versione nel campo "TypeHandlerVersion" nell'output.  

#### <a name="usepowershell"></a>Usare PowerShell

Eseguire i comandi seguenti da un prompt di PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Individuare l'estensione AzureNetworkWatcher nell'output. Identificare il numero di versione nel campo "TypeHandlerVersion" nell'output.

### <a name="update-your-extension"></a>Aggiornare l'estensione

Se la versione è precedente `1.4.1654.1` a, ovvero la versione più recente corrente, aggiornare l'estensione usando una delle opzioni seguenti.

#### <a name="option-1-use-powershell"></a>Opzione 1: usare PowerShell

Eseguire i comandi seguenti:

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```

#### <a name="option-2-use-the-azure-cli"></a>Opzione 2: usare l'interfaccia della riga di comando di Azure

Forzare un aggiornamento.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Se il tentativo non funziona, rimuovere e installare nuovamente l'estensione e attenersi alla procedura seguente per aggiungere automaticamente la versione più recente.

Rimuovere l'estensione.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Installare di nuovo l'estensione.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

#### <a name="option-3-reboot-your-vms"></a>Opzione 3: riavviare le VM

Se l'aggiornamento automatico è impostato su true per l'estensione Network Watcher, riavviare l'installazione della macchina virtuale nell'estensione più recente.

## <a name="support"></a>Supporto

Per ulteriori informazioni in qualsiasi punto di questo articolo, vedere la documentazione dell'estensione Network Watcher per [Linux](./network-watcher-linux.md) o [Windows](./network-watcher-windows.md). È anche possibile contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/). In alternativa, archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/)e selezionare **ottenere supporto**. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).