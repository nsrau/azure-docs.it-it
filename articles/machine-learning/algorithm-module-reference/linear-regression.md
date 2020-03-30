---
title: 'Regressione lineare: riferimento al moduloLine Regression: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Regressione lineare in Azure Machine Learning per creare un modello di regressione lineare da usare in una pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 602553637e21b17aa4f9bc7402753af024c697c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477562"
---
# <a name="linear-regression-module"></a>Modulo di regressione lineare
Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per creare un modello di regressione lineare da utilizzare in una pipeline.  La regressione lineare tenta di stabilire una relazione lineare tra una o più variabili indipendenti e un risultato numerico o una variabile dipendente. 

Utilizzare questo modulo per definire un metodo di regressione lineare e quindi eseguire il training di un modello usando un set di dati con etichetta. Sarà quindi possibile usare il modello con training per ottenere stime.

## <a name="about-linear-regression"></a>Informazioni sulla regressione lineare

La regressione lineare è un metodo statistico comune, che è stato adottato nell'apprendimento automatico e migliorato con molti nuovi metodi per adattare la linea e misurare l'errore. Nel senso più elementare, la regressione si riferisce alla previsione di un obiettivo numerico. La regressione lineare è ancora una buona scelta quando si desidera un modello semplice per un'attività predittiva di base. La regressione lineare tende anche a funzionare bene su set di dati ad alta dimensione e sparse privi di complessità.

Azure Machine Learning supporta una varietà di modelli di regressione, oltre alla regressione lineare. Tuttavia, il termine "regressione" può essere interpretato liberamente e alcuni tipi di regressione forniti in altri strumenti non sono supportati.

+ Il problema di regressione classica coinvolge una singola variabile indipendente e una variabile dipendente. Questa operazione è *denominata regressione semplice*.  Questo modulo supporta la regressione semplice.

+ *La regressione lineare multipla* coinvolge due o più variabili indipendenti che contribuiscono a una singola variabile dipendente. I problemi in cui vengono utilizzati più input per prevedere un singolo risultato numerico sono anche chiamati *regressione lineare multivariata*.

    Il modulo **di regressione lineare** può risolvere questi problemi, così come la maggior parte degli altri moduli di regressione.

+ *La regressione con* più etichette è il compito di prevedere più variabili dipendenti all'interno di un singolo modello. Nella regressione logistica multietichetta, ad esempio, un campione può essere assegnato a più etichette diverse. Questo è diverso dal compito di prevedere più livelli all'interno di una singola variabile di classe.

    Questo tipo di regressione non è supportato in Azure Machine Learning.This type of regression is not supported in Azure Machine Learning. Per prevedere più variabili, crea un Allievo separato per ogni output che desideri stimare.

Per anni gli statistici hanno sviluppato metodi sempre più avanzati di regressione. Questo vale anche per la regressione lineare. Questo modulo supporta due metodi per misurare l'errore e adattarsi alla retta di regressione: metodo dei minimi quadrati ordinari e discesa del gradiente.

- **Discesa sfumata** è un metodo che riduce al minimo la quantità di errore in ogni fase del processo di training del modello. Esistono molte varianti del metodo dei valori descent con sfumatura e l'ottimizzazione di tale metodo per diversi problemi di apprendimento è stata oggetto di approfonditi studi. Se si sceglie questa opzione per **Metodo soluzione**, è possibile impostare una varietà di parametri per controllare la dimensione del passo, la velocità di apprendimento e così via. Questa opzione supporta anche l'uso di una sweep di parametro integrata.

- **I minimi quadrati ordinari** sono una delle tecniche più comunemente utilizzate nella regressione lineare. Ad esempio, i minimi quadrati sono il metodo utilizzato in Strumenti di analisi per Microsoft Excel.

    Il metodo dei minimi quadrati fa riferimento alla funzione di perdita, che calcola l'errore come la somma del quadrato della distanza dal valore effettivo alla riga stimata e adatta il modello riducendo al minimo l'errore quadratico. Questo metodo presuppone una relazione lineare forte tra gli input e la variabile dipendente.

## <a name="configure-linear-regression"></a>Configurare la regressione lineare

Questo modulo supporta due metodi per adattarsi a un modello di regressione, con diverse opzioni:This module supports two methods for fitting a regression model, with different options:

+ [Adattare un modello di regressione utilizzando i minimi quadrati ordinari](#create-a-regression-model-using-ordinary-least-squares)

    Per set di dati di piccole dimensioni, è consigliabile selezionare i minimi quadrati ordinari. Questo dovrebbe dare risultati simili a Excel.
    
+ [Creare un modello di regressione usando il discesa del gradiente onlineCreate a regression model using online gradient descent](#create-a-regression-model-using-online-gradient-descent)

    Il metodo dei valori descent con sfumatura rappresenta una funzione di perdita migliore per i modelli più complessi o per i quali sono disponibili pochi dati di training, considerato il numero di variabili.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Creare un modello di regressione usando i minimi quadrati normaliCreate a regression model using ordinary least squares

1. Aggiungere il modulo Modello di **regressione lineare** alla pipeline nella finestra di progettazione.

    È possibile trovare questo modulo nella categoria **Machine Learning.You can** find this module in the Machine Learning category. Espandere **Inizializza modello**, **regressione**, quindi trascinare il modulo Modello di **regressione lineare** nella pipeline.

2. Nell'elenco a discesa **Metodo soluzione** del riquadro **Proprietà** selezionare Quadrati **minimi ordinari**. Questa opzione specifica il metodo di calcolo utilizzato per trovare la retta di regressione.

3. In **Peso regolarizzazione L2**digitare il valore da utilizzare come peso per la regolarizzazione L2. Si consiglia di utilizzare un valore diverso da zero per evitare l'overfitting.

     Per altre informazioni su come la regolarizzazione influisce sul montaggio del modello, vedere questo articolo: [Regolarizzazione L1 e L2 per Machine LearningTo](https://msdn.microsoft.com/magazine/dn904675.aspx) learn more about how regularization affects model fitting, see this article: L1 and L2 Regularization for Machine Learning

4. Selezionare l'opzione **Includi termine di intercettazione**, se si desidera visualizzare il termine per l'intercetta.

    Deselezionare questa opzione se non è necessario esaminare la formula di regressione.

5. Per **Valore di serie con numero casuale**, è possibile digitare facoltativamente un valore per eseguire il seeded del generatore di numeri casuali utilizzato dal modello.

    L'uso di un valore di seeding è utile se si desidera mantenere gli stessi risultati tra diverse esecuzioni della stessa pipeline. In caso contrario, l'impostazione predefinita prevede l'utilizzo di un valore dell'orologio di sistema.


7. Aggiungere il modulo [Train Model](./train-model.md) alla pipeline e connettere un set di dati con etichetta.

8. Inviare la pipeline.

### <a name="results-for-ordinary-least-squares-model"></a>Risultati per il modello ordinario dei minimi quadrati

Al termine dell'allenamento:


+ Per eseguire stime, collegare il modello sottoposto a training al modulo Modello di [punteggio,](./score-model.md) insieme a un set di dati di nuovi valori. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Creare un modello di regressione usando il discesa del gradiente onlineCreate a regression model using online gradient descent

1. Aggiungere il modulo Modello di **regressione lineare** alla pipeline nella finestra di progettazione.

    È possibile trovare questo modulo nella categoria **Machine Learning.You can** find this module in the Machine Learning category. Espandere **Inizializza modello**, espandere **Regressione**e trascinare il modulo Modello di **regressione lineare** nella pipeline

2. Nell'elenco a discesa **Metodo soluzione** del riquadro **Proprietà** scegliere **Discesa sfumatura online** come metodo di calcolo utilizzato per trovare la linea di regressione.

3. Per **Modalità formatore**, indicare se si desidera eseguire il training del modello con un set predefinito di parametri o se si desidera ottimizzare il modello utilizzando una sweep di parametro.

    + **Parametro singolo**: se si sa come si desidera configurare la rete di regressione lineare, è possibile fornire un set specifico di valori come argomenti.
    
    + **Intervallo parametri**: Selezionare questa opzione se non si è sicuri dei parametri migliori e si desidera eseguire una sweep di parametro. Selezionare un intervallo di valori su cui eseguire l'iterazione e gli [Hyperparametri del modello di ottimizzazione](tune-model-hyperparameters.md) scorrono tutte le possibili combinazioni delle impostazioni fornite per determinare gli iperparametri che producono i risultati ottimali.  

   
4. Per **Velocità di apprendimento**, specificare la velocità di apprendimento iniziale per l'ottimizzatore di discesa del gradiente stocastico.

5. Per **Numero di epoche di training**, digitare un valore che indica quante volte l'algoritmo deve scorrere gli esempi. Per i set di dati con un numero limitato di esempi, questo numero deve essere elevato per raggiungere la convergenza.

6. **Normalizzare le funzioni**: se sono già stati normalizzati i dati numerici utilizzati per eseguire il training del modello, è possibile deselezionare questa opzione. Per impostazione predefinita, il modulo normalizza tutti gli input numerici in un intervallo compreso tra 0 e 1.

    > [!NOTE]
    > 
    > Ricordarsi di applicare lo stesso metodo di normalizzazione ai nuovi dati usati per il punteggio.

7. In **Peso regolarizzazione L2**digitare il valore da utilizzare come peso per la regolarizzazione L2. Si consiglia di utilizzare un valore diverso da zero per evitare l'overfitting.

    Per altre informazioni su come la regolarizzazione influisce sul montaggio del modello, vedere questo articolo: [Regolarizzazione L1 e L2 per Machine LearningTo](https://msdn.microsoft.com/magazine/dn904675.aspx) learn more about how regularization affects model fitting, see this article: L1 and L2 Regularization for Machine Learning


9. Selezionare l'opzione **Diminuisci velocità di apprendimento**se si desidera che la velocità di apprendimento diminuisca man mano che le iterazioni avanzano.  

10. Per **Valore di serie con numero casuale**, è possibile digitare facoltativamente un valore per eseguire il seeded del generatore di numeri casuali utilizzato dal modello. L'uso di un valore di seeding è utile se si desidera mantenere gli stessi risultati tra diverse esecuzioni della stessa pipeline.


12. Aggiungere un set di dati con etichetta e uno dei moduli di training.

    Se non si utilizza una sweep di parametro, utilizzare il modulo [Train Model.](train-model.md)

13. Inviare la pipeline.

### <a name="results-for-online-gradient-descent"></a>Risultati per discesa gradiente online

Al termine dell'allenamento:

+ Per eseguire stime, collegare il modello sottoposto a training al modulo Modello di [punteggio,](./score-model.md) insieme ai nuovi dati di input.


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 