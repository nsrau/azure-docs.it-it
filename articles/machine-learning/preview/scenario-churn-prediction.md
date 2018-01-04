---
title: Stima dell'abbandono dei clienti con Azure Machine Learning | Microsoft Docs
description: Come eseguire l'analisi dell'abbandono con Azure ML Workbench.
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 7db93786b71fb7876ae02fd8fd006a1e8e0c2271
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Stima dell'abbandono dei clienti con Azure Machine Learning

In media, conservare i clienti esistenti è cinque volte più economico rispetto al costo per trovarne di nuovi. Di conseguenza, i dirigenti di marketing cercano spesso di stimare la probabilità di abbandono da parte dei clienti e di individuare le azioni necessarie per ridurre al minimo il tasso di abbandono.

L'obiettivo di questa soluzione è quello di illustrare l'analisi predittiva dell'abbandono usando Azure Machine Learning Workbench. Questa soluzione fornisce un modello facile da usare per lo sviluppo di pipeline di dati predittivi dell'abbandono per i rivenditori. Il modello può essere usato con set di dati e definizioni di abbandono di diverso tipo. Gli obiettivi dell'esempio pratico sono i seguenti:

1. Comprendere gli strumenti di preparazione dei dati di Azure Machine Learning Workbench per la pulizia e l'inserimento di dati sulle relazioni con i clienti per l'analisi dell'abbandono.

2. Eseguire una trasformazione di funzionalità per gestire dati eterogenei non significativi.

3. Integrare librerie di terze parti (ad esempio `scikit-learn` e `azureml`) per lo sviluppo di classificatori bayesiani e basati su albero per la stima dell'abbandono.

4. Eseguire la distribuzione.

## <a name="link-of-the-gallery-github-repository"></a>Collegamento al repository GitHub delle raccolte
Di seguito è disponibile il collegamento al repository GitHub pubblico in cui è ospitato tutto il codice:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Panoramica del caso d'uso
Le aziende necessitano di una strategia efficace per gestire i casi di abbandono dei clienti. L'abbandono dei clienti include i clienti che smettono di usare un servizio, quelli che passano a un servizio della concorrenza e quelli che passano a un'esperienza di livello inferiore del servizio o riducono l'uso del servizio.

In questo caso d'uso vengono analizzati i dati della società di telecomunicazioni francese Orange per identificare i clienti che probabilmente abbandoneranno l'azienda a breve, al fine di migliorare il servizio e creare campagne personalizzate per la fidelizzazione dei clienti.

Le aziende nel settore delle telecomunicazioni operano in un mercato competitivo. Molti operatori perdono i ricavi provenienti da clienti abbonati a causa dell'abbandono. La possibilità di identificare in modo accurato le probabilità di abbandono dei clienti può quindi offrire un notevole vantaggio competitivo.

Alcuni dei fattori che contribuiscono all'abbandono da parte dei clienti nel settore delle telecomunicazioni sono:

* Interruzioni frequenti del servizio
* Esperienza non soddisfacente con il servizio clienti in punti vendita al dettaglio o online
* Offerte di altri operatori concorrenti (piano familiare migliore, piano dati e così via).

In questa soluzione verrà usato un esempio concreto di creazione di un modello predittivo per l'abbandono dei clienti per le aziende di telecomunicazioni.

## <a name="prerequisites"></a>Prerequisiti

* Un [account di Azure](https://azure.microsoft.com/free/) (sono disponibili versioni di valutazione gratuite).

* Una copia di [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installata seguendo la [guida introduttiva all'installazione](./quickstart-installation.md) per installare il programma e creare un'area di lavoro.

* Per l'operazionalizzazione, è consigliabile installare ed eseguire in locale un motore Docker. In caso contrario, è possibile usare l'opzione cluster, ma tenere presente che l'esecuzione di un servizio contenitore di Azure può essere costosa.

* Questa soluzione presuppone che Azure Machine Learning Workbench sia in esecuzione in Windows 10 con il motore Docker installato in locale. Se si usa macOS, le istruzioni sono in gran parte uguali.

## <a name="create-a-new-workbench-project"></a>Creare un nuovo progetto Workbench

Creare un nuovo progetto usando questo esempio come modello:
1.  Aprire Azure Machine Learning Workbench
2.  Nella pagina **Projects** (Progetti) fare clic sul segno **+** e selezionare **New Project** (Nuovo progetto)
3.  Nel riquadro **Create New Project** (Crea nuovo progetto) specificare le informazioni per il nuovo progetto
4.  Nella casella di ricerca **Search Project Templates** (Cerca modelli di progetto) digitare "Customer Churn Prediction" (Stima abbandono clienti) e selezionare il modello
5.  Fare clic su **Crea**

## <a name="data-description"></a>Descrizione dei dati

Il set di dati usato in questa soluzione è ricavato dalla competizione SIDKDD 2009. Il file è denominato `CATelcoCustomerChurnTrainingSample.csv` e si trova nella cartella [`data`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data). Il set di dati è costituito da dati eterogenei non significativi (variabili numeriche/categoriche) della società di telecomunicazioni francese Orange che sono stati resi anonimi.

Le variabili acquisiscono informazioni demografiche sui clienti, statistiche di chiamata (ad esempio la durata media delle chiamate, la percentuale di errori di chiamata e così via), informazioni sui contratti e statistiche relative ai reclami. La variabile relativa all'abbandono è binaria (0 - non ha abbandonato, 1 - ha abbandonato).

## <a name="scenario-structure"></a>Struttura dello scenario

La struttura di cartelle è la seguente:

__data__: contiene il set di dati usato nella soluzione  

__docs__: contiene tutte le esercitazioni pratiche

L'ordine delle esercitazioni pratiche per l'esecuzione della soluzione è il seguente:
1. Preparazione dei dati: il file principale correlato alla preparazione dei dati nella cartella data è `CATelcoCustomerChurnTrainingSample.csv`
2. Modellazione e valutazione: il file principale correlato alla modellazione e alla valutazione nella cartella radice è `CATelcoCustomerChurnModeling.py`
3. Modellazione e valutazione senza .dprep: il file principale per questa attività nella cartella radice è `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operazionalizzazione: i file principali per la distribuzione sono il modello (`model.pkl`) e `churn_schema_gen.py`

| Ordine| File Name | File correlati |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | "data/CATelcoCustomerChurnTrainingSample.csv" |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | "CATelcoCustomerChurnModeling.py" |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | 'CATelcoCustomerChurnModelingWithoutDprep.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | "model.pkl"<br>"churn_schema_gen.py" |

Seguire le esercitazioni nell'ordine indicato in precedenza.

## <a name="conclusion"></a>Conclusioni
Questo scenario pratico ha illustrato come stimare l'abbandono con Azure Machine Learning Workbench. Prima di tutto è stata eseguita la pulizia dei dati eterogenei e non significativi, seguita dalla progettazione delle funzionalità tramite gli strumenti di preparazione dei dati. Sono poi stati usati strumenti di apprendimento automatico open source per creare e valutare un modello di classificazione, quindi è stato usato un contenitore Docker locale per distribuire il modello rendendolo pronto per la produzione.
