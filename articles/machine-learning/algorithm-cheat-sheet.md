---
title: Machine Learning Algoritmo Cheat Foglio
titleSuffix: Azure Machine Learning
description: Un foglio di trucco dell'algoritmo di Machine Learning stampabile consente di scegliere l'algoritmo giusto per il modello predittivo in Progettazione Azure Machine Learning.A printable Machine Learning Algorithm Cheat Sheet helps you choose the right algorithm for your predictive model in Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: 85fbb1c1d26f71903adab2eb96b0c1dd3bf74c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328618"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Foglio trucco algoritmo di Machine Learning per la finestra di progettazione di Azure Machine LearningMachine Learning Algorithm Cheat Sheet for Azure Machine Learning designer

Il **foglio informativo sugli algoritmi di Azure Machine Learning** aiuta a scegliere il giusto algoritmo per un modello di analisi predittiva.

Azure Machine Learning dispone di un'ampia libreria di algoritmi delle famiglie ***di classificazione,*** ***sistemi di raccomandazione,*** ***clustering,*** ***rilevamento anomalie,*** ***regressione***e ***analisi del testo.*** Ognuno è progettato per risolvere un tipo diverso di problema di Machine Learning.

Per altre indicazioni, vedere [Come selezionare gli algoritmiFor](how-to-select-algorithms.md) additional guidance, see How to select algorithms

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Scarica: Machine Learning Algorithm Cheat Sheet

**Download del foglio informativo: [Foglio informativo sugli algoritmi di Machine Learning (27,9 x 43,2 cm)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**.

![Machine Learning Algorithm Cheat Sheet: scopri come scegliere un algoritmo di Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Scaricare e stampare il foglio informativo sugli algoritmi di Machine Learning in formato tabloid per averlo sempre a disposizione quando è necessario scegliere un algoritmo.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Come utilizzare il foglio trucco algoritmo di Machine Learning

I consigli offerti in questo foglio informativo sugli algoritmi sono regole empiriche puramente indicative. Alcuni possono essere modificati, altri totalmente ignorati. Questo foglio trucco ha lo scopo di suggerire un punto di partenza. È anche possibile provare a eseguire un confronto in parallelo tra diversi algoritmi sui dati. Semplicemente non c'è alcun sostituto per comprendere i principi di ogni algoritmo e il sistema che ha generato i dati.

Ogni algoritmo di apprendimento automatico ha il proprio stile o bias induttivo. Per un problema specifico, diversi algoritmi possono essere appropriati e un algoritmo può essere più adatto di altri. Non è sempre possibile, tuttavia, conoscere in anticipo la soluzione ottimale. In casi simili, nel foglio informativo sono elencati insieme diversi algoritmi. Una strategia appropriata può essere quella di provare un algoritmo e quindi provarne altri se i risultati del primo non sono soddisfacenti. 

Per altre informazioni sugli algoritmi in Azure Machine Learning, vedere Informazioni di riferimento [sull'algoritmo e](algorithm-module-reference/module-reference.md)sui moduli .

## <a name="kinds-of-machine-learning"></a>Tipi di apprendimento automatico

L'apprendimento automatico si divide in tre categorie principali: *apprendimento supervisionato*, *apprendimento non supervisionato* e *apprendimento per rinforzo*.

### <a name="supervised-learning"></a>Apprendimento supervisionato

Nell'apprendimento supervisionato ogni punto dati è etichettato con o associato a una categoria o un valore di interesse. Un esempio di un'etichetta di categoria è l'assegnazione di un'immagine come "gatto" o "cane". Un esempio di un'etichetta di valore è il prezzo di vendita associato a un'auto usata. L'obiettivo di apprendimento controllato consiste nello studio di molti esempi etichettati come questi e quindi di essere in grado di eseguire stime sui futuri punti dati, ad esempio l'identificazione di nuove foto con l'animale corretto o l'assegnazione di prezzi di vendita precisi ad altre auto usate. Questo tipo di Machine Learning è utile e diffuso.

### <a name="unsupervised-learning"></a>Apprendimento senza supervisione

Nell'apprendimento non supervisionato, ai punti dati non sono associate etichette. L'obiettivo di un algoritmo di apprendimento non supervisionato è invece l'organizzazione dei dati in un certo modo o la descrizione della loro struttura. L'apprendimento non supervisionato raggruppa i dati in cluster, come fa K-means, o trova diversi modi di esaminare dati complessi in modo che appaiano più semplici.

### <a name="reinforcement-learning"></a>Apprendimento per rinforzo

Nell'apprendimento per rinforzo l'algoritmo arriva a scegliere un'azione in risposta a ogni punto dati. Si tratta di un approccio comune in robotica, in cui il set di letture del sensore in un certo momento è un punto dati e l'algoritmo deve scegliere l'azione successiva del robot. Questo approccio è ideale anche per applicazioni "Internet delle cose" (Internet of Things, IoT). L'algoritmo di apprendimento riceve anche un segnale di ricompensa poco dopo, a indicare il livello di correttezza della decisione presa. Sulla base di questo segnale, l'algoritmo modifica la sua strategia al fine di ottenere la ricompensa più alta. 

## <a name="next-steps"></a>Passaggi successivi

* Vedere ulteriori indicazioni su [come selezionare gli algoritmiSee](how-to-select-algorithms.md) additional guidance on How to select algorithms

* [Informazioni sullo studio in Azure Machine Learning e nel portale](overview-what-is-azure-ml.md)di Azure.

* Esercitazione: Creare un modello di stima nella finestra di progettazione di [Azure Machine Learning.Tutorial: Build](tutorial-designer-automobile-price-train-score.md)a prediction model in Azure Machine Learning designer .

* [Informazioni sul deep learning e sull'apprendimento automatico](concept-deep-learning-vs-machine-learning.md).
