---
title: Esecuzione di runbook in Automazione di Azure
description: Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: eb6516400d362fe60adc05590353ec003c70e059
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2018
ms.locfileid: "42140922"
---
# <a name="runbook-execution-in-azure-automation"></a>Esecuzione di runbook in Automazione di Azure

Quando si avvia un runbook in Automazione di Azure, viene creato un processo. Un processo è una singola istanza di esecuzione di un runbook. Per eseguire ogni processo, viene assegnato un computer di lavoro di Automazione di Azure. I computer di lavoro sono condivisi da più account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non si dispone di alcun controllo sul computer di lavoro che gestisce la richiesta per il processo. In un singolo runbook possono venire eseguiti più processi contemporaneamente. È possibile riusare l'ambiente di esecuzione per i processi dallo stesso account di automazione. Quando si visualizza l'elenco dei runbook nel portale di Azure, è visibile lo stato di tutti i processi avviati per ogni runbook. È possibile visualizzare l'elenco dei processi per il singolo runbook per tenere traccia dello stato di ognuno. Per una descrizione dei diversi stati dei processi, vedere [Stati dei processi](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Nel diagramma seguente viene illustrato il ciclo di vita di un processo del Runbook per [Runbook grafici](automation-runbook-types.md#graphical-runbooks) e [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

Nel diagramma seguente viene illustrato il ciclo di vita di un processo runbook per [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks).

![Stati del processo - Script PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

I processi accedono alle risorse di Azure eseguendo una connessione alla sottoscrizione di Azure. Possono accedere solo alle risorse del data center dell'utente se tali risorse sono accessibili dal cloud pubblico.

## <a name="job-statuses"></a>Stati dei processi

La tabella seguente descrive i diversi stati possibili per un processo.

| Status | Descrizione |
|:--- |:--- |
| Completed |Il processo è stato completato. |
| Failed |Per [Runbook grafico e runbook flusso di lavoro PowerShell](automation-runbook-types.md), la compilazione di runbook non è riuscita. Per [Runbook di Script di PowerShell](automation-runbook-types.md), non è stato possibile avviare il runbook o il processo ha rilevato un'eccezione. |
| Failed, waiting for resources |Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](#fair-share) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook. |
| Queued |Il processo è in attesa che diventino disponibili risorse in un computer di lavoro di Automazione per poter essere avviato. |
| Avvio in corso |Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando. |
| Resuming |Il sistema sta per riprendere il processo dopo che è stato sospeso. |
| Running |Il processo è in esecuzione. |
| Running, waiting for resources |Il processo è stato scaricato perché ha raggiunto il limite di [condivisione equa](#fair-share) . Riprende a breve dall'ultimo checkpoint. |
| Arrestato |Il processo è stato arrestato dall'utente prima del completamento. |
| Stopping |Il sistema sta arrestando il processo. |
| Suspended |Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Un processo sospeso può essere riavviato e viene ripreso dall'ultimo checkpoint o dall'inizio del runbook se non è in possesso di checkpoint. Il runbook viene sospeso dal sistema quando ha luogo un'eccezione. Per impostazione predefinita, il valore di ErrorActionPreference è impostato su **Continua**, a indicare che il processo continua a essere eseguito in caso di errore. Se questa variabile di preferenza è impostata su **Stop**, il processo viene sospeso in caso di errore. Si applica solo a [Runbook grafico e al flusso di lavoro PowerShell](automation-runbook-types.md) . |
| Suspending |Il sistema sta tentando di sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, il processo viene completato prima di poter essere sospeso. Si applica solo a [Runbook grafico e al flusso di lavoro PowerShell](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Visualizzazione dello stato del processo dal portale di Azure

È possibile visualizzare lo stato riassuntivo di tutti i processi del runbook oppure esaminare i dettagli di uno specifico processo del runbook nel portale di Azure o configurando l'integrazione con l'area di lavoro di Log Analytics per inoltrare i flussi e lo stato del processo del runbook. Per altre informazioni sull'integrazione con Log Analytics, vedere [Inoltrare lo stato e i flussi del processo da Automazione a Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Riepilogo dei processi del Runbook di Automazione

A destra dell'account di Automazione selezionato, è possibile visualizzare tutti i processi del Runbook relativi a un account di Automazione selezionato sotto al riquadro **Statistiche processi**.

![Riquadro Statistiche del processo](./media/automation-runbook-execution/automation-account-job-status-summary.png).

Questo riquadro visualizza un conteggio e una rappresentazione grafica dello stato dei processi per tutti i processi eseguiti.

Facendo clic sul riquadro appare il pannello **Processi**, che include un elenco riepilogativo di tutti i processi eseguiti, indicando lo stato e l'ora di avvio e di completamento del processo.

![Pannello Processi dell'account di Automazione](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

È possibile filtrare l'elenco dei processi selezionando **Filtra processi** e filtrare in uno specifico runbook, lo stato del processo, o dall'elenco a discesa, l'intervallo di data/ora entro cui eseguire la ricerca.

![Filtrare i processi in base allo stato](./media/automation-runbook-execution/automation-account-jobs-filter.png)

In alternativa, è possibile visualizzare i dettagli di riepilogo dei processi per un runbook specifico selezionando tale runbook dal pannello **Runbook** nel proprio account di Automazione e, successivamente, selezionare il riquadro **Processi**. A questo punto viene visualizzato il pannello **Processi**, dove è possibile fare clic sul record del processo per visualizzarne i dettagli e l'output.

![Pannello Processi dell'account di Automazione](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Riepilogo dei processi

È possibile visualizzare un elenco di tutti i processi creati per un determinato runbook e il relativo stato più recente. È possibile filtrare l'elenco in base allo stato del processo e all'intervallo di date dell'ultima modifica del processo. Fare clic sul nome di un processo per visualizzarne le informazioni dettagliate e l'output. La visualizzazione dettagliata del processo include i valori per i parametri del runbook che sono stati forniti al processo.

Per visualizzare i processi per un runbook, seguire questa procedura.

1. Nel portale di Azure, selezionare **Automazione** e selezionare il nome di un account di automazione.
2. Dall'hub, selezionare **Runbook** e quindi sul pannello **Runbook** selezionare un runbook dall'elenco.
3. Nel pannello per il runbook selezionato, fare clic sul riquadro **Processi**.
4. Fare clic su uno dei processi nell'elenco e nel pannello dei dettagli del processo di runbook è possibile visualizzarne i dettagli e l'output.

## <a name="retrieving-job-status-using-windows-powershell"></a>Recupero dello stato di un processo tramite Windows PowerShell

È possibile usare [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) per recuperare i processi creati per un runbook e i dettagli di un processo specifico. Se si avvia un runbook con Windows PowerShell tramite [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), viene restituito il processo risultante. Usare [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) per ottenere l'output di un processo.

I comandi di esempio seguenti recuperano l'ultimo processo per un runbook di esempio e ne visualizzano lo stato, i valori specificati per i parametri del runbook e l'output del processo.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

L'esempio seguente recupera l'output di un processo specifico e restituisce ogni record. Se si è verificata un'eccezione per uno dei record, invece del valore viene scritta l'eccezione. Ciò è utile poiché le eccezioni possono fornire informazioni aggiuntive che potrebbero non essere registrate normalmente durante l'output.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Ottenere dettagli dal log attività

Altri dettagli, ad esempio la persona o l'account che ha avviato il runbook, possono essere recuperati dal log attività relativo all'account di Automazione. L'esempio di PowerShell seguente consente di ottenere i dati relativi all'ultimo utente che ha eseguito il runbook in questione:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>condivisione equa

Per condividere le risorse tra tutti i runbook nel cloud, Automazione di Azure scaricherà temporaneamente qualsiasi processo in esecuzione da tre ore. Durante questo periodo, i processi per [runbook basati su PowerShell](automation-runbook-types.md#powershell-runbooks) vengono arrestati e non devono essere riavviati. Lo stato processo visualizza **Arrestato**. Questo tipo di runbook viene sempre riavviato dall'inizio perché non supporta i checkpoint.

I [runbook basati su Flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vengono ripresi a partire dall'ultimo [checkpoint](https://docs.microsoft.com/system-center/sma/overview-powershell-workflows#bk_Checkpoints). Dopo essere in esecuzione per tre ore, il processo del runbook verrà sospeso dal servizio e il relativo stato visualizza **In esecuzione, in attesa delle risorse**. Quando un sandbox diventa disponibile, il runbook viene riavviato automaticamente dal servizio di Automazione di Azure e l'esecuzione viene ripresa a partire dall'ultimo checkpoint. Questo è il comportamento normale di Workflow-PowerShell per sospendere/riavviare. Se il runbook supera nuovamente tre ore di runtime, il processo viene ripetuto fino a tre volte. Dopo il terzo riavvio, se il runbook ancora non è stato completato in tre ore, allora il processo di runbook non ha esito positivo, e lo stato del processo visualizza **Non riuscito, in attesa di risorse**. In tal caso, l'operazione avrà esito negativo e si riceve un'eccezione che indica quanto segue.

*L'esecuzione del processo non può continuare perché il processo è stato rimosso ripetutamente dallo stesso checkpoint. Verificare che il runbook non esegua operazioni di lunga durata senza rendere persistente il proprio stato.*

Questo avviene per impedire che nel servizio i runbook vengano eseguiti all'infinito senza essere completati, in quanto non riusciranno a giungere al checkpoint successivo senza essere scaricati di nuovo.

Se il runbook non dispone di alcun checkpoint o non ha raggiunto il primo checkpoint prima di essere scaricato, viene riavviato dall'inizio.

Per le attività che richiedono una lunga esecuzione, è consigliabile un [ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md#job-behavior). I ruoli di lavoro ibrido per runbook non sono limitati da condivisione equa e non hanno una limitazione rispetto alla possibile durata dell'esecuzione di un runbook.

Se si utilizza un runbook del flusso di lavoro di PowerShell in Azure, al momento della sua creazione è consigliabile assicurarsi che il tempo necessario per eseguire qualsiasi attività tra due checkpoint non superi le tre ore. Può essere necessario aggiungere checkpoint al runbook per assicurarsi che non raggiunga tale limite o suddividere le operazioni che richiedono una lunga esecuzione. Ad esempio, il runbook potrebbe eseguire una reindicizzazione su un database SQL di grandi dimensioni. Se questa singola operazione non viene completata entro il limite di condivisione equa, il processo viene scaricato e riavviato dall'inizio. In tal caso, è opportuno suddividere l'operazione di reindicizzazione in più passaggi, ad esempio specificando la reindicizzazione di una tabella alla volta, e quindi inserire un checkpoint dopo ogni operazione in modo che il processo possa riprendere dopo l'ultima operazione da completare.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui diversi metodi che possono essere usati per avviare un runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)
