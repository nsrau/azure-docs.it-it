<properties
   pageTitle="Esecuzione di runbook in Automazione di Azure"
   description="Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# Esecuzione di runbook in Automazione di Azure


Quando si avvia un runbook in Automazione di Azure, viene creato un processo. Un processo è una singola istanza di esecuzione di un runbook. Per eseguire ogni processo, viene assegnato un computer di lavoro di Automazione di Azure. I computer di lavoro sono condivisi da più account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non si dispone inoltre di alcun controllo sul computer di lavoro che gestirà la richiesta per il processo. In un singolo runbook possono venire eseguiti più processi contemporaneamente. Quando si visualizza l'elenco dei runbook nel portale di Azure, è visibile lo stato dell'ultimo processo avviato per ogni runbook. È possibile visualizzare l'elenco dei processi per il singolo runbook per tenere traccia dello stato di ognuno. Per una descrizione dei diversi stati dei processi, vedere [Stati dei processi](#job-statuses).

Nel diagramma seguente viene illustrato il ciclo di vita di un processo runbook per [Runbook grafici](automation-runbook-types.md#graphical-runbooks) e [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

Nel diagramma seguente viene illustrato il ciclo di vita di un processo runbook per [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks).

![Stati del processo - Script PowerShell](./media/automation-runbook-execution/job-statuses-script.png)


I processi accederanno alle risorse di Azure effettuando una connessione alla sottoscrizione di Azure. Potranno accedere solo alle risorse del data center dell'utente se tali risorse sono accessibili dal cloud pubblico.

## Stati dei processi

La tabella seguente descrive i diversi stati possibili per un processo.

| Stato| Descrizione|
|:---|:---|
|Completed|Il processo è stato completato.|
|Failed| Per [Runbook grafico e runbook flusso di lavoro PowerShell](automation-runbook-types.md), la compilazione di runbook non è riuscita. Per [Runbook di Script di PowerShell](automation-runbook-types.md), non è stato possibile avviare il runbook o il processo ha rilevato un'eccezione. |
|Failed, waiting for resources|Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](#fairshare) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook.|
|Queued|Il processo è in attesa che diventino disponibili risorse in un computer di lavoro di Automazione per poter essere avviato.|
|Starting|Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando.|
|Resuming|Il sistema sta per riprendere il processo dopo che è stato sospeso.|
|Running|Il processo è in esecuzione.|
|Running, waiting for resources|Il processo è stato scaricato perché ha raggiunto il limite di [condivisione equa](#fairshare). Riprenderà a breve dall'ultimo checkpoint.|
|Stopped|Il processo è stato arrestato dall'utente prima del completamento.|
|Stopping|Il sistema sta arrestando il processo.|
|Suspended|Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Un processo sospeso può essere riavviato e verrà ripreso dall'ultimo checkpoint o dall'inizio del runbook se non sono presenti checkpoint. Il runbook verrà sospeso dal sistema solo in caso di eccezione. Per impostazione predefinita, il valore di ErrorActionPreference è impostato su **Continue**, a indicare che il processo continuerà a essere eseguito in caso di errore. Se questa variabile di preferenza è impostata su **Stop**, il processo verrà sospeso in caso di errore. Si applica solo a [Runbook grafico e al flusso di lavoro PowerShell](automation-runbook-types.md).|
|Suspending|Il sistema sta tentando di sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, il processo verrà completato prima di poter essere sospeso. Si applica solo a [Runbook grafico e al flusso di lavoro PowerShell](automation-runbook-types.md).|

## Visualizzazione dello stato del processo mediante il portale di gestione di Azure

### Dashboard di automazione

Il dashboard di automazione mostra un riepilogo di tutti i runbook per un account di automazione specifico. Include anche una panoramica di utilizzo per l'account. Il grafico riepilogativo mostra il numero di processi totali per tutti i runbook che hanno attraversato ogni stato in un numero specificato di ore o giorni. È possibile selezionare l'intervallo di tempo nell'angolo superiore destro del grafico. L'asse temporale del grafico cambierà in base al tipo di intervallo di tempo selezionato. È possibile scegliere se visualizzare la riga per un determinato stato facendo clic su di esso nella parte superiore dello schermo.

Per visualizzare il dashboard di automazione, seguire questa procedura.

1. Nel portale di gestione di Azure selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
1. Selezionare la scheda **Dashboard**.

### Dashboard del runbook

Il dashboard del runbook mostra un riepilogo per un singolo runbook. Il grafico riepilogativo mostra il numero di processi totali per il runbook che ha attraversato ogni stato in un numero specificato di ore o giorni. È possibile selezionare l'intervallo di tempo nell'angolo superiore destro del grafico. L'asse temporale del grafico cambierà in base al tipo di intervallo di tempo selezionato. È possibile scegliere se visualizzare la riga per un determinato stato facendo clic su di esso nella parte superiore dello schermo.

Per visualizzare il dashboard del runbook, seguire questa procedura.

1. Nel portale di gestione di Azure selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
1. Fare clic sul nome di un runbook.
1. Selezionare la scheda **Dashboard**.

### Riepilogo dei processi

È possibile visualizzare un elenco di tutti i processi creati per un determinato runbook e il relativo stato più recente. È possibile filtrare l'elenco in base allo stato del processo e all'intervallo di date dell'ultima modifica del processo. Fare clic sul nome di un processo per visualizzarne le informazioni dettagliate e l'output. La visualizzazione dettagliata del processo include i valori per i parametri del runbook che sono stati forniti al processo.

Per visualizzare i processi per un runbook, seguire questa procedura.

1. Nel portale di gestione di Azure selezionare **Automazione** e quindi fare clic sul nome di un account di automazione.
1. Fare clic sul nome di un runbook.
1. Selezionare la scheda **Processi**.
1. Fare clic sulla colonna **Processo creato** per un processo per visualizzarne i dettagli e l'output.

## Recupero dello stato di un processo tramite Windows PowerShell

È possibile usare [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) per recuperare i processi creati per un runbook e i dettagli di un processo specifico. Se si avvia un runbook con Windows PowerShell usando [Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx), verrà restituito il processo risultante. Usare [Get-AzureAutomationJob](http://msdn.microsoft.com/library/azure/dn690263.aspx) per ottenere l'output di un processo.

I comandi di esempio seguenti recuperano l'ultimo processo per un runbook di esempio e ne visualizzano lo stato, i valori specificati per i parametri del runbook e l'output del processo.

	$job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
	$job.Status
	$job.JobParameters
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## Condivisione equa

Per condividere le risorse tra tutti i runbook nel cloud, Automazione di Azure scaricherà temporaneamente qualsiasi processo in esecuzione da tre ore. I runbook [Grafico](automation-runbook-types.md#graphical-runbooks) e [Flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) verranno ripresi dall'ultimo [checkpoint](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints). Durante questo periodo, per il processo verrà visualizzato lo stato Running, waiting for resources. Se il runbook non dispone di alcun checkpoint o non ha raggiunto il primo checkpoint prima di essere scaricato, verrà riavviato dall'inizio. I runbook [PowerShell](automation-runbook-types.md#powershell-runbooks) vengono sempre riavviati dall'inizio perché non supportano i checkpoint.

Se il runbook viene riavviato dallo stesso checkpoint o dall'inizio per tre volte consecutive, verrà terminato con lo stato Failed, waiting for resources. Questo avviene per impedire che i runbook vengano eseguiti all'infinito senza essere completati, in quanto non riusciranno a giungere al checkpoint successivo senza essere scaricati di nuovo. In tal caso, l'operazione avrà esito negativo e si riceverà un'eccezione che indica quanto segue.

*L'esecuzione non potrà proseguire perché il processo è stato rimosso ripetutamente dallo stesso checkpoint. Verificare che il runbook non esegua operazioni di lunga durata senza rendere persistente il proprio stato.*

Quando si crea un runbook, è consigliabile assicurarsi che il tempo necessario per eseguire qualsiasi attività tra due checkpoint non superi le tre ore. Può essere necessario aggiungere checkpoint al runbook per garantire che non raggiunga tale limite o suddividere le operazioni che richiedono una lunga esecuzione. Ad esempio, il runbook potrebbe eseguire una reindicizzazione su un database SQL di grandi dimensioni. Se questa singola operazione non viene completata entro il limite di condivisione equa, il processo verrà scaricato e riavviato dall'inizio. In tal caso, è opportuno suddividere l'operazione di reindicizzazione in più passaggi, ad esempio specificando la reindicizzazione di una tabella alla volta, e quindi inserire un checkpoint dopo ogni operazione in modo che il processo possa riprendere dopo l'ultima operazione da completare.



## Articoli correlati

- [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)

<!---HONumber=AcomDC_0211_2016-->