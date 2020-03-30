---
title: 'Pulire i dati mancanti: riferimento al moduloClean Missing Data: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Pulisci dati mancanti in Azure Machine Learning per rimuovere, sostituire o dedurre i valori mancanti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 14c3bc968da9d398fbc14eda74378047cf28277b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477698"
---
# <a name="clean-missing-data-module"></a>Modulo Pulisci dati mancanti

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per rimuovere, sostituire o dedurre i valori mancanti. 

Gli scienziati dei dati spesso controllano i dati per i valori mancanti e quindi eseguono varie operazioni per correggere i dati o inserire nuovi valori. L'obiettivo di tali operazioni di pulizia è quello di evitare problemi causati da dati mancanti che possono verificarsi durante il training di un modello. 

Questo modulo supporta più tipi di operazioni per i valori mancanti di "pulizia", tra cui:

+ Sostituzione dei valori mancanti con un segnaposto, una media o un altro valore
+ Rimozione completa di righe e colonne con valori mancanti
+ Deduzione di valori basati su metodi statistici


L'utilizzo di questo modulo non comporta la modifica del set di dati di origine. Al contrario, crea un nuovo set di dati nell'area di lavoro che è possibile utilizzare nel flusso di lavoro successivo. È inoltre possibile salvare il nuovo set di dati pulito per riutilizzarlo.

Questo modulo restituisce anche una definizione della trasformazione utilizzata per pulire i valori mancanti. È possibile riutilizzare questa trasformazione in altri set di dati con lo stesso schema, usando il modulo [Applica trasformazione.](./apply-transformation.md)  

## <a name="how-to-use-clean-missing-data"></a>Come utilizzare Pulisci dati mancanti

Questo modulo consente di definire un'operazione di pulizia. È inoltre possibile salvare l'operazione di pulizia in modo da poterla applicare in un secondo momento ai nuovi dati. Vedere le sezioni seguenti su come creare e salvare un processo di pulizia: 
 
+ [Per sostituire i valori mancanti](#replace-missing-values)
  
+ [Per applicare una trasformazione di pulizia ai nuovi dati](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> Il metodo di pulizia utilizzato per la gestione dei valori mancanti può influire notevolmente sui risultati. Si consiglia di sperimentare con metodi diversi. Considerare sia la giustificazione per l'uso di un particolare metodo, sia la qualità dei risultati.

### <a name="replace-missing-values"></a>Sostituire valori mancanti  

Ogni volta che si applica il modulo [Pulisci dati mancanti](./clean-missing-data.md) a un set di dati, la stessa operazione di pulizia viene applicata a tutte le colonne selezionate. Pertanto, se è necessario pulire colonne diverse utilizzando metodi diversi, utilizzare istanze separate del modulo.

1.  Aggiungere il modulo [Pulisci dati mancanti](./clean-missing-data.md) alla pipeline e connettere il set di dati con valori mancanti.  
  
2.  Per **Colonne da pulire**, scegliere le colonne che contengono i valori mancanti che si desidera modificare. È possibile scegliere più colonne, ma è necessario utilizzare lo stesso metodo di sostituzione in tutte le colonne selezionate. Pertanto, in genere è necessario pulire le colonne stringa e le colonne numeriche separatamente.

    Ad esempio, per verificare la presenza di valori mancanti in tutte le colonne numeriche:For example, to check for missing values in all numeric columns:

    1. Selezionare il modulo **Pulisci dati mancanti** e fare clic su **Modifica colonna** nel pannello di destra del modulo.

    3. Per **Includi**selezionare **Tipi di colonna** dall'elenco a discesa e quindi **selezionare Numerico**. 
  
    Qualsiasi metodo di pulizia o sostituzione scelto deve essere applicabile a **tutte le** colonne della selezione. Se i dati in qualsiasi colonna non sono compatibili con l'operazione specificata, il modulo restituisce un errore e interrompe la pipeline.
  
3.  Per **Rapporto valori minimo mancante**specificare il numero minimo di valori mancanti necessari per l'esecuzione dell'operazione.  
  
    Utilizzare questa opzione in combinazione con **il rapporto di valori mancanti massimo** per definire le condizioni in cui viene eseguita un'operazione di pulizia sul set di dati. Se sono presenti troppe o troppo poche righe che mancano valori, l'operazione non può essere eseguita. 
  
    Il numero immesso rappresenta il **rapporto** tra i valori mancanti e tutti i valori nella colonna. Per impostazione predefinita, la proprietà **Rapporto valore minimo mancante** è impostata su 0.By default, the Minimum missing value ratio property is set to 0. Ciò significa che i valori mancanti vengono puliti anche se è presente un solo valore mancante. 

    > [!WARNING]
    > Questa condizione deve essere soddisfatta da ogni colonna affinché l'operazione specificata venga applicata. Si supponga, ad esempio, di aver selezionato tre colonne e di aver impostato il rapporto minimo dei valori mancanti su 0,22 (20%), ma solo una colonna ha effettivamente il 20% di valori mancanti. In questo caso, l'operazione di pulizia si applicherebbe solo alla colonna con oltre il 20% di valori mancanti. Pertanto, le altre colonne verrebbero invariate.
    > 
    > In caso di dubbi sulla modifica o meno dei valori mancanti, selezionare l'opzione **Generate missing value indicator column**. Al set di dati viene aggiunta una colonna per indicare se ogni colonna soddisfa i criteri specificati per gli intervalli minimo e massimo.  
  
4. In Rapporto massimo valori **mancanti**specificare il numero massimo di valori mancanti che possono essere presenti per l'esecuzione dell'operazione.   
  
    Ad esempio, è possibile eseguire la sostituzione dei valori mancanti solo se il 30% o meno delle righe contiene valori mancanti, ma lasciare i valori così come sono se più del 30% delle righe hanno valori mancanti.  
  
    È possibile definire il numero come rapporto tra i valori mancanti e tutti i valori della colonna. Per impostazione predefinita, il **rapporto valore massimo mancante** è impostato su 1.By default, the Maximum missing value ratio is set to 1. Ciò significa che i valori mancanti vengono puliti anche se manca il 100% dei valori nella colonna.  
  
   
  
5. Per **Modalità di pulizia**, selezionare una delle seguenti opzioni per la sostituzione o la rimozione dei valori mancanti:  
  
  
    + **Valore**di sostituzione personalizzato : utilizzare questa opzione per specificare un valore segnaposto (ad esempio 0 o NA) che si applica a tutti i valori mancanti. Il valore specificato come sostituzione deve essere compatibile con il tipo di dati della colonna.
  
    + **Sostituisci con media**: Calcola la media della colonna e utilizza la media come valore di sostituzione per ogni valore mancante nella colonna.  
  
        Si applica solo alle colonne con tipi di dati Integer, Double o Boolean.  
  
    + **Sostituisci con mediana**: Calcola il valore mediano della colonna e utilizza il valore mediano come sostituzione di qualsiasi valore mancante nella colonna.  
  
        Si applica solo alle colonne con tipi di dati Integer o Double. 
  
    + **Sostituisci con modalità**: Calcola la modalità per la colonna e utilizza la modalità come valore di sostituzione per ogni valore mancante nella colonna.  
  
        Si applica alle colonne con tipi di dati Integer, Double, Boolean o Categorical. 
  
    + **Rimuovi intera riga**: Rimuove completamente qualsiasi riga del set di dati con uno o più valori mancanti. Ciò è utile se il valore mancante può essere considerato mancante in modo casuale.  
  
    + **Rimuovi intera colonna**: Rimuove completamente qualsiasi colonna del set di dati con uno o più valori mancanti.  
  
    
  
6. L'opzione **Valore** di sostituzione è disponibile se è stata selezionata l'opzione **Valore di sostituzione personalizzato**. Digitare un nuovo valore da utilizzare come valore di sostituzione per tutti i valori mancanti nella colonna.  
  
    Si noti che è possibile utilizzare questa opzione solo nelle colonne con Integer, Double, Boolean o String.
  
7. **Genera colonna indicatore di valore mancante**: Selezionare questa opzione se si desidera produrre qualche indicazione se i valori nella colonna soddisfano i criteri per la pulizia dei valori mancanti. Questa opzione è particolarmente utile quando si imposta una nuova operazione di pulizia e si desidera assicurarsi che funzioni come previsto.
  
8. Inviare la pipeline.

### <a name="results"></a>Risultati

Il modulo restituisce due output:  

-   **Set di dati pulito:** un set di dati costituito dalle colonne selezionate, con i valori mancanti gestiti come specificato, insieme a una colonna indicatore, se è stata selezionata tale opzione.  

    Anche le colonne non selezionate per la pulizia vengono "passate".  
  
-  **Trasformazione di pulizia**: Trasformazione dei dati utilizzata per la pulizia, che può essere salvata nell'area di lavoro e applicata ai nuovi dati in un secondo momento.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Applicare un'operazione di pulizia salvata ai nuovi dati  

Se è necessario ripetere spesso le operazioni di pulizia, è consigliabile salvare la ricetta per la pulizia dei dati come *trasformazione,* per riutilizzarla con lo stesso set di dati. Il salvataggio di una trasformazione di pulizia è particolarmente utile se è necessario reimportare frequentemente e quindi pulire i dati con lo stesso schema.  
      
1.  Aggiungere il modulo [Applica trasformazione](./apply-transformation.md) alla pipeline.  
  
2.  Aggiungere il set di dati da pulire e connetterlo alla porta di input a destra.  
  
3.  Espandere il gruppo **Trasforma** nel riquadro sinistro della finestra di progettazione. Individuare la trasformazione salvata e trascinarla nella pipeline.  

4.  Connettere la trasformazione salvata alla porta di input sinistra di [Applica trasformazione](./apply-transformation.md). 

    Quando si applica una trasformazione salvata, non è possibile selezionare le colonne a cui viene applicata la trasformazione. Ciò è dovuto al fatto che la trasformazione è già stata definita e si applica automaticamente alle colonne specificate nell'operazione originale.

    Si supponga tuttavia di aver creato una trasformazione in un sottoinsieme di colonne numeriche. È possibile applicare questa trasformazione a un set di dati di tipi di colonna misti senza generare un errore, perché i valori mancanti vengono modificati solo nelle colonne numeriche corrispondenti.

6.  Inviare la pipeline.  

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 