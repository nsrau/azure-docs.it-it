---
title: 'Pulire i dati mancanti: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo clean Missing data nel servizio Azure Machine Learning per rimuovere, sostituire o dedurre i valori mancanti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 7167d53cce2c44f754f438753acda008e53bb2b3
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693207"
---
# <a name="clean-missing-data-module"></a>Pulisci modulo dati mancanti

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per rimuovere, sostituire o dedurre i valori mancanti. 

I data scientist controllano spesso i dati mancanti e quindi eseguono diverse operazioni per correggere i dati o inserire nuovi valori. L'obiettivo di tali operazioni di pulizia è impedire problemi causati da dati mancanti che possono verificarsi durante il training di un modello. 

Questo modulo supporta più tipi di operazioni per la pulizia dei valori mancanti, tra cui:

+ Sostituzione dei valori mancanti con un segnaposto, una media o un altro valore
+ Rimozione completa di righe e colonne con valori mancanti
+ Deduzione di valori in base a metodi statistici


L'uso di questo modulo non comporta la modifica del set di dati di origine. Viene invece creato un nuovo set di dati nell'area di lavoro che è possibile usare nel flusso di lavoro successivo. È anche possibile salvare il nuovo set di dati pulito per il riutilizzo.

Questo modulo restituisce inoltre una definizione della trasformazione utilizzata per pulire i valori mancanti. È possibile riutilizzare questa trasformazione in altri set di impostazioni con lo stesso schema utilizzando il modulo [Apply Transformation](./apply-transformation.md) .  

## <a name="how-to-use-clean-missing-data"></a>Come usare clean Missing data

Questo modulo consente di definire un'operazione di pulizia. È anche possibile salvare l'operazione di pulizia in modo che sia possibile applicarla in un secondo momento ai nuovi dati. Per una descrizione di come creare e salvare un processo di pulizia, vedere i collegamenti seguenti: 
 
+ Per sostituire i valori mancanti
  
+ Per applicare una trasformazione di pulizia ai nuovi dati
 
> [!IMPORTANT]
> Il metodo di pulizia usato per la gestione dei valori mancanti può influire in modo significativo sui risultati. Si consiglia di sperimentare metodi diversi. Prendere in considerazione sia la giustificazione per l'utilizzo di un particolare metodo che la qualità dei risultati.

### <a name="replace-missing-values"></a>Sostituisci valori mancanti  

Ogni volta che si applica il modulo [Clean Missing data](./clean-missing-data.md) a un set di dati, la stessa operazione di pulizia viene applicata a tutte le colonne selezionate. Se pertanto è necessario pulire colonne diverse utilizzando metodi diversi, utilizzare istanze separate del modulo.

1.  Aggiungere il modulo [Clean Missing data](./clean-missing-data.md) alla pipeline e connettere il set di dati con valori mancanti.  
  
2.  Per le **colonne da pulire**, scegliere le colonne che contengono i valori mancanti che si desidera modificare. È possibile scegliere più colonne, ma è necessario utilizzare lo stesso metodo di sostituzione in tutte le colonne selezionate. Pertanto, in genere è necessario pulire separatamente le colonne stringa e le colonne numeriche.

    Ad esempio, per verificare la presenza di valori mancanti in tutte le colonne numeriche:

    1. Aprire il selettore di colonna e selezionare **with Rules**.
    2. Per **Begin with**selezionare **Nessuna colonna**.

        È anche possibile iniziare con tutte le colonne ed escludere le colonne. Inizialmente, le regole non vengono visualizzate se si fa prima clic su **tutte le colonne**, ma è possibile fare clic su **Nessuna colonna** e quindi fare di nuovo clic su **tutte le colonne** per iniziare con tutte le colonne, quindi filtrare (escludere) le colonne in base al nome, al tipo di dati o all'indice delle colonne.

    3. Per **Includi**selezionare **tipo di colonna** dall'elenco a discesa, quindi selezionare **numerico**oppure un tipo numerico più specifico. 
  
    Qualsiasi metodo di pulizia o sostituzione scelto deve essere applicabile a **tutte** le colonne nella selezione. Se i dati in una colonna non sono compatibili con l'operazione specificata, il modulo restituisce un errore e arresta la pipeline.
  
3.  Per il **rapporto valore mancante minimo**specificare il numero minimo di valori mancanti necessari per l'esecuzione dell'operazione.  
  
    Usare questa opzione in combinazione con la **percentuale massima di valori mancanti** per definire le condizioni in base alle quali viene eseguita un'operazione di pulizia sul set di dati. Se sono presenti troppe righe o troppe righe prive di valori, non è possibile eseguire l'operazione. 
  
    Il numero immesso rappresenta il **rapporto** tra i valori mancanti e tutti i valori nella colonna. Per impostazione predefinita, la proprietà **Minimum missing value ratio** è impostata su 0. Ciò significa che i valori mancanti vengono puliti anche se è presente un solo valore mancante. 

    > [!WARNING]
    > Questa condizione deve essere soddisfatta da ogni colonna per poter applicare l'operazione specificata. Si supponga, ad esempio, di aver selezionato tre colonne e di impostare il rapporto minimo di valori mancanti su 0,2 (20%), ma solo una colonna contenga effettivamente il 20% dei valori mancanti. In questo caso, l'operazione di pulizia verrebbe applicata solo alla colonna con oltre il 20% dei valori mancanti. Pertanto, le altre colonne rimarranno invariate.
    > 
    > In caso di dubbi sull'eventuale modifica dei valori mancanti, selezionare l'opzione **genera colonna indicatore valore mancante**. Una colonna viene aggiunta al set di dati per indicare se ogni colonna soddisfa i criteri specificati per gli intervalli minimo e massimo.  
  
4. Per il **rapporto valore mancante massimo**, specificare il numero massimo di valori mancanti che possono essere presenti per l'operazione da eseguire.   
  
    È ad esempio possibile che si desideri eseguire la sostituzione dei valori mancanti solo se il 30% o un numero inferiore di righe contiene valori mancanti, ma lasciare i valori invariati se più del 30% delle righe presenta valori mancanti.  
  
    Il numero viene definito come rapporto tra i valori mancanti e tutti i valori nella colonna. Per impostazione predefinita, la **percentuale massima di valori mancanti** è impostata su 1. Ciò significa che i valori mancanti vengono puliti anche se manca il 100% dei valori nella colonna.  
  
   
  
5. Per la **modalità di pulizia**, selezionare una delle opzioni seguenti per sostituire o rimuovere i valori mancanti:  
  
  
    + **Valore di sostituzione personalizzato**: usare questa opzione per specificare un valore segnaposto, ad esempio 0 o na, che si applica a tutti i valori mancanti. Il valore specificato come sostituzione deve essere compatibile con il tipo di dati della colonna.
  
    + **Sostituisci con Mean**: calcola la media della colonna e usa la media come valore di sostituzione per ogni valore mancante nella colonna.  
  
        Si applica solo alle colonne con tipi di dati Integer, Double o Boolean.  
  
    + **Sostituisci con mediana**: calcola il valore mediano della colonna e usa il valore mediano come sostituzione per qualsiasi valore mancante nella colonna.  
  
        Si applica solo alle colonne con tipi di dati Integer o Double. 
  
    + **Sostituisci con Mode**: calcola la modalità per la colonna e usa la modalità come valore di sostituzione per ogni valore mancante nella colonna.  
  
        Si applica alle colonne con tipi di dati Integer, Double, Boolean o categorico. 
  
    + **Rimuovi intera riga**: rimuove completamente tutte le righe del set di dati che hanno uno o più valori mancanti. Questa operazione è utile se il valore mancante può essere considerato mancante in modo casuale.  
  
    + **Rimuovi intera colonna**: rimuove completamente tutte le colonne del set di dati che hanno uno o più valori mancanti.  
  
    
  
6. Il **valore di sostituzione** dell'opzione è disponibile se è stata selezionata l'opzione **valore di sostituzione personalizzato**. Digitare un nuovo valore da utilizzare come valore di sostituzione per tutti i valori mancanti nella colonna.  
  
    Si noti che è possibile utilizzare questa opzione solo nelle colonne con tipi di dati Integer, Double, Boolean o date. Per le colonne di data, il valore di sostituzione può anche essere immesso come numero di cicli 100-nanosecondi a partire dalle 1/1/0001 12:00.  
  
7. **Genera colonna indicatore valore mancante**: selezionare questa opzione se si desidera restituire un valore che indica se i valori nella colonna soddisfano i criteri per la pulizia dei valori mancanti. Questa opzione è particolarmente utile quando si configura una nuova operazione di pulizia e si desidera assicurarsi che funzioni come previsto.
  
8. Eseguire la pipeline.

### <a name="results"></a>Risultati

Il modulo restituisce due output:  

-   **DataSet pulito**: set di dati costituito dalle colonne selezionate, con valori mancanti gestiti come specificato, insieme a una colonna indicatore, se è stata selezionata questa opzione.  

    Anche le colonne non selezionate per la pulizia sono "passate".  
  
-  **Trasformazione pulizia**: trasformazione dei dati usata per la pulizia, che può essere salvata nell'area di lavoro e applicata ai nuovi dati in un secondo momento.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Applicare un'operazione di pulizia salvata ai nuovi dati  

Se è necessario ripetere spesso le operazioni di pulizia, è consigliabile salvare la ricetta per la pulizia dei dati come *trasformazione*, per riutilizzarla con lo stesso set di dati. Il salvataggio di una trasformazione di pulizia è particolarmente utile se è necessario reimportare di frequente e quindi pulire i dati con lo stesso schema.  
      
1.  Aggiungere il modulo [Apply Transformation](./apply-transformation.md) alla pipeline.  
  
2.  Aggiungere il set di dati che si desidera pulire e connettere il set di dati alla porta di input di destra.  
  
3.  Espandere il gruppo **trasformazioni** nel riquadro a sinistra dell'interfaccia. Individuare la trasformazione salvata e trascinarla nella pipeline.  
  
4.  Connettere la trasformazione salvata alla porta di input sinistra di [Apply Transformation](./apply-transformation.md). 

    Quando si applica una trasformazione salvata, non è possibile selezionare le colonne a cui viene applicata la trasformazione. Ciò è dovuto al fatto che la trasformazione è già stata definita e viene applicata automaticamente alle colonne specificate nell'operazione originale.

    Si supponga, tuttavia, che sia stata creata una trasformazione su un subset di colonne numeriche. È possibile applicare questa trasformazione a un set di dati di tipi di colonna misti senza generare un errore, in quanto i valori mancanti vengono modificati solo nelle colonne numeriche corrispondenti.

6.  Eseguire la pipeline.  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 