---
title: 'Two-Class Support Vector Machine: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il **Two-Class Support Vector Machine** modulo nel servizio di Azure Machine Learning per creare un modello basato su algoritmo macchina a vettori di supporto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027931"
---
# <a name="two-class-support-vector-machine-module"></a>Modulo Two-Class Support Vector Machine

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello basato su algoritmo macchina a vettori di supporto. 

Macchine a vettori di supporto (SVM) sono una classe di metodi di apprendimento supervisionato e consolidata. Questa implementazione specifica è adatti per la stima dei due possibili risultati, in base alle variabili categoriche o continue.

Dopo aver definito i parametri di modello, il training del modello usando i moduli di training e specificando una *set di dati con tag* che include una colonna di etichetta o il risultato.

## <a name="about-support-vector-machines"></a>Sulle macchine a vettori di supporto

Macchine a vettori di supporto sono tra i primi algoritmi di machine learning e i modelli SVM sono stati usati in molte applicazioni, dal recupero di informazioni alla classificazione di testo e immagini. SVM possono essere utilizzate per le attività di classificazione e regressione.

Questo modello SVM è un modello di apprendimento supervisionato che richiede dati etichettati. Nel processo di training, l'algoritmo analizza i dati di input e riconoscere gli schemi in uno spazio multidimensionale denominata la *iperpiano*.  Tutti gli esempi di input sono rappresentati come punti in questo spazio e vengono eseguito il mapping di output di categorie in modo che le categorie sono divise da larga e cancellare un gap possibile.

Per la stima, l'algoritmo SVM assegna i nuovi esempi in una categoria o l'altro, eseguendone il mapping nello stesso spazio. 

## <a name="how-to-configure"></a>Come configurare 

Per questo tipo di modello, è consigliabile normalizzare il set di dati prima di usarlo per eseguire il training del classificatore.
  
1.  Aggiungere il **Two-Class Support Vector Machine** modulo nell'esperimento.  
  
2.  Specificare come si desidera che il modello di esecuzione del training, impostando il **modalità di creazione trainer** opzione.  
  
    -   **Singolo parametro**: Se si conosce la modalità con cui si desidera configurare il modello, è possibile definire un set specifico di valori come argomenti.  

3.  Per la **numero di iterazioni**, digitare un numero che indica il numero di iterazioni usate quando si compila il modello.  
  
     Questo parametro può essere utilizzato per controllare il compromesso tra precisione e velocità di training.  
  
4.  Per la **Lambda**, digitare un valore da usare come peso per la regolarizzazione L1.  
  
     Questo coefficiente di regolarizzazione è utilizzabile per ottimizzare il modello. Valori più elevati penalizzano i modelli più complessi.  
  
5.  Selezionare l'opzione **funzioni vengono normalizzate**, se si desidera normalizzare le funzionalità prima del training.
  
     Se si applica la normalizzazione, prima del training, i punti dati sono centrati nella Media e adeguati per avere un'unità di deviazione standard.
  
6.  Selezionare l'opzione **progetto per la sfera unitaria**per normalizzare i coefficienti.
  
     Inserimento dei valori nello spazio di unità significa che prima del training, i punti dati sono centrati nella 0 e ridimensionati in modo da avere un'unità di deviazione standard.
  
7.  Nelle **seed numeri Random**, digitare un valore intero da utilizzare come valore di inizializzazione per garantire la riproducibilità nelle esecuzioni.  In caso contrario, un valore dell'orologio di sistema viene utilizzato come valore di inizializzazione, che può comportare risultati leggermente diversi da un'esecuzione.
  
9. Connettere un set di dati con etichette e uno dei [moduli di training](module-reference.md):
  
    -   Se si imposta **modalità di creazione trainer** al **singolo parametro**, usare il [Train Model](train-model.md) modulo.
  

10. Eseguire l'esperimento.

## <a name="results"></a>Risultati

Dopo aver completata la formazione:

+ Per visualizzare un riepilogo dei parametri del modello, con i pesi delle funzionalità appreso dal training, fare doppio clic sull'output del [Train Model](./train-model.md)e selezionare **Visualize**.

+ Per usare i modelli con training per eseguire stime, connettere il modello con training per il [Score Model](score-model.md) modulo.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 