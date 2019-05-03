---
title: 'Partition and Sample: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Partition and Sample nel servizio Azure Machine Learning per eseguire il campionamento di un set di dati o per creare partizioni dal set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 72f9e11e3582d804eecc7479ea079276564bd12f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029281"
---
# <a name="partition-and-sample-module"></a>Modulo Partition and Sample

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per eseguire il campionamento di un set di dati o per creare partizioni dal set di dati.

Il campionamento è uno strumento importante di machine learning perché consente di ridurre le dimensioni di un set di dati, mantenendo la stessa percentuale di valori. Questo modulo supporta diverse attività correlate che sono importanti nell'apprendimento automatico: 

- Divisione dei dati in più sottosezioni delle stesse dimensioni. 

    È possibile utilizzare le partizioni per la convalida incrociata o per assegnare i casi a gruppi casuali.

- Separazione dei dati in gruppi e quindi usare i dati da un gruppo specifico. 

    Dopo aver assegnato in modo casuale i casi a gruppi diversi, si potrebbe essere necessario modificare le funzionalità che sono associate a un solo gruppo.

- Campionamento. 

    È possibile estrarre una percentuale dei dati, applicare un campionamento casuale o scegliere una colonna da usare per bilanciare il set di dati ed eseguire un campionamento stratificato sui relativi valori.

- Creazione di un set di dati più piccoli per i test. 

    Se si dispone di una grande quantità di dati, si potrebbe voler usare solo le prime *n* righe durante la configurazione dell'esperimento e quindi passare all'uso di set di dati completo quando si compila il modello. È anche possibile usare campionamento per creare set di dati più piccoli s per l'uso in fase di sviluppo.

## <a name="configure-partition-and-sample"></a>Configurare Partition and Sample

Questo modulo supporta diversi metodi per la suddivisione dei dati in partizioni o per il campionamento. Scegliere innanzitutto il metodo e quindi impostare le opzioni aggiuntive richieste dal metodo.

- Head
- campionamento
- Assegna alle sezioni
- Scegliere una sezione

### <a name="get-top-n-rows-from-a-dataset"></a>Ottenere prime N righe da un set di dati

Utilizzare questa modalità per ottenere solo le prime *n* righe. Questa opzione è utile se si vuole testare un esperimento in un numero ridotto di righe e non necessario di dati da bilanciato o campionati in alcun modo.

1. Aggiungere il **Partition and Sample** all'esperimento nell'interfaccia del modulo e connettere il set di dati.  

2. **Modalità di partizione o sample**: Impostare questa opzione su **Head**.

3. **Numero di righe da selezionare**: Digitare il numero di righe da restituire.

    Il numero di righe specificato deve essere un numero intero non negativo. Se il numero di righe selezionate è maggiore del numero di righe nel set di dati, viene restituito l'intero set di dati.

4. Eseguire l'esperimento.

Il modulo restituisce un singolo set di dati contenente solo il numero di righe specificato. Le righe vengono sempre lette dall'inizio del set di dati.

### <a name="create-a-sample-of-data"></a>Creare un campione di dati

Questa opzione supporta il campionamento casuale semplice o il campionamento casuale stratificato. Ciò è utile se si desidera creare un set di dati più piccolo campione rappresentativo per i test.

1. Aggiungere il **Partition and Sample** modulo nell'esperimento e connettere il set di dati.

2. **Modalità di partizione o sample**: Impostare questa opzione su **campionamento**.

3. **Frequenza di campionamento**: Digitare un valore compreso tra 0 e 1. Questo valore specifica la percentuale di righe del set di dati di origine che deve essere incluso nel set di dati di output.

    Ad esempio, se si desidera solo metà del set di dati originale, digitare `0.5` per indicare che la frequenza di campionamento pari al 50%.

    Le righe del set di dati di input vengono mescolate e inserite selettivamente nel set di dati di output in base al rapporto specificato.

4. **Valore di inizializzazione casuale per il campionamento**: Facoltativamente, digitare un numero intero da utilizzare come un valore di inizializzazione.

    Questa opzione è importante se si desidera che le righe da dividere lo stesso modo in cui ogni volta. Il valore predefinito è 0, che un valore di inizializzazione iniziale viene generato un significato in base al clock di sistema. Questo può causare risultati leggermente diversi ogni volta che si esegue l'esperimento.

5. **Suddivisione stratificata per il campionamento**: Selezionare questa opzione se è importante che le righe nel set di dati devono essere diviso equamente per alcune colonne chiave prima di campionamento.

    Per la **la colonna chiave di stratificazione per il campionamento**, selezionare una singola *colonna degli strati* da usare quando si divide il set di dati. Le righe nel set di dati sono quindi suddivise come segue:

    1. Tutte le righe di input vengono raggruppate (stratificate) in base ai valori nella colonna di strati specificata.

    2. Le righe vengono mescolate all'interno di ogni gruppo.

    3. Ogni gruppo viene aggiunto selettivamente al set di dati di output per soddisfare il rapporto specificato.


6. Eseguire l'esperimento.

    Con questa opzione, il modulo restituisce un singolo set di dati che contiene un campione rappresentativo dei dati. Non viene restituita la parte rimanente non campionata del set di dati. 

## <a name="split-data-into-partitions"></a>Suddividere i dati in partizioni

Usare questa opzione quando si desidera dividere il set di dati in subset di dati. Questa opzione è utile anche quando si desidera creare un numero personalizzato di sezioni per la convalida incrociata, o per suddividere le righe in diversi gruppi.

1. Aggiungere il **Partition and Sample** modulo nell'esperimento e connettere il set di dati.

2. Per la **partizione o modalità campionamento**, selezionare **assegna alle sezioni**.

3. **Usa la sostituzione di partizionamento**: Selezionare questa opzione se si desidera che la riga campionata per essere reinserito nel pool di righe per un potenziale riutilizzo. Di conseguenza, la stessa riga potrà essere assegnata a diverse sezioni.

    Se non si usa sostituzione (opzione predefinita), la riga campionata non viene inserita nuovamente nel pool di righe per un potenziale riutilizzo. Di conseguenza, ogni riga può essere assegnato a solo una sezione.

4. **Suddivisione casuale**:  Selezionare questa opzione se si desidera che le righe in modo casuale da assegnare a sezioni.

    Se non si seleziona questa opzione, le righe vengono assegnate a sezioni usando il metodo round robin.

5. **Valore di inizializzazione casuale**: Facoltativamente, digitare un numero intero da utilizzare come valore di inizializzazione. Questa opzione è importante se si desidera che le righe da dividere lo stesso modo in cui ogni volta. In caso contrario, il valore predefinito pari a 0 significa che uno casuale valore di inizializzazione verrà usato.

6. **Specificare il metodo partitioner**: Indicare come si desidera che dati essere ripartite per ogni partizione, usare queste opzioni:

    - **Partizione in modo uniforme**: Usare questa opzione per inserire un numero uguale di righe in ogni partizione. Per specificare il numero di partizioni di output, digitare un numero intero nel **specificare numero di riduzioni in cui dividere uniformemente in** casella di testo.

    - **Partizione con proporzioni personalizzate**: Usare questa opzione per specificare le dimensioni di ogni partizione sotto forma di elenco delimitato da virgole.

        Ad esempio, se si desidera creare tre partizioni, con la prima partizione contenente il 50% dei dati e le due partizioni rimanenti ogni 25% che lo contiene dei dati, selezionare la **elenco di proporzioni separate da virgole** casella di testo e Digitare i numeri: `.5, .25, .25`

        La somma di tutte le dimensioni delle partizioni è necessario aggiungere fino a 1.

        - Se si immette i numeri che aggiungono **minore di 1**, viene creata una partizione aggiuntiva per contenere le righe rimanenti. Ad esempio, se si digita la partizione di valori. 2 e.3, un terzo viene creata che contiene i restanti 50% di tutte le righe.

        - Se si immette i numeri che aggiungono **maggiore di 1**, viene generato un errore quando si esegue l'esperimento.

7. **Suddivisione stratificata**: Selezionare questa opzione se si desidera che le righe vengano stratificate quando suddiviso e quindi scegliere il _colonna degli strati_.

8. Eseguire l'esperimento.

    Con questa opzione, il modulo restituisce più set di dati, partizionati usando le regole specificate.

### <a name="use-data-from-a-predefined-partition"></a>Usare i dati da una partizione predefinito  

Questa opzione viene utilizzata quando si hanno suddiviso un set di dati in più partizioni e si vuole caricare ogni partizione, a sua volta per un'ulteriore analisi o l'elaborazione.

1. Aggiungere il **Partition and Sample** modulo all'esperimento.

2. Connetterlo all'output di un'istanza precedente della **Partition and Sample**. Tale istanza deve essere utilizzato il **assegna alle sezioni** opzione per generare un certo numero di partizioni.

3. **Modalità di partizione o sample**: Selezionare **scegliere una sezione**.

4. **Specificare quale sezione a cui eseguire il campionamento**: Selezionare una partizione da usare digitando il relativo indice. Gli indici di partizione sono basati su 1. Ad esempio, se il set di dati suddivisa in tre parti, le partizioni avrebbe gli indici 1, 2 e 3.

    Se si digita un valore di indice non valido, viene generato un errore in fase di progettazione: "Errore 0018: Set di dati contiene dati non validi."

    Oltre a raggruppare il set di dati riduzioni, è possibile separare il set di dati in due gruppi: una sezione di destinazione e tutti gli altri elementi. A questo scopo, digitare l'indice di una singola sezione e quindi selezionare l'opzione **Pick complement della sezione selezionata**, per ottenere tutti gli elementi tranne i dati nella sezione specificata.

5. Se si lavora con più partizioni, è necessario aggiungere altre istanze del **Partition and Sample** modulo per gestire ogni partizione.

    Ad esempio supponga patients partizionato in precedenza in quattro sezioni usando age. Per lavorare con ogni riduzione singoli, è necessario quattro copie del **Partition and Sample** modulo, e in ognuno, si seleziona una sezione diversa, come illustrato di seguito. Non è corretto, utilizzare il **assegna alle sezioni** direttamente l'output.  

    [![Esempio e partizione](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Eseguire l'esperimento.

    Con questa opzione, il modulo restituisce un singolo set di dati contenente solo le righe assegnate a tale sezione.

> [!NOTE]
>  Non è possibile visualizzare le designazioni di riduzione direttamente. sono presenti solo nei metadati.

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 