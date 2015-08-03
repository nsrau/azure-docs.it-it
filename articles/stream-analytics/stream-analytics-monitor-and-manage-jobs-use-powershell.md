<properties 
	pageTitle="Monitorare e gestire i processi di Analisi di flusso con PowerShell | Microsoft Azure" 
	description="Informazioni su come usare Azure PowerShell e i cmdlet per monitorare e gestire i processi di Analisi di flusso." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="06/17/2015" 
	ms.author="jeffstok"/>


# Monitorare e gestire i processi di Analisi di flusso con i cmdlet di Azure PowerShell

Informazioni su come monitorare e gestire le risorse di Analisi di flusso con i cmdlet di Azure PowerShell che eseguono attività di Analisi di flusso di base.


## Prerequisiti per l'esecuzione dei cmdlet di Azure PowerShell per Analisi dei flussi

1.	Installare e configurare Azure PowerShell.

	Seguire le istruzioni in [Come installare e configurare Azure PowerShell][powershell-install] per installare Azure PowerShell.

	Per connettersi alla sottoscrizione di Azure tramite il metodo Azure Active Directory:

		Add-AzureAccount

	Per selezionare la sottoscrizione di Azure con il servizio Analisi di flusso di Azure abilitato, usare il metodo:

		Select-AzureSubscription


2.	Configurare la modalità Azure

	Dopo aver installato Azure PowerShell, eseguire il cmdlet [Switch-AzureMode][msdn-switch-azuremode] per impostare la modalità Azure appropriata per accedere ai cmdlet di Analisi dei flussi:

		Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]Nei processi di Analisi di flusso creati a livello di codice il monitoraggio non è abilitato per impostazione predefinita. È possibile attivare manualmente il monitoraggio nel portale di Azure passando alla pagina Monitoraggio del processo e facendo clic sul pulsante Attiva o procedere a livello di codice seguendo i passaggi in [Analisi dei flussi di Azure - Monitorare i processi di Analisi dei flussi a livello di codice](stream-analytics-monitor-jobs.md)

## Cmdlet di Azure PowerShell per Analisi dei flussi
I cmdlet di Azure PowerShell indicati di seguito possono essere utilizzati per monitorare e gestire i processi di Analisi dei flussi di Azure.

### Get-AzureStreamAnalyticsJob
Elenca tutti i processi di Analisi dei flussi definiti nella sottoscrizione di Azure o nel gruppo di risorse specificato oppure ottiene informazioni su uno specifico processo all'interno di un gruppo di risorse.

**Esempio 1**

	Get-AzureStreamAnalyticsJob

Questo comando di PowerShell restituisce informazioni su tutti i processi di Analisi di flusso nella sottoscrizione di Azure.

**Esempio 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
Questo comando di PowerShell restituisce informazioni su tutti i processi di Analisi di flusso nel gruppo di risorse StreamAnalytics-Default-Central-US.

**Esempio 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
Questo comando di PowerShell restituisce informazioni sul processo StreamingJob di Analisi di flusso nel gruppo di risorse StreamAnalytics-Default-West-US.

### Get-AzureStreamAnalyticsInput
Elenca tutti gli input definiti in un processo di Analisi dei flussi specificato oppure ottiene informazioni su un input specifico.

**Esempio 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Questo comando di PowerShell restituisce informazioni su tutti gli input definiti nel processo StreamingJob.

**Esempio 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
Questo comando di PowerShell restituisce informazioni sull'input denominato EntryStream definito nel processo StreamingJob.

### Get-AzureStreamAnalyticsOutput
Elenca tutti gli output definiti in un processo di Analisi dei flussi specificato oppure ottiene informazioni su un output specifico.

**Esempio 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
Questo comando di PowerShell restituisce informazioni sugli output definiti nel processo StreamingJob.

**Esempio 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
Questo comando di PowerShell restituisce informazioni sull'output denominato Output definito nel processo StreamingJob.

### Get-AzureStreamAnalyticsQuota
Ottiene informazioni sulla quota di unità di streaming di un'area specificata.

**Esempio 1**

	Get-AzureStreamAnalyticsQuota –Location "Central US" 
Questo comando di PowerShell restituisce informazioni sulla quota di unità di streaming e sul relativo utilizzo nell'area Stati Uniti centrali.

### Get-AzureStreamAnalyticsTransformation
Ottiene informazioni su una specifica trasformazione definita nel processo di Analisi dei flussi.

**Esempio 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
Questo comando di PowerShell restituisce informazioni sulla trasformazione denominata StreamingJob nel processo StreamingJob.

### New-AzureStreamAnalyticsInput
Crea un nuovo input all'interno di un processo di Analisi dei flussi o aggiorna un input esistente specificato.
  
Il nome dell'input può essere specificato nel file json o nella riga di comando. Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un input già esistente e non si specifica il parametro –Force, il cmdlet chiederà se si desidera sostituire l'input esistente.

Se si specifica un nome di input esistente usando il parametro –Force, l'input verrà sostituito senza chiedere conferma.

Per informazioni dettagliate sulla struttura e sul contenuto dei file JSON, vedere la sezione [Create Input (Analisi dei flussi di Azure)][msdn-rest-api-create-stream-analytics-input] della [libreria di riferimento delle API REST di gestione di Analisi dei flussi di Azure][stream.analytics.rest.api.reference].

**Esempio 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
Questo comando di PowerShell crea un nuovo input dal file Input.json. Se è già definito un input esistente con il nome specificato nel file di definizione dell'input, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
Questo comando di PowerShell crea un nuovo input nel processo denominato EntryStream. Se un input esistente con questo nome è già definito, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
Questo comando di PowerShell sostituisce la definizione dell'origine di input esistente, denominata EntryStream, con la definizione presente nel file.

### New-AzureStreamAnalyticsJob
Crea un nuovo processo di Analisi dei flussi in Microsoft Azure o aggiorna la definizione di un processo esistente specificato.

Il nome del processo può essere specificato nel file json o nella riga di comando. Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un nome di processo già esistente e non si specifica il parametro –Force, il cmdlet chiederà se si desidera sostituire il processo esistente.

Se si specifica un nome di processo esistente usando il parametro –Force, la definizione del processo verrà sostituita senza chiedere conferma.

Per informazioni dettagliate sulla struttura e sul contenuto dei file JSON, vedere la sezione [Create Stream (Analisi dei flussi di Azure)][msdn-rest-api-create-stream-analytics-job] della [libreria di riferimento delle API REST di gestione di Analisi dei flussi di Azure][stream.analytics.rest.api.reference].

**Esempio 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
Questo comando di PowerShell crea un nuovo processo dalla definizione presente in JobDefinition.json. Se è già definito un processo esistente con il nome specificato nel file di definizione del processo, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
Questo comando di PowerShell sostituisce la definizione del processo per StreamingJob.

### New-AzureStreamAnalyticsOutput
Crea un nuovo output all'interno di un processo di Analisi dei flussi o aggiorna un output esistente.

Il nome dell'output può essere specificato nel file json o nella riga di comando. Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un output già esistente e non si specifica il parametro –Force, il cmdlet chiederà se si desidera sostituire l'output esistente.

Se si specifica un nome di output esistente usando il parametro –Force, l'output verrà sostituito senza chiedere conferma.

Per informazioni dettagliate sulla struttura e sul contenuto dei file JSON, vedere la sezione [Create Output (Analisi dei flussi di Azure)][msdn-rest-api-create-stream-analytics-output] della [libreria di riferimento delle API REST di gestione di Analisi dei flussi di Azure][stream.analytics.rest.api.reference].

**Esempio 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
Questo comando di PowerShell crea un nuovo output denominato "output" nel processo StreamingJob. Se un output esistente con questo nome è già definito, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
Questo comando di PowerShell sostituisce la definizione di "output" nel processo StreamingJob.

### New-AzureStreamAnalyticsTransformation
Crea una nuova trasformazione all'interno di un processo di Analisi dei flussi o aggiorna la trasformazione esistente.
  
Il nome della trasformazione può essere specificato nel file json o nella riga di comando. Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica una trasformazione già esistente e non si specifica il parametro –Force, il cmdlet chiederà se si desidera sostituire la trasformazione esistente.

Se si specifica un nome di trasformazione esistente usando il parametro –Force, la trasformazione verrà sostituita senza chiedere conferma.

Per informazioni dettagliate sulla struttura e sul contenuto dei file JSON, vedere la sezione [Create Transformation (Analisi dei flussi di Azure)][msdn-rest-api-create-stream-analytics-transformation] della [libreria di riferimento delle API REST di gestione di Analisi dei flussi di Azure][stream.analytics.rest.api.reference].

**Esempio 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
Questo comando di PowerShell crea una nuova trasformazione denominata StreamingJobTransform nel processo StreamingJob. Se una trasformazione esistente con questo nome è già definita, il cmdlet chiederà se si desidera sostituirla.

**Esempio 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
 Questo comando di PowerShell sostituisce la definizione di StreamingJobTransform nel processo StreamingJob.

### Remove-AzureStreamAnalyticsInput
Elimina in modo asincrono uno specifico input da un processo di Analisi dei flussi in Microsoft Azure. Se si specifica il parametro –Force, l'input verrà eliminato senza chiedere conferma.

**Esempio 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
Questo comando di PowerShell rimuove l'input EventStream nel processo StreamingJob.

### Remove-AzureStreamAnalyticsJob
Elimina in modo asincrono uno specifico processo di Analisi dei flussi in Microsoft Azure. Se si specifica il parametro –Force, il processo verrà eliminato senza chiedere conferma.

**Esempio 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Questo comando di PowerShell rimuove il processo StreamingJob.

### Remove-AzureStreamAnalyticsOutput
Elimina in modo asincrono uno specifico output da un processo di Analisi dei flussi in Microsoft Azure. Se si specifica il parametro –Force, l’output verrà eliminato senza chiedere conferma.

**Esempio 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Questo comando di PowerShell rimuove l'output Output nel processo StreamingJob.

### Start-AzureStreamAnalyticsJob
In modo asincrono e avvia un processo di Analisi dei flussi in Microsoft Azure.

**Esempio 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Questo comando di PowerShell avvia il processo StreamingJob con un'ora di inizio output personalizzata impostata su 12 dicembre 2012 12:12:12 UTC.


### Stop-AzureStreamAnalyticsJob
Interrompe in modo asincrono l'esecuzione di un processo di Analisi dei flussi in Microsoft Azure e dealloca le risorse in uso. La definizione del processo e i metadati rimarranno disponibili all'interno della sottoscrizione tramite il portale di Azure e le API di gestione, in modo che sia possibile modificare e riavviare il processo. Non si incorre in alcun addebito per un processo in stato Arrestato.

**Esempio 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Questo comando di PowerShell arresta il processo StreamingJob.

### Test-AzureStreamAnalyticsInput
Verifica la capacità di Analisi dei flussi di connettersi a un input specificato.

**Esempio 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
Questo comando di PowerShell verifica lo stato di connessione dell'input EntryStream in StreamingJob.

###Test-AzureStreamAnalyticsOutput
Verifica la capacità di Analisi dei flussi di connettersi a un output specificato.

**Esempio 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Questo comando di PowerShell verifica lo stato di connessione dell'output Output in StreamingJob.

## Supporto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=July15_HO4-->