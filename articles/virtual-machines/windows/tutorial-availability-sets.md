---
title: "Esercitazione sui set di disponibilità per macchine virtuali Windows in Azure | Microsoft Docs"
description: "Informazioni sui set di disponibilità per macchine virtuali Windows in Azure."
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b8577a02f0c9396b64af986950fddaa1e00925ec
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-use-availability-sets"></a>Come usare i set di disponibilità

In questa esercitazione si apprenderà come aumentare la disponibilità e l'affidabilità delle soluzioni delle proprie macchine virtuali in Azure tramite una funzionalità denominata set di disponibilità. I set di disponibilità assicurano che le macchine virtuali distribuite in Azure vengano distribuite tra più nodi hardware isolati in un cluster. Questa operazione assicura che, se si verifica un errore hardware o software all'interno di Azure, solo un subset delle macchine virtuali viene interessato e che nel complesso la soluzione rimane disponibile e operativa. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili
> * Selezionare Azure Advisor

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.3 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="availability-set-overview"></a>Informazioni generali sui set di disponibilità

Un set di disponibilità è una funzionalità di raggruppamento logico che è possibile usare in Azure per garantire che le risorse delle macchine virtuali inserite dall'utente siano isolate tra loro quando vengono distribuite all'interno di un data center di Azure. Azure garantisce che le macchine virtuali inserite all'interno di un set di disponibilità vengano eseguite tra più server fisici, rack di calcolo, unità di archiviazione e commutatori di rete. In caso di guasto hardware o errore software in Azure, viene interessato solo un subset delle macchine virtuali. L'applicazione nel suo complesso rimarrà attiva e disponibile per i clienti. I set di disponibilità sono una funzionalità essenziale da sfruttare quando si vogliono creare soluzioni cloud affidabili.

Si consideri una soluzione tipica basata su macchine virtuali, in cui si dispone di quattro server Web front-end e vengono usate 2 macchine virtuali di back-end che ospitano un database. Con Azure è possibile definire due set di disponibilità prima di distribuire le macchine virtuali: un set di disponibilità per il livello Web e un set di disponibilità per il livello database. Quando si crea una nuova macchina virtuale, è quindi possibile specificare il set di disponibilità come parametro per il comando az vm create. Azure garantisce automaticamente che le macchine virtuali create all'interno del set di disponibilità vengano isolate tramite installazione in più risorse hardware fisiche. Se l'hardware fisico in cui è in esecuzione una delle macchine virtuali dei server di database o dei server Web presenta un problema, le altre istanze delle macchine virtuali dei server Web e di database rimangono in esecuzione, perché si trovano all'interno di risorse hardware diverse.

È consigliabile usare i set di disponibilità quando si vogliono distribuire soluzioni affidabili basate su macchine virtuali in Azure.

## <a name="create-an-availability-set"></a>Creare un set di disponibilità

È possibile creare un set di disponibilità con il comando [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). In questo esempio sia il numero di domini di aggiornamento che quello di domini di errore viene impostato su *2* per il set di disponibilità denominato *myAvailabilitySet* nel gruppo di risorse *myResourceGroupAvailability*.

Creare un gruppo di risorse.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Creare un set di disponibilità gestito usando [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) con il parametro `-sku aligned`.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Creare macchine virtuali in un set di disponibilità
Per garantire la corretta distribuzione delle macchine virtuali in tutto l'hardware, le VM devono essere create all'interno del set di disponibilità. Non è possibile aggiungere una macchina virtuale esistente a un set di disponibilità dopo la sua creazione. 

L'hardware in un percorso è suddiviso in più domini di aggiornamento e domini di errore. I **domini di aggiornamento** sono gruppi di macchine virtuali con relativo hardware fisico sottostante che è possibile riavviare nello stesso momento. Le macchine virtuali nello stesso **dominio di errore** condividono risorse di archiviazione comuni, nonché un alimentatore e un commutatore di rete comune. 

Quando si crea una VM con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm), si usa il parametro `-AvailabilitySetName` per specificare il nome del set di disponibilità.

Impostare prima di tutto nome utente e password dell'amministratore della macchina virtuale con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Creare quindi due macchine virtuali con il comando [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) nel set di disponibilità.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Il parametro `-AsJob` crea la VM come attività in background, in modo che i prompt di PowerShell vengano restituiti all'utente. È possibile visualizzare i dettagli dei processi in background con il cmdlet `Job`. La creazione e la configurazione di entrambe le macchine virtuali richiedono alcuni minuti. Al termine, sono disponibili due macchine virtuali distribuite nell'hardware sottostante. 

Se si esamina il set di disponibilità nel portale, selezionando Gruppi di risorse > myResourceGroupAvailability > myAvailabilitySet, è possibile vedere in che modo sono distribuite le macchine virtuali tra i due domini di errore e di aggiornamento.

![Set di disponibilità nel portale](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Controllare le dimensioni delle macchine virtuali disponibili 

È possibile aggiungere più macchine virtuali al set di disponibilità in un secondo momento, ma è necessario sapere quali dimensioni di macchina virtuale sono disponibili nell'hardware. Usare il comando [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) per elencare tutte le dimensioni disponibili nel cluster hardware per il set di disponibilità.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Selezionare Azure Advisor 

È possibile anche usare Azure Advisor per ottenere altre informazioni su come migliorare la disponibilità delle macchine virtuali. Azure Advisor illustra come seguire le procedure consigliate per ottimizzare le distribuzioni di Azure. Analizza i dati di telemetria dell'uso e della configurazione delle risorse e consiglia soluzioni che consentono di migliorare l'efficienza dei costi, le prestazioni, la disponibilità elevata e la sicurezza delle risorse di Azure.

Accedere al [portale di Azure](https://portal.azure.com), selezionare **Altri servizi** e digitare **Advisor**. Nel dashboard di Advisor vengono visualizzate raccomandazioni personalizzate per la sottoscrizione selezionata. Per altre informazioni vedere [Presentazione di Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili
> * Selezionare Azure Advisor

Passare all'esercitazione successiva per informazioni sui set di scalabilità di macchine virtuali.

> [!div class="nextstepaction"]
> [Creare un set di scalabilità di macchine virtuali](tutorial-create-vmss.md)


