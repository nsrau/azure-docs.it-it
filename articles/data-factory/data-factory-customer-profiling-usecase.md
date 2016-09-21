<properties 
	pageTitle="Caso di utilizzo - Profilo clienti" 
	description="Informazioni su come utilizzare Data Factory di Azure per creare un flusso di lavoro basato sui dati (pipeline) per analizzare il profilo dei clienti di società di giochi." 
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
	ms.date="09/06/2016" 
	ms.author="spelluru"/>

# Caso di utilizzo - Profilo clienti

Azure Data Factory è uno dei numerosi servizi usati per implementare i Solution Accelerator di Cortana Intelligence Suite. Per ulteriori informazioni su Cortana Intelligence, vedere [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). In questo documento viene descritto un caso di semplice utilizzo che consente di iniziare a capire come Data Factory di Azure può risolvere problemi comuni di analisi.

Per poter accedere e provare questo semplice caso d'uso è necessaria una [sottoscrizione Azure](https://azure.microsoft.com/pricing/free-trial/). È possibile distribuire un esempio che implementi questo caso di utilizzo seguendo i passaggi descritti nell’articolo[Esempi](data-factory-samples.md).

## Scenario

Contoso è una società di giochi online che crea giochi per più piattaforme: console di gioco, dispositivi palmari e personal computer (PC). Quando gli utenti giocano a questi giochi, vengono prodotti grandi volumi di dati di registro che tengono traccia dei modelli di uso, dello stile di gioco e delle preferenze dell'utente. Se vengono combinati con dati demografici, regionali e del prodotto, Contoso può eseguire l'analisi per migliorare l'esperienza dei giocatori e indirizzarli ad aggiornamenti e acquisti utili al gioco.

L'obiettivo di Contoso consiste nell'identificare opportunità di up-selling o cross-selling basate sulla cronologia dei giochi degli utenti e aggiungere nuove funzionalità interessanti per promuovere la crescita del business e fornire un'esperienza migliore ai clienti. In questo caso, usiamo una società di giochi come esempio di società. La società desidera ottimizzare i giochi in base al comportamento dei giocatori. Questi principi si applicano a qualsiasi società che desideri coinvolgere i clienti nei suoi beni e servizi e migliorare la loro esperienza.

## Problematiche


## Panoramica della soluzione

Questo semplice caso di utilizzo esemplifica la modalità di utilizzo di Azure Data Factory per inserire, preparare, trasformare, analizzare e pubblicare i dati.

![Flusso di lavoro end-to-end](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Questa figura illustra come vengono visualizzate le pipeline di dati nel portale di Azure dopo la distribuzione.

1.	**PartitionGameLogsPipeline** legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB e crea partizioni basate su anno, mese e giorno.
2.	**EnrichGameLogsPipeline** unisce gli eventi di giochi partizionati con dati di riferimento del codice geografico e arricchisce i dati eseguendo il mapping di indirizzi IP per le aree geografiche corrispondenti.
3.	La pipeline **AnalyzeMarketingCampaignPipeline** usa i dati migliorati e li elabora con i dati pubblicitari per creare l'output finale contenente l'efficacia della campagna di marketing.

In questo esempio Data Factory viene usato per gestire le attività che copiano i dati di input, trasformare ed elaborare i dati e produrre i dati finali per un database SQL di Azure. È anche possibile visualizzare la rete di pipeline di dati, gestirli e monitorarne lo stato dall'interfaccia utente.

## Vantaggi

Per ottimizzare l'analisi del profilo dell'utente e allinearla agli obiettivi aziendali, le società di giochi online sono in grado di raccogliere rapidamente modelli di uso e analizzare l'efficacia delle campagne pubblicitarie.

<!---HONumber=AcomDC_0907_2016-->