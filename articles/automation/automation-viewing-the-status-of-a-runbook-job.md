<properties 
   pageTitle="Visualizzazione dello stato di un processo di runbook in Automazione di Azure"
   description="Quando si avvia un runbook in Automazione di Azure, viene creato un processo. Questo articolo fornisce informazioni su come tenere traccia di ogni processo e visualizzarne lo stato corrente."
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
   ms.date="03/30/2015"
   ms.author="bwren" />

# Visualizzazione dello stato di un processo di runbook in Automazione di Azure


Quando si avvia un runbook in Automazione di Azure, viene creato un processo. Un processo è una singola istanza di esecuzione di un runbook. Un singolo runbook può avere più processi, ognuno con il proprio set di valori per i parametri del runbook. È possibile controllare lo stato di un determinato processo e di tutti i processi per uno o più runbook in diversi modi.

## Stati dei processi

La tabella seguente descrive i diversi stati possibili per un processo.

| Stato| Descrizione|
|:---|:---|
|Completed|Il processo è stato completato.|
|Failed|Il processo è terminato con un errore.|
|Failed, waiting for resources|Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook.|
|Queued|Il processo è in attesa che diventino disponibili risorse in un computer di lavoro di Automazione per poter essere avviato.|
|Starting|Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando.|
|Resuming|Il sistema sta per riprendere il processo dopo che è stato sospeso.|
|Running|Il processo è in esecuzione.|
|Running, waiting for resources|Il processo è stato scaricato perché ha raggiunto il limite di [condivisione equa](http://msdn.microsoft.com/library/azure/3179b655-ab39-407a-9169-33571f958325#fairshare). Riprenderà a breve dall'ultimo checkpoint.|
|Stopped|Il processo è stato arrestato dall'utente prima del completamento.|
|Stopping|Il sistema sta arrestando il processo.|
|Suspended|Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Un processo sospeso può essere riavviato e verrà ripreso dall'ultimo checkpoint o dall'inizio del runbook se non sono presenti checkpoint. Il runbook verrà sospeso dal sistema solo in caso di eccezione. Per impostazione predefinita, il valore di ErrorActionPreference è impostato su **Continue**, a indicare che il processo continuerà a essere eseguito in caso di errore. Se questa variabile di preferenza è impostata su **Stop**, il processo verrà sospeso in caso di errore.|
|Suspending|Il sistema sta tentando di sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, il processo verrà completato prima di poter essere sospeso.|

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

## Articoli correlati

- [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) 

<!---HONumber=July15_HO3-->