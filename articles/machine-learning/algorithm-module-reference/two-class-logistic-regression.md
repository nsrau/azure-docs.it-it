---
title: 'Regressione logistica a due classi: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione logistica a due classi in Azure Machine Learning per creare un modello di regressione logistica che può essere usato per prevedere due (e solo due) risultati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455826"
---
# <a name="two-class-logistic-regression-module"></a>Modulo di regressione logistica a due classi

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per creare un modello di regressione logistica che può essere utilizzato per stimare due (e solo due) risultati. 

La regressione logistica è una tecnica statistica nota che viene utilizzata per la modellazione di molti tipi di problemi. Questo algoritmo è un metodo di *apprendimento supervisionato;*  pertanto, è necessario fornire un set di dati che contiene già i risultati per eseguire il training del modello.  

### <a name="about-logistic-regression"></a>Informazioni sulla regressione logistica  

La regressione logistica è un metodo noto nelle statistiche che viene utilizzato per prevedere la probabilità di un risultato ed è particolarmente popolare per le attività di classificazione. L'algoritmo stima la probabilità di occorrenza di un evento adattando i dati a una funzione logistica.
  
In questo modulo, l'algoritmo di classificazione è ottimizzato per variabili dicotomiche o binarie. se è necessario classificare più risultati, utilizzare il modulo [Regressione logistica multiclasse.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Modalità di configurazione  

Per eseguire il training di questo modello, è necessario fornire un set di dati che contiene un'etichetta o una colonna di classe. Poiché questo modulo è destinato a problemi di due classi, la colonna label o class deve contenere esattamente due valori. 

Ad esempio, la colonna label potrebbe essere [Votato] con i possibili valori "Sì" o "No". In alternativa, potrebbe essere [Rischio di credito], con i possibili valori di "Alto" o "Basso". 
  
1.  Aggiungere il modulo **regressione logistica a due classi** alla pipeline.  
  
2.  Specificare la modalità di training del modello impostando l'opzione **Crea modalità trainer.**  
  
    -   **Parametro singolo**: se si conosce la configurazione del modello, è possibile fornire un set specifico di valori come argomenti.  

    -   **Intervallo parametri**: se non si è sicuri dei parametri migliori, è possibile trovare i parametri ottimali utilizzando il modulo [Ottimizzazione dei peridi modello.](tune-model-hyperparameters.md) Si fornisce un certo intervallo di valori e il trainer scorre più combinazioni delle impostazioni per determinare la combinazione di valori che produce il risultato migliore.
  
3.  Per **Tolleranza ottimizzazione**, specificare un valore di soglia da utilizzare durante l'ottimizzazione del modello. Se il miglioramento tra le iterazioni scende al di sotto della soglia specificata, si ritiene che l'algoritmo sia convergente in una soluzione e il training si interrompe.  
  
4.  Per il peso della **regolarizzazione L1** e il peso della **regolarizzazione L2,** digitare un valore da utilizzare per i parametri di regolarizzazione L1 e L2. È consigliabile usare per entrambi un valore diverso da zero.  
     *La regolarizzazione* è un metodo per prevenire l'overfitting penalizzando i modelli con valori di coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori coefficienti all'errore dell'ipotesi. Pertanto, un modello accurato con valori di coefficienti estremi verrebbe penalizzato di più, ma un modello meno accurato con valori più conservativi verrebbe penalizzato meno.  
  
     Le regolarizzazioni di tipo L1 e L2 hanno effetti e usi differenti.  
  
    -   La regolarizzazione L1 può essere applicata a modelli di tipo sparse, caratteristica utile quando si lavora con dati a elevata dimensionalità.  
  
    -   La regolarizzazione L2, al contrario, è preferibile per i dati non di tipo sparse.  
  
     Questo algoritmo supporta una combinazione lineare di valori di regolarizzazione L1 e L2: ovvero, if <code>x = L1</code> e <code>y = L2</code>, <code>ax + by = c</code> definisce quindi l'intervallo lineare dei termini di regolarizzazione.  
  
    > [!NOTE]
    >  Vuoi saperne di più sulla regolarizzazione L1 e L2? L'articolo seguente illustra come la regolarizzazione L1 e L2 sia diversa e come influiscano sul montaggio dei modelli, con esempi di codice per la regressione logistica e i modelli di rete neurale: [regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Sono state ideate diverse combinazioni lineari di termini L1 e L2 per i modelli di regressione logistica: ad esempio, [regolarizzazione netta elastica.](https://wikipedia.org/wiki/Elastic_net_regularization) Si consiglia di fare riferimento a queste combinazioni per definire una combinazione lineare efficace nel modello.
      
5.  Per **Dimensione memoria per L-BFGS**, specificare la quantità di memoria da utilizzare per l'ottimizzazione *L-BFGS.*  
  
     L-BFGS sta per "memoria limitata Broyden-Fletcher-Goldfarb-Shanno". Si tratta di un algoritmo di ottimizzazione usato di frequente per la stima dei parametri. Questo parametro indica il numero di posizioni e gradienti precedenti da archiviare per il calcolo del passaggio successivo.  
  
     Questo parametro di ottimizzazione limita la quantità di memoria usata per il calcolo del passaggio e della direzione successivi. Quando si specifica una quantità di memoria inferiore, il training è più veloce ma meno accurato.  
  
6.  Per **Valore di controllo del numero casuale**restituito , digitare un valore intero. La definizione di un valore di valore di serie è importante se si desidera che i risultati siano riproducibili su più esecuzioni della stessa pipeline.  
  
  
8. Aggiungere un set di dati con tag alla pipeline e connettere uno dei moduli di [training.](module-reference.md)  
  
    -   Se si imposta **La modalità Crea trainer** su **Parametro singolo**, utilizzare il modulo Modello di [treno.](./train-model.md)  
  
9. Inviare la pipeline.  
  
## <a name="results"></a>Risultati

Al termine dell'allenamento:
 
  
+ Per eseguire stime sui nuovi dati, usare il modello sottoposto a training e i nuovi dati come input per il modulo Modello di [punteggio.](./score-model.md) 


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 