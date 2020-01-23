---
title: 'Regressione logistica a due classi: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione logistica a due classi in Azure Machine Learning per creare un modello di regressione logistica che può essere usato per stimare due (e solo due) risultati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 7c2618f7703ba1a9803952efabcfbd800149ada4
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548340"
---
# <a name="two-class-logistic-regression-module"></a>Modulo di regressione logistica a due classi

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello di regressione logistica che può essere usato per stimare due (e solo due) risultati. 

La regressione logistica è una tecnica statistica ben nota utilizzata per la modellazione di molti tipi di problemi. Questo algoritmo è un metodo di *apprendimento supervisionato* .  Pertanto, è necessario fornire un set di dati che contenga già i risultati per il training del modello.  

### <a name="about-logistic-regression"></a>Informazioni sulla regressione logistica  

La regressione logistica è un metodo noto nelle statistiche usato per stimare la probabilità di un risultato ed è particolarmente comune per le attività di classificazione. L'algoritmo consente di stimare la probabilità di occorrenza di un evento mediante l'inserimento di dati in una funzione logistica.
  
In questo modulo, l'algoritmo di classificazione è ottimizzato per dicotomica o variabili binarie. Se è necessario classificare più risultati, usare il modulo di [regressione logistica multiclasse](./multiclass-logistic-regression.md) .

##  <a name="how-to-configure"></a>Modalità di configurazione  

Per eseguire il training di questo modello, è necessario fornire un set di dati contenente un'etichetta o una colonna di classe. Poiché questo modulo è concepito per problemi a due classi, l'etichetta o la colonna di classe deve contenere esattamente due valori. 

Ad esempio, la colonna Label potrebbe essere [voted] con i possibili valori di "Yes" o "No". In alternativa, potrebbe essere [rischio di credito], con i valori possibili "High" o "low". 
  
1.  Aggiungere il modulo di **regressione logistica a due classi** alla pipeline.  
  
2.  Specificare il modo in cui si desidera eseguire il training del modello, impostando l'opzione **crea modalità trainer** .  
  
    -   **Singolo parametro**: se si sa come si desidera configurare il modello, è possibile fornire un set di valori specifico come argomenti.  
  
3.  Per la **tolleranza di ottimizzazione**, specificare un valore soglia da usare per l'ottimizzazione del modello. Se il miglioramento tra le iterazioni scende al di sotto della soglia specificata, l'algoritmo viene considerato convergente in una soluzione e il training viene arrestato.  
  
4.  Per il peso della **regolarizzazione L1** e il **peso della regolarizzazione L2**, digitare un valore da usare per i parametri di regolarizzazione L1 e L2. È consigliabile usare per entrambi un valore diverso da zero.  
     La *regolarizzazione* è un metodo per evitare l'overfitting penalizzando i modelli con valori di coefficienti estremi. La regolarizzazione funziona aggiungendo la penalità associata ai valori coefficienti all'errore dell'ipotesi. Pertanto, un modello accurato con valori di coefficienti estremi verrebbe penalizzato più, ma un modello meno accurato con valori più conservativi verrebbe penalizzato meno.  
  
     Le regolarizzazioni di tipo L1 e L2 hanno effetti e usi differenti.  
  
    -   La regolarizzazione L1 può essere applicata a modelli di tipo sparse, caratteristica utile quando si lavora con dati a elevata dimensionalità.  
  
    -   La regolarizzazione L2, al contrario, è preferibile per i dati non di tipo sparse.  
  
     Questo algoritmo supporta una combinazione lineare di valori di regolarizzazione L1 e L2, ovvero se <code>x = L1</code> e <code>y = L2</code>, <code>ax + by = c</code> definisce la portata lineare dei termini di regolarizzazione.  
  
    > [!NOTE]
    >  Per ulteriori informazioni sulla regolarizzazione L1 e L2, Nell'articolo seguente viene illustrato il modo in cui la regolarizzazione L1 e L2 sono diverse e il modo in cui influiscono sul montaggio del modello, con esempi di codice per la regressione logistica e i modelli di rete neurale: [regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Sono state concepite diverse combinazioni lineari di termini L1 e L2 per i modelli di regressione logistica: ad esempio, la [regolarizzazione di reti elastiche](https://wikipedia.org/wiki/Elastic_net_regularization). Si consiglia di fare riferimento a queste combinazioni per definire una combinazione lineare che sia efficace nel modello.
      
5.  Per le **dimensioni della memoria per l-BFGS**, specificare la quantità di memoria da usare per l'ottimizzazione *l-BFGS* .  
  
     L-BFGS è l'acronimo di "Limited Memory Broyden-Fletcher-Goldfarb-Shanno". Si tratta di un algoritmo di ottimizzazione usato di frequente per la stima dei parametri. Questo parametro indica il numero di posizioni e gradienti precedenti da archiviare per il calcolo del passaggio successivo.  
  
     Questo parametro di ottimizzazione limita la quantità di memoria usata per il calcolo del passaggio e della direzione successivi. Quando si specifica una quantità di memoria inferiore, il training è più veloce ma meno accurato.  
  
6.  Per il valore di **inizializzazione numerico casuale**, digitare un valore integer. La definizione di un valore di inizializzazione è importante se si desidera che i risultati siano riproducibili su più esecuzioni della stessa pipeline.  
  
  
8. Aggiungere un set di dati con tag alla pipeline e connettere uno dei [moduli di training](module-reference.md).  
  
    -   Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, usare il modulo [Train Model](./train-model.md) .  
  
9. Eseguire la pipeline.  
  
## <a name="results"></a>Risultati

Al termine del training:
 
  
+ Per eseguire stime sui nuovi dati, usare il modello sottoposto a training e i nuovi dati come input per il modulo [Score Model](./score-model.md) . 


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 