---
title: Aggiornare l'estensione Network Watcher alla versione più recente
description: Informazioni su come aggiornare Network Watcher estensione alla versione più recente
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
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410342"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Come aggiornare l'estensione Network Watcher alla versione più recente 

## <a name="overview"></a>Panoramica

[Network Watcher di Azure](../../network-watcher/network-watcher-monitoring-overview.md) è un servizio di monitoraggio, diagnostica e analisi delle prestazioni di rete che consente di monitorare le reti di Azure. L'estensione della macchina virtuale dell'agente Network Watcher è un requisito per l'acquisizione del traffico di rete su richiesta e altre funzionalità avanzate nelle macchine virtuali di Azure. L'estensione Network Watcher viene utilizzata da funzionalità quali monitoraggio connessione, monitoraggio connessione (anteprima), risoluzione dei problemi di connessione e acquisizione pacchetti.   

## <a name="prerequisites"></a>Prerequisiti
In questo documento si presuppone che l'estensione Network Watcher sia installata nella macchina virtuale e si forniscano istruzioni per l'aggiornamento alla versione più recente. 

## <a name="latest-version"></a>Versione più recente
È attualmente disponibile la versione più recente dell'estensione Network Watcher `1.4.1654.1` .

## <a name="updating-your-extension"></a>Aggiornamento dell'estensione 

### <a name="check-your-extension-version"></a>Controllare la versione dell'estensione  

**Uso della portale di Azure**

1. Passare al pannello "estensioni" della macchina virtuale nella portale di Azure.   
2. Fare clic sull'estensione "AzureNetworkWatcher" per visualizzare il riquadro dei dettagli.  
3. Individuare il numero di versione nel campo ' version '.  

**Uso dell'interfaccia** della riga di comando Eseguire il comando seguente da un prompt dell'interfaccia della riga di comando di Azure.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Individuare l'estensione AzureNetworkWatcher nell'output e identificare il numero di versione dal campo "TypeHandlerVersion" nell'output.  


**Uso di PowerShell** Eseguire i comandi seguenti da un prompt di PowerShell:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Individuare l'estensione AzureNetworkWatcher nell'output e identificare il numero di versione dal campo "TypeHandlerVersion" nell'output.   


### <a name="update-your-extension"></a>Aggiornare l'estensione

Se la versione è inferiore a `1.4.1654.1` (la versione più recente corrente), aggiornare l'estensione usando una delle opzioni seguenti. 

**Opzione 1: usare PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Opzione 2: usare l'interfaccia della riga di comando di Azure**  

Forza aggiornamento 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Se questo non funziona. Rimuovere e installare di nuovo l'estensione, eseguendo la procedura riportata di seguito. Verrà aggiunta automaticamente la versione più recente. 

Rimozione dell'estensione 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Installazione dell'estensione

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Opzione 3: riavviare le VM**

Se l'aggiornamento automatico è impostato su true per l'estensione Network Watcher. Il riavvio della macchina virtuale installa l'estensione più recente.


## <a name="support"></a>Supporto

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile fare riferimento alla documentazione dell'estensione Network Watcher ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux), [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)) oppure contattare gli esperti di Azure nei [Forum MSDN e stack overflow di Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
