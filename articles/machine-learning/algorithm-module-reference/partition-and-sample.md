---
title: 'Partition and Sample: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Partition and Sample in Azure Machine Learning per eseguire il campionamento in un set di dati o per creare partizioni dal set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477511"
---
# <a name="partition-and-sample-module"></a>Modulo Partition and Sample

Questo articolo descrive un modulo disponibile nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare il modulo Partition and Sample per eseguire il campionamento in un set di dati o per creare partizioni dal set di dati.

Il campionamento è uno strumento importante di Machine Learning perché consente di ridurre le dimensioni di un set di dati, mantenendo lo stesso rapporto di valori. Questo modulo supporta diverse attività correlate che sono importanti per Machine Learning: 

- Suddivisione dei dati in più sottosezioni delle stesse dimensioni. 

  È possibile utilizzare le partizioni per la convalida incrociata o per assegnare i case a gruppi casuali.

- Separare i dati in gruppi e quindi usare i dati di un gruppo specifico. 

  Dopo aver assegnato in modo casuale i case a gruppi diversi, potrebbe essere necessario modificare le funzionalità associate a un solo gruppo.

- Campionamento. 

  È possibile estrarre una percentuale dei dati, applicare il campionamento casuale o scegliere una colonna da usare per il bilanciamento del set di dati ed eseguire il campionamento stratificato sui rispettivi valori.

- Creazione di un set di dati più piccolo per il test. 

  Se si dispone di una grande quantità di dati, è possibile utilizzare solo le prime *n* righe durante la configurazione della pipeline, quindi passare a utilizzando il set di dati completo quando si compila il modello. È anche possibile usare il campionamento per creare un set di dati più piccolo da usare in fase di sviluppo.

## <a name="configure-the-module"></a>Configurare il modulo

Questo modulo supporta i metodi seguenti per dividere i dati in partizioni o per il campionamento. Scegliere prima il metodo, quindi impostare le opzioni aggiuntive necessarie per il metodo.

- Head
- campionamento
- Assegna a riduzioni
- Scegliere una sezione

### <a name="get-top-n-rows-from-a-dataset"></a>Ottenere le prime N righe da un set di dati

Usare questa modalità per ottenere soltanto le prime *n* righe. Questa opzione è utile se si desidera testare una pipeline su un numero ridotto di righe e non è necessario che i dati siano bilanciati o campionati in alcun modo.

1. Aggiungere il modulo **Partition and Sample** alla pipeline nell'interfaccia e connettere il set di dati.  

1. **Partition o sample Mode**: impostare questa opzione su **Head**.

1. **Numero di righe da selezionare**: immettere il numero di righe da restituire.

   Il numero di righe deve essere un numero intero non negativo. Se il numero di righe selezionate è maggiore del numero di righe nel set di dati, viene restituito l'intero set di dati.

1. Inviare la pipeline.

Il modulo restituisce un singolo set di dati che contiene solo il numero di righe specificato. Le righe vengono lette sempre dalla parte superiore del set di dati.

### <a name="create-a-sample-of-data"></a>Creazione di un campione di dati

Questa opzione supporta il campionamento casuale semplice o il campionamento casuale stratificato. È utile se si vuole creare un set di dati di esempio rappresentativo più piccolo per il test.

1. Aggiungere il modulo **Partition and Sample** alla pipeline e connettere il set di dati.

1. **Partizione o modalità di esempio**: impostare questa opzione su **campionamento**.

1. **Frequenza di campionamento**: immettere un valore compreso tra 0 e 1. Questo valore specifica la percentuale di righe del set di dati di origine che deve essere inclusa nel set di dati di output.

   Se, ad esempio, si desidera solo metà del set di dati originale, immettere `0.5` per indicare che la frequenza di campionamento deve essere pari al 50%.

   Le righe del set di dati di input vengono mescolate e inserite selettivamente nel set di dati di output, in base al rapporto specificato.

1. Valore di **inizializzazione casuale per il campionamento**: Immettere facoltativamente un intero da usare come valore di inizializzazione.

   Questa opzione è importante se si desidera che le righe vengano divise allo stesso modo ogni volta. Il valore predefinito è **0**, vale a dire che un valore di inizializzazione iniziale viene generato in base al clock di sistema. Questo valore può generare risultati leggermente diversi ogni volta che si esegue la pipeline.

1. **Suddivisione stratificata per il campionamento**: selezionare questa opzione se è importante che le righe nel set di dati siano divise in modo uniforme da alcune colonne chiave prima del campionamento.

   Per **colonna chiave di stratificazione per il campionamento**, selezionare una singola *colonna di strati* da usare quando si divide il set di dati. Le righe nel set di dati vengono quindi divise come segue:

   1. Tutte le righe di input vengono raggruppate (stratificate) in base ai valori nella colonna di strati specificata.

   1. Le righe vengono mescolate all'interno di ogni gruppo.

   1. Ogni gruppo viene aggiunto selettivamente al set di dati di output per soddisfare il rapporto specificato.


1. Inviare la pipeline.

   Con questa opzione, il modulo restituisce un singolo set di dati che contiene un campionamento dei dati rappresentativo. La parte rimanente non campionata del set di dati non è output. 

## <a name="split-data-into-partitions"></a>Suddividere i dati in partizioni

Utilizzare questa opzione quando si desidera suddividere il set di dati in subset di dati. Questa opzione è utile anche quando si desidera creare un numero personalizzato di riduzioni per la convalida incrociata oppure suddividere le righe in più gruppi.

1. Aggiungere il modulo **Partition and Sample** alla pipeline e connettere il set di dati.

1. Per la **modalità partizione o di esempio**, selezionare **assegna a riduzioni**.

1. **USA sostituzione nel partizionamento**: selezionare questa opzione se si desidera che la riga campionata venga rimessa nel pool di righe per il possibile riutilizzo. Di conseguenza, la stessa riga potrebbe essere assegnata a diverse riduzioni.

   Se non si usa la sostituzione (opzione predefinita), la riga campionata non viene rimessa nel pool di righe per il possibile riutilizzo. Di conseguenza, ogni riga può essere assegnata a una sola riduzioni.

1. **Suddivisione casuale**: selezionare questa opzione se si desidera che le righe vengano assegnate in modo casuale alle riduzioni.

   Se non si seleziona questa opzione, le righe vengono assegnate per ripiegare il metodo Round Robin.

1. **Seed casuale**: Immettere facoltativamente un valore integer da usare come valore di inizializzazione. Questa opzione è importante se si desidera che le righe vengano divise allo stesso modo ogni volta. In caso contrario, il valore predefinito **0** indica che verrà utilizzato un valore di inizializzazione iniziale casuale.

1. **Specificare il metodo Partitioner**: indicare come si desidera che i dati vengano ripartiti in ogni partizione, usando le opzioni seguenti:

   - **Partizionamento uniforme**: usare questa opzione per inserire un numero uguale di righe in ogni partizione. Per specificare il numero di partizioni di output, immettere un numero intero nella casella **specificare il numero di riduzioni da dividere uniformemente** .

   - **Partition with Custom proports**: usare questa opzione per specificare le dimensioni di ogni partizione come un elenco delimitato da virgole.

     Si supponga, ad esempio, di voler creare tre partizioni. La prima partizione conterrà il 50% dei dati. Le due partizioni rimanenti conterranno ognuna il 25% dei dati. Nella casella **elenco di proporzioni separate da virgola** immettere i numeri seguenti: **0,5, 25, 25**.

     La somma di tutte le dimensioni della partizione deve essere sommata esattamente a 1.

     Se si immettono numeri che si aggiungono a un valore *minore di 1*, viene creata una partizione aggiuntiva che conterrà le righe rimanenti. Se, ad esempio, si immettono i valori **0,2** e **,3**, viene creata una terza partizione per conservare il 50% di tutte le righe rimanenti.
     
     Se si immettono numeri che si aggiungono a *più di 1*, viene generato un errore quando si esegue la pipeline.

1. **Suddivisione stratificata**: selezionare questa opzione se si desidera che le righe vengano stratificate quando si divide, quindi scegliere la _colonna strati_.

1. Inviare la pipeline.

   Con questa opzione, il modulo restituisce più set di impostazioni. I set di impostazioni vengono partizionati in base alle regole specificate.

### <a name="use-data-from-a-predefined-partition"></a>Usare i dati di una partizione predefinita  

Usare questa opzione quando si divide un set di dati in più partizioni e ora si vuole caricare ogni partizione a sua volta per un'ulteriore analisi o elaborazione.

1. Aggiungere la **partizione e** il modulo di esempio alla pipeline.

1. Connettere il modulo all'output di un'istanza precedente di **Partition ed Sample**. Per generare un certo numero di partizioni, è necessario che nell'istanza sia stata utilizzata l'opzione **assegna a riduzioni** .

1. **Partition o sample Mode**: selezionare **pick fold**.

1. Specificare la sezione da **campionare**: selezionare una partizione da usare immettendo il relativo indice. Gli indici di partizione sono basati su 1. Se, ad esempio, il set di dati è stato diviso in tre parti, le partizioni avrebbero gli indici 1, 2 e 3.

   Se si immette un valore di indice non valido, viene generato un errore in fase di progettazione: "errore 0018: il set di dati contiene dati non validi".

   Oltre a raggruppare il set di dati in base alle riduzioni, è possibile separare il set di dati in due gruppi: una sezione di destinazione e tutto il resto. A tale scopo, immettere l'indice di una singola sezione, quindi selezionare l'opzione seleziona **complemento della sezione selezionata** per ottenere tutto tranne i dati nella sezione specificata.

1. Se si usano più partizioni, è necessario aggiungere altre istanze della **partizione e** del modulo di esempio per gestire ogni partizione.

   Ad esempio, il modulo **Partition and Sample** nella seconda riga è impostato su **assign to Folds**e il modulo nella terza riga è impostato su **pick fold**.   

   ![Partition and sample (Partizione ed esempio)](./media/module/partition-and-sample.png)

1. Inviare la pipeline.

   Con questa opzione, il modulo restituisce un singolo set di dati che contiene solo le righe assegnate a tale riduzioni.

> [!NOTE]
>  Non è possibile visualizzare direttamente le designazioni di riduzioni. Sono presenti solo nei metadati.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 