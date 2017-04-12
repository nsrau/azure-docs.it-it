---
title: Ridimensionare una macchina virtuale Windows nel modello di distribuzione classico - Azure | Documentazione Microsoft
description: Ridimensionare una macchina virtuale Windows creata nel modello di distribuzione classico usando Azure PowerShell.
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 4277bc8394c7ba140291e9dc776162e87deab96b
ms.lasthandoff: 03/31/2017


---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Ridimensionare una VM Windows creata nel modello di distribuzione classico
Questo articolo illustra come ridimensionare una VM Windows creata nel modello di distribuzione classico usando Azure PowerShell.

Riguardo alla possibilità di ridimensionare una VM, è necessario tenere in considerazione i due fattori che controllano l'intervallo di dimensioni disponibili. Il primo fattore è la regione in cui è distribuita la VM. L'elenco delle dimensioni di VM disponibili nella regione si trova nella scheda Servizi della pagina Web Regioni di Azure. Il secondo fattore è l'hardware fisico che ospita attualmente la VM. I server fisici che ospitano le VM sono raggruppati in cluster di hardware fisico comune. Il metodo di modifica delle dimensioni di una VM varia a seconda che le nuove dimensioni siano supportate dal cluster hardware che ospita attualmente la VM stessa.

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. È anche possibile [ridimensionare una VM creata nel modello di distribuzione Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>Aggiungere il proprio account
È necessario configurare Azure PowerShell per garantirne il funzionamento con le risorse classiche di Azure. Per configurare Azure PowerShell per la gestione delle risorse classiche, seguire questa procedura.

1. Al prompt di PowerShell digitare `Add-AzureAccount` e premere **INVIO**. 
2. Digitare l'indirizzo di posta elettronica associato alla sottoscrizione di Azure e fare clic su **Continua**. 
3. Digitare la password per l'account. 
4. Fare clic su **Accedi**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Ridimensionare nello stesso cluster hardware
Per ridimensionare una VM a una dimensione disponibile nel cluster hardware che ospita la VM stessa, seguire questa procedura.

1. Eseguire il comando PowerShell seguente per elencare le dimensioni di VM disponibili nel cluster hardware che ospita il servizio cloud contenente la VM in questione.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. Per ridimensionare la VM, eseguire i comandi seguenti.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Ridimensionare in un nuovo cluster hardware
Per ridimensionare una VM a una dimensione non disponibile nel cluster hardware che ospita la VM stessa, è necessario ricreare il servizio cloud e tutte le VM presenti in tale servizio. Ogni servizio cloud è ospitato in un singolo cluster hardware, motivo per cui tutte le VM presenti nel servizio cloud devono avere dimensioni supportate in un cluster hardware. La procedura seguente descrive come ridimensionare una VM eliminando e quindi ricreando il servizio cloud.

1. Eseguire il comando PowerShell seguente per elencare le dimensioni di VM disponibili nella regione. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Prendere nota di tutte le impostazioni di configurazione per ciascuna VM presente nel servizio cloud che contiene la VM da ridimensionare. 
3. Eliminare tutte le VM nel servizio cloud selezionando l'opzione per conservare i dischi per ciascuna di esse.
4. Ricreare la VM da ridimensionare usando la dimensione desiderata.
5. Ricreare tutte le altre VM che si trovavano nel servizio cloud usando una dimensione di VM disponibile nel cluster hardware che ora ospita il servizio cloud.

Uno script di esempio per l'eliminazione e la nuova creazione di un servizio cloud mediante una nuova dimensione di VM è disponibile [qui](https://github.com/Azure/azure-vm-scripts). 

## <a name="next-steps"></a>Passaggi successivi
* [Ridimensionare una VM creata nel modello di distribuzione Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


