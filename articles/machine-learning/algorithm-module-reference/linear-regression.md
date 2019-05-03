---
title: 'Regressione lineare: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Linear Regression nel servizio Azure Machine Learning per creare un modello di regressione lineare per l'uso in un esperimento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029716"
---
# <a name="linear-regression-module"></a>Modulo di regressione lineare
Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un modello di regressione lineare per l'uso in un esperimento.  Regressione lineare tenta di stabilire una relazione lineare tra uno o più variabili indipendenti e un risultato numerico o la variabile dipendente. 

Utilizzare questo modulo per definire un metodo di regressione lineare e quindi eseguire il training di un modello usando un set di dati con etichette. Il modello con training può quindi utilizzabile per eseguire stime.

## <a name="about-linear-regression"></a>Sulla regressione lineare

Regressione lineare è un metodo statistico comune, che è stato adottato nell'apprendimento automatico e migliorato con molti nuovi metodi per adattare la riga e la misurazione di errore. Nel suo significato più semplice, la regressione fa riferimento alla stima di una destinazione numerica. Regressione lineare è ancora un'ottima scelta quando si desidera che un modello semplice per un'attività predittiva di base. Regressione lineare in genere anche funziona bene in set di dati di tipo sparse, a elevata dimensionalità privi di complessità.

Azure Machine Learning supporta un'ampia gamma di modelli di regressione, oltre a regressione lineare. Tuttavia, il termine "regressione" può essere interpretato a regime di controllo libero, e non sono supportati alcuni tipi di regressione fornito in altri strumenti.

+ Il problema di regressione classico prevede una singola variabile indipendente e una variabile dipendente. Questa operazione viene definita *basato sulla regressione semplice*.  Questo modulo supporta basato sulla regressione semplice.

+ *Regressione lineare multipla* prevede due o più variabili indipendenti che contribuiscono a una singola variabile dipendente. I problemi in cui vengono utilizzate più input per stimare un singolo risultato numerico vengono inoltre chiamati *regressione lineare multivariata*.

    Il **Linear Regression** modulo può risolvere questi problemi, nonché la maggior parte degli altri moduli di regressione.

+ *Regressione multietichetta* è l'attività di stima di più variabili dipendenti all'interno di un singolo modello. Ad esempio, nella regressione logistica multietichetta, un campione può essere assegnato a più etichette diverse. (Questo è diverso dall'attività di stima di più livelli all'interno di una variabile di classe singola).

    Questo tipo di regressione non è supportato in Azure Machine Learning. Per stimare più variabili, creare uno strumento di apprendimento separato per ogni output che si desidera stimare.

Per anni statistici hanno sviluppato metodi sempre più avanzati per la regressione. Questo vale anche per la regressione lineare. Questo modulo supporta due metodi per misurare l'errore e adatta la linea di regressione: metodo dei minimi quadrati e valori descent con sfumatura.

- **Valori descent con sfumatura** è un metodo che riduce al minimo la quantità di errori in ogni fase del processo di training del modello. Esistono molte varianti su valori descent con sfumatura e l'ottimizzazione per diversi problemi di apprendimento è stata ampiamente studiato. Se si sceglie questa opzione per eseguire **Solution method**, è possibile impostare una serie di parametri per controllare la dimensione di incremento, velocità di apprendimento e così via. Questa opzione supporta anche l'utilizzo di un integrata sweep di parametri.

- **Metodo dei minimi quadrati** è una delle tecniche più usate nella regressione lineare. Ad esempio, dei minimi quadrati è il metodo che viene usato negli strumenti di analisi per Microsoft Excel.

    Metodo dei minimi quadrati fa riferimento alla funzione di perdita, che calcola l'errore come la somma del quadrato della distanza dal valore effettivo alla riga stimata e adatta il modello riducendo al minimo l'errore quadratico. Questo metodo presuppone una forte relazione lineare tra gli input e la variabile dipendente.

## <a name="configure-linear-regression"></a>Configurare la regressione lineare

Questo modulo supporta due metodi per l'adattamento di un modello di regressione, con opzioni diverse:

+ [Creare un modello di regressione usando i valori descent con sfumatura online](#bkmk_GradientDescent)

    Valori descent con sfumatura è una funzione di perdita migliore per i modelli che sono più complesse o che sono troppo pochi dati di training, dato il numero di variabili.



+ [Adatta un modello di regressione usando metodo dei minimi quadrati](#bkmk_OrdinaryLeastSquares)

    Per set di dati di piccole dimensioni, è consigliabile selezionare metodo dei minimi quadrati. Questo metodo deve produrre risultati simili in Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a> Creare un modello di regressione usando metodo dei minimi quadrati

1. Aggiungere il **modello di regressione lineare** modulo all'esperimento nell'interfaccia.

    È possibile trovare questo modulo nel **Machine Learning** categoria. Espandere **modello di inizializzazione**, espandere **regressione**, quindi trascinare il **modello di regressione lineare** modulo nell'esperimento.

2. Nel **delle proprietà** riquadro, nella **Solution method** elenco a discesa, seleziona **Ordinary Least Squares**. Questa opzione specifica il metodo di calcolo usato per trovare la linea di regressione.

3. Nelle **peso regolarizzazione L2**, digitare il valore da usare come peso per la regolarizzazione L2. È consigliabile utilizzare un valore diverso da zero per evitare l'overfitting.

     Per altre informazioni su come regolarizzazione influisce sull'adattamento di modelli, vedere questo articolo: [Regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Selezionare l'opzione **termine dell'intercetta inclusione**, se si desidera visualizzare il termine per l'intercettazione.

    Deselezionare questa opzione se non è necessario esaminare la formula di regressione.

5. Per **seed numeri Random**, è possibile digitare un valore per inizializzare il generatore di numeri casuali usato dal modello.

    Uso di un valore di inizializzazione è utile se si desidera mantenere gli stessi risultati tra diverse esecuzioni dello stesso esperimento. In caso contrario, l'impostazione predefinita consiste nell'usare un valore dal clock di sistema.


7. Aggiungere il [Train Model](./train-model.md) modulo nell'esperimento e connettere un set di dati con etichette.

8. Eseguire l'esperimento.

## <a name="results-for-ordinary-least-squares-model"></a>Risultati per il modello di metodo dei minimi quadrati

Dopo aver completata la formazione:

+ Per visualizzare i parametri del modello, l'output trainer e scegliere **Visualize**.

+ Per eseguire stime, connettere il modello con training per il [Score Model](./score-model.md) modulo, insieme a un set di dati di nuovi valori. 


## <a name="bkmk_GradientDescent"></a> Creare un modello di regressione usando i valori descent con sfumatura online

1. Aggiungere il **modello di regressione lineare** modulo all'esperimento nell'interfaccia.

    È possibile trovare questo modulo nel **Machine Learning** categoria. Espandere **modello di inizializzazione**, espandere **regressione**e trascinare il **modello di regressione lineare** modulo nell'esperimento

2. Nel **delle proprietà** riquadro, nella **Solution method** elenco a discesa scegliere **valori descent con sfumatura Online** come metodo di calcolo usato per trovare la linea di regressione.

3. Per la **modalità di creazione trainer**, indicare se si desidera eseguire il training del modello con un set di parametri predefinito o se si vuole ottimizzare il modello tramite sweep di parametri.

    + **Singolo parametro**: Se si conosce la modalità con cui si desidera configurare la rete di regressione lineare, è possibile definire un set specifico di valori come argomenti.

   
4. Per la **velocità di apprendimento**, specificare la velocità di apprendimento iniziale per l'ottimizzatore di valori descent con sfumatura stocastica.

5. Per la **numero di periodi di training**, digitare un valore che indica quante volte l'algoritmo deve scorrere gli esempi. Per set di dati con un numero limitato di esempi di questo numero deve essere elevato per raggiungere la convergenza.

6. **Funzioni vengono normalizzate**: Se si sono normalizzati già i dati numerici utilizzati per il training del modello, è possibile deselezionare questa opzione. Per impostazione predefinita, il modulo Normalizza tutti gli input numerici a un intervallo compreso tra 0 e 1.

    > [!NOTE]
    > 
    > Ricordarsi di applicare lo stesso metodo di normalizzazione ai nuovi dati usati per il punteggio.

7. Nelle **peso regolarizzazione L2**, digitare il valore da usare come peso per la regolarizzazione L2. È consigliabile utilizzare un valore diverso da zero per evitare l'overfitting.

    Per altre informazioni su come regolarizzazione influisce sull'adattamento di modelli, vedere questo articolo: [Regolarizzazione L1 e L2 per Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Selezionare l'opzione **velocità di apprendimento diminuzione**se si vuole che la velocità di apprendimento per diminuire mentre le iterazioni proseguono.  

10. Per **seed numeri Random**, è possibile digitare un valore per inizializzare il generatore di numeri casuali usato dal modello. Uso di un valore di inizializzazione è utile se si desidera mantenere gli stessi risultati tra diverse esecuzioni dello stesso esperimento.


12. Aggiungere un set di dati con etichette e uno dei moduli di training.

    Se non si usa uno sweep di parametri, usare il [Train Model](train-model.md) modulo.

13. Eseguire l'esperimento.

## <a name="results-for-online-gradient-descent"></a>Risultati per valori descent con sfumatura online

Dopo aver completata la formazione:

+ Per eseguire stime, connettere il modello con training per il [Score Model](./score-model.md) modulo, insieme ai nuovi dati di input.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 