---
title: Monitoraggio e aggiornamento di Azure e macchine virtuali Windows | Microsoft Docs
description: 'Esercitazione: Monitorare e aggiornare una macchina virtuale Windows con Azure PowerShell'
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
ms.custom: mvc
ms.openlocfilehash: a37aed8b3321d3518ffd73e09f5bb21266a7e577
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-update-a-windows-virtual-machine-with-azure-powershell"></a>Monitorare e aggiornare una macchina virtuale Windows con Azure PowerShell

Monitoraggio di Azure usa gli agenti per raccogliere dati di avvio e sulle prestazioni da macchine virtuali di Azure, archiviare tali dati in Archiviazione di Azure e renderli accessibili tramite il portale, il modulo Azure PowerShell e l'interfaccia della riga di comando di Azure. La gestione degli aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host della macchina virtuale
> * Installare l'estensione di diagnostica
> * Visualizzare le metriche della macchina virtuale
> * Creare un avviso
> * Gestire gli aggiornamenti di Windows
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

## <a name="manage-windows-updates"></a>Gestire gli aggiornamenti di Windows

La gestione degli aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure.
Direttamente dalla macchina virtuale è possibile valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente alla macchina virtuale.

Per informazioni sui prezzi, vedere [Prezzi di Automazione per la gestione degli aggiornamenti](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Abilitare Gestione aggiornamenti

Abilitare Gestione aggiornamenti per la macchina virtuale:
 
1. Sul lato sinistro della schermata selezionare **Macchine virtuali**.
2. Selezionare una macchina virtuale dall'elenco.
3. Nella schermata della macchina virtuale nella sezione **Operazioni** fare clic su **Gestione aggiornamenti**. Viene visualizzata la schermata **Abilita Gestione aggiornamenti**.

Viene eseguita una convalida per determinare se Gestione aggiornamenti è abilitata per la macchina virtuale. La convalida include controlli per un'area di lavoro di Log Analytics e un account di Automazione collegato e verifica se la soluzione è presente nell'area di lavoro.

L'area di lavoro di Log Analytics consente di raccogliere i dati generati da funzionalità e servizi, ad esempio Gestione aggiornamenti. L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini. Per eseguire altre operazioni nelle macchine virtuali che richiedono gli aggiornamenti, Automazione di Azure consente di eseguire script nelle macchine virtuali, ad esempio per scaricare e applicare gli aggiornamenti.

Il processo di convalida controlla anche se nella macchina virtuale è presente Microsoft Monitoring Agent (MMA) e un ruolo di lavoro ibrido. L'agente consente di comunicare con la macchina virtuale e ottenere informazioni sullo stato dell'aggiornamento. 

Se non vengono soddisfatti questi prerequisiti, viene visualizzato un banner che offre l'opzione per abilitare la soluzione.

![Banner di configurazione di caricamento di Gestione aggiornamenti](./media/tutorial-monitoring/manageupdates-onboard-solution-banner.png)

Fare clic sul banner per abilitare la soluzione. Se risultano mancanti dopo la convalida, i prerequisiti seguenti vengono aggiunti automaticamente:

* Area di lavoro di [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automazione](../../automation/automation-offering-get-started.md)
* [Ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md) abilitato nella macchina virtuale

Viene visualizzata la schermata **Abilita Gestione aggiornamenti**. Configurare le impostazioni e fare clic su **Abilita**.

![Abilitare la soluzione Gestione aggiornamenti](./media/tutorial-monitoring/manageupdates-update-enable.png)

L'abilitazione della soluzione può richiedere fino a 15 minuti. Non chiudere la finestra del browser in questo periodo di tempo. Dopo l'abilitazione della soluzione, le informazioni sugli aggiornamenti mancanti nella macchina virtuale passano a Log Analytics.
Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

### <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Dopo aver abilitato **Gestione aggiornamenti**, viene visualizzata la schermata **Gestione aggiornamenti**. È possibile visualizzare un elenco degli aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

 ![Visualizzare lo stato degli aggiornamenti](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio.
È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Pianificare una nuova distribuzione di aggiornamenti per la macchina virtuale facendo clic su **Pianifica la distribuzione di aggiornamenti** nella parte superiore della schermata **Gestione aggiornamenti**. Nella schermata **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

* **Nome**: specificare un nome univoco per identificare la distribuzione di aggiornamenti.
* **Classificazioni aggiornamenti**: selezionare i tipi di software inclusi nella distribuzione di aggiornamenti. I tipi di classificazione sono:
  * Aggiornamenti critici
  * Aggiornamenti della sicurezza
  * Aggiornamenti cumulativi
  * Feature Pack
  * Service Pack
  * Aggiornamenti della definizione
  * Strumenti
  * Aggiornamenti

* **Impostazioni di pianificazione**: è possibile accettare la data e l'ora predefinite, ovvero 30 minuti dopo l'ora corrente o specificare un'ora diversa.
  È anche possibile specificare se eseguire la distribuzione una sola volta o impostare una pianificazione ricorrente. Per impostare una pianificazione ricorrente, fare clic sull'opzione Ricorrente in Ricorrenza.

  ![Schermata di impostazioni della pianificazione di aggiornamenti](./media/tutorial-monitoring/manageupdates-schedule-win.png)

* **Finestra di manutenzione (minuti)**: specificare il periodo di tempo nel quale eseguire la distribuzione di aggiornamenti.  In questo modo è possibile garantire che le modifiche vengano eseguite negli intervalli di servizio definiti.

Dopo aver configurato la pianificazione, fare clic sul pulsante **Crea**. Viene nuovamente visualizzato il dashboard di stato.
Si noti che la tabella **Pianificata** mostra la pianificazione della distribuzione creata.

> [!WARNING]
> Per gli aggiornamenti che richiedono un riavvio, la macchina virtuale viene riavviata automaticamente.

### <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** nella schermata **Gestione aggiornamenti**.
Se la distribuzione è in corso, viene visualizzato lo stato **In corso**. Quando la distribuzione viene completata correttamente, lo stato diventa **Completato**.
Se si verifica un errore in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.
Fare clic sulla distribuzione di aggiornamenti completata per visualizzare il dashboard della distribuzione.

   ![Dashboard di stato di una distribuzione di aggiornamenti specifica](./media/tutorial-monitoring/manageupdates-view-results.png)

Il riquadro **Risultati aggiornamento** include un riepilogo del numero totale di aggiornamenti e dei risultati della distribuzione nella macchina virtuale.
La tabella di destra visualizza una descrizione dettagliata di ogni aggiornamento e i risultati dell'installazione che possono corrispondere a uno dei valori seguenti:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato a causa di tempo disponibile non sufficiente basato sulla durata della finestra di manutenzione specificata.
* **Completato**: l'aggiornamento è stato completato
* **Non riuscito**: l'aggiornamento non è riuscito

Fare clic su **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Fare clic sul riquadro **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Fare clic su **Errori** per visualizzare informazioni dettagliate sugli errori della distribuzione.

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
> * Gestire gli aggiornamenti di Windows
> * Configurare il monitoraggio avanzato

Passare all'esercitazione successiva per informazioni sul Centro sicurezza di Azure.

> [!div class="nextstepaction"]
> [Gestire la sicurezza delle VM](./tutorial-azure-security.md)