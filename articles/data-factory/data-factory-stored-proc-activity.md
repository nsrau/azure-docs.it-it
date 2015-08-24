<properties 
	pageTitle="Attività di stored procedure di SQL Server" 
	description="Informazioni sull'uso dell'attività di stored procedure di SQL Server da una pipeline di Data factory per richiamare una stored procedure in un database SQL di Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Attività di stored procedure di SQL Server

È possibile usare l'attività di stored procedure di SQL Server in una [pipeline](data-factory-create-pipelines.md) di Data factory per richiamare una stored procedure in un database **SQL di Azure**. Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](data-factory-data-transformation-activities.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

## Sintassi
	{
    	"name": "SQLSPROCActivity",
    	"description": "description", 
    	"type": "SqlServerStoredProcedure",
    	"inputs":  [ { "name": "inputtable"  } ],
    	"outputs":  [ { "name": "outputtable" } ],
    	"typeProperties":
    	{
        	"storedProcedureName": "<name of the stored procedure>",
        	"storedProcedureParameters":  
        	{
				"param1": "param1Value"
				…
        	}
    	}
	}

## Dettagli sintassi

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
name | Nome dell'attività | Sì
description | Testo descrittivo per lo scopo dell'attività | No
type | SqlServerStoredProcedure | Sì
inputs | Input disponibili (in stato 'Ready') per l'esecuzione dell'attività di stored procedure | No
outputs | Output generati dall'attività di stored procedure. Assicurarsi che la tabella di output usi un servizio collegato che collega un database SQL di Azure alla data factory. | Sì
storedProcedureName | Specificare il nome della stored procedure nel database SQL di Azure rappresentato dal servizio collegato che usa la tabella di output. | Sì
storedProcedureParameters | Specificare i valori dei parametri della stored procedure | No

> [AZURE.NOTE]Gli input per l'attività di stored procedure vengono usati solo per la gestione della dipendenza e il concatenamento di questa attività con le altre. Gli input non possono essere utilizzati nella stored procedure come parametro.
 

## Esempio

Si consideri un esempio in cui si desidera creare una tabella nel database SQL di Azure con due colonne:

Colonna | Tipo
------ | ----
ID | Identificatore univoco
Datetime | Data e ora in cui è stato generato l'ID corrispondente

![Dati di esempio](./media/data-factory-stored-proc-activity/sample-data.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime)
	END

> [AZURE.NOTE]**Nome** e **convenzione per maiuscole e minuscole** del parametro (DateTime in questo esempio) devono corrispondere a quelli del parametro specificato nell'attività JSON riportata di seguito. Nella definizione della stored procedure, assicurarsi che **@** sia utilizzato come prefisso per il parametro.

Per eseguire questa stored procedure in una pipeline di Data factory, è necessario seguire questa procedura:

1.	Creare un [servizio collegato](data-factory-azure-sql-connector.md/#azure-sql-linked-service-properties) per registrare la stringa di connessione del database SQL di Azure in cui deve essere eseguita la stored procedure.
2.	Creare un [set di dati](data-factory-azure-sql-connector.md/#azure-sql-dataset-type-properties) che punta alla tabella di output nel database SQL di Azure. Il set di dati viene denominato sprocsampleout. Questo set di dati deve fare riferimento al servizio collegato del passaggio #1. 
3.	Creare la stored procedure nel database SQL di Azure.
4.	Creare la [pipeline](data-factory-azure-sql-connector.md/#azure-sql-copy-activity-type-properties) seguente con l'attività SqlServerStoredProcedure per richiamare la stored procedure nel database SQL di Azure.

		{
		    "name": "SprocActivitySamplePipeline",
		    "properties":
		    {
		        "activities":
		        [
		            {
		            	"name": "SprocActivitySample",
		             	"type": " SqlServerStoredProcedure",
		             	"outputs": [ {"name": "sprocsampleout"} ],
		             	"typeProperties":
		              	{
		                	"storedProcedureName": "sp_sample",
			        		"storedProcedureParameters": 
		        			{
		            			"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
		        			}
						}
	            	}
		        ]
		     }
		}
5.	Distribuire la [pipeline](data-factory-create-pipelines.md).
6.	[Monitorare la pipeline](data-factory-monitor-manage-pipelines.md) mediante le viste di monitoraggio e gestione delle pipeline di Data factory.

> [AZURE.NOTE]Nell'esempio precedente, l'attività SprocActivitySample non contiene dati di input. Se si desidera concatenarla con un'attività upstream, è possibile usare gli output dell'attività upstream come input di questa attività. In questo caso, l'attività non verrà eseguita finché l'attività upstream non sarà completata e gli output non saranno disponibili (in stato Ready). Non è possibile usare gli input direttamente come parametri dell'attività di stored procedure.
> 
> I nomi e le maiuscole e minuscole dei parametri della stored procedure nel file JSON devono corrispondere ai nomi dei parametri della stored procedure nel database di destinazione.

A questo punto, si consideri l'aggiunta di un'altra colonna denominata 'Scenario' nella tabella contenente un valore statico denominato 'Document sample'.

![Dati di esempio 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

	CREATE PROCEDURE sp_sample @DateTime nvarchar(127) , @Scenario nvarchar(127)
	
	AS
	
	BEGIN
	    INSERT INTO [sampletable]
	    VALUES (newid(), @DateTime, @Scenario)
	END

A tale scopo, passare il parametro di Scenario e il valore dall'attività di stored procedure. La sezione typeProperties nell'esempio precedente è simile alla seguente:

	"typeProperties":
	{
		"storedProcedureName": "sp_sample",
	    "storedProcedureParameters": 
	    {
	    	"DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
			"Scenario": "Document sample"
		}
	}

<!---HONumber=August15_HO7-->