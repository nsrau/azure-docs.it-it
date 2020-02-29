---
title: 'Macchina a vettori di supporto a due classi: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo **Two-Class Support Vector Machine** in Azure Machine Learning per creare un modello basato sull'algoritmo di macchina a vettori di supporto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3b2f6b2bb1dc5f9e16c537f78b5a456ee4984e80
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916728"
---
# <a name="two-class-support-vector-machine-module"></a>Modulo della macchina a vettori di supporto a due classi

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello basato sull'algoritmo della macchina a vettori di supporto. 

Le macchine a vettori di supporto (SVM) sono una classe ben ricercata di metodi di apprendimento supervisionato. Questa particolare implementazione è adatta per la stima di due possibili risultati, in base alle variabili continue o categoriche.

Dopo aver definito i parametri del modello, eseguire il training del modello usando i moduli di training e fornire un *set di dati con tag* che includa un'etichetta o una colonna risultato.

## <a name="about-support-vector-machines"></a>Informazioni sulle macchine a vettori di supporto

Le macchine a vettori di supporto (SVM) sono tra gli algoritmi di Machine Learning meno recenti e i modelli SVM sono stati usati in molte applicazioni, dal recupero di informazioni alla classificazione di testo e immagini. SVM può essere usato sia per le attività di classificazione che di regressione.

Questo modello SVM è un modello di apprendimento supervisionato che richiede dati con etichetta. Nel processo di training, l'algoritmo analizza i dati di input e riconosce i modelli in uno spazio di funzionalità multidimensionale denominato *iperpiano*.  Tutti gli esempi di input sono rappresentati come punti in questo spazio ed è stato eseguito il mapping alle categorie di output in modo che le categorie siano divise per il livello più ampio e determinino il gap possibile.

Per la stima, l'algoritmo SVM assegna nuovi esempi in una categoria o nell'altra, eseguendone il mapping nello stesso spazio. 

## <a name="how-to-configure"></a>Modalità di configurazione 

Per questo tipo di modello, è consigliabile normalizzare il set di dati prima di usarlo per il training del classificatore.
  
1.  Aggiungere il modulo **Two-Class Support Vector Machine** alla pipeline.  
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: se si sa come si desidera configurare il modello, è possibile fornire un set di valori specifico come argomenti.  

    -   **Intervallo**di parametri: se non si è certi dei parametri migliori, è possibile trovare i parametri ottimali usando il modulo [Tune Model iperparametri](tune-model-hyperparameters.md) . Viene fornito un intervallo di valori e il trainer scorre più combinazioni di impostazioni per determinare la combinazione di valori che produce il risultato migliore.

3.  Per **numero di iterazioni**, digitare un numero che indica il numero di iterazioni utilizzate durante la compilazione del modello.  
  
     Questo parametro può essere usato per controllare il compromesso tra velocità e precisione di training.  
  
4.  Per **lambda**, digitare un valore da usare come peso per la regolarizzazione L1.  
  
     Questo coefficiente di regolarizzazione consente di ottimizzare il modello. I valori più elevati penalizzano i modelli più complessi.  
  
5.  Selezionare l'opzione **normalizzare le funzionalità**se si vuole normalizzare le funzionalità prima del training.
  
     Se si applica la normalizzazione, prima del training, i punti dati vengono centrati sul valore medio e ridimensionati in modo da avere un'unità di deviazione standard.
  
6.  Selezionare l'opzione **progetto per la sfera di unità**per normalizzare i coefficienti.
  
     La proiezione di valori nello spazio unità significa che prima del training, i punti dati vengono centrati su 0 e ridimensionati in modo da avere un'unità di deviazione standard.
  
7.  In valore di **inizializzazione numeri casuali**Digitare un valore intero da utilizzare come valore di inizializzazione se si desidera garantire la riproducibilità tra le esecuzioni.  In caso contrario, viene usato un valore di clock di sistema come valore di inizializzazione, che può produrre risultati leggermente diversi tra le esecuzioni.
  
9. Connettere un set di dati con etichetta e uno dei [moduli di training](module-reference.md):
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](train-model.md) .
  
10. Eseguire la pipeline.

## <a name="results"></a>Risultati

Al termine del training:

+ Per salvare uno snapshot del modello sottoposto a training, selezionare la scheda **output** nel riquadro di destra del modulo **Train Model** . Selezionare l'icona **registra set di dati** per salvare il modello come modulo riutilizzabile.

+ Per usare il modello per l'assegnazione dei punteggi, aggiungere il modulo **Score Model** a una pipeline.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 