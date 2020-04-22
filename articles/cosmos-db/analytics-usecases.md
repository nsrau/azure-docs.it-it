---
title: Casi d'uso per l'analisi incorporata con Azure Cosmos DB.
description: Informazioni su come usare l'analisi incorporata con Azure Cosmos DB in diversi casi d'uso.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: adf1db2d737de7004b5ab71c8e0e44d61d1da8c9
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768506"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Casi d'uso per l'analisi incorporata con Azure Cosmos DBUse-cases for built-in analytics with Azure Cosmos DB

The following use-cases can be achieved by using the built-in analytics with Apache Spark in Azure Cosmos DB:

## <a name="htap-scenarios"></a>Scenari HTAP

L'analisi integrata in Azure Cosmos DB può essere usata per:Built-in analytics in Azure Cosmos DB can be used to:

* Rilevare le frodi durante l'elaborazione delle transazioni.
* Fornisci consigli agli acquirenti mentre esplorano un negozio eCommerce.
* Avvisare gli operatori dell'imminente guasto di un pezzo critico di apparecchiature di produzione.
* Crea informazioni rapide e fruibili incorporando analisi in tempo reale direttamente sui dati operativi.

Azure Cosmos DB supporta scenari HTAP (Hybrid Transactional/Analytical Processing) usando Apache Spark incorporato in modo nativo. Azure Cosmos DB rimuove la separazione operativa e analitica fornita con i sistemi tradizionali.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Data lake distribuito a livello globale senza richiedere alcun ETL

Con Apache Spark integrato in modo nativo, Azure Cosmos DB offre un modo rapido, semplice e scalabile per creare un data lake distribuito a livello globale che fornisca una singola immagine del sistema. I dati multimodello distribuiti a livello globale eliminano la necessità di investire in costose pipeline ETL e scale on-demand, rivoluzionando il modo in cui i team di dati analizzano i propri set di dati.

## <a name="time-series-analytics-over-historic-data"></a>Analisi delle serie temporali sui dati storici

In alcuni casi, potrebbe essere necessario rispondere a domande basate su dati come in un momento specifico rispetto agli eventi completati in passato. Ad esempio, per ottenere il conteggio degli stati degli impegno CRM in una determinata data. Se il report fosse stato eseguito una settimana fa, il conteggio degli stati sarebbe in base allo stato di ogni attività in quel momento. L'esecuzione dello stesso rapporto di oggi vi darà il conteggio delle attività i cui stati sono come sono oggi, che possono essere cambiati dalla scorsa settimana, come passano attraverso il loro ciclo di vita da aperto a chiusura. Pertanto, è necessario creare report sullo snapshot in ogni fase del ciclo di vita del caso.

Con Azure Cosmos DB, gli utenti hanno la possibilità di implementare il concetto di viaggio nel tempo, essendo in grado di eseguire query ed eseguire analisi sui dati in modo retrospettivo e chiedere come i dati hanno guardato a un momento specifico nella storia. Ciò significa che gli utenti possono visualizzare facilmente sia le viste attuali che storiche dei dati ed eseguire analisi su di essi.

## <a name="globally-distributed-machine-learning-and-ai"></a>Apprendimento automatico e AI distribuiti a livello globale

Poiché le aziende si contendono volumi di dati in rapida crescita e una varietà in espansione di tipi di dati e formati, la capacità di ottenere informazioni più approfondite e accurate diventa quasi impossibile su scala petabyte in tutto il mondo. Con Apache Spark integrato in modo nativo, Azure Cosmos DB offre una piattaforma di analisi distribuita a livello globale che offre un'ampia libreria di algoritmi di apprendimento automatico. È possibile usare notebook Jupyter interattivi per creare e addestrare modelli e funzionalità di gestione dei cluster. Queste funzionalità consentono di eseguire il provisioning di cluster Spark altamente sintonizzati ed elastici automaticamente su richiesta.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Deep Learning sui dati distribuiti a livello globale multimodello

Il deep learning è il modo ideale per fornire soluzioni di analisi predittiva dei Big Data, in quanto il volume e la complessità dei dati continuano a crescere. Con il deep learning, le aziende possono sfruttare la potenza dei dati non strutturati e semistrutturati per fornire casi d'uso che sfruttano tecniche come l'ia, l'elaborazione del linguaggio naturale e altro ancora.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Creazione di report (integrazione con Power Apps, Power BI)

Power BI offre visualizzazioni interattive con funzionalità di business intelligence self-service, consentendo agli utenti finali di creare report e dashboard autonomamente. Usando il connettore Spark incorporato, è possibile connettere Power BI Desktop ai cluster Apache Spark in Azure Cosmos DB. Questo connettore consente di usare query diretta per eseguire l'offload dell'elaborazione in Apache Spark in Azure Cosmos DB, che è ideale quando si dispone di una quantità enorme di dati che non si vuole caricare in Power BI o quando si vuole eseguire analisi quasi in tempo reale.

## <a name="iot-analytics-at-global-scale"></a>Analisi IoT su scala globale

I dati generati dall'aumento dei sensori di rete offrono una visibilità senza precedenti nei sistemi e nei processi precedentemente opachi. La chiave è trovare informazioni utili in questo torrente di informazioni indipendentemente da dove i dispositivi IoT sono distribuiti in tutto il mondo. Azure Cosmos DB consente alle aziende IOT di analizzare sensori ad alta velocità e dati di serie temporali in tempo reale in qualsiasi parte del mondo. Consente di sfruttare il vero valore di un mondo interconnesso per offrire migliori esperienze ai clienti, efficienze operative e nuove opportunità di guadagno.

## <a name="stream-processing-and-event-analytics"></a>Elaborazione del flusso e analisi degli eventi 

Dai file di log ai dati dei sensori, le aziende hanno sempre più la necessità di far fronte a "flussi" di dati. Questi dati arrivano in un flusso costante, spesso da più fonti contemporaneamente. Sebbene sia possibile archiviare questi flussi di dati su disco e analizzarli in modo retrospettivo, a volte può essere ragionevole o importante elaborare e agire sui dati non appena arrivano. Ad esempio, i flussi di dati relativi alle transazioni finanziarie possono essere elaborati in tempo reale per identificare e rifiutare le transazioni potenzialmente fraudolente.

## <a name="interactive-analytics"></a>Analisi interattiva

Oltre a eseguire query predefinite per creare dashboard statici per le vendite, la produttività o i prezzi delle azioni, è possibile esplorare i dati in modo interattivo. L'analisi interattiva consente di porre domande, visualizzare i risultati, modificare la domanda iniziale in base alla risposta o approfondire i risultati. Apache Spark in Azure Cosmos DB supporta le query interattive rispondendo e adattandosi rapidamente.

## <a name="data-exploration-using-jupyter-notebooks"></a>Esplorazione dei dati con taccuini Jupyter

Quando si dispone di un nuovo set di dati, prima di approfondire l'esecuzione di modelli e test, è necessario esaminare i dati. In altre parole, è necessario eseguire l'analisi esplorativa dei dati. L'esplorazione dei dati può prendere in tempo in mente diverse decisioni. Ad esempio, è possibile trovare dettagli quali i metodi appropriati per l'utilizzo sui dati, se i dati soddisfano determinati presupposti di modellazione, se i dati devono essere puliti, ristrutturati e così via. Usando i notebook Jupyter e Apache Spark predefiniti di Azure Cosmos DB, è possibile eseguire analisi esplorative e transazionali rapida ed efficace dei dati.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare questi casi d'uso agli articoli seguenti:

* [Blocchi appunti Jupyter incorporati in Azure Cosmos DBBuilt-in Jupyter notebooks in Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Come abilitare i blocchi appunti per gli account Cosmos di AzureHow to enable notebooks for Azure Cosmos accounts](enable-notebooks.md)
* [Creare un blocco appunti per analizzare e visualizzare i dati](create-notebook-visualize-data.md)