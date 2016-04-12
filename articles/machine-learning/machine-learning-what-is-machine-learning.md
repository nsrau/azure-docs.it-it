<properties
    pageTitle="Cos'è Machine Learning in Azure? | Microsoft Azure"
    description="Illustra i concetti di base del servizio Machine Learning completamente gestito, una tecnologia cloud che è possibile usare per creare, rendere operative e monetizzare le soluzioni."
	keywords="Informazioni sull'apprendimento automatico, tecnologia cloud, predittiva, informazioni sull'analisi predittiva, operatività"
	services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="02/02/2016"
    ms.author="cgronlun;tedway;olgali"/>


# Introduzione all'apprendimento automatico in Microsoft Azure

## Che cos'è l'apprendimento automatico?

L'apprendimento automatico usa i computer per eseguire modelli predittivi che apprendono dai dati esistenti per prevedere comportamenti, risultati e tendenze futuri.

Queste previsioni o stime dell'apprendimento automatico possono rendere più intelligenti le app e i dispositivi. Quando si effettuano acquisti online, l'apprendimento automatico consente di consigliare altri prodotti che potrebbero interessare in base a ciò che si è acquistato. Quando si usa la carta di credito, l'apprendimento automatico confronta la transazione con un database di transazioni e consente alla banca di rilevare eventuali frodi.

## Cos'è Machine Learning in Microsoft Azure?

Azure Machine Learning è un potente servizio di analisi predittive basate sul cloud che consente di creare e distribuire rapidamente modelli predittivi come soluzioni di analisi.

Azure Machine Learning fornisce non solo gli strumenti per l'analisi predittiva dei modelli, ma anche un servizio completamente gestito che è possibile usare per distribuire i modelli predittivi come servizi Web pronti all'uso. Azure Machine Learning fornisce gli strumenti per creare soluzioni di analisi predittiva complete nel cloud: creare rapidamente, testare, rendere operativi e gestire i modelli predittivi. Non è necessario acquistare un hardware né gestire manualmente macchine virtuali.

![Che cos'è l'apprendimento automatico? Flusso di lavoro di base per rendere operativa l'analisi predittiva in Azure Machine Learning.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Cos'è l'analisi predittiva?

L'analisi predittiva usa svariate tecniche statistiche, in questo caso l'apprendimento automatico, per analizzare i dati raccolti o correnti per individuare schemi o tendenze e prevedere così gli eventi futuri.

Azure Machine Learning è uno strumento davvero potente per eseguire l'analisi predittiva: è possibile lavorare da una libreria di algoritmi pronta all'uso, creare modelli su un PC connesso a Internet senza acquistare altre apparecchiature o infrastrutture e distribuire rapidamente la soluzione predittiva. Esempi e soluzioni pronti all'uso sono disponibili anche in [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) o in [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/).

## Compilare soluzioni di apprendimento automatico complete nel cloud

Azure Machine Learning include tutto quanto è necessario per creare soluzioni di analisi predittiva nel cloud, ad esempio una libreria di algoritmi di grandi dimensioni, uno studio per compilare modelli e un semplice strumento per distribuire il modello come servizio Web.

### Machine Learning Studio: creare modelli predittivi

È possibile creare modelli predittivi in [Machine Learning Studio](machine-learning-what-is-ml-studio.md), uno strumento basato sul browser, trascinando, rilasciando e connettendo i moduli.

![Cos'è l'analisi predittiva: esempio di esperimento di analisi predittiva in Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* Usare una grande libreria di [moduli e algoritmi di Machine Learning](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) in Machine Learning Studio per implementare rapidamente i modelli predittivi. Scegliere da una libreria esperimenti di esempio, pacchetti R e Python e i migliori algoritmi di aziende Microsoft, come Xbox e Bing. Estendere i moduli di Studio con gli script [R](machine-learning-r-quickstart.md) e [Python](machine-learning-execute-python-scripts.md) personalizzati.
* In [Cortana Intelligence Gallery](machine-learning-gallery-how-to-use-contribute-publish.md) è possibile provare soluzioni di analisi create da altri utenti o pubblicare i propri servizi di Azure, tra cui Machine Learning, HDInsight (Hadoop), analisi di flusso, Analisi Data Lake, nonché i servizi di gestione dati e archivi Big Data di Azure. Inviare domande o commenti sugli esperimenti nella community oppure condividere collegamenti a esperimenti tramite social network come LinkedIn e Twitter.  

	![Provare gli esperimenti predittivi o pubblicare il proprio esperimento in Azure Cortana Intelligence Gallery](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-analytics-gallery.png)

### Rendere operative le soluzioni di analisi predittiva: acquistare servizi Web o pubblicare i propri

* Acquistare servizi Web pronti all'uso da [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), ad esempio Recommendations, Text Analytics e Anomaly Detection.

* Rendere operativi i modelli di analisi predittiva:
    * [Distribuire i servizi Web](machine-learning-publish-a-machine-learning-web-service.md)
    * [Eseguire e ripetere il training dei modelli tramite le API](machine-learning-retrain-models-programmatically.md)
    * [Gestire gli endpoint di servizio Web](machine-learning-create-endpoint.md)
    * [Scalare i servizi Web](machine-learning-scaling-endpoints.md)
    * [Utilizzare i servizi Web](machine-learning-consume-web-services.md)

## Concetti e terminologia chiave dell'apprendimento automatico
### Esplorazione dei dati, analisi descrittiva e analisi predittiva

L'**esplorazione dei dati** è il processo di raccolta di informazioni su un set di dati di grandi dimensioni e spesso non strutturato per trovare caratteristiche che consentano di eseguire un'analisi mirata. Il **data mining** è l'esplorazione dei dati automatizzata.

L'**analisi descrittiva** è il processo di analisi di un set di dati per riepilogare cosa è accaduto. La maggior parte delle analisi business, ad esempio i report sulle vendite, le metriche Web e l'analisi dei social network, è descrittiva.

L'**analisi predittiva** è il processo di compilazione di modelli da dati cronologici o correnti per prevedere i futuri risultati.


### Apprendimento supervisionato e non supervisionato
 Il training degli algoritmi di **apprendimento supervisionato** viene eseguito con dati con etichette, ovvero dati costituiti da esempi delle risposte desiderate. Ad esempio, il training di un modello che identifica l'uso fraudolento di una carta di credito verrà eseguito da un set di dati in cui i punti dati indicanti le spese valide e quelle fraudolente note sono stati etichettati. La maggior parte dell'apprendimento automatico viene supervisionato.

 L'**apprendimento non supervisionato** viene usato sui dati senza etichette con l'obiettivo di trovare relazioni tra i dati. Ad esempio, è possibile trovare raggruppamenti di dati demografici relativi ai clienti con abitudini di acquisto simili.

### Training e valutazione del modello
Un modello di apprendimento automatico è un'astrazione della domanda a cui si sta cercando di rispondere o del risultato che si desidera stimare. Il training e la valutazione dei modelli vengono effettuati a partire da dati esistenti.

#### Training da dati
In Azure Machine Learning, un modello viene compilato da un modulo di algoritmo che elabora i dati di training e i moduli funzionali, ad esempio un modulo di assegnazione dei punteggi.

Nell'apprendimento supervisionato, se si sta eseguendo il training di un modello di rilevamento delle frodi, si userà un set di transazioni etichettate come fraudolente o valide. Si suddividerà in modo casuale il set di dati e si userà una parte per eseguire il training del modello e una parte per testare o valutare il modello.

#### Dati di valutazione
Dopo aver creato un modello con training, valutare il modello usando i rimanenti dati di test. Si usano dati i cui risultati sono già noti, in modo da poter verificare se la stima del modello sia accurata.

### Altri termini comuni relativi all'apprendimento automatico

* **algoritmo**: set autonomo di regole usate per risolvere i problemi di elaborazione dati, calcolo o ragionamento automatizzato.
* **dati categorici **: dati organizzati in categorie e divisibili in gruppi. Ad esempio, un set di dati categorici per le auto può specificare l'anno, la marca, il modello e il prezzo.
* **classificazione**: modello per organizzare i punti dati in categorie in base a un set di dati di cui sono già noti i raggruppamenti in categorie.
* **progettazione delle funzioni**: processo di estrazione o selezione di funzioni correlate a un set di dati per integrare il set di dati e migliorare i risultati. Ad esempio, i dati delle tariffe aeree potrebbero essere integrati con giorni della settimana e festivi. Vedere [Progettazione e selezione di funzioni in Azure Machine Learning](machine-learning-feature-selection-and-engineering.md).
* **modulo**: elemento funzionale in un modello di Machine Learning Studio, ad esempio il modulo Enter Data che consente di immettere e modificare piccoli set di dati. Anche un algoritmo è un tipo di modulo in Machine Learning Studio.
* **modello**: per l'apprendimento supervisionato, un modello è il prodotto di un esperimento di apprendimento automatico costituito da un set di dati di training, un modulo di algoritmo e moduli funzionali, ad esempio il modulo Score Model.
* **dati numerici**: dati indicanti misure (dati continui) o conteggi (dati discreti). Sono detti anche *dati quantitativi*.
* **partizione**: metodo con cui si dividono i dati in campioni. Per altre informazioni, vedere [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx).
* **stima**: una stima è una previsione di uno o più valori da un modello di apprendimento automatico. Si potrebbe anche incontrare il termine "punteggio stimato". Tuttavia, i punteggi stimati non sono l'output finale di un modello. Il punteggio è seguito da una valutazione del modello.
* **regressione**: modello per la stima di un valore continuo basato su variabili indipendenti, ad esempio la stima del prezzo di un'auto basata sull'anno e sulla marca.
* **punteggio**: valore stimato generato da un modello di classificazione o regressione con training, usando il [modulo Score Model](https://msdn.microsoft.com/library/azure/dn905995.aspx) in Machine Learning Studio. I modelli di classificazione restituiscono inoltre un punteggio relativo alla probabilità del valore stimato. Una volta generati i punteggi da un modello, è possibile valutare l'accuratezza del modello con il [modulo Evaluate Model](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **campione**: parte di un set di dati rappresentativa dell'intero. I campioni possono essere selezionati in modo casuale o in base a funzioni specifiche del set di dati.



## Passaggi successivi
È possibile apprendere le nozioni di base sulle analisi predittive e sull'apprendimento automatico tramite un'[esercitazione dettagliata](machine-learning-create-experiment.md) e [gli esempi](machine-learning-sample-experiments.md).


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!---HONumber=AcomDC_0406_2016-->