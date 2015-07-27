<properties
   pageTitle="Cloud Cruiser e integrazione delle API di fatturazione di Microsoft Azure"
   description="Fornisce un punto di vista unico del partner di fatturazione di Microsoft Azure, Cloud Cruiser, sulle esperienze di integrazione delle API di fatturazione di Azure nel prodotto. Ciò è particolarmente utile per i clienti di Azure e Cloud Cruiser che sono interessati a utilizzare/provare Cloud Cruiser per Microsoft Azure Pack."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="06/17/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# Cloud Cruiser e integrazione delle API di fatturazione di Microsoft Azure 

In questo articolo viene descritto in che modo le informazioni raccolte dalle nuove API di fatturazione di Microsoft Azure possono essere utilizzate in Cloud Cruiser per l'analisi e la simulazione dei costi e l’analisi dei flussi di lavoro.

## API RateCard di Azure
L'API RateCard fornisce informazioni sui costi da Azure. Dopo l'autenticazione con le credenziali appropriate, è possibile eseguire query all’API per raccogliere i metadati relativi a servizi disponibili in Azure, insieme ai costi associati al proprio ID di offerta.

Di seguito viene fornita una risposta di esempio dell’API in cui sono mostrati i prezzi per l’istanza A0 (Windows):

    {       
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",       
		"MeterName": "Compute Hours",       
		"MeterCategory": "Virtual Machines",       
		"MeterSubCategory": "A0 VM (Windows)",       
		"Unit": "Hours",       
		"MeterRates": 
		{         
			"0": 0.029       
		},       
		"EffectiveDate": "2014-08-01T00:00:00Z",       
		"IncludedQuantity": 0.0     
	}, 

## Interfaccia di Cloud Cruiser per l’API RateCard di Azure
Cloud Cruiser può utilizzare le informazioni dell’API RateCard in modi diversi. In questo articolo verrà illustrato come può essere utilizzata per eseguire la simulazione dei costi e l’analisi del carico di lavoro IaaS.

Per illustrare questo caso di utilizzo, si immagini un carico di lavoro di diverse istanze in esecuzione in Microsoft Azure Pack (WAP). L'obiettivo è quello di simulare questo stesso carico di lavoro in Azure e di stimare i costi dell’esecuzione di tale migrazione. Per creare questa simulazione, è necessario eseguire due attività principali:

1. **Importare ed elaborare le informazioni sul servizio raccolti dall'API RateCard**: questa attività viene eseguita anche sulle cartelle di lavoro, dove l'estratto dall'API RateCard viene trasformato e pubblicato in un nuovo piano tariffario. Questo nuovo piano tariffario verrà utilizzato nelle simulazioni per stimare i prezzi di Azure.

2. **Normalizzare i servizi WAP e i servizi di Azure per IaaS**: per impostazione predefinita, i servizi WAP sono basati sulle singole risorse (CPU, dimensioni della, dimensioni del disco e così via) mentre i servizi di Azure si basano sulle dimensioni dell’istanza (A0, A1, A2, ecc.). Questa prima attività può essere eseguita dal motore ETL di Cloud Cruiser, denominato cartelle di lavoro, in cui queste risorse possono essere inserite in dimensioni di istanza, in modo analogo ai servizi di istanza di Azure.

## Importare dati dall’API RateCard

Le cartelle di lavoro di Cloud Cruiser consentono di raccogliere ed elaborare informazioni dall'API RateCard in modo automatico. Le cartelle di lavoro ETL (extract-transform-load) consentono di configurare la raccolta, la trasformazione e la pubblicazione dei dati nel database Cloud Cruiser.

Ogni cartella di lavoro può contenere una o più raccolte. In tal modo è possibile correlare le informazioni provenienti da origini diverse per integrare o estendere i dati di utilizzo. Nelle due schermate riportate di seguito, viene illustrata la creazione di una nuova *raccolta* in una cartella di lavoro esistente e l'importazione di informazioni nella *raccolta* dall'API RateCard:

![Figura 1 - Creazione di una nuova raccolta][1]

![Figura 2 - Importare i data dalla nuova raccolta][2]

Dopo aver importato i dati nella cartella di lavoro, è possibile creare più passaggi e processi di trasformazione per modificare e modellare i dati. Per questo esempio, poiché l’interesse è incentrato unicamente su IaaS, è possibile utilizzare la procedura di trasformazione per rimuovere le righe o i record non necessari correlati a servizi diversi da IaaS.

Nella schermata riportata di seguito viene illustrata la procedura di trasformazione utilizzata per elaborare i dati raccolti dall'API RateCard:

![Figura 3 - Procedura di trasformazione per elaborare i dati raccolti dall'API RateCard][3]

## Definizione di nuovi servizi e piani tariffari

Esistono diversi modi per definire i servizi in Cloud Cruiser. Una delle opzioni consiste nell’importare i servizi dai dati di utilizzo. Questo metodo viene in genere utilizzato quando si lavora con i cloud pubblici, in cui i servizi sono già definiti dal provider.

Un piano tariffario è un set di costi o i prezzi che possono essere applicati a diversi servizi, in base alle date di validità oppure a un gruppo di clienti, tra le altre opzioni. I piani tariffari possono essere utilizzati in Cloud Cruiser per creare una simulazione o scenari di simulazione, per comprendere in che modo le modifiche apportate ai servizi possano influire sul costo totale di un carico di lavoro.

In questo esempio, si utilizzeranno le informazioni dell'API RateCard per definire nuovi servizi in Cloud Cruiser. Nello stesso modo, è possibile utilizzare i costi associate ai servizi per creare un nuovo piano tariffario in Cloud Cruiser.

Al termine del processo di trasformazione, è possibile creare un nuovo passaggio e pubblicare i dati dell'API RateCard come nuovi servizi e costi.

![Figura 4 - Pubblicazione di dati dall'API RateCard come nuovi servizi e tariffe][4]

## Verificare i servizi e i costi di Azure

Dopo la pubblicazione di servizi e costi, è possibile verificare l'elenco dei servizi importati nella scheda *Servizi* di Cloud Cruiser:

![Figura 5 - Verifica dei nuovi servizi][5]

Nella scheda *Piani tariffari* è possibile controllare il nuovo piano tariffario denominato "AzureSimulation" con i costi importati dall'API RateCard.

![Figura 6 - Verifica dei nuovi piani tariffari e delle tariffe associate][6]

## Normalizzare i servizi WAP e Azure

Per impostazione predefinita, WAP fornisce informazioni sull'utilizzo basate sull'uso delle risorse di calcolo, memoria e rete. In Cloud Cruiser è possibile definire i servizi direttamente in base all'allocazione o all’utilizzo a consumo di queste risorse. È possibile, ad esempio, impostare un costo di base per ogni ora di utilizzo della CPU o addebitare i GB di memoria allocata a un'istanza.

Per questo esempio, al fine di confrontare i costi tra WAP e Azure, è necessario aggregare l'utilizzo delle risorse in bundle WAP, che possono quindi essere mappati ai servizi di Azure. Questa trasformazione può essere implementata facilmente nelle cartelle di lavoro:

![Figure 7 - Trasformazione dei dati WAP in servizi normalizzati][7]

L'ultimo passaggio nella cartella di lavoro consiste nel pubblicare i dati nel database Cloud Cruiser. Durante questo passaggio, i dati di utilizzo sono ora aggregati in servizi (che eseguono il mapping ai servizi di Azure) e associati a costi predefiniti per creare gli addebiti.

Dopo aver completato la cartella di lavoro, è possibile automatizzare l'elaborazione dei dati aggiungendo una nuova attività nell'utilità di pianificazione e specificando la frequenza e l'ora di esecuzione della cartella di lavoro.

![Figura 8 - Pianificazione della cartella di lavoro][8]

## Creare report per l'analisi di simulazione dei costi del carico di lavoro

Quando l'utilizzo viene raccolto e le spese vengono caricate nel database Cloud Cruiser, è possibile utilizzare il modulo Insights di Cloud Cruiser, uno strumento avanzato per la creazione di report, per creare la simulazione dei costi del carico di lavoro che si desidera.

Per illustrare questo scenario, è stato creato il report seguente:

![Confronto dei costi][9]

Nel grafico superiore è illustrato un confronto dei costi suddivisi per servizi e viene confrontato il costo dell’esecuzione del carico di lavoro per ogni specifico servizio tra WAP (blu scuro) e Azure (blu chiaro).

Nel grafico inferiore vengono illustrati gli stessi dati ma suddivisi per reparto, per illustrare i costi di ciascun reparto per eseguire il relativo carico di lavoro in WAP e Azure, insieme alle differenze tra i due (barra Risparmi, in verde)

## Passaggi successivi

+ Per istruzioni dettagliate sulla creazione di cartelle di lavoro e report Cloud Cruiser, fare riferimento alla [documentazione](http://docs.cloudcruiser.com/) online di Cloud Cruiser (è necessario un account di accesso valido). Per ulteriori informazioni su Cloud Cruiser, contattare [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Per una panoramica sulle API di utilizzo delle risorse e sulle API RateCard di Azure, vedere [Ottenere informazioni significative sul consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md). 
+ Per ulteriori informazioni su entrambe le API, appartenenti al set di API fornito da Gestione risorse di Azure, vedere il [riferimento all'API REST di fatturazione di Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).
+ Se si desidera approfondire il codice di esempio, vedere gli [esempi di codice dell'API di fatturazione di Microsoft Azure in Github](https://github.com/Azure/BillingCodeSamples).

## Ulteriori informazioni
+ Per ulteriori informazioni su Gestione risorse di Azure, vedere l'articolo [Panoramica su Gestione risorse di Azure](resource-group-overview.md).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figura 1 - Creazione di una nuova raccolta"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figura 2 - Importare i data dalla nuova raccolta"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 - Procedura di trasformazione per elaborare i dati raccolti dall'API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 - Pubblicazione di dati dall'API RateCard come nuovi servizi e tariffe"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 - Verifica dei nuovi servizi"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 - Verifica dei nuovi piani tariffari e delle tariffe associate"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figure 7 - Trasformazione dei dati WAP in servizi normalizzati"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8 - Pianificazione della cartella di lavoro"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - Report di esempio per lo scenario di confronto dei costi del carico di lavoro"

<!---HONumber=July15_HO3-->