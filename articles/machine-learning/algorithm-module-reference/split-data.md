---
title: 'Dividi dati: riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Dividi dati in Azure Machine Learning per dividere un set di dati in due set distinti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9eba6f2c47629b708dde4a5a2888b76dbd24b4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455894"
---
# <a name="split-data-module"></a>Modulo Dividi dati

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare il modulo Dividi dati per dividere un set di dati in due set distinti.

Questo modulo è utile quando è necessario separare i dati in set di training e test. È inoltre possibile personalizzare il modo in cui i dati vengono suddivisi. Alcune opzioni supportano la randomizzazione dei dati. Altri sono personalizzati per un determinato tipo di dati o tipo di modello.

## <a name="configure-the-module"></a>Configurare il modulo

> [!TIP]
> Prima di scegliere la modalità di suddivisione, leggere tutte le opzioni per determinare il tipo di divisione necessario.
> Se si modifica la modalità di divisione, tutte le altre opzioni potrebbero essere reimpostate.

1. Aggiungere il modulo **Dividi dati** alla pipeline nella finestra di progettazione. Questo modulo è disponibili in **Trasformazione dati**, nella categoria **Esempio e divisione** .

1. **Modalità di divisione**: Scegliere una delle seguenti modalità, a seconda del tipo di dati che hai e di come vuoi dividerlo. Ogni modalità di divisione ha diverse opzioni.

   - **Dividi righe**: Utilizzare questa opzione se si desidera dividere i dati in due parti. È possibile specificare la percentuale di dati da inserire in ogni divisione. Per impostazione predefinita, i dati sono divisi 50/50.

     È inoltre possibile randomizzare la selezione di righe in ogni gruppo e utilizzare il campionamento stratificato. Nel campionamento stratificato, è necessario selezionare una singola colonna di dati per la quale si desidera che i valori vengano ripartiti equamente tra i due set di dati dei risultati.  

   - **Divisione espressione regolare**: scegliere questa opzione quando si desidera dividere il set di dati verificando un valore in una singola colonna.

     Ad esempio, se stai analizzando il sentiment, puoi verificare la presenza di un determinato nome di prodotto in un campo di testo. È quindi possibile dividere il set di dati in righe con il nome del prodotto di destinazione e le righe senza il nome del prodotto di destinazione.

   - **Divisione espressione relativa**: utilizzare questa opzione ogni volta che si desidera applicare una condizione a una colonna numerica. Il numero può essere un campo data/ora, una colonna che contiene importi di età o in dollari o anche una percentuale. Ad esempio, è possibile dividere il set di dati in base al costo degli elementi, raggruppare le persone per fasce di età o separare i dati in base a una data del calendario.

### <a name="split-rows"></a>Dividi righe

1. Aggiungere il modulo [Dividi dati](./split-data.md) alla pipeline nella finestra di progettazione e connettere il set di dati che si desidera dividere.
  
1. Per **la modalità di divisione**, selezionare **Dividi righe**. 

1. **Frazione di righe nel primo set di dati**di output: utilizzare questa opzione per determinare il numero di righe che andranno nel primo output (sul lato sinistro). Tutte le altre righe andranno nel secondo uscita (lato destro).

   Il rapporto rappresenta la percentuale di righe inviate al primo set di dati di output, pertanto è necessario immettere un numero decimale compreso tra 0 e 1.
     
   Ad esempio, se si immette **0,75** come valore, il set di dati verrà diviso 75/25. In questa divisione, il 75% delle righe verrà inviato al primo set di dati di output. Il restante 25% verrà inviato al secondo set di dati di output.
  
1. Selezionare l'opzione **Divisione casuale** se si desidera randomizzare la selezione di dati nei due gruppi. Questa è l'opzione preferita quando si creano set di dati di training e test.

1. **Seed casuale**: Immettere un valore intero non negativo per avviare la sequenza pseudocasuale delle istanze da utilizzare. Questo valore di inizializzazione predefinito viene usato in tutti i moduli che generano numeri casuali. 

   La specifica di un valore di serie rende i risultati riproducibili. Se è necessario ripetere i risultati di un'operazione di divisione, è necessario specificare un valore di serie per il generatore di numeri casuali. In caso contrario, il valore di inizializzazione casuale è impostato per impostazione predefinita su **0**, il che significa che il valore di inizializzazione iniziale viene ottenuto dall'orologio di sistema. Di conseguenza, la distribuzione dei dati può differire leggermente ogni volta che si esegue una suddivisione. 

1. **Divisione stratificata**: Impostare questa opzione su **True** per garantire che i due set di dati di output contengano un campione rappresentativo dei valori nella *colonna degli strati* o nella colonna della chiave di *stratificazione*. 

   Con il campionamento stratificato, i dati vengono divisi in modo che ogni set di dati di output ottenga all'incirca la stessa percentuale di ogni valore di destinazione. Ad esempio, è possibile assicurarsi che i set di training e test siano approssimativamente bilanciati per quanto riguarda il risultato o per un'altra colonna (ad esempio il sesso).

1. Inviare la pipeline.


## <a name="select-a-regular-expression"></a>Selezionare un'espressione regolare

1. Aggiungere il modulo [Dividi dati](./split-data.md) alla pipeline e connetterlo come input al set di dati che si desidera dividere.  
  
1. Per **Modalità di divisione**, selezionare **Espressione regolare divisa**.

1. Nella casella **Espressione regolare** immettere un'espressione regolare valida. 
  
   L'espressione regolare deve seguire la sintassi Python per le espressioni regolari.

1. Inviare la pipeline.

   In base all'espressione regolare fornita, il set di dati viene suddiviso in due set di righe: righe con valori che corrispondono all'espressione e a tutte le righe rimanenti. 

Negli esempi seguenti viene illustrato come dividere un set di dati usando **l'opzione Espressione regolare.** 

### <a name="single-whole-word"></a>Singola parola intera 

In questo esempio vengono inserite nel `Gryphon` primo dataset `Text`tutte le righe che contengono il testo nella colonna . Inserisce altre righe nel secondo output di **Split Data**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Substring

Questo esempio cerca la stringa specificata in qualsiasi posizione all'interno della seconda colonna del set di dati. La posizione è indicata qui dal valore di indice di 1. La corrispondenza prevede la distinzione tra maiuscole e minuscole.

```text
(\1) ^[a-f]
```

Il primo set di dati del risultato contiene tutte `a` `b`le `c` `d`righe `e`in cui la colonna di indice inizia con uno dei seguenti caratteri: , , , , , , . `f` Tutte le altre righe vengono indirizzate al secondo output.

## <a name="select-a-relative-expression"></a>Selezionare un'espressione relativa

1. Aggiungere il modulo [Dividi dati](./split-data.md) alla pipeline e connetterlo come input al set di dati che si desidera dividere.
  
1. Per **Modalità di divisione**, selezionare **Espressione relativa**.
  
1. Nella casella **Espressione relazionale** immettere un'espressione che esegue un'operazione di confronto su una singola colonna.

   Per **colonna numerica**:
   - La colonna contiene numeri di qualsiasi tipo di dati numerico, inclusi i tipi di dati di data e ora.
   - L'espressione può fare riferimento a un massimo di un nome di colonna.
   - Utilizzare il carattere e `&`commerciale, , per l'operazione AND. Utilizzare il carattere barra verticale, `|`, per l'operazione OR.
   - Sono supportati i `<`seguenti `>` `<=`operatori: , , , `>=`, `==`, . `!=`
   - Non è possibile raggruppare `(` `)`le operazioni utilizzando e .
   
   Per **la colonna Stringa**:
   - Sono supportati i `==`seguenti `!=`operatori: , .

1. Inviare la pipeline.

   L'espressione divide il set di dati in due set di righe: righe con valori che soddisfano la condizione e tutte le righe rimanenti.

Negli esempi seguenti viene illustrato come dividere un set di dati utilizzando l'opzione **Espressione relativa** nel modulo **Dividi dati.**  

### <a name="calendar-year"></a>Anno di calendario

Uno scenario comune consiste nel dividere un set di dati in base agli anni. L'espressione seguente seleziona tutte le righe `Year` in `2010`cui i valori nella colonna sono maggiori di .

```text
\"Year" > 2010
```

L'espressione di data deve tenere conto di tutte le parti di data incluse nella colonna di dati. Il formato delle date nella colonna di dati deve essere coerente. 

Ad esempio, in una colonna `mmddyyyy`data che utilizza il formato , l'espressione deve essere simile alla seguente:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Indice di colonna

La seguente espressione illustra come usare l'indice di colonna per selezionare tutte le righe della prima colonna del set di dati che contengono valori minori o uguali a 30, ma non uguali a 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 
