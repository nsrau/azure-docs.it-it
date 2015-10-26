<properties 
	pageTitle="Pianificare un ambiente di analisi avanzata dei dati di Machine Learning | Microsoft Azure" 
	description="Pianificare l'ambiente di analisi avanzata tenendo presenti le domande principali." 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="mohabib;bradsev" />


# Pianificare l'ambiente di analisi avanzata dei dati di Azure Machine Learning

Quale scenario corrisponde ai problemi di analisi quando ci si prepara a configurare un ambiente per eseguire analisi avanzate con Azure Machine Learning? Le decisioni prese in merito alle risorse necessarie si basano sul tipo, sulle dimensioni e sulla posizione di origine dei dati e sulla destinazione di tali dati. Questo articolo illustra queste domande per facilitare l'identificazione del proprio scenario.

Dopo aver identificato lo scenario rilevante, il flusso di lavoro di ADAPT (Advanced Analytics Process and Technology) presentato in [Percorso di apprendimento: Creazione di soluzioni di analisi avanzata in Azure](machine-learning-data-science-how-to-create-machine-learning-service.md) illustra una serie di attività, da come ottenere un set di dati fino alla creazione e pubblicazione di un modello come servizio Web di Azure utilizzabile dalle applicazioni.

Questo argomento enumera anche alcune delle risorse e degli strumenti usati da questo processo di analisi avanzata.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Rispondere a queste domande
Rispondere a queste domande per determinare lo scenario in uso prima di creare l'ambiente di analisi avanzata.

1. **Dove si trovano i dati?** (Questo percorso viene definito ***origine dati***.) Ad esempio:
	- I dati sono disponibili a un indirizzo HTTP.
	- I dati si trovano in un percorso locale/di rete.
	- I dati si trovano in un database di SQL Server.
	- I dati sono archiviati in un contenitore di archiviazione di Azure.
2. **Come sono formattati i dati?** Ad esempio:
    - File delimitati da virgole o separati da tabulazioni, non compressi.
    - File delimitati da virgole o separati da tabulazioni, compressi.
	- BLOB di Azure compressi o non compressi.
	- Tabelle di SQL Server.
3. **Quali sono le dimensioni dei dati?**
    - Piccole: meno di 2 GB
    - Medie: compresi tra 2 GB e 10 GB
	- Grandi: più di 10 GB
	- Molto grandi: centinaia di GB
4. **Quanta familiarità si ha con i dati?**
    - È necessario esplorare i dati per individuarne lo schema, le distribuzioni di variabili, i valori mancanti e così via? 
	- I dati necessitano di pre-elaborazione o pulizia prima di poter essere trasformati in una rappresentazione tabulare? 
5. **Si prevede (o si è in grado) di spostare tutti i dati in Archiviazione di Azure?**
    - Sì, si sta pianificando di copiare l'intero set di dati nel cloud per l'elaborazione.
	- No, solo un set di dati verrà copiato in Azure.
6. **Qual è la destinazione del cloud di Azure preferita?** Ad esempio:
	- Spostare dati nei BLOB di archiviazione Azure.
	- Archiviare i dati su dischi rigidi virtuali di Azure montabili.
	- Caricare i dati in un database di SQL Server in una macchina virtuale di Azure.
	- Eseguire il mapping dei dati in tabelle Hive di Azure HDInsight.

## Qual è il proprio scenario?
Dopo aver risposto alle domande nella sezione precedente, si è pronti per determinare quale scenario è più adatto al caso specifico. Gli scenari di esempio sono descritti in [Scenari relativi ad ADAPT (Advanced Analytics Process and Technology) in Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md).

## Risorse di analisi avanzata in Azure
A seconda dello scenario, potrebbero essere necessari alcuni degli strumenti e delle risorse seguenti.

1.  Strumenti di Azure: 
	* 	[Azure PowerShell SDK](../install-configure-powershell.md), 
	* 	[Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)
	* 	[AzCopy](../storage-use-azcopy.md)
2.  Macchine virtuali di Azure che eseguono SQL Server
3.  Azure HDInsight (Hadoop)
4.  Reti virtuali di Azure per la condivisione di un file di Azure locale
5.  Azure Data Factory per lo spostamento dei dati pianificato






 

<!---HONumber=Oct15_HO3-->