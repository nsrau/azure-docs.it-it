---
title: Monitoraggio di Azure e macchine virtuali Windows | Microsoft Docs
description: 'Esercitazione: monitorare una macchina virtuale Windows con Azure PowerShell'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 2bf6e3cf2f039a9c92617203a3d4cf2aac8901ce
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017

---

# <a name="monitor-a-windows-virtual-machine-with-azure-powershell"></a>Monitorare una macchina virtuale Windows con Azure PowerShell

Monitoraggio di Azure usa gli agenti per raccogliere dati di avvio e sulle prestazioni da macchine virtuali di Azure, archiviare tali dati in Archiviazione di Azure e renderli accessibili tramite il portale, il modulo Azure PowerShell e l'interfaccia della riga di comando di Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host della macchina virtuale
> * Installare l'estensione di diagnostica
> * Visualizzare le metriche della macchina virtuale
> * Creare un avviso
> * Configurare il monitoraggio avanzato

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, questo [script di esempio](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) può crearne una appositamente. Quando si esegue l'esercitazione, sostituire il gruppo di risorse, il nome della macchina virtuale e la posizione dove necessario.

## <a name="view-boot-diagnostics"></a>Visualizzare la diagnostica di avvio

All'avvio delle macchine virtuali Windows, l'agente di diagnostica di avvio acquisisce l'output su schermo utilizzabile per la risoluzione dei problemi. Questa funzionalità è abilitata per impostazione predefinita. Le schermate acquisite vengono archiviate in un account di archiviazione di Azure, anch'esso creato per impostazione predefinita. 

È possibile ottenere i dati di diagnostica di avvio con il comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata). Nell'esempio seguente i dati di diagnostica di avvio vengono scaricati nella radice dell'unità *c:\*. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Visualizzare le metriche host

Una macchina virtuale Windows ha una macchina virtuale host dedicata in Azure con cui interagisce. Le metriche per l'host vengono raccolte automaticamente e possono essere visualizzate nel portale di Azure.

1. Nel portale di Azure fare clic su **Gruppi di risorse**, selezionare **myResourceGroup** e quindi selezionare **myVM** nell'elenco delle risorse.
2. Fare clic su **Metriche** nel pannello della macchina virtuale e quindi selezionare una metrica host in **Metriche disponibili** per visualizzare le prestazioni della macchina virtuale host.

    ![Visualizzare le metriche host](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installare l'estensione di diagnostica

Sono disponibili le metriche di base host, ma per visualizzare metriche specifiche per la macchina virtuale e più granulari è necessario installare l'estensione Diagnostica di Azure nella macchina virtuale. L'estensione Diagnostica di Azure consente un monitoraggio aggiuntivo e il recupero dei dati di diagnostica dalla macchina virtuale. È possibile visualizzare queste metriche delle prestazioni e creare avvisi in base al funzionamento della macchina virtuale. L'estensione Diagnostica viene installata tramite il portale di Azure come indicato di seguito:

1. Nel portale di Azure fare clic su **Gruppi di risorse**, selezionare **myResourceGroup** e quindi selezionare **myVM** nell'elenco delle risorse.
2. Fare clic su **Impostazioni di diagnostica**. L'elenco mostra che *Diagnostica di avvio* è già stata abilitata nella sezione precedente. Selezionare la casella di controllo per *Metriche di base*.
3. Fare clic sul pulsante **Abilita monitoraggio a livello di guest**.

    ![Visualizzare le metriche di diagnostica](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visualizzare le metriche della macchina virtuale

È possibile visualizzare le metriche della macchina virtuale nello stesso modo in cui sono state visualizzate le metriche della macchina virtuale host:

1. Nel portale di Azure fare clic su **Gruppi di risorse**, selezionare **myResourceGroup** e quindi selezionare **myVM** nell'elenco delle risorse.
2. Per visualizzare le prestazioni della macchina virtuale, fare clic su **Metriche** nel pannello della macchina virtuale, quindi selezionare una metrica di diagnostica in **Metriche disponibili**.

    ![Visualizzare le metriche della macchina virtuale](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Creare avvisi

È possibile creare avvisi basati sulle metriche di prestazioni specifiche. Gli avvisi possono essere usati, ad esempio, per inviare una notifica quando l'uso medio della CPU supera una determinata soglia o lo spazio su disco disponibile è inferiore a una determinata quantità. Gli avvisi vengono visualizzati nel portale di Azure o possono essere inviati tramite posta elettronica. In risposta agli avvisi generati, è anche possibile attivare i runbook di Automazione di Azure o App per la logica di Azure.

L'esempio seguente crea un avviso per l'uso medio della CPU.

1. Nel portale di Azure fare clic su **Gruppi di risorse**, selezionare **myResourceGroup** e quindi selezionare **myVM** nell'elenco delle risorse.
2. Nel pannello della macchina virtuale fare clic su **Regole di avviso**, quindi fare clic su **Aggiungi avviso per la metrica** nella parte superiore del pannello degli avvisi.
4. Inserire un **Nome** per l'avviso, ad esempio *myAlertRule*
5. Per attivare un avviso quando la percentuale di CPU supera 1.0 per cinque minuti, lasciare tutte le altre impostazioni predefinite selezionate.
6. Facoltativamente, è possibile selezionare la casella per *Invia messaggio di posta elettronica a proprietari, collaboratori e lettori* per inviare una notifica tramite posta elettronica. L'azione predefinita è di presentare una notifica nel portale.
7. Fare clic sul pulsante **OK**.

## <a name="advanced-monitoring"></a>Monitoraggio avanzato 

È possibile eseguire un monitoraggio più approfondito della macchina virtuale tramite [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Se non è già stato fatto, è possibile iscriversi per avere una [versione di prova gratuita](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) di Operations Management Suite.

Quando si ha accesso al portale di OMS, è possibile trovare la chiave e l'identificatore dell'area di lavoro nel pannello Impostazioni. Usare il comando [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) per aggiungere l'estensione OMS alla macchina virtuale. Aggiornare i valori delle variabili nell'esempio seguente in base alla chiave e all'identificatore dell'area di lavoro di OMS.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Dopo alcuni minuti, la nuova macchina virtuale verrà visualizzata nell'area di lavoro di OMS. 

![Pannello OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione le macchine virtuali sono state configurate ed esaminate con il Centro sicurezza di Azure. Si è appreso come:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un gruppo di risorse e una macchina virtuale 
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host
> * Installare l'estensione di diagnostica
> * Visualizzare le metriche della macchina virtuale
> * Creare un avviso
> * Configurare il monitoraggio avanzato

Passare all'esercitazione successiva per informazioni sul Centro sicurezza di Azure.

> [!div class="nextstepaction"]
> [Gestire la sicurezza delle VM](./tutorial-azure-security.md)
