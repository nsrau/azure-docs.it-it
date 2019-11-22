---
title: 'Esercitazione: Gestire la configurazione delle macchine virtuali Windows in Azure'
description: Questa esercitazione illustra come identificare le modifiche e gestire gli aggiornamenti dei pacchetti in una macchina virtuale Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d97323f1916ee46e6b1f8d4ca8723b950baca39c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74064807"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Esercitazione: Monitorare le modifiche e aggiornare una macchina virtuale Windows in Azure

Con [Rilevamento modifiche](../../automation/change-tracking.md) e [Gestione aggiornamenti](../../automation/automation-update-management.md) di Azure, è possibile identificare facilmente le modifiche apportate alle macchine virtuali Windows in Azure e gestire gli aggiornamenti del sistema operativo per tali VM.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Gestire gli aggiornamenti di Windows.
> * Monitorare le modifiche e l'inventario.

## <a name="open-azure-cloud-shell"></a>Aprire Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account Azure.

In Cloud Shell selezionare **Prova** nell'angolo in alto a destra di qualsiasi blocco di codice per aprirlo.

È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli nella scheda Cloud Shell, quindi premere INVIO per eseguire il codice.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Per configurare il monitoraggio di Azure e la gestione degli aggiornamenti in questa esercitazione, è necessario disporre di una macchina virtuale Windows in Azure.

Impostare prima di tutto nome utente e password dell'amministratore della macchina virtuale con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Successivamente, creare la VM con [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). L'esempio seguente crea una VM denominata `myVM` nella località `East US`. Se non esistono già, vengono creati il gruppo di risorse `myResourceGroupMonitor` e le risorse di rete di supporto:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Per creare le risorse e la macchina virtuale sono necessari alcuni minuti.

## <a name="manage-windows-updates"></a>Gestire gli aggiornamenti di Windows

Gestione aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure. Direttamente dalla macchina virtuale, è possibile:

- Valutare lo stato degli aggiornamenti disponibili.
- Pianificare l'installazione degli aggiornamenti necessari.
- Esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente alla macchina virtuale.

Per informazioni sui prezzi, vedere [Prezzi di Automazione per Gestione aggiornamenti](https://azure.microsoft.com/pricing/details/automation/).

### <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

Per abilitare Gestione aggiornamenti per la VM:

1. All'estrema sinistra della finestra selezionare **Macchine virtuali**.
1. Scegliere una VM nell'elenco.
1. Nel riquadro **Operazioni** della finestra della VM selezionare **Gestione aggiornamenti**.
1. Viene visualizzata la finestra **Abilita Gestione aggiornamenti**.

Viene eseguita una convalida per determinare se la soluzione Gestione aggiornamenti è abilitata per la VM. La convalida include controlli per un'area di lavoro Log Analytics e per un account di Automazione collegato, oltre a verificare se la soluzione è presente nell'area di lavoro.

L'area di lavoro [Log Analytics](../../log-analytics/log-analytics-overview.md) consente di raccogliere i dati generati da funzionalità e servizi, ad esempio Gestione aggiornamenti. L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.

Per eseguire altre azioni nelle macchine virtuali che richiedono aggiornamenti, è possibile usare Automazione di Azure per eseguire runbook sulle VM. Tali azioni includono il download o l'applicazione di aggiornamenti.

Il processo di convalida controlla anche se nella macchina virtuale è presente Microsoft Monitoring Agent (MMA) e un ruolo di lavoro ibrido per runbook di Automazione. L'agente consente di comunicare con la macchina virtuale e ottenere informazioni sullo stato di aggiornamento.

Nella finestra **Abilita Gestione aggiornamenti** scegliere l'area di lavoro Log Analytics e l'account di automazione, quindi selezionare **Abilita**. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

Se risultano mancanti durante l'onboarding, i prerequisiti seguenti vengono aggiunti automaticamente:

* Area di lavoro di [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Automazione](../../automation/automation-offering-get-started.md)
* Un [ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md), abilitato nella macchina virtuale

Una volta abilitata la soluzione, viene visualizzata la finestra **Gestione aggiornamenti**. Configurare la posizione, l'area di lavoro Log Analytics e l'account di Automazione da usare, quindi selezionare **Abilita**. Se queste opzioni sono inattive, significa che per la VM è abilitata un'altra soluzione di automazione ed è necessario usare l'area di lavoro e l'account di automazione di tale soluzione.

![Abilitare la soluzione Gestione aggiornamenti](./media/tutorial-monitoring/manageupdates-update-enable.png)

L'abilitazione della soluzione Gestione aggiornamenti può richiedere fino a 15 minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser. Dopo l'abilitazione della soluzione, le informazioni sugli aggiornamenti mancanti nella macchina virtuale passano ai log di Monitoraggio di Azure. Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

### <a name="view-an-update-assessment"></a>Visualizzare una valutazione degli aggiornamenti

Dopo aver abilitato Gestione aggiornamenti, viene visualizzata la finestra **Gestione aggiornamenti**. Al termine della valutazione degli aggiornamenti, nella scheda **Aggiornamenti mancanti** viene visualizzato un elenco di aggiornamenti mancanti.

 ![Visualizzare lo stato degli aggiornamenti](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio. Scegliere quali tipi di aggiornamento includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Per pianificare una nuova distribuzione di aggiornamenti per la macchina virtuale, selezionare **Pianifica la distribuzione di aggiornamenti** nella parte superiore della finestra **Gestione aggiornamenti**. Nella finestra **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

| Opzione | DESCRIZIONE |
| --- | --- |
| **Nome** |immettere un nome univoco per identificare la distribuzione di aggiornamenti. |
|**Sistema operativo**| Selezionare **Linux** o **Windows**.|
| **Gruppi da aggiornare** |Per le macchine virtuali ospitate in Azure, definire una query basata su una combinazione di sottoscrizione, gruppi di risorse, posizioni e tag. Questa query crea un gruppo dinamico di macchine virtuali ospitate in Azure da includere nella distribuzione. </br></br>Per le macchine virtuali non ospitate in Azure, selezionare una ricerca salvata esistente. Con questa ricerca, è possibile selezionare un gruppo di queste VM da includere nella distribuzione. </br></br> Per altre informazioni, vedere [Gruppi dinamici](../../automation/automation-update-management-groups.md).|
| **Computer da aggiornare** |Selezionare **Ricerca salvata**, **Gruppi importati**o **Computer**.<br/><br/>Se si seleziona **Computer**, è possibile scegliere singoli computer nell'elenco a discesa. L'idoneità di ogni computer è indicata nella colonna **AGGIORNA IDONEITÀ AGENTE** della tabella.</br></br> Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../../azure-monitor/platform/computer-groups.md) |
|**Classificazioni aggiornamenti**|Scegliere tutte le classificazioni degli aggiornamenti necessarie.|
|**Includi/Escludi aggiornamenti**|Selezionare questa opzione per aprire il riquadro **Includi/Escludi**. Gli aggiornamenti da includere e quelli da escludere si trovano in schede separate. Per altre informazioni su come viene gestita l'inclusione, vedere [Pianificare la distribuzione degli aggiornamenti](../../automation/automation-tutorial-update-management.md#schedule-an-update-deployment). |
|**Impostazioni pianificazione**|Selezionare l'ora di inizio, quindi selezionare **Una sola volta** o **Ricorrente**.|
| **Pre-script e post-script**|Scegliere gli script da eseguire prima e dopo la distribuzione.|
| **Finestra di manutenzione** | Immettere il numero di minuti impostato per gli aggiornamenti. I valori validi sono compresi tra 30 e 360 minuti. |
| **Controllo riavvio**| Selezionare come vengono gestiti i riavvii. Le opzioni disponibili sono:<ul><li>**Riavvia se necessario**</li><li>**Riavvia sempre**</li><li>**Non riavviare mai**</li><li>**Solo riavvio**</li></ul>**Riavvia se necessario** è l'opzione predefinita. Se si seleziona **Solo riavvio**, gli aggiornamenti non vengono installati.|

Dopo avere configurato la pianificazione, fare clic su **Crea** per tornare nel dashboard di stato. La tabella **Pianificata** mostra la pianificazione della distribuzione appena creata.

È anche possibile creare distribuzioni di aggiornamenti a livello di codice. Per informazioni su come creare una distribuzione di aggiornamenti con l'API REST, vedere [Configurazione degli aggiornamenti software - Creazione](/rest/api/automation/softwareupdateconfigurations/create). È anche disponibile un runbook di esempio che può essere usato per creare una distribuzione di aggiornamenti settimanale. Per altre informazioni su questo runbook, vedere [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (Creare una distribuzione di aggiornamenti settimanale per una o più macchine virtuali in un gruppo di risorse).

### <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo l'avvio della distribuzione pianificata, è possibile visualizzare il relativo stato nella scheda **Distribuzioni di aggiornamenti** della finestra **Gestione aggiornamenti**.

Se la distribuzione è attualmente in esecuzione, lo stato sarà "In corso". Al termine dell'operazione, lo stato diventa "Operazione riuscita". Se la distribuzione di uno o più aggiornamenti non riesce, lo stato sarà invece "Operazione riuscita parzialmente".

Selezionare la distribuzione di aggiornamenti completata per visualizzare il dashboard corrispondente.

![Dashboard di stato di una distribuzione di aggiornamenti specifica](./media/tutorial-monitoring/manageupdates-view-results.png)

Il riquadro **Risultati aggiornamento** include un riepilogo del numero totale di aggiornamenti e dei risultati della distribuzione nella macchina virtuale. La tabella a destra offre una suddivisione dettagliata di ogni aggiornamento e dei risultati dell'installazione. Ogni risultato ha uno dei valori seguenti:

* **Tentativo non eseguito**: l'aggiornamento non viene installato. Il tempo disponibile in base alla durata della finestra di manutenzione definita non è sufficiente.
* **Completato**: aggiornamento completato.
* **Operazione non riuscita**: aggiornamento non riuscito.

Selezionare **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Selezionare il riquadro **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Selezionare **Errori** per visualizzare informazioni dettagliate sugli errori della distribuzione.

## <a name="monitor-changes-and-inventory"></a>Monitorare le modifiche e l'inventario

È possibile raccogliere e visualizzare un inventario di software, file, daemon Linux, servizi Windows e chiavi del Registro di sistema di Windows presenti nei computer. Tenendo traccia delle configurazioni dei computer, è possibile individuare i problemi operativi dell'intero ambiente e conoscere meglio lo stato dei computer.

### <a name="enable-change-and-inventory-management"></a>Abilitare la gestione delle modifiche e dell'inventario

Per abilitare la gestione delle modifiche e dell'inventario per la macchina virtuale:

1. All'estrema sinistra della finestra selezionare **Macchine virtuali**.
1. Scegliere una VM nell'elenco.
1. In **Operazioni** nella finestra della VM selezionare **Inventario** o **Rilevamento modifiche**.
1. Viene visualizzato il riquadro **Abilita Rilevamento modifiche e inventario**.

Configurare la posizione, l'area di lavoro Log Analytics e l'account di Automazione da usare, quindi selezionare **Abilita**. Se le opzioni sono inattive, significa che per la VM è già abilitata una soluzione di automazione. In tal caso, è necessario usare l'area di lavoro e l'account di Automazione già abilitati.

Sebbene le soluzioni siano presentate separatamente nel menu, rappresentano la stessa soluzione. L'abilitazione di una di queste le abilita entrambe per la macchina virtuale.

![Abilitare il rilevamento delle modifiche e dell'inventario](./media/tutorial-monitoring/manage-inventory-enable.png)

Dopo l'abilitazione della soluzione, la raccolta dell'inventario sulla macchina virtuale può richiedere alcuni minuti prima che i dati vengono visualizzati.

### <a name="track-changes"></a>Rilevare le modifiche

Nella macchina virtuale in **OPERAZIONI**selezionare **Rilevamento modifiche**, quindi **Modifica impostazioni**. Viene visualizzato il riquadro **Rilevamento modifiche**. Selezionare il tipo di impostazione che si vuole rilevare e quindi fare clic su **+ Aggiungi** per configurare le impostazioni.

Le opzioni delle impostazioni disponibili per Windows sono:

* Registro di sistema di Windows
* File Windows

Per informazioni dettagliate su Rilevamento modifiche, vedere [Risolvere i problemi delle modifiche in una macchina virtuale](../../automation/automation-tutorial-troubleshoot-changes.md).

### <a name="view-inventory"></a>Visualizzare l'inventario

Nella macchina virtuale selezionare **Inventario** in **OPERAZIONI**. Nella scheda **Software** è presente una tabella con l'elenco del software trovato. I dettagli generali di ogni record software sono visibili nella tabella. Questi dettagli includono il nome del software, la versione, l'editore e l'ora dell'ultimo aggiornamento.

![Visualizzare l'inventario](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorare i log attività e le modifiche

Nella pagina **Rilevamento modifiche** della VM selezionare **Gestisci connessione al log attività** per aprire il riquadro **Log attività di Azure**. Selezionare **Connetti** per connettere Rilevamento modifiche al log attività di Azure per la VM.

Dopo aver abilitato Rilevamento modifiche, passare al riquadro **Panoramica** della VM e selezionare **Arresta** per arrestare la macchina virtuale. Quando richiesto, selezionare **Sì** per arrestare la VM. Una volta deallocata la VM, selezionare **Avvia** per riavviarla.

Con l'arresto e il riavvio di una macchina virtuale viene registrato un evento nel log attività. Tornare nel riquadro **Rilevamento modifiche** e selezionare la scheda **Eventi** nella parte inferiore. Dopo poco, gli eventi verranno visualizzati nel grafico e nella tabella. È possibile selezionare ogni evento per visualizzare le relative informazioni dettagliate.

![Visualizzare le modifiche nel log attività](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Il grafico precedente mostra le modifiche che si sono verificate nel corso nel tempo. Dopo aver aggiunto una connessione al log attività di Azure, il grafico a linee nella parte superiore visualizza gli eventi del log attività di Azure.

Ogni riga dei grafici a barre rappresenta un tipo diverso di modifica rilevabile. I tipi sono daemon Linux, file, chiavi del Registro di sistema di Windows, software e servizi di Windows. La scheda **Modifica** visualizza i dettagli delle modifiche. Le modifiche vengono visualizzate nell'ordine in cui si sono verificate, con quella più recente mostrata per prima.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state configurate e verificate le funzionalità Rilevamento modifiche e Gestione aggiornamenti per la macchina virtuale. Si è appreso come:

> [!div class="checklist"]
> * Creare un gruppo di risorse e una macchina virtuale.
> * Gestire gli aggiornamenti di Windows.
> * Monitorare le modifiche e l'inventario.

Passare all'esercitazione successiva per informazioni sul monitoraggio delle macchine virtuali.

> [!div class="nextstepaction"]
> [Monitorare le macchine virtuali](tutorial-monitor.md)
