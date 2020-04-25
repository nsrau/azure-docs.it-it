---
title: 'Regressione lineare: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo di regressione lineare in Azure Machine Learning per creare un modello di regressione lineare da usare in una pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9d83a9ffb9dc334ef959b7a8039b9a9c4a1fced7
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137456"
---
# <a name="linear-regression-module"></a>Modulo di regressione lineare
Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per creare un modello di regressione lineare da usare in una pipeline.  La regressione lineare tenta di stabilire una relazione lineare tra una o più variabili indipendenti e un risultato numerico o una variabile dipendente. 

Usare questo modulo per definire un metodo di regressione lineare e quindi eseguire il training di un modello usando un set di dati con etichetta. Sarà quindi possibile usare il modello con training per ottenere stime.

## <a name="about-linear-regression"></a>Informazioni sulla regressione lineare

La regressione lineare è un metodo statistico comune, che è stato adottato in Machine Learning ed è stato migliorato con molti nuovi metodi per adattare la linea e l'errore di misurazione. Nel senso più elementare, la regressione si riferisce alla stima di una destinazione numerica. La regressione lineare è ancora una scelta ottimale quando si desidera un modello semplice per un'attività predittiva di base. La regressione lineare tende anche a funzionare correttamente in set di dati di tipo sparse e ad alta dimensionalità privi di complessità.

Azure Machine Learning supporta un'ampia gamma di modelli di regressione, oltre alla regressione lineare. Tuttavia, il termine "regressione" può essere interpretato in modo debole e alcuni tipi di regressione disponibili in altri strumenti non sono supportati.

+ Il problema di regressione classica prevede una singola variabile indipendente e una variabile dipendente. Questa operazione è detta *regressione semplice*.  Questo modulo supporta la regressione semplice.

+ *Più regressione lineare* implica due o più variabili indipendenti che contribuiscono a una singola variabile dipendente. I problemi in cui vengono usati più input per stimare un singolo risultato numerico sono anche denominati *regressione lineare multivariata*.

    Il modulo di **regressione lineare** può risolvere questi problemi, in quanto può la maggior parte degli altri moduli di regressione.

+ La *regressione multi-label* è l'attività di stima di più variabili dipendenti all'interno di un singolo modello. Nella regressione logistica multietichetta, ad esempio, un campione può essere assegnato a più etichette diverse. Questa operazione è diversa dall'attività di stima di più livelli all'interno di una singola variabile di classe.

    Questo tipo di regressione non è supportato in Azure Machine Learning. Per stimare più variabili, creare un discente separato per ogni output che si desidera stimare.

Per gli statistici degli anni sono stati sviluppati metodi sempre più avanzati per la regressione. Questo vale anche per la regressione lineare. Questo modulo supporta due metodi per misurare l'errore e adattarlo alla retta di regressione, ovvero al metodo dei minimi quadrati normali e alla discesa della sfumatura.

- La **discesa sfumatura** è un metodo che riduce al minimo la quantità di errori in ogni passaggio del processo di training del modello. Esistono molte varianti del metodo dei valori descent con sfumatura e l'ottimizzazione di tale metodo per diversi problemi di apprendimento è stata oggetto di approfonditi studi. Se si sceglie questa opzione per il **metodo della soluzione**, è possibile impostare diversi parametri per controllare le dimensioni del passaggio, la velocità di apprendimento e così via. Questa opzione supporta anche l'uso di uno sweep di parametri integrato.

- Il numero **minimo di quadrati** è una delle tecniche più diffuse nella regressione lineare. Ad esempio, i quadrati minimi sono il metodo usato nell'analisi strumenti per Microsoft Excel.

    Il metodo dei minimi quadrati fa riferimento alla funzione di perdita, che calcola l'errore come la somma del quadrato della distanza dal valore effettivo alla riga stimata e adatta il modello riducendo al minimo l'errore quadratico. Questo metodo presuppone una forte relazione lineare tra gli input e la variabile dipendente.

## <a name="configure-linear-regression"></a>Configurare la regressione lineare

Questo modulo supporta due metodi per adattare un modello di regressione, con diverse opzioni:

+ [Adattare un modello di regressione usando i quadrati minimi normali](#create-a-regression-model-using-ordinary-least-squares)

    Per i set di impostazioni di piccole dimensioni, è preferibile selezionare i quadrati minimi. Questa operazione dovrebbe dare risultati simili a Excel.
    
+ [Creare un modello di regressione usando la discesa sfumatura online](#create-a-regression-model-using-online-gradient-descent)

    Il metodo dei valori descent con sfumatura rappresenta una funzione di perdita migliore per i modelli più complessi o per i quali sono disponibili pochi dati di training, considerato il numero di variabili.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Creare un modello di regressione usando i quadrati minimi normali

1. Aggiungere il modulo **modello di regressione lineare** alla pipeline nella finestra di progettazione.

    È possibile trovare questo modulo nella categoria **Machine Learning** . Espandere **Initialize Model**, espandere **regressione**, quindi trascinare il modulo **Linear regressione Model** nella pipeline.

2. Nell'elenco a discesa **Metodo soluzione** nel riquadro **Proprietà** selezionare i **quadrati minimi**. Questa opzione specifica il metodo di calcolo utilizzato per trovare la linea di regressione.

3. In **peso regolarizzazione L2**Digitare il valore da usare come peso per la regolarizzazione L2. È consigliabile usare un valore diverso da zero per evitare l'overfitting.

     Per altre informazioni sul modo in cui la regolarizzazione influiscono sul montaggio del modello, vedere questo articolo: [regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Selezionare l'opzione **Includi termine intercetta**se si desidera visualizzare il termine per l'intercettazione.

    Deselezionare questa opzione se non è necessario esaminare la formula di regressione.

5. Per il valore di **inizializzazione del numero casuale**, è possibile digitare facoltativamente un valore per inizializzare il generatore di numeri casuali usato dal modello.

    L'uso di un valore di inizializzazione è utile se si desidera mantenere gli stessi risultati in esecuzioni diverse della stessa pipeline. In caso contrario, l'impostazione predefinita prevede l'utilizzo di un valore dal clock di sistema.


7. Aggiungere il modulo [Train Model](./train-model.md) alla pipeline e connettere un set di dati con etichetta.

8. Inviare la pipeline.

### <a name="results-for-ordinary-least-squares-model"></a>Risultati per il modello di tipo minimo comune

Al termine del training:


+ Per eseguire stime, connettere il modello sottoposto a training al modulo [Score Model](./score-model.md) , insieme a un set di dati con nuovi valori. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Creare un modello di regressione usando la discesa sfumatura online

1. Aggiungere il modulo **modello di regressione lineare** alla pipeline nella finestra di progettazione.

    È possibile trovare questo modulo nella categoria **Machine Learning** . Espandere **Inizializza modello**, espandere **regressione**e trascinare il modulo **modello di regressione lineare** nella pipeline

2. Nell'elenco a discesa **Metodo soluzione** nel riquadro **Proprietà** scegliere **discesa sfumatura online** come metodo di calcolo utilizzato per trovare la linea di regressione.

3. Per la **modalità di creazione**di un trainer, indicare se si desidera eseguire il training del modello con un set di parametri predefinito oppure se si desidera ottimizzare il modello utilizzando uno sweep di parametri.

    + **Singolo parametro**: se si sa come si vuole configurare la rete di regressione lineare, è possibile fornire un set di valori specifico come argomenti.
    
    + **Intervallo parametri**: selezionare questa opzione se non si è certi dei parametri migliori e si vuole eseguire uno sweep di parametri. Selezionare un intervallo di valori di cui eseguire l'iterazione e l' [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) esegue l'iterazione su tutte le combinazioni possibili delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  

   
4. Per la **velocità di apprendimento**specificare la velocità di apprendimento iniziale per l'utilità di ottimizzazione per la discesa con gradienti stocastici.

5. Per **numero di epoche di training**, digitare un valore che indichi il numero di volte in cui l'algoritmo deve scorrere gli esempi. Per i set di dati con un numero limitato di esempi, questo numero deve essere elevato per raggiungere la convergenza.

6. **Normalizzare le funzionalità**: se i dati numerici utilizzati per il training del modello sono già stati normalizzati, è possibile deselezionare questa opzione. Per impostazione predefinita, il modulo normalizza tutti gli input numerici in un intervallo compreso tra 0 e 1.

    > [!NOTE]
    > 
    > Ricordarsi di applicare lo stesso metodo di normalizzazione ai nuovi dati usati per l'assegnazione dei punteggi.

7. In **peso regolarizzazione L2**Digitare il valore da usare come peso per la regolarizzazione L2. È consigliabile usare un valore diverso da zero per evitare l'overfitting.

    Per altre informazioni sul modo in cui la regolarizzazione influiscono sul montaggio del modello, vedere questo articolo: [regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Selezionare l'opzione **Diminuisci velocità di apprendimento**se si desidera che la velocità di apprendimento diminuisca con l'avanzamento delle iterazioni.  

10. Per il valore di **inizializzazione del numero casuale**, è possibile digitare facoltativamente un valore per inizializzare il generatore di numeri casuali usato dal modello. L'uso di un valore di inizializzazione è utile se si desidera mantenere gli stessi risultati in esecuzioni diverse della stessa pipeline.


12. Eseguire il training del modello:

    + Se si imposta la **modalità di creazione dell'allenatore** su un **singolo parametro**, connettere un set di dati con tag e il modulo [Train Model](train-model.md) .  
  
    + Se si imposta la **modalità di creazione dell'allenatore** sull'intervallo di **parametri**, connettere un set di dati con tag ed eseguire il training del modello usando gli [iperparametri del modello di ottimizzazione](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Se si passa un intervallo di parametri a [Training Model](train-model.md), viene utilizzato solo il valore predefinito nell'elenco di parametri singoli.  
    > 
    > Se si passa un singolo set di valori di parametro al modulo [Tune Model iperparameters](tune-model-hyperparameters.md) , quando si prevede un intervallo di impostazioni per ogni parametro, i valori vengono ignorati e vengono usati i valori predefiniti per lo Learner.  
    > 
    > Se si seleziona l'opzione **Parameter range** e si immette un solo valore per qualsiasi parametro, il singolo valore specificato viene usato durante lo sweep, anche se altri parametri cambiano in un intervallo di valori.

13. Inviare la pipeline.

### <a name="results-for-online-gradient-descent"></a>Risultati per la discesa sfumatura online

Al termine del training:

+ Per eseguire stime, connettere il modello sottoposto a training al modulo [Score Model](./score-model.md) , insieme ai nuovi dati di input.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 