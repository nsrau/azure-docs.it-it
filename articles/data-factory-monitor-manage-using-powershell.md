<properties title="Monitor and manage Azure Data Factory using Azure PowerShell" pageTitle="Monitorare e gestire Data factory di Azure con Azure PowerShell" description="Learn how to use Azure PowerShell to monitor and manage Azure data factories you have created." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Monitorare e gestire Data factory di Azure con Azure PowerShell
La seguente tabella elenca i cmdlet che è possibile usare per monitorare e gestire le istanze di Data factory di Azure con Azure PowerShell. 

> [WACOM.NOTE] Vedere [Riferimento ai cmdlet di Data factory][cmdlet-reference] per la documentazione completa sui cmdlet di Data factory. 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
Ottiene le informazioni su una specifica istanza di Data factory o su tutte le istanze di Data factory in una sottoscrizione di Azure all'interno di uno specifico gruppo di risorse.
 
###Esempio 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

Questo comando restituisce tutte le istanze di Data factory nel gruppo di risorse ADFTutorialResourceGroup.
 
###Esempio 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

Questo comando restituisce i dettagli sull'istanza di Data factory ADFTutorialDataFactory nel gruppo di risorse ADFTutorialResourceGroup. 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService ##
Il cmdlet Get-AzureDataFactoryLinkedService ottiene le informazioni su uno specifico servizio collegato o su tutti i servizi collegati in un'istanza di Data factory di Azure.

### Esempio 1 ###

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
Questo comando restituisce le informazioni su tutti i servizi collegati nell'istanza di Data factory di Azure ADFTutorialDataFactory.


È possibile usare il parametro -DataFactory al posto dei parametri DataFactoryName e ResourceGroupName. Ciò consente di immettere i nomi del gruppo di risorse e della factory solo una volta e di usare l'oggetto Data factory come parametro per tutti i cmdlet che accettano sia ResourceGroupName che DataFactoryName come parametri.

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### Esempio 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

Questo comando restituisce le informazioni sul servizio collegato MyBlobStore nell'istanza di Data factory di Azure ADFTutorialDataFactory. 

È possibile usare il parametro -DataFactory al posto dei parametri -ResourceGroup e -DataFactoryName come mostrato di seguito: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
Il cmdlet Get-AzureDataFactoryTable ottiene le informazioni su una specifica tabella o su tutte le tabelle in un'istanza di Data factory di Azure. 

### Esempio 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Questo comando restituisce le informazioni su tutte le tabelle nell'istanza Data factory di Azure ADFTutorialDataFactory.

È possibile usare il parametro -DataFactory al posto dei parametri -ResourceGroup e -DataFactoryName come mostrato di seguito: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### Esempio 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

Questo comando restituisce le informazioni sulla tabella EmpTableFromBlob nell'istanza di Data factory di Azure ADFTutorialDataFactory.



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
Il cmdlet Get-AzureDataFactoryPipeline ottiene le informazioni su una specifica pipeline o su tutte le pipeline in un'istanza di Data factory di Azure.

### Esempio 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Questo comando restituisce le informazioni su tutte le pipeline nell'istanza di Data factory di Azure ADFTutorialDataFactory.

### Esempio 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

Ottiene le informazioni sulla pipeline ADFTutorialPipeline nell'istanza di Data factory di Azure ADFTutorialDataFactory.

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
Il cmdlet Get-AzureDataFactorySlice ottiene tutte le sezioni per una tabella in un'istanza di Data factory di Azure generate dopo StartDateTime e prima di EndDateTime. La sezione dei dati con stato Pronto è pronta per essere usata dalle sezioni dipendenti.

La seguente tabella elenca tutti gli stati di una sezione e le relative descrizioni.

<table border="1">	
	<tr>
		<th align="left">Status</th>
		<th align="left">Descrizione</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>L'elaborazione dati non è stata ancora avviata.</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>L'elaborazione dati è in corso.</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>L'elaborazione dati è stata completata e la sezione di dati è pronta.</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>L'esecuzione che genera la sezione non è riuscita.</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>L'elaborazione delle sezione è stata ignorata.</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Nuovo tentativo dell'esecuzione che genera la sezione.</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>Timeout dell'elaborazione dati della sezione.</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>La sezione di dati è in attesa di convalida in base ai criteri di convalida prima di poter essere elaborata.</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>Riprovare la convalida della sezione.</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>La convalida della sezione non è riuscita.</td>
	</tr>

	<tr>
		<td>LongRetry</td>
		<td>Una sezione è in questo stato se LongRetry è specificato nel JSON della tabella e i normali tentativi per la sezione non sono riusciti.</td>
	</tr>

	<tr>
		<td>ValidationInProgress</td>
		<td>La convalida della sezione (basata sui criteri definiti nel JSON della tabella) è in esecuzione.</td>
	</tr>

</table>

Per ciascuna sezione è possibile eseguire il drill-down dettagliato e ottenere altre informazioni sull'esecuzione che ha generato la sezione usando i cmdlet Get-AzureDataFactoryRun e Save-AzureDataFactoryLog.

### Esempio

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

Questo comando ottiene tutte le sezioni per la tabella EmpSQLTable nell'istanza di Data factory di Azure ADFTutorialDataFactory generata dopo 2014-05-20T10:00:00 (GMT). Sostituire la data/ora con la data/ora di inizio specificata quando è stato eseguito Set-AzureDataFactoryPipelineActivePeriod.

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

Il cmdlet Get-AzureDataFactoryRun ottiene tutte le esecuzioni per una sezione di dati di una tabella in un'istanza di Data factory di Azure.  Una tabella in un'istanza di Data factory di Azure è formata da sezioni sull'asse temporale. La larghezza di una sezione viene determinata dalla pianificazione, oraria o giornaliera. L'esecuzione è un'unità di elaborazione per una sezione. Possono esserci più esecuzioni di una sezione se vengono eseguiti più tentativi o se si ripete l'esecuzione della sezione in caso di errori. Una sezione viene identificata in base all'ora di inizio. Quindi, per il cmdlet Get-AzureDataFactoryRun è necessario indicare l'ora di inizio della sezione dai risultati del cmdlet Get-AzureDataFactorySlice.

Ad esempio, per ottenere un'esecuzione per la seguente sezione, usare 2015-04-02T20:00:00. 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### Esempio

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

Questo comando ottiene tutte le esecuzioni per le sezioni della tabella EmpSQLTable nell'istanza di Data factory di Azure ADFTutorialDataFactory a partire dalle 4 PM GMT del 21/05/2014.

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
Il cmdlet Save-AzureDataFactoryLog scarica i file di log associati all'elaborazione di HDInsight di Azure dei progetti Pig o Hive o per le attività personalizzate nel disco rigido locale. Eseguire prima il cmdlet Get-AzureDataFactoryRun per ottenere un ID per l'esecuzione di un'attività per una sezione di dati, quindi usare l'ID per recuperare i file di log dall'archiviazione BLOB (Binary Large Object) associata al cluster HDInsight. 

Se non si specifica il parametro **-DownloadLogs**, il cmdlet restituisce solo il percorso dei file di log. 

Se si specifica il parametro **-DownloadLogs** senza specificare una directory di output (parametro **-Output **), i file di log vengono scaricati nella cartella predefinita **Documenti**. 

Se si specifica il parametro **-DownloadLogs** con una cartella di output (**-Output**), i file di log vengono scaricati nella cartella specificata. 


### Esempio 1
Questo comando salva i file di log per l'attività eseguita con l'ID 841b77c9-d56c-48d1-99a3-8c16c3e77d39 in cui l'attività appartiene a una pipeline nell'istanza di Data factory denominata LogProcessingFactory nel gruppo di risorse denominato ADF. I file di log vengono salvati nella cartella C:\Test. 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### Esempio 2
Questo comando salva i file di log nella cartella Documenti (predefinita).


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### Esempio 3
Questo comando restituisce il percorso dei file di log. Notare che il parametro -DownloadLogs non è specificato. 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
Il cmdlet Get-AzureDataFactoryGateway ottiene le informazioni su uno specifico gateway o su tutti i gateway in un'istanza di Data factory di Azure. È necessario installare un gateway nel computer locale per poter aggiungere SQL Server locale come servizio collegato a un'istanza di Data factory.

### Esempio 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
Questo comando restituisce le informazioni su tutti i gateway nell'istanza di Data factory di Azure ADFTutorialDataFactory.

### Esempio 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

Questo comando restituisce le informazioni sul gateway ADFTutorialGateway nell'istanza di Data factory di Azure ADFTutorialDataFactory.

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
Questo cmdlet imposta il periodo attivo per le sezioni di dati elaborate dalla pipeline. Se si usa Set-AzureDataFactorySliceStatus, verificare che le date di inizio e di fine della sezione siano comprese nel periodo attivo della pipeline.

Una volta create le pipeline, è possibile specificare la durata dell'elaborazione dati. Specificando il periodo attivo per una pipeline, si definisce quanto tempo durerà l'elaborazione delle sezioni di dati in base alle proprietà della disponibilità definite per ogni tabella ADF.

### Esempio

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

Questo comando imposta il periodo attivo per le sezioni di dati elaborate dalla pipeline ADFTutoiralPipeline dal 21/05/2014 4 PM GMT al 22/05/2014 4 PM GMT.

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
Imposta lo stato di una sezione per una tabella. Le date di inizio e di fine della sezione devono essere comprese nel periodo attivo della pipeline.

### Valori supportati per lo stato
Ogni sezione di dati per una tabella passa attraverso diverse fasi. Queste fasi sono leggermente diverse a seconda dei criteri di convalida specificati.


- Se i criteri di convalida   non sono specificati: PendingExecution -> InProgress -> Ready
- Se i criteri di convalida sono specificati: PendingExecution -> Pending Validation -> InProgress -> Ready

La seguente tabella fornisce le descrizioni dei possibili stati di una sezione e indica se lo stato può essere impostato o meno con Set-AzureDataFactorySliceStatus.

<table border="1">	
	<tr>
		<th>Status</th>
		<th>Descrizione</th>
		<th>Può essere impostato con un cmdlet></th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>L'elaborazione dati non è stata ancora avviata.</td>
		<td>S</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>L'elaborazione dati è in corso.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>L'elaborazione dati è stata completata e la sezione di dati è pronta.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>L'esecuzione che genera la sezione non è riuscita.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>L'elaborazione delle sezione è stata ignorata.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Nuovo tentativo dell'esecuzione che genera la sezione.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>Timeout dell'elaborazione dati.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>La sezione di dati è in attesa di convalida in base ai criteri di convalida prima di poter essere elaborata.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>Riprovare la convalida della sezione.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>La convalida della sezione non è riuscita.</td>
		<td>N</td>
	</tr>
	</table>


### Valori supportati - Tipo di aggiornamento
Per ogni tabella in un'istanza di Data factory di Azure, quando si imposta lo stato di una sezione, è necessario specificare se l'aggiornamento dello stato si applica solo alla tabella o si propaga a tutte le sezioni interessate.

<table border="1">	
	<tr>
		<th>Tipo di aggiornamento</th>
		<th>Descrizione</th>
		<th>Può essere impostato con un cmdlet</th>
	</tr>

	<tr>
		<td>Singolo</td>
		<td>Imposta lo stato di ogni sezione per la tabella nell'intervallo di tempo specificato</td>
		<td>S</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>Imposta lo stato di ogni sezione per la tabella e di tutte le tabelle dipendenti (upstream) usate come tabelle di input per le attività nella pipeline.</td>
		<td>S</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
Il cmdlet Suspend-AzureDataFactoryPipeline sospende la pipeline specificata in un'istanza di Data factory di Azure. È possibile riprendere la pipeline in un secondo momento usando il cmdlet Resume-AzureDataFactoryPipeline.

### Esempio

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Questo comando sospende la pipeline ADFTutorialPipeline nell'istanza di Data factory di Azure ADFTutorialDataFactory.

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
Il cmdlet Resume-AzureDataFactoryPipeline riprende la pipeline specificata attualmente in stato sospeso in un'istanza di Data factory di Azure. 

### Esempio

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Questo comando riprende la pipeline ADFTutorialPipeline nell'istanza di Data factory di Azure ADFTutorialDataFactory sospesa in precedenza usando il comando Suspend-AzureDataFactoryPipeline.

## Vedere anche

Articolo | Descrizione
------ | ---------------
[Monitorare e gestire Data factory di Azure con il portale di anteprima di Azure][monitor-manage-using-portal] | Questo articolo descrive come monitorare e gestire un'istanza di Data factory di Azure con il portale di anteprima di Azure.
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database SQL Server locale a un BLOB di Azure.
[Usare Pig e Hive con Data factory][use-pig-and-hive-with-data-factory] | Questo articolo contiene una procedura dettagliata che mostra come usare l'attività HDInsight per eseguire uno script hive/pig per elaborare i dati di input in modo da generare i dati di output. 
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure.
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 
[Informazioni di riferimento per i cmdlet di Data factory di Azure][cmdlet-reference] | Questo contenuto di riferimento include i dettagli su tutti i **cmdlet di Data factory**.

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
