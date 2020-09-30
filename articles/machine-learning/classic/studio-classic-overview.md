---
title: Che cosa è possibile fare con Machine Learning Studio (versione classica)? - Azure
description: Machine Learning Studio (versione classica) è uno strumento di trascinamento della selezione per la creazione rapida di modelli da una libreria di algoritmi e moduli pronti per l'uso.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: 24b7679c92b8f69b9406677ebe6355c0e1e51f55
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348239"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Che cosa è possibile fare con Machine Learning Studio (versione classica)?

**SI APPLICA A:**  ![sì](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica)   ![no](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Machine Learning Studio (versione classica) è uno strumento basato sul trascinamento della selezione che consente di creare, testare e distribuire modelli di Machine Learning. Studio (versione classica) pubblica modelli e servizi Web che possono essere facilmente utilizzati da app personalizzate o strumenti di business intelligence come Excel.


## <a name="studio-classic--interactive-workspace"></a>Area di lavoro interattiva di Studio (versione classica)

Per sviluppare un modello di analisi predittiva, in genere si usano dati provenienti da una o più origini, che vengono trasformati e analizzati tramite varie funzioni di modifica dei dati e statistiche e da cui viene generato un set di risultati. Lo sviluppo di un modello come questo è un processo iterativo. Man mano che si modificano le varie funzioni e i relativi parametri, i risultati convergono fino a quando l'utente non è soddisfatto del modello sottoposto a training.

Machine Learning Studio (versione classica) offre un'area di lavoro grafica e interattiva per eseguire facilmente le operazioni di compilazione, test e iterazione di un modello di analisi predittiva. È possibile trascinare la selezione di ***set di dati*** e ***moduli*** di analisi in un'area di disegno interattiva, collegandoli tra loro per ottenere un ***esperimento*** da eseguire in Machine Learning Studio (versione classica). Per eseguire l'iterazione della progettazione del modello, modificare l'esperimento, salvare eventualmente una copia e ripeterne l'esecuzione. Quando si è pronti, è possibile convertire l'***esperimento di training*** in un ***esperimento predittivo*** e quindi pubblicarlo come un ***servizio Web*** in modo che altri utenti possano accedere al modello.

Non sono necessarie operazioni di programmazione, è sufficiente collegare visivamente i set di dati e i moduli per costruire un modello di analisi predittiva.

![Diagramma di Machine Learning Studio (versione classica): Creare esperimenti, leggere dati per molte origini, scrivere dati con punteggio, scrivere modelli.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Scaricare il diagramma della panoramica di ML Studio (versione classica)
Scaricare il diagramma **Panoramica delle funzionalità di Microsoft ML Studio (versione classica)** per una panoramica generale delle funzionalità di Machine Learning Studio (versione classica). Per tenerlo a portata di mano, stampare il diagramma in formato tabloid (27 x 43 cm circa).

**Scaricare il diagramma qui: [Panoramica delle funzionalità di Microsoft Machine Learning Studio (versione classica)](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Panoramica delle funzionalità di Microsoft Machine Learning Studio (versione classica)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Componenti di un esperimento di Studio (versione classica)
Un esperimento è costituito da set di dati che forniscono i dati a moduli di analisi, che sono collegati tra loro per costruire un modello di analisi predittiva. In particolare, un esperimento valido ha le caratteristiche seguenti:

* Ha almeno un set di dati e un modulo.
* I set di dati possono essere collegati solo ai moduli.
* I moduli possono essere collegati a set di dati o ad altri moduli.
* Tutte le porte di input per i moduli devono avere un collegamento al flusso di dati.
* Tutti i parametri necessari per ogni modulo devono essere impostati.

È possibile creare un esperimento da zero oppure è possibile usare un esperimento di esempio esistente come modello. Per altre informazioni, vedere [Copiare gli esperimenti di esempio per creare nuovi esperimenti di Machine Learning](sample-experiments.md).

Per un esempio di creazione di un esperimento, vedere [Creare un semplice esperimento in Machine Learning Studio (versione classica)](create-experiment.md).

Per una procedura dettagliata più completa della creazione di una soluzione di analisi predittiva, vedere [Sviluppare una soluzione predittiva con Machine Learning Studio (versione classica)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Set di dati
Un set di dati include dati caricati in Machine Learning Studio (versione classica) per essere usati nel processo di modellazione. In Machine Learning Studio (versione classica) sono inclusi alcuni set di dati di esempio per provare a usare il programma. È possibile caricare altri set di dati in base alle esigenze. Ecco alcuni esempi di set di dati inclusi:

* **MPG data for various automobiles** : valori di consumo del carburante per le automobili identificate in base a numero di cilindri, potenza e così via.
* **Breast cancer data** : dati relativi alla diagnosi del tumore al seno.
* **Forest fires data** : dimensioni degli incendi boschivi nel nord-est del Portogallo.

Quando si compila un esperimento, è possibile effettuare una selezione nell'elenco dei set di dati disponibile a sinistra dell'area di disegno.

Per un elenco dei set di dati di esempio inclusi in Machine Learning Studio (versione classica), vedere [Usare i set di dati di esempio in Machine Learning Studio (versione classica)](use-sample-datasets.md).

### <a name="modules"></a>Moduli
Un modulo è un algoritmo che è possibile applicare ai dati. Machine Learning Studio (versione classica) include diversi moduli, che spaziano da funzioni di inserimento dei dati a processi di training, assegnazione di punteggi e convalida. Ecco alcuni esempi di moduli inclusi:

* [Convert to ARFF][convert-to-arff] (Converti in ARFF): converte un set di dati serializzato .NET nel formato ARFF (Attribute-Relation File Format).
* [Compute Elementary Statistics][elementary-statistics] (Calcola statistiche elementari): calcola le statistiche elementari come media, deviazione standard e così via.
* [Linear Regression][linear-regression] (Regressione lineare): crea un modello di regressione lineare online basato su discesa del gradiente.
* [Score Model][score-model] (Assegna un punteggio al modello): assegna un punteggio a un modello di regressione o di classificazione con training.

Quando si compila un esperimento, è possibile effettuare una selezione nell'elenco dei moduli disponibile a sinistra dell'area di disegno.

Un modulo può avere un set di parametri che è possibile usare per configurare gli algoritmi interni del modulo. Quando si seleziona un modulo nell'area di disegno, i parametri del modulo sono visualizzati nel riquadro **Properties** a destra dell'area di disegno. È possibile modificare i parametri in questo riquadro per ottimizzare il modello.

Per altre informazioni di riferimento sull'ampia libreria di algoritmi di Machine Learning disponibili, vedere [Come scegliere gli algoritmi per Microsoft Machine Learning Studio (versione classica)](../how-to-select-algorithms.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Distribuzione di un servizio Web di analisi predittiva
Quando il modello di analisi predittiva è pronto, è possibile distribuirlo come servizio Web direttamente da Machine Learning Studio (versione classica). Per altre informazioni su questo processo, vedere [Distribuire un servizio Web di Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Passaggi successivi
È possibile ottenere informazioni di base sulle analisi predittive e sull'apprendimento automatico tramite una[guida introduttiva dettagliata](create-experiment.md) e gli [esempi](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
