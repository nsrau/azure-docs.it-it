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
	ms.date="05/29/2015" 
	ms.author="mohabib;bradsev" />


# Pianificare l'ambiente di analisi avanzata dei dati di Azure Machine Learning

È necessario prendere alcune decisioni quando si configura un ambiente di analisi avanzata dei dati di Azure Machine Learning. Le decisioni prese si baseranno sul tipo, sulla dimensione e sulla posizione di origine dei dati e sulla destinazione di tali dati nel cloud. Il processo di analisi avanzata dei dai cloud è una serie di attività end-to-end che consentono di accedere dai dati originali in una specifica origine attraverso la creazione e la pubblicazione di un modello come un servizio Web di Azure da utilizzare nelle applicazioni.

Il flusso di Advanced Analytics Process and Technology viene presentato nell'argomento relativo alla [creazione di soluzioni di analisi avanzate dei dati in Azure](machine-learning-data-science-how-to-create-machine-learning-service.md). Per altre informazioni sui singoli passaggi in Advanced Analytics Process and Technology (ADAPT), selezionare gli elementi pertinenti nella Guida.

Questo articolo illustra le domande da tenere presenti durante la configurazione dell'ambiente di analisi avanzata dei dati, enumera le risorse e gli strumenti utili per questo processo e fornisce indicazioni su come usare la guida di Advanced Analytics Process and Technology.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Domande da tenere presenti

Prima di iniziare a creare l'ambiente di analisi avanzata dei dati, tenere presenti le domande seguenti.

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
5. **Si prevede (o si è in grado) di spostare tutti i dati in Azure?**
    - Sì, si sta pianificando di copiare l'intero set di dati nel cloud per l'elaborazione.
	- No, solo un set di dati verrà copiato in Azure.
6. **Qual è la destinazione del cloud di Azure preferita?** Ad esempio:
	- Spostare dati nei BLOB di archiviazione Azure.
	- Archiviare i dati su dischi rigidi virtuali di Azure montabili.
	- Caricare i dati in un database di SQL Server in una macchina virtuale di Azure.
	- Eseguire il mapping dei dati in tabelle Hive di Azure HDInsight.

## Risorse di analisi avanzata in Azure

A seconda dello scenario, può essere necessario quanto segue:

1.  Strumenti di Azure: [Azure PowerShell SDK](../install-configure-powershell.md), [Esplora archivi di Azure](http://azurestorageexplorer.codeplex.com/), [AzCopy](../storage-use-azcopy.md) e altri
2.  Macchine virtuali di Azure che eseguono SQL Server
3.  Azure HDInsight (Hadoop)
4.  Reti virtuali di Azure per la condivisione di un file di Azure locale
5.  Azure Data Factory per lo spostamento dei dati pianificato


## Come usare la guida di Advanced Analytics Process and Technology (ADAPT)

La guida fornita nell'argomento relativo alla [creazione di soluzioni di analisi avanzate in Azure](machine-learning-data-science-how-to-create-machine-learning-service.md) presenta numerosi esercizi di analisi scientifica dei dati. La mappa mostra i passaggi fondamentali previsti in un tipico flusso di lavoro di analisi avanzata dei dati. Non tutti i passaggi sono necessari in ogni esercizio di analisi scientifica dei dati. Inoltre, il processo è iterativo per natura e la sequenza dei passaggi potrebbe variare in un determinato esercizio. Le risposte alle domande poste sopra aiuteranno l'utente a decidere i passaggi riguardanti il proprio caso, quando sono necessari nel processo e le condizioni in cui le iterazioni dei passaggi sono necessarie.

Per scenari di esempio basati sulle dimensioni dei dati originali, sul percorso di origine dei dati e sull'archivio di destinazione in Azure, vedere l'argomento relativo agli [scenari di Advanced Analytics Process and Technology in Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md).


 

<!---HONumber=July15_HO4-->