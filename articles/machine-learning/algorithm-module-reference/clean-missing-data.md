---
title: 'Pulisci dati mancanti: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Clean Missing Data nel servizio Azure Machine Learning per rimuovere, sostituire o dedurre i valori mancanti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: de81204219a102734f1820258a3c32e59a64c685
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028786"
---
# <a name="clean-missing-data-module"></a>Modulo di Clean Missing Data

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per rimuovere, sostituire o dedurre i valori mancanti. 

I data Scientist spesso controllare i dati per i valori mancanti e quindi eseguire diverse operazioni per correggere i dati o inserire nuovi valori. L'obiettivo di queste operazioni di pulizia è per evitare problemi causati da dati mancanti che possono verificarsi durante il training di un modello. 

Questo modulo supporta più i tipo di operazioni di "pulizia" i valori mancanti, tra cui:

+ Sostituzione dei valori mancanti con un segnaposto, Media o un altro valore
+ Rimuovere completamente righe e colonne con valori mancanti
+ Deduzione di valori basati sui metodi statistici


Uso di questo modulo non modifica il set di dati di origine. Ma crea un nuovo set di dati nell'area di lavoro che è possibile usare nel flusso di lavoro successivo. È inoltre possibile salvare il nuovo set di dati pulito per riutilizzarlo.

Questo modulo restituisce anche una definizione della trasformazione usata per pulire i valori mancanti. È possibile riusare questa trasformazione in altri set di dati che hanno lo stesso schema, utilizzando il [Apply Transformation](./apply-transformation.md) modulo.  

## <a name="how-to-use-clean-missing-data"></a>Come usare Clean Missing Data

Questo modulo consente di definire un'operazione di pulizia. È anche possibile salvare l'operazione di pulizia in modo che sia possibile applicare in un secondo momento ai nuovi dati. Vedere i collegamenti seguenti per una descrizione di come creare e salvare un processo di pulitura: 
 
+ Per sostituire i valori mancanti
  
+ Per applicare una trasformazione di pulizia ai nuovi dati
 
> [!IMPORTANT]
> Il metodo di pulizia che usa per la gestione dei valori mancanti può influire significativamente sui risultati. È consigliabile provare con metodi diversi. Prendere in considerazione la giustificazione per l'uso di un particolare metodo e la qualità dei risultati.

### <a name="replace-missing-values"></a>Sostituire i valori mancanti  

Ogni volta che applica i [Clean Missing Data](./clean-missing-data.md) modulo a un set di dati, l'operazione di pulizia stesso viene applicato a tutte le colonne selezionate. Pertanto, se si desidera eliminare colonne diverse usando metodi diversi, utilizzare istanze separate del modulo.

1.  Aggiungere il [Clean Missing Data](./clean-missing-data.md) modulo nell'esperimento e connettere il set di dati con valori mancanti.  
  
2.  Per la **le colonne da pulire**, scegliere le colonne che contengono i valori mancanti da modificare. È possibile scegliere più colonne, ma è necessario usare lo stesso metodo di sostituzione in tutte le colonne selezionate. Pertanto, in genere è necessario pulire le colonne stringa e le colonne numeriche separatamente.

    Ad esempio, per verificare la presenza di valori mancanti in tutte le colonne numeriche:

    1. Aprire il selettore di colonna e selezionare **con regole**.
    2. Per la **iniziano con**, selezionare **n colonne**.

        È possibile iniziare anche con tutte le colonne e quindi escludere le colonne. Inizialmente, vengono riportate le regole non se prima di tutto fare clic su **tutte le colonne**, ma è possibile fare clic su **n colonne** e quindi fare clic su **tutte le colonne** nuovamente per iniziare con tutte le colonne e quindi applicare un filtro le colonne (esclusione) in base al nome, tipo di dati o indice le colonne.

    3. Per la **inclusione**, selezionare **tipo di colonna** dall'elenco a discesa e quindi selezionare **numerico**, o un tipo numerico più specifico. 
  
    Deve essere applicabile a qualsiasi metodo di pulizia o la sostituzione che si sceglie **tutti** colonne nella selezione. Se i dati in qualsiasi colonna non sono compatibili con l'operazione specificata, il modulo restituisce un errore e arresta l'esperimento.
  
3.  Per la **minimo rapporto di valori mancanti**, specificare il numero minimo di valori mancanti necessari per l'operazione da eseguire.  
  
    Si utilizza questa opzione in combinazione con **massimo rapporto di valori mancanti** per definire le condizioni in cui viene eseguita un'operazione di pulizia nel set di dati. Se sono presenti troppe o troppo poche righe che sono valori mancanti, non è possibile eseguire l'operazione. 
  
    Il numero immesso rappresenta il **ratio** i valori mancanti e tutti i valori nella colonna. Per impostazione predefinita, il **rapporto di valori mancano minimo** è impostata su 0. Ciò significa che i valori mancanti verranno puliti anche se è presente un solo valore mancante. 

    > [!WARNING]
    > Questa condizione deve essere soddisfatta per ogni colonna in ordine per l'operazione specificata da applicare. Si supponga, ad esempio, è tre colonne selezionate e quindi impostare il rapporto minimo dei valori mancanti su.2 (% 20), ma una sola colonna effettivamente è 20% dei valori mancanti. In questo caso, l'operazione di pulizia verrà applicata solo alla colonna con oltre il 20% i valori mancanti. Pertanto, le altre colonne sarà invariate.
    > 
    > Se si hanno dubbi sul fatto che sono stati modificati i valori mancanti, selezionare l'opzione **generare colonne indicatore di valore mancante**. Una colonna viene aggiunto al set di dati per indicare se ogni colonna soddisfa i criteri specificati per gli intervalli minimi e massimo.  
  
4. Per la **massimo rapporto di valori mancanti**, specificare il numero massimo di valori mancanti che possono essere presenti per l'operazione da eseguire.   
  
    Ad esempio, si potrebbero voler eseguire la sostituzione di valori mancanti solo se più di 30% delle righe contengono valori mancanti, ma lasciare i valori come-è se più del 30% delle righe con valori mancanti.  
  
    Definire il numero come rapporto tra i valori mancanti e tutti i valori nella colonna. Per impostazione predefinita, il **massimo rapporto di valori mancanti** è impostato su 1. Ciò significa che i valori mancanti vengono eliminati anche se non sono presenti 100% dei valori nella colonna.  
  
   
  
5. Per la **modalità di pulizia**, selezionare una delle opzioni seguenti per la sostituzione o rimozione mancante valori:  
  
  
    + **Valore di sostituzione personalizzato**: Usare questa opzione per specificare un valore di segnaposto (ad esempio, il valore 0 o NA) che si applica a tutti i valori mancanti. Il valore specificato come una sostituzione deve essere compatibile con il tipo di dati della colonna.
  
    + **Sostituire con Media**: Calcola la media della colonna e viene utilizzato il valore medio come valore di sostituzione per ogni valore mancante nella colonna.  
  
        Si applica solo a colonne con valore Integer, Double o tipi di dati Boolean.  
  
    + **Sostituire con valore mediano**: Calcola il valore mediano della colonna e viene utilizzato il valore mediano come valore di sostituzione per ogni valore mancante nella colonna.  
  
        Si applica solo alle colonne con tipi di dati Integer o Double. 
  
    + **Sostituire con la modalità**: Calcola la moda della colonna e Usa la modalità come valore di sostituzione per ogni valore mancante nella colonna.  
  
        Si applica alle colonne con tipi di dati Integer, Double, Boolean o Categorical. 
  
    + **Rimuovi riga intera**: Rimuove completamente una riga nel set di dati contiene uno o più dei valori mancanti. Ciò è utile se il valore mancante può essere considerato manca in modo casuale.  
  
    + **Rimuovere l'intera colonna**: Rimuove completamente una colonna nel set di dati contiene uno o più dei valori mancanti.  
  
    
  
6. L'opzione **valore di sostituzione** è disponibile se è stata selezionata l'opzione **il valore di sostituzione personalizzata**. Digitare un nuovo valore da utilizzare come valore di sostituzione per tutti i valori mancanti nella colonna.  
  
    Si noti che è possibile usare questa opzione solo in colonne con i tipi di dati Integer, Double, Boolean o Date. Per le colonne data, il valore di sostituzione può anche essere immesso come numero di tick di 100 nanosecondi trascorsi dal 1/1/0001 12:00.  
  
7. **Generare colonne indicatore di valore mancante**: Selezionare questa opzione se si desidera restituire alcune indicazioni se i valori nella colonna soddisfa i criteri per la pulizia dei valori mancanti. Questa opzione è particolarmente utile quando si sta configurando una nuova operazione di pulizia e si desidera assicurarsi che funzioni come previsto.
  
8. Eseguire l'esperimento.

### <a name="results"></a>Risultati

Il modulo restituisce due output:  

-   **Set di dati pulito**: Un set di dati costituito da colonne selezionate, con valori mancanti gestiti come specificato, insieme a una colonna indicatore, se è stata selezionata tale opzione.  

    Le colonne non selezionate per la pulizia vengono anche "passate".  
  
-  **Trasformazione di pulizia**: Una trasformazione di dati utilizzata per la pulizia, che può essere salvata nell'area di lavoro e applicata ai nuovi dati in un secondo momento.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Applicare un'operazione di pulizia salvata ai nuovi dati  

Se è necessario ripetere spesso operazioni di pulizia, è consigliabile salvare il file recipe per la pulizia dei dati come una *trasformare*, riutilizzare con stesso set di dati. Il salvataggio di una trasformazione di pulizia è particolarmente utile se è necessario importare frequente di nuovo e quindi pulire i dati che ha lo stesso schema.  
      
1.  Aggiungere il [Apply Transformation](./apply-transformation.md) modulo nell'esperimento.  
  
2.  Aggiungere il set di dati da pulire e connettere il set di dati per la porta di input destra.  
  
3.  Espandere la **trasforma** gruppo nel riquadro sinistro dell'interfaccia. Individuare la trasformazione salvata e trascinarlo nell'esperimento.  
  
4.  Connettere la trasformazione salvata per la porta di input sinistra del [Apply Transformation](./apply-transformation.md). 

    Quando si applica una trasformazione salvata, è possibile selezionare le colonne a cui vengono applicate dalla trasformazione. Ciò avviene perché la trasformazione è stata già definita e viene applicato automaticamente alle colonne specificate nell'operazione originale.

    Tuttavia, si supponga che una trasformazione è stato creato su un subset di colonne numeriche. È possibile applicare questa trasformazione per un set di dati dei tipi di colonna misto senza generare un errore, perché i valori mancanti sono stati modificati solo nelle colonne numeriche corrispondente.

6.  Eseguire l'esperimento.  

## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 