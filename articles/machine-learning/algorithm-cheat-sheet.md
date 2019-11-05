---
title: Foglio informativo sugli algoritmi di Machine Learning
titleSuffix: Azure Machine Learning
description: Un foglio informativo sull'algoritmo di Machine Learning stampabile consente di scegliere l'algoritmo appropriato per il modello predittivo in Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 11/04/2019
ms.openlocfilehash: 10714e155bebdd7acc5d7febb345c17fa81bab13
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516292"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Foglio informativo sugli algoritmi di Machine Learning per Azure Machine Learning Designer

Il **foglio informativo sugli algoritmi di Azure Machine Learning** aiuta a scegliere il giusto algoritmo per un modello di analisi predittiva.

Azure Machine Learning dispone di una grande libreria di algoritmi di ***classificazione***, ***sistemi di raccomandazione***, ***clustering***, ***rilevamento di anomalie***, ***regressione*** e famiglie di ***analisi del testo*** . Ognuno è progettato per risolvere un tipo diverso di problema di Machine Learning.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Download: foglio informativo sugli algoritmi Machine Learning

**Download del foglio informativo: [Foglio informativo sugli algoritmi di Machine Learning (27,9 x 43,2 cm)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)** .

![Foglio informativo sugli algoritmi di Machine Learning: informazioni su come scegliere un algoritmo di Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Scaricare e stampare il foglio informativo sugli algoritmi di Machine Learning in formato tabloid per averlo sempre a disposizione quando è necessario scegliere un algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Come usare il foglio informativo sugli algoritmi di Machine Learning

I consigli offerti in questo foglio informativo sugli algoritmi sono regole empiriche puramente indicative. Alcuni possono essere modificati, altri totalmente ignorati. Queste informazioni vengono fornite come punto di partenza consigliato. È anche possibile provare a eseguire un confronto in parallelo tra diversi algoritmi sui dati. Non esiste semplicemente alcun sostituto per comprendere i principi di ogni algoritmo e il sistema che ha generato i dati.

Ogni algoritmo di Machine Learning ha il proprio stile o la distorsione indotta. Per un problema specifico, è possibile che diversi algoritmi siano appropriati e che un algoritmo possa essere un adattamento migliore rispetto ad altri. Non è sempre possibile, tuttavia, conoscere in anticipo la soluzione ottimale. In casi simili, nel foglio informativo sono elencati insieme diversi algoritmi. Una strategia appropriata può essere quella di provare un algoritmo e quindi provarne altri se i risultati del primo non sono soddisfacenti. 

Per ulteriori informazioni sugli algoritmi in Azure Machine Learning, vedere il [riferimento all'algoritmo e al modulo](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Tipi di Machine Learning

L'apprendimento automatico si divide in tre categorie principali: *apprendimento supervisionato*, *apprendimento non supervisionato* e *apprendimento per rinforzo*.

### <a name="supervised-learning"></a>Apprendimento supervisionato

Nell'apprendimento supervisionato ogni punto dati è etichettato o associato a una categoria o un valore di interesse. Un esempio di un'etichetta di categoria è l'assegnazione di un'immagine come "gatto" o "cane". Un esempio di un'etichetta di valore è il prezzo di vendita associato a un'auto usata. L'obiettivo di apprendimento controllato consiste nello studio di molti esempi etichettati come questi e quindi di essere in grado di eseguire stime sui futuri punti dati, ad esempio l'identificazione di nuove foto con l'animale corretto o l'assegnazione di prezzi di vendita precisi ad altre auto usate. Questo tipo di Machine Learning è utile e diffuso.

### <a name="unsupervised-learning"></a>Apprendimento non supervisionato

Nell'apprendimento non supervisionato, ai punti dati non sono associate etichette. L'obiettivo di un algoritmo di apprendimento non supervisionato è invece l'organizzazione dei dati in un certo modo o la descrizione della loro struttura. Questo può significare il raggruppamento dei dati in cluster, come fa K-Means, o l'individuazione di modi diversi in cui osservare dati complessi perché appaiano semplici.

### <a name="reinforcement-learning"></a>Apprendimento per rinforzo

Nell'apprendimento per rinforzo l'algoritmo arriva a scegliere un'azione in risposta a ogni punto dati. Si tratta di un approccio comune in robotica, in cui il set di letture del sensore in un certo momento è un punto dati e l'algoritmo deve scegliere l'azione successiva del robot. Questo approccio è ideale anche per applicazioni "Internet delle cose" (Internet of Things, IoT). L'algoritmo di apprendimento riceve anche un segnale di ricompensa poco dopo, a indicare il livello di correttezza della decisione presa. In base a questo segnale, l'algoritmo modifica la propria strategia per ottenere la ricompensa maggiore. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su Studio in Azure Machine Learning e portale di Azure](service/overview-what-is-azure-ml.md).

* Vedere un elenco di algoritmi e moduli nell' [algoritmo e nel riferimento al modulo](algorithm-module-reference/module-reference.md).

* [Esercitazione: creare un modello di stima in Azure machine learning designer](service/ui-tutorial-automobile-price-train-score.md).

* [Scopri di più su apprendimento avanzato e Machine Learning](service/concept-deep-learning-vs-machine-learning.md).
