---
title: Usare il modulo Train Wide & Deep Recommender
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Train Wide & Deep Recommender per eseguire il training di un modello di raccomandazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: a548a1aa6b7c6382d00e218f1b61347002df2b38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907790"
---
# <a name="train-wide--deep-recommender"></a>Eseguire il training dell'ampia & raccomandazione profonda
In questo articolo viene descritto come usare il modulo **Train Wide di & Deep Recommender** in Azure machine learning designer, per eseguire il training di un modello di raccomandazione. Questo modulo si basa su un'ampia gamma di & Deep Learning, proposta da Google.

Il modulo **Train Wide pro&s Deep Recommender** legge un set di dati di triple utente-elemento-classificazione e, facoltativamente, alcune funzionalità utente ed elemento. Viene restituito un Consiglio di approfondimento & di livello avanzato.  È quindi possibile usare il modello sottoposto a training per generare stime di classificazione o consigli usando il modulo [Score Wide e Deep Recommender](score-wide-and-deep-recommender.md) .  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>Ulteriori informazioni sui modelli di raccomandazione e sull'ampia gamma di consigli &  

L'obiettivo principale di un sistema di raccomandazione è quello di consigliare uno o più *elementi* agli *utenti* del sistema. Esempi di elemento possono essere un film, un ristorante, un libro o un brano musicale. Un utente può essere una persona, un gruppo di persone o un'altra entità con preferenze per determinati elementi.  

Esistono due approcci principali ai sistemi di raccomandazione. 

+ Il primo è l'approccio **basato sul contenuto** che si avvale sia di funzioni utente sia di funzioni elemento. Gli utenti possono essere descritti da proprietà quali età e sesso, mentre gli elementi possono essere descritti da proprietà quali autore e produttore. Esempi tipici di sistemi di raccomandazione basati sul contenuto sono disponibili nei siti di social matchmaking. 
+ Il secondo approccio è il **filtro collaborativo**, che usa solo identificatori degli utenti e degli elementi e ottiene implicite informazioni su queste entità da una matrice di classificazioni (di tipo sparse) fornita dagli utenti agli elementi. È possibile apprendere informazioni su un utente dagli elementi che ha classificato e dagli altri utenti che hanno classificato gli stessi elementi.  

L'ampia & raccomandazione profonda combina questi approcci, usando il filtro collaborativo con un approccio basato sul contenuto. Per questo motivo, viene considerato un **sistema di raccomandazione ibrido**. 

Come funziona: quando un utente è relativamente nuovo al sistema, le stime sono migliorate mediante l'uso delle informazioni sulle funzionalità relative all'utente, risolvendo così il noto problema di "avvio a freddo". Tuttavia, una volta raccolto un numero sufficiente di classificazioni eseguite da un particolare utente, è possibile eseguire stime completamente personalizzate per tale utente in base alle sue specifiche classificazioni piuttosto che in base alle sole funzioni. Di conseguenza, esiste una transizione graduale da raccomandazioni basate sul contenuto a raccomandazioni basate sul filtro collaborativo. Anche se non sono disponibili funzionalità per gli utenti o gli elementi, Wide & raccomandazione Deep continuerà a funzionare nella modalità di filtro collaborativo.  

Per informazioni dettagliate sull'ampia & raccomandazione approfondita e sul relativo algoritmo probabilistico sottostante, vedere il documento di ricerca pertinente: [wide & Deep learning per i sistemi di raccomandazione](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>Come configurare il training Wide & raccomandazione Deep  

+ [Preparare i dati di training](#prepare-data)
+ [Eseguire il training del modello](#train-the-model)

### <a name="prepare-data"></a>Preparazione dei dati

Prima di provare a usare il modulo, è essenziale che i dati siano nel formato previsto dal modello di raccomandazione. È necessario un set di dati di training di **triple utente-elemento-classificazione** , ma è anche possibile includere le funzionalità utente e le funzionalità degli elementi (se disponibili) in set di dati separati.

#### <a name="required-dataset-of-user-item-ratings"></a>Set di dati obbligatorio di User-Item-ratings

I dati di input utilizzati per il training devono contenere il tipo di dati corretto nel formato corretto: 

+ La prima colonna deve contenere gli identificatori utente.
+ La seconda colonna deve contenere gli identificatori di elemento.
+ La terza colonna contiene la classificazione per la coppia utente-elemento. I valori di classificazione devono essere di tipo numerico. 

Un tipico set di classificazioni utente-elemento, ad esempio, potrebbe essere simile al seguente:

|UserId|MovieId|Classificazione|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Set di dati delle funzionalità utente (facoltativo)

Il set di dati delle **funzionalità utente** deve contenere gli identificatori degli utenti e usare gli stessi identificatori forniti nella prima colonna del set di dati users-Items-ratings. Le colonne rimanenti possono contenere un numero qualsiasi di funzionalità che descrivono gli utenti.  

Per un esempio, un set tipico di funzionalità utente potrebbe essere simile al seguente: 

|UserId|Età|Sesso|Interest|Location|
|------------|--------------|-----------------------|---------------|------------|
|1|25|male| Dramma    |Europa|
|223|40|female|Romanticismo|Asia|

#### <a name="item-features-dataset-optional"></a>Set di dati features (facoltativo)

Il set di dati delle funzioni elemento deve contenere gli identificatori elemento nella prima colonna. Le colonne rimanenti possono contenere un numero qualsiasi di funzionalità descrittive per gli elementi.  

Per un esempio, un set tipico di funzionalità degli elementi potrebbe essere simile al seguente:  

|MovieId|Titolo|Lingua originale|Generi|Year|
|-------------|-------------|-------------------|-----------|---------------|
|68646|Padrino|Inglese|Dramma|1972|
|31381|Al vento|Inglese|Cronologia|1939|

### <a name="train-the-model"></a>Eseguire il training del modello

1.  Aggiungere il modulo **Train Wide and Deep Recommender** all'esperimento nella finestra di progettazione e connetterlo al set di dati di training.  
  
2. Se si dispone di un set di dati separato per le funzionalità utente e/o le funzionalità degli elementi, connetterle al modulo **Train Wide e Deep Recommender** .  
  
    - **Set di dati delle funzionalità utente**: connettere il set di dati che descrive gli utenti al secondo input.
    - **Set di dati delle funzionalità elemento**: connettere il set di dati che descrive gli elementi al terzo input.  
    
3.  **Epochs**: indica il numero di volte in cui l'algoritmo deve elaborare i dati di training completi. 

    Maggiore è questo numero, più è appropriato il training; Tuttavia, il training costa più tempo e può causare l'overfitting.

4. **Dimensioni batch**: digitare il numero di esempi di training usati in un solo passaggio di training. 

     Questo iperparametro può influenzare la velocità di training. Una dimensione di batch più elevata comporta un'epoca di costo inferiore, ma può aumentare il tempo di convergenza. Se il batch è troppo grande per adattarsi alla GPU o alla CPU, è possibile che venga generato un errore di memoria.

5.  **Ottimizzazione della parte ampia**: selezionare un ottimizzatore per applicare le sfumature alla parte ampia del modello.

6.  **Velocità di apprendimento di Wide Optimizer**: immettere un numero compreso tra 0,0 e 2,0 che definisce la velocità di apprendimento di Wide part Optimizer.

    Questo iperparametro determina le dimensioni del passaggio a ogni passaggio di training, passando verso una funzione di perdita minima. Una velocità di apprendimento troppo grande può causare il passaggio alla formazione minima, mentre una velocità di apprendimento troppo piccola può causare un problema di convergenza.

7.  **Dimensione funzionalità superata**: digitare la dimensione immettendo le funzionalità ID utente e ID elemento desiderate. 

    Per impostazione predefinita, l'ampia & raccomandazione profonda esegue la trasformazione tra prodotti rispetto alle funzionalità ID utente e ID elemento. Viene eseguito l'hashing del risultato incrociato in base a questo numero per garantire la dimensione.

8.  **Ottimizzazione della parte completa**: selezionare un ottimizzatore per applicare le sfumature alla parte completa del modello.

9.  Velocità di apprendimento per l'utilità di **ottimizzazione approfondita**: immettere un numero compreso tra 0,0 e 2,0 che definisce la velocità di apprendimento dell'utilità di ottimizzazione delle parti approfondite.

10.  **Dimensione di incorporamento utente**: digitare un Integer per specificare la dimensione dell'incorporamento dell'ID utente.

     L'ampia & raccomandazione profonda crea gli incorporamenti dell'ID utente condiviso e gli incorporamenti di ID elemento sia per la parte ampia che per la parte profonda.

11.  **Dimensione di incorporamento elementi**: digitare un Integer per specificare la dimensione dell'incorporamento dell'ID elemento.

12.  **Funzionalità categoriche che incorporano la dimensione**: immettere un numero intero per specificare le dimensioni degli incorporamenti di funzionalità categorici.

     In un componente approfondito della gamma & Deep Recommender, un vettore di incorporamento è learnt per ogni funzionalità categorica. E questi vettori di incorporamento condividono la stessa dimensione.

13.  **Unità nascoste**: digitare il numero di nodi nascosti di un componente profondo. Il numero di nodi in ogni livello è separato da virgole. Per il tipo "1.000.500.100", ad esempio, si specifica che il componente Deep dispone di tre livelli, il primo livello e l'ultimo hanno rispettivamente i nodi 1000, 500 nodi e 100.

14.  **Funzione di attivazione**: selezionare una funzione di attivazione applicata a ogni livello. l'impostazione predefinita è ReLU.

15.  **Abbandono**: immettere un numero compreso tra 0,0 e 1,0 per determinare la probabilità che gli output vengano eliminati in ogni livello durante il training.

     L'abbandono è un metodo di regolarizzazione per impedire l'overfitting delle reti neurali. Una decisione comune per questo valore consiste nell'iniziare con 0,5, che sembra essere quasi ottimale per un'ampia gamma di reti e attività.

16.  **Normalizzazione batch**: selezionare questa opzione per utilizzare la normalizzazione batch dopo ogni livello nascosto nel componente Deep.

     La normalizzazione di batch è una tecnica per contrastare il problema di spostamento interno della covariante durante il training delle reti. In generale, può contribuire a migliorare la velocità, le prestazioni e la stabilità delle reti. 

17.  Eseguire la pipeline.

##  <a name="technical-notes"></a>Note tecniche

La vasta & consente di eseguire congiuntamente ampi modelli lineari e reti neurali profonde per combinare i punti di forza della memorizzazione e della generalizzazione. Il componente Wide accetta un set di funzionalità non elaborate e trasformazioni di funzionalità per memorizzare le interazioni delle funzionalità. E con meno progettazione delle funzionalità, il componente profondo si generalizza alle combinazioni di funzionalità non rilevate tramite incorporamenti di funzioni dense a dimensione ridotta. 

Nell'implementazione del Recommender Wide &, il modulo usa una struttura del modello predefinita. Il componente Wide accetta gli incorporamenti degli utenti, gli incorporamenti di elementi e la trasformazione di prodotto incrociato di ID utente e ID elemento come input. Per la parte completa del modello, viene illustrato un vettore di incorporamento per ogni funzionalità categorico. Insieme ad altri vettori di funzionalità numeriche, questi vettori vengono quindi inseriti nella rete neurale Deep feed-Inoltr. La parte ampia e la parte completa vengono combinate sommando la probabilità finale dei log di output come la stima, che infine passa a una funzione di perdita comune per il training misto.


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) in Azure Machine Learning. 
