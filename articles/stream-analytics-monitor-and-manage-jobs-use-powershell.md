<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="Monitorare e gestire i processi di Analisi dei flussi tramite PowerShell | Azure" description="Informazioni su come usare cmdlet di Azure PowerShell per monitorare e gestire processi di Analisi dei flussi." metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#Monitorare e gestire i processi di Analisi dei flussi tramite Azure PowerShell

Informazioni su come gestire le risorse di Analisi dei flussi di Azure tramite Azure PowerShell.

##Contenuto dell'articolo

- [Prerequisiti](#prerequisites)
- [Cmdlet di PowerShell per Analisi dei flussi](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [Vedere anche](#seealso)

## <a name="prerequisites"></a>Prerequisiti per l'esecuzione dei cmdlet di PowerShell per Analisi dei flussi

1.	Installare e configurare Azure PowerShell

	Seguire le istruzioni riportate in [Come installare e configurare Azure PowerShell][powershell-install] per installare Azure PowerShell.

2.	Configurare la modalità di Azure

	Dopo l'installazione di Azure PowerShell, eseguire il cmdlet [Switch-AzureMode][msdn-switch-azuremode] per impostare la modalità di Azure appropriata per accedere ai cmdlet di Analisi dei flussi:

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] Esiste una limitazione temporanea per cui il monitoraggio dei processi di Analisi dei flussi creati tramite Azure PowerShell non è abilitato.  Per risolvere questo problema, passare alla pagina di monitoraggio del processo nel portale di Azure e fare clic sul pulsante "Abilita".  

##<a name="cmdlets"></a>Cmdlet di PowerShell per Analisi dei flussi
La tabella seguente contiene un elenco dei cmdlet che è possibile usare per monitorare e gestire i processi di Analisi dei flussi con Azure PowerShell.

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
Elenca tutti i processi di Analisi dei flussi definiti nella sottoscrizione di Azure o nel gruppo di risorse specificato oppure ottiene informazioni su uno specifico processo all'interno di un gruppo di risorse.

**Esempio 1**

	Get-AzureStreamAnalyticsJob

Questo comando restituisce informazioni su tutti i processi di Analisi dei flussi nella sottoscrizione di Azure.

**Esempio 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
Questo comando restituisce informazioni su tutti i processi di Analisi dei flussi nel gruppo di risorse StreamAnalytics-Default-West-US.

**Esempio 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
Questo comando restituisce informazioni sul processo StreamingJob di Analisi dei flussi nel gruppo di risorse StreamAnalytics-Default-West-US.

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
Elenca tutti gli input definiti in un processo di Analisi dei flussi specificato oppure ottiene informazioni su un input specifico.

**Esempio 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

Questo comando restituisce informazioni su tutti gli input definiti nel processo StreamingJob.

**Esempio 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Questo comando restituisce informazioni sull'input denominato EntryStream definito nel processo StreamingJob.

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
Elenca tutti gli output definiti in un processo di Analisi dei flussi specificato oppure ottiene informazioni su un output specifico.

**Esempio 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
Questo comando restituisce informazioni sugli output definiti nel processo StreamingJob.

**Esempio 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Questo comando restituisce informazioni sull'output denominato Output definito nel processo StreamingJob.

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
Ottiene informazioni sulla quota di unità di streaming di un'area specificata.

**Esempio 1**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
Questo comando restituisce informazioni sulla quota di unità di streaming e l'uso nell'area Stati Uniti occidentali.

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
Ottiene informazioni una specifica trasformazione definita nel processo di Analisi dei flussi.

**Esempio 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
Questo comando restituisce informazioni sulla trasformazione denominata StreamingJob nel processo StreamingJob.

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
Crea un nuovo input all'interno di un processo di Analisi dei flussi o aggiorna un input esistente specificato.
  
Il nome dell'input può essere specificato nel file JSON o nella riga di comando.  Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un input già esistente e non si specifica il parametro -Force, il cmdlet chiederà se si desidera sostituire l'input esistente.

Se si specifica un nome di input esistente usando il parametro -Force, l'input verrà sostituito senza chiedere conferma.

**Esempio 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
Questo comando crea un nuovo input dal file Input.json.  Se è già definito un input esistente con il nome specificato nel file di definizione dell'input, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
Questo comando crea un nuovo input nel processo denominato EntryStream.  Se un input esistente con questo nome è già definito, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
Questo comando sostituisce la definizione dell'origine di input esistente denominata EntryStream con la definizione presente nel file.

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
Crea un nuovo processo di Analisi dei flussi in Microsoft Azure o aggiorna la definizione di un processo esistente specificato.

Il nome del processo può essere specificato nel file JSON o nella riga di comando.  Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un nome di processo già esistente e non si specifica il parametro -Force, il cmdlet chiederà se si desidera sostituire il processo esistente.

Se si specifica un nome di processo esistente usando il parametro -Force, la definizione del processo verrà sostituita senza chiedere conferma.

**Esempio 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
Questo comando crea un nuovo processo dalla definizione presente in JobDefinition.json.  Se è già definito un processo esistente con il nome specificato nel file di definizione del processo, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
Questo comando sostituisce la definizione del processo per StreamingJob.

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
Crea un nuovo output all'interno di un processo di Analisi dei flussi o aggiorna un output esistente.  

Il nome dell'output può essere specificato nel file JSON o nella riga di comando.  Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica un output già esistente e non si specifica il parametro -Force, il cmdlet chiederà se si desidera sostituire l'output esistente.

Se si specifica un nome di output esistente usando il parametro -Force, l'output verrà sostituito senza chiedere conferma.

**Esempio 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
Questo comando crea un nuovo output denominato "output" nel processo StreamingJob.  Se un output esistente con questo nome è già definito, il cmdlet chiederà se si desidera sostituirlo.

**Esempio 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
Questo comando sostituisce la definizione di "output" nel processo StreamingJob.

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
Crea una nuova trasformazione all'interno di un processo di Analisi dei flussi o aggiorna la trasformazione esistente.
  
Il nome della trasformazione può essere specificato nel file JSON o nella riga di comando.  Se vengono specificati entrambi, il nome nella riga di comando deve corrispondere a quello nel file.

Se si specifica una trasformazione già esistente e non si specifica il parametro -Force, il cmdlet chiederà se si desidera sostituire la trasformazione esistente.

Se si specifica un nome di trasformazione esistente usando il parametro -Force, la trasformazione verrà sostituita senza chiedere conferma.

**Esempio 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
Questo comando crea una nuova trasformazione denominata StreamingJobTransform nel processo StreamingJob.  Se una trasformazione esistente con questo nome è già definita, il cmdlet chiederà se si desidera sostituirla.

**Esempio 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 Questo comando sostituisce la definizione di StreamingJobTransform nel processo StreamingJob.

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
Elimina in modo asincrono uno specifico input da un processo di Analisi dei flussi in Microsoft Azure.  
Se si specifica il parametro -Force, l'input verrà eliminato senza chiedere conferma.

**Esempio 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
Questo comando rimuove l'input EventStream nel processo StreamingJob.  

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
Elimina in modo asincrono uno specifico processo di Analisi dei flussi in Microsoft Azure.  
Se si specifica il parametro -Force, il processo verrà eliminato senza chiedere conferma.

**Esempio 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Questo comando rimuove il processo StreamingJob.  

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
Elimina in modo asincrono uno specifico output da un processo di Analisi dei flussi in Microsoft Azure.  
Se si specifica il parametro -Force, l'output verrà eliminato senza chiedere conferma.

**Esempio 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Questo comando rimuove l'output Output nel processo StreamingJob.  

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
In modo asincrono e avvia un processo di Analisi dei flussi in Microsoft Azure.

**Esempio 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Questo comando avvia il processo StreamingJob.  

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
Interrompe in modo asincrono l'esecuzione di un processo di Analisi dei flussi in Microsoft Azure e dealloca le risorse in uso. La definizione del processo e i metadati rimarranno disponibili all'interno della sottoscrizione tramite il portale di Azure e le API di gestione, in modo che sia possibile modificare e riavviare il processo. Non si incorre in alcun addebito per un processo in stato Arrestato.

**Esempio 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Questo comando arresta il processo StreamingJob.  

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
Verifica la capacità di Analisi dei flussi di connettersi a un input specificato.

**Esempio 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Verifica lo stato di connessione dell'input EntryStream in StreamingJob.  

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
Verifica la capacità di Analisi dei flussi di connettersi a un output specificato.

**Esempio 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Verifica lo stato di connessione dell'output Output in StreamingJob.  


##<a name="seealso"></a>Vedere anche

- [Introduzione ad Analisi dei flussi di Azure][stream.analytics.introduction]
- [Introduzione ad Analisi dei flussi][stream.analytics.get.started]
- [Limitazioni e problemi noti della versione di anteprima di Analisi dei flussi][stream.analytics.limitations]
- [Guida per gli sviluppatori di Analisi dei flussi][stream.analytics.developer.guide]
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi][stream.analytics.query.language.reference]
- [Informazioni di riferimento sulle API REST di Analisi dei flussi][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/it-it/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->
