---
title: Foglio informativo sugli algoritmi di Machine Learning | Microsoft Docs
description: Un foglio informativo sugli algoritmi di Machine Learning stampabile aiuta a scegliere il giusto algoritmo per il proprio modello predittivo in Azure Machine Learning Studio.
keywords: foglio informativo sugli algoritmi, foglio informatico, algoritmo di machine learning
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e1dc31ec-1acb-463f-ba77-de565d4ddf4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: garye
ms.openlocfilehash: 31251e039414ee6f268aeb54f3eef755fcde9cb3
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="machine-learning-algorithm-cheat-sheet-for-microsoft-azure-machine-learning-studio"></a>Foglio informativo sugli algoritmi di Machine Learning per Microsoft Azure Machine Learning Studio
Il **foglio informativo sugli algoritmi di Microsoft Azure Machine Learning** aiuta a scegliere il giusto algoritmo per un modello di analisi predittiva.

[Azure Machine Learning Studio](https://studio.azureml.net/) include una grande libreria di algoritmi delle famiglie di ***regressione***, ***classificazione***, ***clustering*** e ***rilevamento anomalie***. Ognuno è progettato per risolvere un tipo diverso di problema di Machine Learning.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: foglio informativo sugli algoritmi di Machine Learning
**Download del foglio informativo: [Foglio informativo sugli algoritmi di Machine Learning (27,9 x 43,2 cm)](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v6.pdf)**.

![Foglio informativo sugli algoritmi di Machine Learning: informazioni su come scegliere un algoritmo di Machine Learning.][cheat-sheet]

[cheat-sheet]: ./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png

Scaricare e stampare il foglio informativo sugli algoritmi di Machine Learning in formato tabloid per averlo sempre a disposizione quando è necessario scegliere un algoritmo.

> [!NOTE]
> Vedere l'articolo [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](algorithm-choice.md) per una Guida dettagliata all'utilizzo di questo foglio informativo.
> 
> 

## <a name="more-help-with-algorithms"></a>Altre informazioni sugli algoritmi
* Per informazioni sull'uso di questo foglio informativo per la scelta dell'algoritmo corretto e per una discussione più approfondita sui diversi tipi di algoritmi di apprendimento automatico e sul relativo uso, vedere [Come scegliere gli algoritmi di Microsoft Azure Machine Learning](algorithm-choice.md).
* Per ottenere una descrizione degli algoritmi e alcuni esempi, vedere [Infografica scaricabile: nozioni fondamentali di Machine Learning con esempi di algoritmi](basics-infographic-with-algorithm-examples.md).
* Per un elenco per categoria degli algoritmi di Machine Learning disponibili in Machine Learning Studio, vedere l'argomento relativo al [modello di inizializzazione][initialize-model] nella Guida degli algoritmi e dei moduli di Machine Learning Studio.
* Per un elenco alfabetico completo degli algoritmi e dei moduli disponibili in Machine Learning Studio, vedere l'argomento relativo all'[elenco alfabetico dei moduli di Machine Learning Studio][a-z-list] nella Guida degli algoritmi e dei moduli di Machine Learning Studio.
* Per scaricare e stampate un diagramma che offra una panoramica delle funzionalità di Machine Learning Studio, vedere [Diagramma della panoramica delle funzionalità di Azure Machine Learning Studio](studio-overview-diagram.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="notes-and-terminology-definitions-for-the-machine-learning-algorithm-cheat-sheet"></a>Note e definizioni terminologiche per il foglio informativo sugli algoritmi di Machine Learning

* I consigli offerti in questo foglio informativo sugli algoritmi sono regole empiriche puramente indicative. Alcuni possono essere modificati, altri totalmente ignorati. Queste informazioni vengono fornite come punto di partenza consigliato. È anche possibile provare a eseguire un confronto in parallelo tra diversi algoritmi sui dati. Questa può rivelarsi la soluzione migliore per comprendere i principi di ogni algoritmo e il sistema che ha generato i dati.

* Ogni algoritmo di apprendimento automatico ha il proprio stile o *bias induttivo*. Per un problema specifico, possono essere appropriati diversi algoritmi, uno dei quali può rivelarsi una scelta più adatta rispetto agli altri. Non è sempre possibile, tuttavia, conoscere in anticipo la soluzione ottimale. In casi simili, nel foglio informativo sono elencati insieme diversi algoritmi. Una strategia appropriata può essere quella di provare un algoritmo e quindi provarne altri se i risultati del primo non sono soddisfacenti. Un esempio tratto da [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) di un esperimento che prova diversi algoritmi sugli stessi dati e ne confronta i risultati è disponibile in: [Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92) (Confrontare classificatori multiclasse: riconoscimento di lettere).

* L'apprendimento automatico si divide in tre categorie principali: **apprendimento supervisionato**, **apprendimento non supervisionato** e **apprendimento per rinforzo**.

  * Nell'**apprendimento supervisionato** ogni punto dati è etichettato con o associato a una categoria o un valore di interesse.  Un esempio di un'etichetta di categoria è l'assegnazione di un'immagine come "gatto" o "cane".  Un esempio di un'etichetta di valore è il prezzo di vendita associato a un'auto usata. L'obiettivo di apprendimento controllato consiste nello studio di molti esempi etichettati come questi e quindi di essere in grado di eseguire stime sui futuri punti dati, ad esempio l'identificazione di nuove foto con l'animale corretto o l'assegnazione di prezzi di vendita precisi ad altre auto usate. Questo tipo di Machine Learning è utile e diffuso. Tutti i moduli di Azure Machine Learning sono algoritmi di apprendimento supervisionato, ad eccezione di [K-Means Clustering][k-means-clustering].

  * Nell'**apprendimento non supervisionato** ai punti dati non sono associate etichette. L'obiettivo di un algoritmo di apprendimento non supervisionato è invece l'organizzazione dei dati in un certo modo o la descrizione della loro struttura. Questo può significare il raggruppamento dei dati in cluster, come fa K-Means, o l'individuazione di modi diversi in cui osservare dati complessi perché appaiano semplici.

  * Nell'**apprendimento per rinforzo** l'algoritmo arriva a scegliere un'azione in risposta a ogni punto dati. Si tratta di un approccio comune in robotica, in cui il set di letture del sensore in un certo momento è un punto dati e l'algoritmo deve scegliere l'azione successiva del robot. Questo approccio è ideale anche per applicazioni "Internet delle cose" (Internet of Things, IoT). L'algoritmo di apprendimento riceve anche un segnale di ricompensa poco dopo, a indicare il livello di correttezza della decisione presa. In base a questo segnale, l'algoritmo modifica la propria strategia per ottenere la ricompensa maggiore. Attualmente in Azure ML non sono disponibili moduli di algoritmi di apprendimento per rinforzo.

* I **metodi bayesiani** presuppongono punti dati statisticamente indipendenti. Questo significa che la variabilità non modellata in un punto dati non è correlata agli altri, ovvero non può essere stimata. Ad esempio, se i dati registrati sono il numero di minuti fino all'arrivo del prossimo treno della metropolitana, due misurazioni eseguite a distanza di un giorno sono statisticamente indipendenti. Tuttavia, due misurazioni eseguite a distanza di un minuto non sono statisticamente indipendenti e il valore di una è altamente predittivo rispetto a quello dell'altra.

* La **regressione con alberi delle decisioni con boosting** sfrutta la sovrapposizione delle caratteristiche o l'interazione tra caratteristiche. Questo significa che, in ogni punto dati specifico, il valore di una caratteristica è in parte predittivo del valore di un'altra. Ad esempio, nel caso di dati di bassa/alta temperatura giornaliera, la possibilità di determinare la bassa temperatura del giorno permette di stimare ragionevolmente anche quella alta. Le informazioni contenute nelle due caratteristiche sono in parte ridondanti.

* La classificazione di dati in più di due categorie può essere eseguita usando un classificatore essenzialmente multiclasse o combinando un set di classificatori a due classi in un **insieme**. L'approccio basato su un insieme prevede un classificatore a due classi per ogni classe, ognuno dei quali separa i dati in due categorie: "questa classe" e "non questa classe". I classificatori votano quindi l'assegnazione corrente del punto dati. Questo è il principio operativo alla base del modulo [One-vs-All Multiclass][one-vs-all-multiclass].

* Diversi metodi, tra cui la regressione logistica e Bayes Point Machine, presuppongono **limiti tra classi lineari**, ovvero assumono che i limiti tra le classi siano approssimativamente linee rette (oppure iperpiani nel caso più generale). Spesso questa caratteristica dei dati non viene individuata fino a dopo aver provato a separarli, ma può essere determinata in genere visualizzando i dati in anticipo. Se i limiti di classe appaiono molto irregolari, optare per alberi delle decisioni, giungle delle decisioni, macchine a vettori di supporto o reti neurali.

* Le reti neurali possono essere usate con variabili di categoria creando una **variabile fittizia** per ogni categoria e impostandola su 1 nei casi in cui la categoria è applicabile e su 0 quando non lo è.


<!-- This is how you can embed a link in an image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-docs-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
