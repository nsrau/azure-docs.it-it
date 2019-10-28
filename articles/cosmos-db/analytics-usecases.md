---
title: Casi d'uso per analisi predefinite con Azure Cosmos DB.
description: Informazioni su come usare le analisi predefinite con Azure Cosmos DB in diversi casi d'uso.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757071"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Casi d'uso per analisi predefinite con Azure Cosmos DB

È possibile ottenere i seguenti casi d'uso usando l'analisi incorporata con Apache Spark in Azure Cosmos DB:

## <a name="htap-scenarios"></a>Scenari HTAP

L'analisi incorporata in Azure Cosmos DB può essere usata per:

* Rilevare le frodi durante l'elaborazione della transazione.
* Fornire consigli agli acquirenti durante l'esplorazione di un negozio di ECommerce.
* Gli operatori di avvisi si verificano in caso di errore imminente di una parte critica di apparecchiature di produzione.
* Crea informazioni rapide e praticabili incorporando le analisi in tempo reale direttamente sui dati operativi.

Azure Cosmos DB supporta scenari di elaborazione transazionale/analitici (HTAP) ibridi usando la Apache Spark incorporata in modo nativo. Azure Cosmos DB rimuove la separazione operativa e analitica fornita con i sistemi tradizionali.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Data Lake distribuito a livello globale senza richiedere alcuna ETL

Con Apache Spark incorporato in modo nativo, Azure Cosmos DB fornisce un modo rapido, semplice e scalabile per creare un data Lake distribuito a livello globale che fornisce un'unica immagine del sistema. I dati multimodello distribuiti a livello globale eliminano la necessità di investire in pipeline ETL costose e scalabilità su richiesta, rivoluzionando il modo in cui i team di dati analizzano i set di dati.

## <a name="time-series-analytics-over-historic-data"></a>Analisi delle serie temporali rispetto ai dati cronologici

In alcuni casi, potrebbe essere necessario rispondere a domande basate sui dati come in un momento specifico per gli eventi completati in precedenza. Ad esempio, per ottenere il conteggio degli Stati delle attività CRM in una determinata data. Se il report è stato eseguito una settimana fa, il numero di stati corrisponderebbe a quello degli Stati di ogni attività in quel momento. L'esecuzione dello stesso report consente di ottenere il conteggio delle attività i cui Stati sono così come sono oggi, che potrebbero essere cambiate dall'ultima settimana, man mano che passano attraverso il ciclo di vita da aperto a chiuso. Quindi, è necessario creare un report sullo snapshot in ogni fase del ciclo di vita del case.

Negli scenari di data warehouse tradizionali, il concetto di snapshot non è possibile perché i data warehouse non sono progettati per incorporarla e i dati forniscono solo una visualizzazione corrente delle operazioni eseguite. Con Azure Cosmos DB, gli utenti hanno la possibilità di implementare il concetto di viaggio in tempo, in grado di eseguire query ed eseguire analisi sui dati in modo retrospettivo e di visualizzare il modo in cui i dati hanno esaminato un momento specifico nella cronologia. Ciò significa che gli utenti possono visualizzare con facilità le visualizzazioni correnti e storiche dei dati ed eseguire analisi su di essi.

## <a name="globally-distributed-machine-learning-and-ai"></a>Apprendimento automatico distribuito a livello globale e intelligenza artificiale

Poiché le aziende si contendono la crescita rapida di volumi di dati e una vasta gamma di tipi di dati e formati, la possibilità di ottenere informazioni più dettagliate e accurate diventa quasi impossibile su scala petabyte in tutto il mondo. Con Apache Spark incorporato in modo nativo, Azure Cosmos DB offre una piattaforma di analisi distribuita a livello globale che offre una vasta libreria di algoritmi di machine learning. È possibile usare i Notebook Interactive Jupyter per compilare ed eseguire il training di modelli e funzionalità di gestione dei cluster. Queste funzionalità consentono di effettuare il provisioning di cluster Spark altamente ottimizzati e elastici automatici su richiesta.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Apprendimento avanzato su dati distribuiti a livello globale a più modelli

L'apprendimento avanzato è il modo ideale per fornire Big Data soluzioni di analisi predittiva, in quanto il volume e la complessità dei dati continuano a crescere. Grazie alla formazione approfondita, le aziende possono sfruttare la potenza dei dati non strutturati e semistrutturati per fornire casi d'uso che sfruttano tecniche quali intelligenza artificiale, elaborazione del linguaggio naturale e altro ancora.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Creazione di report (integrazione con Power Apps, Power BI)

Power BI offre visualizzazioni interattive con le funzionalità self-service di business intelligence, consentendo agli utenti finali di creare report e dashboard in modo autonomo. Con il connettore Spark incorporato è possibile connettere Power BI Desktop ai cluster Apache Spark in Azure Cosmos DB. Questo connettore consente di usare la query diretta per eseguire l'offload dell'elaborazione per Apache Spark in Azure Cosmos DB, che è molto utile quando si dispone di una grande quantità di dati che non si desidera caricare in Power BI o quando si desidera eseguire l'analisi quasi in tempo reale.

## <a name="iot-analytics-at-global-scale"></a>Analisi Internet delle cose su scala globale

I dati generati dall'aumento dei sensori di rete portano a una visibilità senza precedenti nei sistemi e nei processi precedentemente opachi. La chiave consiste nel trovare informazioni dettagliate di utilità pratica in questo torrent di informazioni indipendentemente dalla posizione in cui vengono distribuiti i dispositivi Internet in tutto il mondo. Azure Cosmos DB consente alle aziende di tutto il tempo di analizzare il sensore ad alta velocità e i dati delle serie temporali in tempo reale in qualsiasi parte del mondo. Consente di sfruttare il vero valore di un mondo interconnesso per offrire migliori esperienze dei clienti, efficienza operativa e nuove opportunità di ricavi.

## <a name="stream-processing-and-event-analytics"></a>Elaborazione di flussi e analisi degli eventi 

Dai file di log ai dati dei sensori, le aziende hanno sempre la necessità di gestire i "flussi" di dati. Questi dati arrivano in un flusso stabile, spesso da più origini simultaneamente. Sebbene sia possibile archiviare questi flussi di dati su disco e analizzarli in modo retrospettivo, può essere talvolta sensato o importante elaborare e agire sui dati in arrivo. Ad esempio, i flussi di dati correlati a transazioni finanziarie possono essere elaborati in tempo reale per identificare e rifiutare transazioni potenzialmente fraudolente.

## <a name="interactive-analytics"></a>Analisi interattiva

Oltre a eseguire query predefinite per creare dashboard statici per vendite, produttività o prezzi azionari, può essere utile esplorare i dati in modo interattivo. Le analisi interattive consentono di porre domande, visualizzare i risultati, modificare la domanda iniziale in base alla risposta o analizzare i risultati in modo più approfondito. Apache Spark in Azure Cosmos DB supporta le query interattive rispondendo e adattando rapidamente.

## <a name="data-exploration-using-jupyter-notebooks"></a>Esplorazione dei dati con notebook di Jupyter

Quando si dispone di un nuovo set di dati, prima di approfondire i modelli e i test in esecuzione, è necessario esaminare i dati. In altre parole, è necessario eseguire l'analisi esplorativa dei dati. L'esplorazione dei dati può informare diverse decisioni. Ad esempio, è possibile trovare dettagli quali i metodi che è opportuno usare nei dati, se i dati soddisfano determinati presupposti di modellazione, se i dati devono essere puliti, ristrutturati e così via. Grazie all'utilizzo dei notebook Jupyter e Apache Spark predefiniti di Azure Cosmos DB, è possibile eseguire un'analisi esplorativa rapida ed efficace sui dati transazionali e analitici.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare questi casi d'uso, vedere gli articoli seguenti:

* [Notebook Jupyter predefiniti in Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Come abilitare i notebook per gli account Azure Cosmos](enable-notebooks.md)
* [Creare un notebook per analizzare e visualizzare i dati](create-notebook-visualize-data.md)