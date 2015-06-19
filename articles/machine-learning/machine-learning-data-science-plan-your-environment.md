<properties 
	pageTitle="Pianificazione dell'ambiente di analisi scientifica dei dati cloud | Azure" 
	description="Pianificazione dell'ambiente di analisi scientifica dei dati cloud" 
	metaKeywords="" 
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
	ms.date="03/24/2015" 
	ms.author="mohabib;bradsev" /> 


# Pianificazione dell'ambiente di analisi scientifica dei dati di Azure Machine Learning

È necessario prendere alcune decisioni quando si configura un ambiente di analisi scientifica dei dati di Azure Machine Learning. Le decisioni prese si baseranno sul tipo, sulla dimensione e sulla posizione di origine dei dati e sulla destinazione di tali dati nel cloud. Il processo di analisi scientifica dei dai cloud è una serie di attività end-to-end che consentono di accedere dai dati originali in una specifica origine attraverso la creazione e la pubblicazione di un modello come un servizio Web di Azure da utilizzare nelle applicazioni.

Il flusso di lavoro del processo di analisi scientifica dei dati cloud è presentato in **[Mappa del processo di analisi scientifica dei dati cloud](machine-learning-data-science-how-to-create-machine-learning-service.md)**. Per ulteriori informazioni sui singoli passaggi del processo di analisi scientifica dei dati, fare clic sulle voci pertinenti nella [mappa](machine-learning-data-science-how-to-create-machine-learning-service.md).

In questo articolo vengono illustrate le domande da considerare durante la configurazione dell'ambiente di analisi scientifica dei dati cloud, enumerate le risorse e gli strumenti utili per questo processo e vengono fornite indicazioni su come utilizzare la mappa del processo di analisi scientifica dei dati cloud.

## Domande da considerare

Prima di iniziare a creare l'ambiente di analisi scientifica dei dati cloud, considerare le domande seguenti.

1. **Dove si trovano i dati?** (il percorso fa riferimento all'***origine dei dati***). Ad esempio:
	- I dati sono disponibili a un indirizzo HTTP.
	- I dati si trovano in un percorso locale/di rete.
	- I dati si trovano in un database di SQL Server.
	- I dati sono archiviati in un contenitore di archiviazione di Azure.
2. **Come sono formattati i dati?** Ad esempio:
    - File delimitati da virgole o separati da tabulazioni, non compressi.
    - File delimitati da virgole o separati da tabulazioni, compressi.
	- BLOB di Azure compressi o non compressi.
	- Tabelle di SQL Server.
3. **Quanto sono grandi i dati?**
    - Piccoli: meno di 2 GB
    - Medi: compresi tra 2 GB e 10 GB
	- Grandi: più di 10 GB
	- Molto grandi: centinaia di GB
4. **Quanto si conoscono i dati?**
    - È necessario esplorare i dati per individuarne lo schema, le distribuzioni di variabili, i valori mancanti e così via? 
	- I dati necessitano di pre-elaborazione o pulizia prima di poter essere trasformati in una rappresentazione tabulare? 
5. **Si prevede (o si è in grado) di spostare tutti i dati in Azure?**
    - Sì, si sta pianificando di copiare l'intero set di dati nel cloud per l'elaborazione.
	- No, solo un set di dati verrà copiato in Azure.
6. **Qual è la destinazione del cloud di Azure preferita?** Ad esempio:
	- Spostare dati nei BLOB di archiviazione Azure.
	- Archiviare i dati su dischi rigidi virtuali di Azure montabili.
	- Caricare i dati in un database di SQL Server in una macchina virtuale di Azure.
	- Mapping dei dati in tabelle Hive di Azure HDInsight.

## Risorse di analisi scientifica dei dati cloud in Azure

A seconda dello scenario, potrebbe essere necessario quanto segue:

1.  Strumenti di Azure: [Azure PowerShell SDK](../install-configure-powershell.md), [Esplora archivi Azure](http://azurestorageexplorer.codeplex.com/), [AzCopy](../storage-use-azcopy.md) e altri
2.  Macchine virtuali di Azure che eseguono SQL Server
3.  Azure HDInsight (Hadoop)
4.  Reti virtuali di Azure per la condivisione di file di Azure locale
5.  Azure Data Factory per lo spostamento dei dati pianificato


## Come utilizzare la mappa del processo di analisi scientifica dei dati

La [mappa del processo di analisi dei dati cloud](machine-learning-data-science-how-to-create-machine-learning-service.md) offre un'ampia gamma di esercizi sull'analisi scientifica dei dati. La mappa mostra i passaggi fondamentali previsti in un tipico flusso di lavoro di analisi scientifica dei dati. Non tutti i passaggi sono necessari in ogni esercizio di analisi scientifica dei dati. Inoltre, il processo è iterativo per natura e la sequenza dei passaggi potrebbe variare in un determinato esercizio. Le risposte alle domande poste sopra aiuteranno l'utente a decidere i passaggi riguardanti il proprio caso, quando sono necessari nel processo e le condizioni in cui le iterazioni dei passaggi sono necessarie.

Per scenari di esempio basati sulle dimensioni dei dati originali, sul percorso di origine dei dati e sull'archivio di destinazione in Azure, vedere [Processo di analisi scientifica dei dati in Azure Machine Learning](../hmachine-learning-data-science-plan-sample-scenarios.md).



<!--HONumber=49--> 