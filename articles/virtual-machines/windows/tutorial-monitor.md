---
title: 'Esercitazione: Monitorare le macchine virtuali Windows in Azure'
description: Questa esercitazione illustra come monitorare le prestazioni e i componenti delle applicazioni individuati in esecuzione nelle macchine virtuali Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 9e807927a10399b02c2c89359c2ffffaf87ba57b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112494"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Esercitazione: Monitorare una macchina virtuale in Azure

Monitoraggio di Azure usa gli agenti per raccogliere dati di avvio e sulle prestazioni dalle macchine virtuali di Azure, archiviare tali dati in Archiviazione di Azure e renderli accessibili tramite il portale, il modulo Azure PowerShell e l'interfaccia della riga di comando di Azure. Monitoraggio di Azure per le macchine virtuali include anche il monitoraggio avanzato, con la raccolta delle metriche delle prestazioni e l'individuazione dei componenti delle applicazioni installati nella macchina virtuale e comprende i grafici delle prestazioni e la mappa delle dipendenze.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host della macchina virtuale
> * Abilita Monitoraggio di Azure per le macchine virtuali
> * Visualizzare le metriche delle prestazioni della macchina virtuale
> * Creare un avviso

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Per configurare il monitoraggio di Azure e la gestione degli aggiornamenti in questa esercitazione, è necessario disporre di una macchina virtuale Windows in Azure. Impostare prima di tutto nome utente e password dell'amministratore della macchina virtuale con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Creare ora la VM con [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). L'esempio seguente crea una macchina virtuale denominata *myVM* nell'area *EastUS*. Se non esistono già, vengono creati il gruppo di risorse *myResourceGroupMonitorMonitor* e le rispettive risorse di rete di supporto:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Per creare le risorse e la macchina virtuale sono necessari alcuni minuti.

## <a name="view-boot-diagnostics"></a>Visualizzare la diagnostica di avvio

All'avvio delle macchine virtuali Windows, l'agente di diagnostica di avvio acquisisce l'output su schermo utilizzabile per la risoluzione dei problemi. Questa funzionalità è abilitata per impostazione predefinita. Gli screenshot acquisiti vengono archiviati in un account di archiviazione di Azure, anch'esso creato per impostazione predefinita.

È possibile ottenere i dati di diagnostica di avvio con il comando [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata). Nell'esempio seguente i dati di diagnostica di avvio vengono scaricati nella radice dell'unità *c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Visualizzare le metriche host

Una macchina virtuale Windows ha una macchina virtuale host dedicata in Azure con cui interagisce. Le metriche per l'host vengono raccolte automaticamente e possono essere visualizzate nel portale di Azure.

1. Nel portale di Azure, fare clic su **Gruppi di risorse** selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.
2. Fare clic su **Metriche** nel pannello della macchina virtuale e quindi selezionare una metrica host in **Metriche disponibili** per visualizzare le prestazioni della macchina virtuale host.

    ![Visualizzare le metriche host](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Abilitare il monitoraggio avanzato

Per abilitare il monitoraggio della macchina virtuale di Azure con Monitoraggio di Azure per le macchine virtuali:

1. Nel portale di Azure, fare clic su **Gruppi di risorse** selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.

2. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)** .

3. Nella pagina **Insights (anteprima)** selezionare **Prova adesso**.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  

    Nell'elenco sono preselezionati l'area di lavoro predefinita e la località in cui è distribuita la macchina virtuale nella sottoscrizione. 

    >[!NOTE]
    >Per creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio della macchina virtuale, vedere [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). L'area di lavoro Log Analytics deve risiedere in una delle [aree supportate](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics).

Dopo aver abilitato il monitoraggio, potrebbe essere necessario attendere alcuni minuti prima di poter visualizzare le metriche delle prestazioni per la macchina virtuale.

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Visualizzare le metriche delle prestazioni della macchina virtuale

Monitoraggio di Azure per le macchine virtuali include un set di grafici delle prestazioni che rappresentano diversi indicatori di prestazioni chiave (KPI) per stabilire l'efficacia delle prestazioni di una macchina virtuale. Per accedere alla macchina virtuale, seguire questa procedura.

1. Nel portale di Azure, fare clic su **Gruppi di risorse** selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.

2. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)** .

3. Selezionare la scheda **Prestazioni**.

Questa pagina non solo include i grafici di utilizzo delle prestazioni, ma anche una tabella che mostra capacità, utilizzo e media totale di ogni disco logico individuato per ogni misura.

## <a name="create-alerts"></a>Creare avvisi

È possibile creare avvisi basati sulle metriche di prestazioni specifiche. Gli avvisi possono essere usati, ad esempio, per inviare una notifica quando l'uso medio della CPU supera una determinata soglia o lo spazio su disco disponibile è inferiore a una determinata quantità. Gli avvisi vengono visualizzati nel portale di Azure o possono essere inviati tramite posta elettronica. In risposta agli avvisi generati, è anche possibile attivare i runbook di Automazione di Azure o App per la logica di Azure.

L'esempio seguente crea un avviso per l'uso medio della CPU.

1. Nel portale di Azure, fare clic su **Gruppi di risorse** selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.

2. Nel pannello della macchina virtuale fare clic su **Regole di avviso**, quindi fare clic su **Aggiungi avviso per la metrica** nella parte superiore del pannello degli avvisi.

3. Inserire un **Nome** per l'avviso, ad esempio *myAlertRule*

4. Per attivare un avviso quando la percentuale di CPU supera 1.0 per cinque minuti, lasciare tutte le altre impostazioni predefinite selezionate.

5. Facoltativamente, è possibile selezionare la casella per *Invia messaggio di posta elettronica a proprietari, collaboratori e lettori* per inviare una notifica tramite posta elettronica. L'azione predefinita è di presentare una notifica nel portale.

6. Fare clic sul pulsante **OK**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state configurate e visualizzate le prestazioni della macchina virtuale. Si è appreso come:

> [!div class="checklist"]
> * Creare un gruppo di risorse e una macchina virtuale
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host
> * Abilita Monitoraggio di Azure per le macchine virtuali
> * Visualizzare le metriche della macchina virtuale
> * Creare un avviso

Passare all'esercitazione successiva per informazioni sul Centro sicurezza di Azure.

> [!div class="nextstepaction"]
> [Gestire la sicurezza delle VM](../../security/fundamentals/overview.md)
