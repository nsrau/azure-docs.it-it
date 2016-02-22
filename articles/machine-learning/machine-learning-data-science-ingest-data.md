<properties 
	pageTitle="Caricare i dati in ambienti di archiviazione per l'analisi | Microsoft Azure" 
	description="Spostamento dei dati da e verso l'archiviazione BLOB di Azure" 
	services="machine-learning,storage" 
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
	ms.date="02/08/2016" 
	ms.author="bradsev" />

# Caricare i dati in ambienti di archiviazione per l'analisi

## Introduzione

Cortana Analytics Process (CAP) richiede l'inserimento o il caricamento di dati in una vasta gamma di ambienti di archiviazione diversi per l'elaborazione e l'analisi nei modi più appropriati in ogni fase del processo. Le destinazioni dei dati più comunemente usate per l'elaborazione da parte del processo CAP includono l'archiviazione BLOB di Azure, i database di SQL Azure, SQL Server nella macchina virtuale di Azure, HDInsight (Hadoop) e Azure Machine Learning.

Questo **menu** si collega ad argomenti che descrivono come inserire dati in questi ambienti di destinazione dove i dati vengono archiviati ed elaborati.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Le esigenze aziendali e tecniche specifiche, nonché la posizione iniziale, il formato e le dimensioni dei dati determinano gli ambienti di destinazione in cui i dati devono essere inseriti per raggiungere gli obiettivi dell'analisi. Non è insolito per uno scenario richiedere lo spostamento dei dati tra ambienti diversi per disporre di tutte le attività necessarie per creare un modello predittivo. Questa sequenza di attività può includere, ad esempio, l'esplorazione dei dati, la pre-elaborazione, la pulizia, il sottocampionamento e il training del modello.

<!---HONumber=AcomDC_0211_2016-->