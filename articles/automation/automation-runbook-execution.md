---
title: Esecuzione di runbook in Automazione di Azure
description: Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 10/30/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bb6236203a1165361505c8699ba94bff54e41c2a
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247351"
---
# <a name="runbook-execution-in-azure-automation"></a>Esecuzione di runbook in Automazione di Azure

Quando si avvia un runbook in Automazione di Azure, viene creato un processo. Un processo è una singola istanza di esecuzione di un runbook. Per eseguire ogni processo, viene assegnato un computer di lavoro di Automazione di Azure. I computer di lavoro sono condivisi da molti account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non si ha alcun controllo sul computer di lavoro che gestisce la richiesta per il processo. In un singolo runbook possono venire eseguiti molti processi contemporaneamente. È possibile riusare l'ambiente di esecuzione per i processi dallo stesso account di automazione. Quanti più processi vengono eseguiti contemporaneamente, tanto più spesso questi possono essere inviati allo stesso ambiente sandbox. I processi in esecuzione nello stesso processo sandbox possono influire l'uno sull'altro. Un esempio è dato dal cmdlet `Disconnect-AzureRMAccount`. L'esecuzione di questo cmdlet ha l'effetto di disconnettere ogni processo runbook nel processo sandbox condiviso. Quando si visualizza l'elenco dei runbook nel portale di Azure, è visibile lo stato di tutti i processi avviati per ogni runbook. È possibile visualizzare l'elenco dei processi per il singolo runbook per tenere traccia dello stato di ognuno. I log dei processi vengono archiviati per un massimo di 30 giorni. Per una descrizione dei diversi stati dei processi, vedere [Stati dei processi](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Nel diagramma seguente viene illustrato il ciclo di vita di un processo del Runbook per [Runbook grafici](automation-runbook-types.md#graphical-runbooks) e [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

Nel diagramma seguente viene illustrato il ciclo di vita di un processo runbook per [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks).

![Stati del processo - Script PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

I processi accedono alle risorse di Azure eseguendo una connessione alla sottoscrizione di Azure. Possono accedere solo alle risorse del data center dell'utente se tali risorse sono accessibili dal cloud pubblico.

## <a name="job-statuses"></a>Stati dei processi

La tabella seguente descrive i diversi stati possibili per un processo. PowerShell ha due tipi di errori, fatali e non fatali. Gli errori fatali impostano lo stato del runbook su **Non riuscito**, se si verificano. Gli errori non fatali consentono allo script di continuare anche dopo che si verificano. Un esempio di errore non fatale è l'uso del cmdlet `Get-ChildItem` con un percorso che non esiste. PowerShell rileva che il percorso non esiste, genera un errore e passa alla cartella successiva. Questo errore non imposta su **Failed** lo stato del runbook, che può essere contrassegnato come **Completato**. Per forzare l'arresto di un runbook in caso di errore non fatale, è possibile usare `-ErrorAction Stop` nel cmdlet.

| Stato | Descrizione |
|:--- |:--- |
| Completed |Il processo è stato completato. |
| Failed |Per [Runbook grafico e runbook flusso di lavoro PowerShell](automation-runbook-types.md), la compilazione di runbook non è riuscita. Per [Runbook di Script di PowerShell](automation-runbook-types.md), non è stato possibile avviare il runbook o il processo conteneva un'eccezione. |
| Failed, waiting for resources |Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](#fair-share) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook. |
| Queued |Il processo è in attesa che diventino disponibili risorse in un computer di lavoro di Automazione per poter essere avviato. |
| Avvio in corso |Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando. |
| Resuming |Il sistema sta riprendendo il processo dopo che è stato sospeso. |
| In esecuzione |Il processo è in esecuzione. |
| Running, waiting for resources |Il processo è stato scaricato perché ha raggiunto il limite di [condivisione equa](#fair-share) . Riprende a breve dall'ultimo checkpoint. |
| Arrestato |Il processo è stato arrestato dall'utente prima del completamento. |
| Stopping |Il sistema sta arrestando il processo. |
| Suspended |Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Se un runbook non ha un checkpoint, viene avviato dall'inizio del runbook. Se ha un checkpoint, può essere nuovamente avviato e riprendere dall'ultimo checkpoint. Il runbook viene sospeso dal sistema quando ha luogo un'eccezione. Per impostazione predefinita, il valore di ErrorActionPreference è impostato su **Continua**, a indicare che il processo continua a essere eseguito in caso di errore. Se questa variabile di preferenza è impostata su **Stop**, il processo viene sospeso in caso di errore. Si applica solo a [Runbook grafico e al flusso di lavoro PowerShell](automation-runbook-types.md) . |
| Suspending |Il sistema sta provando a sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, il processo viene completato prima di poter essere sospeso. Si applica solo a [Runbook grafico e al flusso di lavoro PowerShell](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Visualizzazione dello stato del processo dal portale di Azure

È possibile visualizzare uno stato riassuntivo di tutti i processi del runbook o esaminare i dettagli di uno specifico processo del runbook nel portale di Azure. È anche possibile configurare l'integrazione con l'area di lavoro di Log Analytics per inoltrare lo stato del processo del runbook e i flussi di processo. Per altre informazioni sull'integrazione con Log Analytics, vedere [Inoltrare lo stato e i flussi del processo da Automazione a Log Analytics](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Riepilogo dei processi del Runbook di Automazione

A destra dell'account di Automazione selezionato, è possibile visualizzare tutti i processi del runbook sotto il riquadro **Statistiche processi**.

![Riquadro Statistiche del processo](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Questo riquadro visualizza un conteggio e una rappresentazione grafica dello stato dei processi per tutti i processi eseguiti.

Facendo clic sul riquadro, viene visualizzata la pagina **Processi**, che include un elenco riepilogativo di tutti i processi eseguiti. Questa pagina mostra lo stato, gli orari di inizio e gli orari di completamento.

![Pagina Processi dell'account di Automazione](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

È possibile filtrare l'elenco dei processi selezionando **Filtra processi** e filtrare in base a uno specifico runbook, allo stato del processo o, nell'elenco a discesa, all'intervallo di tempo entro cui eseguire la ricerca.

![Filtrare i processi in base allo stato](./media/automation-runbook-execution/automation-account-jobs-filter.png)

In alternativa, è possibile visualizzare i dettagli di riepilogo dei processi per un runbook specifico selezionando tale runbook dal pannello **Runbook** nel proprio account di Automazione e, successivamente, selezionare il riquadro **Processi**. Questa azione visualizza la pagina **Processi**, dove è possibile fare clic sul record del processo per visualizzarne i dettagli e l'output.

![Pagina Processi dell'account di Automazione](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Riepilogo dei processi

È possibile visualizzare un elenco di tutti i processi creati per un determinato runbook e il relativo stato più recente. È possibile filtrare l'elenco in base allo stato del processo e all'intervallo di date dell'ultima modifica del processo. Fare clic sul nome di un processo per visualizzarne le informazioni dettagliate e l'output. La visualizzazione dettagliata del processo include i valori per i parametri del runbook che sono stati forniti al processo.

Per visualizzare i processi per un runbook, seguire questa procedura.

1. Nel portale di Azure, selezionare **Automazione** e selezionare il nome di un account di automazione.
2. Dall'hub selezionare **Runbook** e quindi nella pagina **Runbook** selezionare un runbook dall'elenco.
3. Nella pagina del runbook selezionato fare clic sul riquadro **Processi**.
4. Fare clic su uno dei processi nell'elenco e nella pagina dei dettagli del processo del runbook è possibile visualizzarne i dettagli e l'output.

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

L'esempio seguente recupera l'output di un processo specifico e restituisce ogni record. Se si è verificata un'eccezione per uno dei record, invece del valore viene scritta l'eccezione. Questo comportamento è utile perché le eccezioni possono fornire informazioni aggiuntive che potrebbero non essere registrate normalmente durante l'output.

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

Per condividere le risorse tra tutti i runbook nel cloud, Automazione di Azure scaricherà o arresterà temporaneamente qualsiasi processo in esecuzione da più di tre ore. I processi dei [runbook basati su PowerShell](automation-runbook-types.md#powershell-runbooks) e dei [runbook di Python](automation-runbook-types.md#python-runbooks) vengono arrestati e non vengono riavviati e lo stato del processo risulta arrestato.

Per le attività che richiedono una lunga esecuzione, è consigliabile un [ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md#job-behavior). I ruoli di lavoro ibridi per runbook non sono limitati da condivisione equa e non hanno una limitazione rispetto alla possibile durata dell'esecuzione di un runbook. Gli altri [limiti](../azure-subscription-service-limits.md#automation-limits) dei processi si applicano sia ai sandbox di Azure che ai ruoli di lavoro ibridi per runbook. Anche se i ruoli di lavoro ibridi per runbook non sono soggetti al limite di 3 ore della condivisione equa, i runbook eseguiti in tali ruoli di lavoro dovranno tuttavia essere sviluppati per poter supportare i comportamenti di riavvio causati da problemi imprevisti dell'infrastruttura locale.

Un'altra opzione consiste nell'ottimizzare il runbook usando runbook figlio. Se il runbook è ciclico per la stessa funzione in più risorse, ad esempio un'operazione di database su più database, è possibile spostare tale funzione in un [runbook figlio](automation-child-runbooks.md) e chiamarla con il cmdlet [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Ognuno di questi runbook figli verrà eseguito in parallelo in processi separati, riducendo la quantità totale di tempo del runbook padre richiesta per il completamento. È possibile usare il cmdlet [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) nel runbook per controllare lo stato del processo per ogni elemento figlio, se sono presenti operazioni che devono essere eseguite al termine del runbook figlio.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui diversi metodi che possono essere usati per avviare un runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)
