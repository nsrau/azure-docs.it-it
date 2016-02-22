<properties 
	pageTitle="Campionare i dati in contenitori BLOB di Azure, SQL Server e nelle tabelle Hive | Microsoft Azure" 
	description="Come esplorare i dati archiviati in vari ambienti di Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/07/2016" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Campionare i dati in contenitori BLOB di Azure, SQL Server e nelle tabelle Hive

## Introduzione

Questo documento descrive come campionare i dati archiviati in tre posizioni di Azure in genere usate per l'analisi e la modellazione dei dati in Cortana Analytics Process:

- **I dati del contenitore BLOB di Azure** vengono campionati scaricandoli a livello di programmazione ed eseguendo il successivo campionamento usando un codice Python di esempio.
- **Dati di SQL Server** vengono campionati utilizzando sia il linguaggio di programmazione Python che SQL. 
- **Dati della tabella hive** vengono campionati utilizzando le query Hive.

Il **menu** seguente collega ad argomenti che descrivono come campionare i dati da ognuno di questi ambienti di archiviazione di Azure.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Questa attività di campionatura è un passaggio in [Cortana Analytics Process (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Perché campionare i dati?

Se il set di dati da analizzare è grande, si consiglia di eseguire il downsampling dei dati per ridurli a una dimensione inferiore e più facilmente gestibile, ma comunque rappresentativa. Questa operazione facilita la comprensione e l'esplorazione dei dati, nonché la progettazione di funzionalità. Il suo ruolo nel Cortana Analytics Process consiste nell'abilitare la creazione relativa a prototipi di funzioni di elaborazione dei dati e di modelli per l'apprendimento automatico.

<!---HONumber=AcomDC_0211_2016-->