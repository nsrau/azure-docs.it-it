---
title: 'Partizione ed esempio: riferimento al moduloPartition and Sample: Module reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Partizione e Esempio in Azure Machine Learning per eseguire il campionamento in un set di dati o per creare partizioni dal set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477511"
---
# <a name="partition-and-sample-module"></a>Modulo partizione e campione

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Usare il modulo Partizione e Campione per eseguire il campionamento in un set di dati o per creare partizioni dal set di dati.

Il campionamento è uno strumento importante di Machine Learning perché consente di ridurre le dimensioni di un set di dati, mantenendo lo stesso rapporto di valori. Questo modulo supporta diverse attività correlate importanti nell'apprendimento automatico:This module supports several related tasks that are important in machine learning: 

- Suddivisione dei dati in più sottosezioni della stessa dimensione. 

  È possibile utilizzare le partizioni per la convalida incrociata o per assegnare casi a gruppi casuali.

- Separare i dati in gruppi e quindi lavorare con i dati di un gruppo specifico. 

  Dopo aver assegnato in modo casuale i casi a gruppi diversi, potrebbe essere necessario modificare le funzionalità associate a un solo gruppo.

- Campionamento. 

  È possibile estrarre una percentuale dei dati, applicare il campionamento casuale o scegliere una colonna da utilizzare per bilanciare il set di dati ed eseguire il campionamento stratificato sui relativi valori.

- Creazione di un set di dati più piccolo per il test. 

  Se si dispone di molti dati, è possibile usare solo le prime *n* righe durante la configurazione della pipeline e quindi passare all'uso del set di dati completo quando si compila il modello. È anche possibile usare il campionamento per creare un set di dati più piccolo da usare nello sviluppo.

## <a name="configure-the-module"></a>Configurare il modulo

Questo modulo supporta i metodi seguenti per dividere i dati in partizioni o per il campionamento. Scegliere prima il metodo, quindi impostare le opzioni aggiuntive richieste dal metodo.

- Head
- campionamento
- Assegna a pieghe
- Scegliere una sezione

### <a name="get-top-n-rows-from-a-dataset"></a>Ottenere righe TOP N da un set di datiGet TOP N rows from a dataset

Usare questa modalità per ottenere soltanto le prime *n* righe. Questa opzione è utile se si desidera testare una pipeline su un numero ridotto di righe e non è necessario bilanciare o campionare i dati in alcun modo.

1. Aggiungere il modulo **Partizione ed Esempio** alla pipeline nell'interfaccia e connettere il set di dati.  

1. **Modalità partizione o campione**: Impostare questa opzione **su Testa**.

1. **Numero di righe da selezionare**: Immettere il numero di righe da restituire.

   Il numero di righe deve essere un numero intero non negativo. Se il numero di righe selezionate è maggiore del numero di righe nel set di dati, viene restituito l'intero set di dati.

1. Inviare la pipeline.

Il modulo restituisce un singolo set di dati che contiene solo il numero specificato di righe. Le righe vengono sempre lette dall'inizio del set di dati.

### <a name="create-a-sample-of-data"></a>Creare un esempio di datiCreate a sample of data

Questa opzione supporta il campionamento casuale semplice o il campionamento casuale stratificato. È utile se si desidera creare un set di dati di esempio rappresentativo più piccolo per il test.

1. Aggiungere il modulo **Partizione ed Esempio** alla pipeline e connettere il set di dati.

1. **Modalità partizione o campione**: impostare questa opzione su **Campionamento**.

1. **Frequenza di campionamento**: Immettere un valore compreso tra 0 e 1. questo valore specifica la percentuale di righe dal set di dati di origine che devono essere incluse nel set di dati di output.

   Ad esempio, se si desidera solo metà `0.5` del set di dati originale, immettere per indicare che la frequenza di campionamento deve essere del 50%.

   Le righe del set di dati di input vengono mescolate e posizionate in modo selettivo nel set di dati di output, in base al rapporto specificato.

1. **Seme casuale per**il campionamento : Facoltativamente, immettere un numero intero da utilizzare come valore di serie.

   Questa opzione è importante se si desidera che le righe vengano divise nello stesso modo ogni volta. Il valore predefinito è **0**, ovvero viene generato un valore di errore iniziale in base all'orologio di sistema. Questo valore può portare a risultati leggermente diversi ogni volta che si esegue la pipeline.

1. **Divisione stratificata per il campionamento:** selezionare questa opzione se è importante che le righe del set di dati siano divise in modo uniforme da una colonna chiave prima del campionamento.

   Per **colonna chiave di stratificazione per**il campionamento , selezionare una singola colonna di *strati* da utilizzare per la divisione del set di dati. Le righe nel set di dati vengono quindi suddivise come segue:

   1. Tutte le righe di input vengono raggruppate (stratificate) in base ai valori nella colonna degli strati specificata.

   1. Le righe vengono mescolate all'interno di ogni gruppo.

   1. Ogni gruppo viene aggiunto selettivamente al set di dati di output per soddisfare il rapporto specificato.


1. Inviare la pipeline.

   Con questa opzione, il modulo restituisce un singolo set di dati che contiene un campionamento rappresentativo dei dati. La parte rimanente non campionata del set di dati non viene restituita. 

## <a name="split-data-into-partitions"></a>Dividere i dati in partizioni

Utilizzare questa opzione quando si desidera dividere il set di dati in sottoinsiemi di dati. Questa opzione è utile anche quando si desidera creare un numero personalizzato di pieghe per la convalida incrociata o per dividere le righe in più gruppi.

1. Aggiungere il modulo **Partizione ed Esempio** alla pipeline e connettere il set di dati.

1. Per **Partizione o modalità esempio**, selezionare **Assegna a pieghe**.

1. **Usa sostituzione nel partizionamento**: Selezionare questa opzione se si desidera che la riga campionata venga reinserita nel pool di righe per un potenziale riutilizzo. Di conseguenza, la stessa riga potrebbe essere assegnata a più pieghe.

   Se non si usa la sostituzione (opzione predefinita), la riga campionata non viene reinserita nel pool di righe per un potenziale riutilizzo. Di conseguenza, ogni riga può essere assegnata a una sola piega.

1. **Divisione casuale**: Selezionare questa opzione se si desidera che le righe vengano assegnate in modo casuale alle pieghe.

   Se non si seleziona questa opzione, le righe vengono assegnate alle pieghe tramite il metodo round robin.

1. **Valore di errore casuale**: Facoltativamente, immettere un numero intero da utilizzare come valore di seme. Questa opzione è importante se si desidera che le righe vengano divise nello stesso modo ogni volta. In caso contrario, il valore predefinito **0** indica che verrà utilizzato un valore di avviamento iniziale casuale.

1. **Specificare il metodo**del partitioner : Indicare come si desidera che i dati vengano ripartiti per ogni partizione, utilizzando queste opzioni:

   - **Partizione uniforme**: utilizzare questa opzione per inserire un numero uguale di righe in ogni partizione. Per specificare il numero di partizioni di output, immettere un numero intero nella casella Specificare il **numero di pieghe da dividere uniformemente** in.

   - **Partizione con proporzioni personalizzate**: utilizzare questa opzione per specificare la dimensione di ogni partizione come elenco separato da virgole.

     Ad esempio, si supponga di voler creare tre partizioni. La prima partizione conterrà il 50% dei dati. Le due partizioni rimanenti conterranno ciascuna il 25% dei dati. Nella casella **Elenco proporzioni separate da virgola** immettere i numeri **seguenti: 0,5, 0,25, 0, 25**.

     La somma di tutte le dimensioni delle partizioni deve essere pari a esattamente 1.

     Se si immettono numeri che sommano fino a *meno di 1*, viene creata una partizione aggiuntiva per contenere le righe rimanenti. Ad esempio, se si immettono i valori **.2** e **.3**, viene creata una terza partizione per contenere il restante 50% di tutte le righe.
     
     Se si immettono numeri che sommano *più di 1*, viene generato un errore quando si esegue la pipeline.

1. **Divisione stratificata**: Selezionare questa opzione se si desidera che le righe vengano stratificate durante la divisione, quindi scegliere la _colonna degli strati_.

1. Inviare la pipeline.

   Con questa opzione, il modulo restituisce più set di dati. I set di dati vengono partizionati in base alle regole specificate.

### <a name="use-data-from-a-predefined-partition"></a>Usare i dati da una partizione predefinitaUse data from a predefined partition  

Utilizzare questa opzione quando è stato suddiviso un set di dati in più partizioni e ora si desidera caricare ogni partizione a sua volta per un'ulteriore analisi o elaborazione.

1. Aggiungere il modulo **Partizione ed Esempio** alla pipeline.

1. Collegare il modulo all'output di un'istanza precedente di **Partition e Sample**. Tale istanza deve aver utilizzato l'opzione **Assegna a pieghe** per generare un certo numero di partizioni.

1. **Modalità partizione o campione**: Selezionare **Piega**.

1. **Specificare la piega da campionato da**: selezionare una partizione da utilizzare immettendone l'indice. Gli indici delle partizioni sono in base 1.Partition indices are 1-based. Ad esempio, se il set di dati è stato suddiviso in tre parti, le partizioni avrà gli indici 1, 2 e 3.

   Se si immette un valore di indice non valido, viene generato un errore in fase di progettazione: "Errore 0018: il set di dati contiene dati non validi".

   Oltre a raggruppare il set di dati tramite pieghe, è possibile separare il set di dati in due gruppi: una piega di destinazione e tutto il resto. Per fare questo, immettere l'indice di una singola piega, quindi selezionare l'opzione **Seleziona complemento della piega selezionata** per ottenere tutto tranne i dati nella piega specificata.

1. Se si utilizzano più partizioni, è necessario aggiungere altre istanze del modulo **Partizione ed Esempio** per gestire ogni partizione.

   Ad esempio, il modulo **Partizione e Campione** nella seconda riga è impostato su **Assegna a pieghe**e il modulo nella terza riga è impostato su Seleziona **piega**.   

   ![Partition and sample (Partizione ed esempio)](./media/module/partition-and-sample.png)

1. Inviare la pipeline.

   Con questa opzione, il modulo restituisce un singolo set di dati che contiene solo le righe assegnate a tale piegatura.

> [!NOTE]
>  Non è possibile visualizzare direttamente le designazioni di piegatura. Sono presenti solo nei metadati.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 