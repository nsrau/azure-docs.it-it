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
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Caso di utilizzo - Profilo clienti

Data factory di Azure è uno dei numerosi servizi disponibili per implementare i Solution Accelerator inclusi nella suite Cortana Analytics. Per ulteriori informazioni su Cortana Analytics, visitare[Cortana Analytics Suite](http://www.microsoft.com/cortanaanalytics). In questo documento viene descritto un caso di semplice utilizzo che consente di iniziare a capire come Data Factory di Azure può risolvere problemi comuni di analisi.

Per poter accedere e provare questo caso di semplice utilizzo si necessita di una[sottoscrizione Azure](https://azure.microsoft.com/pricing/free-trial/). È possibile distribuire un esempio che implementi questo caso di utilizzo seguendo i passaggi descritti nell’articolo[Esempi](data-factory-samples.md).

## Scenario

Contoso è una società di giochi online che crea giochi per più piattaforme: console di gioco, dispositivi palmari e personal computer (PC). Quando gli utenti giocano a questi giochi, vengono prodotti grandi volumi di dati di registro che tengono traccia dei modelli di utilizzo, dello stile di gioco e delle preferenze dell'utente. Se combinati con dati demografici, regionali e dati del prodotto, Contoso può eseguire l'analisi per migliorare l'esperienza degli utenti e indirizzarli ad aggiornamenti e acquisti utili al gioco.

L’obiettivo di Contoso consiste nell'identificare opportunità di up-selling e cross-selling basate sul profilo cronologia giochi degli utenti e sviluppare nuove funzionalità interessanti per promuovere la crescita del business e fornire un'esperienza migliore ai clienti. In questo caso, i rivenditori online verranno presi come un esempio di azienda che desidera ottimizzare la propria offerta per gli utenti, ma i principi espressi sono validi per qualsiasi azienda che desideri attrarre l'attenzione dei clienti sui propri beni e servizi e migliorare la loro esperienza.

## Problematiche

Sono molte le problematiche che le società di giochi online devono affrontare quando tentano di implementare un caso d'uso di questo tipo. In primo luogo, devono riuscire a integrare dati di diverse forme e dimensioni provenienti da più origini dati, sia in locale sia nel cloud, in modo da poter acquisire dati sui prodotti, dati sui comportamenti dei clienti e dati sugli utenti mentre questi giocano su piattaforme multiple. In secondo luogo, i modelli di utilizzo dei giochi devono essere calcolati accuratamente e in modo corretto. Le società di giochi online, infine, devono poter misurare l'efficacia del loro approccio monitorando il tasso di conversione dei clic in vendite (up-selling e cross-selling) e apportare eventuali modifiche alle future campagne promozionali..

## Panoramica della soluzione

Questo semplice caso di utilizzo esemplifica la modalità di utilizzo di Azure Data Factory per inserire, preparare, trasformare, analizzare e pubblicare i dati.

![Flusso di lavoro end-to-end](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)Nella figura precedente viene illustrato come vengono visualizzate le pipeline di dati nell'interfaccia utente del portale di Azure classico dopo la distribuzione.

1.	**PartitionGameLogsPipeline** legge gli eventi del gioco non elaborati da una risorsa di archiviazione BLOB e crea partizioni basate su anno, mese e giorno.
2.	**EnrichGameLogsPipeline** unisce gli eventi di giochi partizionati con dati di riferimento del codice geografico e arricchisce i dati eseguendo il mapping di indirizzi IP per le aree geografiche corrispondenti.
3.	La pipeline **AnalyzeMarketingCampaignPipeline** si avvale dei dati migliorati e li elabora con i dati pubblicitari per creare l'output finale contenente l'efficacia della campagna di marketing.

In questo esempio di caso di utilizzo, Azure Data Factory viene utilizzata per gestire le attività che copiano i dati di input, trasformare ed elaborare i dati con le attività di HDInsight (trasformazioni Hive e Pig) e produrre i dati finali per un Database SQL di Azure. È anche possibile visualizzare la rete di pipeline di dati, gestirli e monitorarne lo stato dall'interfaccia utente.

## Vantaggi

Per ottimizzare l’analisi del profilo dell'utente e allinearla agli obiettivi aziendali, le società giochi online sono in grado di raccogliere rapidamente modelli di utilizzo e analizzare l'efficacia delle campagne pubblicitarie per tutti i propri diversi giochi.

<!---HONumber=AcomDC_0128_2016-->