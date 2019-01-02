---
title: Foglio informativo sugli algoritmi di Machine Learning - Azure Machine Learning Studio | Microsoft Docs
description: Un foglio informativo sugli algoritmi di Machine Learning stampabile aiuta a scegliere il giusto algoritmo per il proprio modello predittivo in Azure Machine Learning Studio.
keywords: foglio informativo sugli algoritmi, foglio informatico, algoritmo di machine learning
services: machine-learning
author: ericlicoding
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.component: studio
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 7ddc3a9829dc05ac1c1df450091789e4cad2b2f5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274514"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Foglio informativo sugli algoritmi di Machine Learning per Azure Machine Learning Studio

Il **foglio informativo sugli algoritmi di Azure Machine Learning** aiuta a scegliere il giusto algoritmo per un modello di analisi predittiva.

[Azure Machine Learning Studio](https://studio.azureml.net/) include una grande libreria di algoritmi delle famiglie di ***regressione***, ***classificazione***, ***clustering*** e ***rilevamento anomalie***. Ognuno è progettato per risolvere un tipo diverso di problema di Machine Learning.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: Foglio informativo sugli algoritmi di Machine Learning

**Scaricare qui il foglio informativo: [Foglio informativo sugli algoritmi di Machine Learning (27,9 x 43,2 cm)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![Foglio informativo sugli algoritmi di Machine Learning: Informazioni su come scegliere un algoritmo di Machine Learning][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Scaricare e stampare il foglio informativo sugli algoritmi di Machine Learning Studio in formato tabloid per averlo sempre a disposizione quando è necessario scegliere un algoritmo.

> [!NOTE]
> Per informazioni sull'uso di questo foglio informativo per la scelta dell'algoritmo corretto e per una discussione più approfondita sui diversi tipi di algoritmi di apprendimento automatico e sul relativo uso, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](algorithm-choice.md).

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Note e definizioni terminologiche per il foglio informativo sugli algoritmi di Machine Learning Studio

* I consigli offerti in questo foglio informativo sugli algoritmi sono regole empiriche puramente indicative. Alcuni possono essere modificati, altri totalmente ignorati. Queste informazioni vengono fornite come punto di partenza consigliato. È anche possibile provare a eseguire un confronto in parallelo tra diversi algoritmi sui dati. Questa può rivelarsi la soluzione migliore per comprendere i principi di ogni algoritmo e il sistema che ha generato i dati.

* Ogni algoritmo di apprendimento automatico ha il proprio stile o *bias induttivo*. Per un problema specifico, possono essere appropriati diversi algoritmi, uno dei quali può rivelarsi una scelta più adatta rispetto agli altri. Non è sempre possibile, tuttavia, conoscere in anticipo la soluzione ottimale. In casi simili, nel foglio informativo sono elencati insieme diversi algoritmi. Una strategia appropriata può essere quella di provare un algoritmo e quindi provarne altri se i risultati del primo non sono soddisfacenti. Un esempio tratto da [Azure AI Gallery](http://gallery.azure.ai/) di un esperimento che prova diversi algoritmi sugli stessi dati e ne confronta i risultati è disponibile in: [Compare Multi-class Classifiers: Letter recognition](http://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92) (Confrontare classificatori multiclasse: riconoscimento di lettere).

* L'apprendimento automatico si divide in tre categorie principali: **apprendimento supervisionato**, **apprendimento non supervisionato** e **apprendimento per rinforzo**.

  * Nell'**apprendimento supervisionato** ogni punto dati è etichettato con o associato a una categoria o un valore di interesse.  Un esempio di un'etichetta di categoria è l'assegnazione di un'immagine come "gatto" o "cane".  Un esempio di un'etichetta di valore è il prezzo di vendita associato a un'auto usata. L'obiettivo di apprendimento controllato consiste nello studio di molti esempi etichettati come questi e quindi di essere in grado di eseguire stime sui futuri punti dati, ad esempio l'identificazione di nuove foto con l'animale corretto o l'assegnazione di prezzi di vendita precisi ad altre auto usate. Questo tipo di Machine Learning è utile e diffuso. Tutti i moduli di Azure Machine Learning sono algoritmi di apprendimento supervisionato, ad eccezione di [K-Means Clustering][k-means-clustering].

  * Nell'**apprendimento non supervisionato** ai punti dati non sono associate etichette. L'obiettivo di un algoritmo di apprendimento non supervisionato è invece l'organizzazione dei dati in un certo modo o la descrizione della loro struttura. Questo può significare il raggruppamento dei dati in cluster, come fa K-Means, o l'individuazione di modi diversi in cui osservare dati complessi perché appaiano semplici.

  * Nell'**apprendimento per rinforzo** l'algoritmo arriva a scegliere un'azione in risposta a ogni punto dati. Si tratta di un approccio comune in robotica, in cui il set di letture del sensore in un certo momento è un punto dati e l'algoritmo deve scegliere l'azione successiva del robot. Questo approccio è ideale anche per applicazioni "Internet delle cose" (Internet of Things, IoT). L'algoritmo di apprendimento riceve anche un segnale di ricompensa poco dopo, a indicare il livello di correttezza della decisione presa. In base a questo segnale, l'algoritmo modifica la propria strategia per ottenere la ricompensa maggiore. Attualmente in Azure ML non sono disponibili moduli di algoritmi di apprendimento per rinforzo.

* I **metodi bayesiani** presuppongono punti dati statisticamente indipendenti. Questo significa che la variabilità non modellata in un punto dati non è correlata agli altri, ovvero non può essere stimata. Ad esempio, se i dati registrati sono il numero di minuti fino all'arrivo del prossimo treno della metropolitana, due misurazioni eseguite a distanza di un giorno sono statisticamente indipendenti. Tuttavia, due misurazioni eseguite a distanza di un minuto non sono statisticamente indipendenti e il valore di una è altamente predittivo rispetto a quello dell'altra.

* La **regressione con alberi delle decisioni con boosting** sfrutta la sovrapposizione delle caratteristiche o l'interazione tra caratteristiche. Questo significa che, in ogni punto dati specifico, il valore di una caratteristica è in parte predittivo del valore di un'altra. Ad esempio, nel caso di dati di bassa/alta temperatura giornaliera, la possibilità di determinare la bassa temperatura del giorno permette di stimare ragionevolmente anche quella alta. Le informazioni contenute nelle due caratteristiche sono in parte ridondanti.

* La classificazione di dati in più di due categorie può essere eseguita usando un classificatore essenzialmente multiclasse o combinando un set di classificatori a due classi in un **insieme**. L'approccio basato su un insieme prevede un classificatore a due classi per ogni classe, ognuno dei quali separa i dati in due categorie: "questa classe" e "non questa classe". I classificatori votano quindi l'assegnazione corrente del punto dati. Questo è il principio operativo alla base del modulo [One-vs-All Multiclass][one-vs-all-multiclass].

* Diversi metodi, tra cui la regressione logistica e Bayes Point Machine, presuppongono **limiti tra classi lineari**, ovvero assumono che i limiti tra le classi siano approssimativamente linee rette (oppure iperpiani nel caso più generale). Spesso questa caratteristica dei dati non viene individuata fino a dopo aver provato a separarli, ma può essere determinata in genere visualizzando i dati in anticipo. Se i limiti di classe appaiono molto irregolari, optare per alberi delle decisioni, giungle delle decisioni, macchine a vettori di supporto o reti neurali.

* Le reti neurali possono essere usate con variabili di categoria creando una **variabile fittizia** per ogni categoria e impostandola su 1 nei casi in cui la categoria è applicabile e su 0 quando non lo è.

## <a name="next-steps"></a>Passaggi successivi

* Per ottenere una descrizione degli algoritmi e alcuni esempi, vedere [Infografica scaricabile: Nozioni fondamentali di Machine Learning con esempi di algoritmi](basics-infographic-with-algorithm-examples.md).

* Per un elenco per categoria degli algoritmi di Machine Learning disponibili in Machine Learning Studio, vedere l'argomento relativo al [modello di inizializzazione][initialize-model] nella Guida degli algoritmi e dei moduli di Machine Learning Studio.

* Per un elenco alfabetico completo degli algoritmi e dei moduli disponibili in Machine Learning Studio, vedere l'argomento relativo all'[elenco alfabetico dei moduli di Machine Learning Studio][a-z-list] nella Guida degli algoritmi e dei moduli di Machine Learning Studio.

* Per scaricare e stampate un diagramma che offra una panoramica delle funzionalità di Machine Learning Studio, vedere [Diagramma della panoramica delle funzionalità di Azure Machine Learning Studio](studio-overview-diagram.md).



<!-- Module References -->
[a-z-list]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/one-vs-all-multiclass
