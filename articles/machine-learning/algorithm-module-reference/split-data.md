---
title: 'Split Data: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Split data in Azure Machine Learning per dividere un set di dati in due set distinti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9eba6f2c47629b708dde4a5a2888b76dbd24b4e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79455894"
---
# <a name="split-data-module"></a>Modulo Split data

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare il modulo Split data per dividere un set di dati in due set distinti.

Questo modulo è utile quando è necessario separare i dati in set di training e di testing. È inoltre possibile personalizzare il modo in cui i dati vengono divisi. Alcune opzioni supportano la sequenza casuale di dati. Altre sono personalizzate per un tipo di dati o un tipo di modello specifico.

## <a name="configure-the-module"></a>Configurare il modulo

> [!TIP]
> Prima di scegliere la modalità di suddivisione, leggere tutte le opzioni per determinare il tipo di suddivisione necessario.
> Se si modifica la modalità di suddivisione, tutte le altre opzioni potrebbero essere reimpostate.

1. Aggiungere il modulo **Split data** alla pipeline nella finestra di progettazione. È possibile trovare questo modulo in **trasformazione dati**, nella categoria **esempio e divisione** .

1. **Modalità di suddivisione**: scegliere una delle modalità seguenti, a seconda del tipo di dati disponibili e del modo in cui si desidera suddividerlo. Ogni modalità di suddivisione ha opzioni diverse.

   - **Dividi righe**: usare questa opzione se si vogliono dividere solo i dati in due parti. È possibile specificare la percentuale di dati da inserire in ogni suddivisione. Per impostazione predefinita, i dati sono divisi 50/50.

     È anche possibile ordinare in modo casuale la selezione delle righe in ogni gruppo e usare il campionamento stratificato. Per il campionamento stratificato, è necessario selezionare una singola colonna di dati per cui si desidera che i valori vengano ripartiti equamente tra i due set di dati del risultato.  

   - **Suddivisione espressione regolare**: scegliere questa opzione quando si desidera dividere il set di dati testando una singola colonna per un valore.

     Se ad esempio si stanno analizzando i sentimenti, è possibile verificare la presenza di un determinato nome di prodotto in un campo di testo. È quindi possibile dividere il set di dati in righe con il nome del prodotto di destinazione e le righe senza il nome del prodotto di destinazione.

   - **Suddivisione espressione relativa**: usare questa opzione quando si vuole applicare una condizione a una colonna numerica. Il numero può essere un campo di data/ora, una colonna che contiene importi di età o dollaro o persino una percentuale. È ad esempio possibile suddividere il set di dati in base al costo degli elementi, raggruppare le persone per intervallo di età o separare i dati in base a una data di calendario.

### <a name="split-rows"></a>Suddividere le righe

1. Aggiungere il modulo [Split data](./split-data.md) alla pipeline nella finestra di progettazione e connettere il set di dati che si desidera suddividere.
  
1. Per la **modalità di suddivisione**selezionare **Dividi righe**. 

1. **Frazione di righe nel primo set di dati di output**: usare questa opzione per determinare il numero di righe che verranno inserite nel primo output (lato sinistro). Tutte le altre righe verranno inserite nel secondo output (lato destro).

   Il rapporto rappresenta la percentuale di righe inviate al primo set di dati di output, quindi è necessario immettere un numero decimale compreso tra 0 e 1.
     
   Se ad esempio si immette **0,75** come valore, il set di dati verrà diviso 75/25. In questa divisione, il 75% delle righe verrà inviato al primo set di dati di output. Il restante 25% verrà inviato al secondo set di dati di output.
  
1. Selezionare l'opzione **suddivisione casuale** se si desidera eseguire la selezione casuale dei dati nei due gruppi. Si tratta dell'opzione preferita quando si creano set di risultati di training e di test.

1. Valore di **inizializzazione casuale**: immettere un valore integer non negativo per avviare la sequenza di istanze di pseudocasuale da usare. Questo valore di inizializzazione predefinito viene usato in tutti i moduli che generano numeri casuali. 

   La specifica di un valore di inizializzazione rende riproducibili i risultati. Se è necessario ripetere i risultati di un'operazione Split, è necessario specificare un valore di inizializzazione per il generatore di numeri casuali. In caso contrario, il valore di inizializzazione casuale viene impostato su **0**per impostazione predefinita, il che significa che il valore di inizializzazione iniziale viene ottenuto dal clock di sistema. Di conseguenza, la distribuzione dei dati può differire leggermente ogni volta che si esegue una suddivisione. 

1. **Suddivisione stratificata**: impostare questa opzione su **true** per assicurarsi che i due set di risultati di output contengano un campione rappresentativo dei valori nella *colonna Strata* o nella *colonna chiave di stratificazione*. 

   Con il campionamento stratificato, i dati vengono divisi in modo che ogni set di dati di output ottenga approssimativamente la stessa percentuale di ogni valore di destinazione Ad esempio, potrebbe essere necessario assicurarsi che i set di training e di testing siano approssimativamente bilanciati rispetto al risultato o a un'altra colonna, ad esempio Gender.

1. Inviare la pipeline.


## <a name="select-a-regular-expression"></a>Selezionare un'espressione regolare

1. Aggiungere il modulo [Split data](./split-data.md) alla pipeline e connetterlo come input al set di dati che si desidera suddividere.  
  
1. Per la **modalità di suddivisione**selezionare **espressione regolare divisa**.

1. Nella casella **espressione regolare** immettere un'espressione regolare valida. 
  
   L'espressione regolare deve seguire la sintassi Python per le espressioni regolari.

1. Inviare la pipeline.

   In base all'espressione regolare fornita, il set di dati è suddiviso in due set di righe: righe con valori corrispondenti all'espressione e tutte le righe rimanenti. 

Negli esempi seguenti viene illustrato come dividere un set di dati utilizzando l'opzione **espressione regolare** . 

### <a name="single-whole-word"></a>Parola intera singola 

In questo esempio viene inserito il primo set di dati per tutte le `Gryphon` righe che contengono `Text`il testo della colonna. Inserisce altre righe nel secondo output di **Split data**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Substring

Questo esempio cerca la stringa specificata in qualsiasi posizione all'interno della seconda colonna del set di dati. La posizione è indicata qui dal valore di indice 1. La corrispondenza prevede la distinzione tra maiuscole e minuscole.

```text
(\1) ^[a-f]
```

Il primo set di dati di risultati contiene tutte le righe in cui la colonna index inizia con `a`uno `b`dei `c`caratteri `d`seguenti `e`: `f`,,,,,. Tutte le altre righe vengono indirizzate al secondo output.

## <a name="select-a-relative-expression"></a>Selezionare un'espressione relativa

1. Aggiungere il modulo [Split data](./split-data.md) alla pipeline e connetterlo come input al set di dati che si desidera suddividere.
  
1. Per la **modalità di suddivisione**selezionare **espressione relativa**.
  
1. Nella casella **espressione relazionale** immettere un'espressione che esegue un'operazione di confronto su una sola colonna.

   Per la **colonna numerica**:
   - La colonna contiene numeri di qualsiasi tipo di dati numerico, inclusi i tipi di dati di data e ora.
   - L'espressione può fare riferimento a un massimo di un nome di colonna.
   - Utilizzare il carattere e commerciale `&`,, per l'operazione e. Usare il carattere barra verticale `|`,, per l'operazione o.
   - Sono supportati gli operatori seguenti: `<`, `>`, `<=`, `>=`, `==`, `!=`.
   - Non è possibile raggruppare le `(` operazioni `)`usando e.
   
   Per la **colonna stringa**:
   - Sono supportati gli operatori seguenti: `==`, `!=`.

1. Inviare la pipeline.

   L'espressione divide il set di dati in due set di righe: righe con valori che soddisfano la condizione e tutte le righe rimanenti.

Gli esempi seguenti illustrano come dividere un set di dati usando l'opzione **espressione relativa** nel modulo **Split data** .  

### <a name="calendar-year"></a>Anno di calendario

Uno scenario comune consiste nel dividere un set di dati in base agli anni. L'espressione seguente seleziona tutte le righe in cui i valori nella `Year` colonna sono maggiori `2010`di.

```text
\"Year" > 2010
```

L'espressione date deve tenere conto di tutte le parti della data incluse nella colonna di dati. Il formato delle date nella colonna di dati deve essere coerente. 

Ad esempio, in una colonna data che usa il formato `mmddyyyy`, l'espressione deve essere simile alla seguente:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Indice di colonna

La seguente espressione illustra come usare l'indice di colonna per selezionare tutte le righe della prima colonna del set di dati che contengono valori minori o uguali a 30, ma non uguali a 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
