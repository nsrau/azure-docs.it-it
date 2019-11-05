---
title: 'Pulire i dati mancanti: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo clean Missing data in Azure Machine Learning per rimuovere, sostituire o dedurre i valori mancanti.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: fef8cb0c29eac4b26b93abee54faa4fe1078678d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493807"
---
# <a name="clean-missing-data-module"></a>Pulisci modulo dati mancanti

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per rimuovere, sostituire o dedurre i valori mancanti. 

I data scientist controllano spesso i dati mancanti e quindi eseguono diverse operazioni per correggere i dati o inserire nuovi valori. L'obiettivo di tali operazioni di pulizia è impedire problemi causati da dati mancanti che possono verificarsi durante il training di un modello. 

Questo modulo supporta più tipi di operazioni per la pulizia dei valori mancanti, tra cui:

+ Sostituzione dei valori mancanti con un segnaposto, una media o un altro valore
+ Rimozione completa di righe e colonne con valori mancanti
+ Deduzione di valori in base a metodi statistici


L'uso di questo modulo non comporta la modifica del set di dati di origine. Instead, it creates a new dataset in your workspace that you can use in the subsequent workflow. You can also save the new, cleaned dataset for reuse.

This module also outputs a definition of the transformation used to clean the missing values. You can re-use this transformation on other datasets that have the same schema, by using the [Apply Transformation](./apply-transformation.md) module.  

## <a name="how-to-use-clean-missing-data"></a>How to use Clean Missing Data

This module lets you define a cleaning operation. You can also save the cleaning operation so that you can apply it later to new data. See the following links for a description of how to create and save a cleaning process: 
 
+ To replace missing values
  
+ To apply a cleaning transformation to new data
 
> [!IMPORTANT]
> The cleaning method that you use for handling missing values can dramatically affect your results. We recommend that you experiment with different methods. Consider both the justification for use of a particular method, and the quality of the results.

### <a name="replace-missing-values"></a>Replace missing values  

Each time that you apply the  [Clean Missing Data](./clean-missing-data.md) module to a set of data, the same cleaning operation is applied to all columns that you select. Therefore, if you need to clean different columns using different methods, use separate instances of the module.

1.  Add the [Clean Missing Data](./clean-missing-data.md) module to your pipeline, and connect the dataset that has missing values.  
  
2.  For **Columns to be cleaned**, choose the columns that contain the missing values you want to change. You can choose multiple columns, but you must use the same replacement method in all selected columns. Therefore, typically you need to clean string columns and numeric columns separately.

    For example, to check for missing values in all numeric columns:

    1. Open the Column Selector, and select **WITH RULES**.
    2. For **BEGIN WITH**, select **NO COLUMNS**.

        You can also start with ALL COLUMNS and then exclude columns. Initially, rules are not shown if you first click **ALL COLUMNS**, but you can click **NO COLUMNS** and then click **ALL COLUMNS** again to start with all columns and then filter out (exclude) columns based on the name, data type, or columns index.

    3. For **Include**, select **Column type** from the dropdown list, and then select **Numeric**, or a more specific numeric type. 
  
    Any cleaning or replacement method that you choose must be applicable to **all** columns in the selection. If the data in any column is incompatible with the specified operation, the module returns an error and stops the pipeline.
  
3.  For **Minimum missing value ratio**, specify the minimum number of missing values required for the operation to be performed.  
  
    You use this option in combination with **Maximum missing value ratio** to define the conditions under which a cleaning operation is performed on the dataset. If there are too many or too few rows that are missing values, the operation cannot be performed. 
  
    The number you enter represents the **ratio** of missing values to all values in the column. By default, the **Minimum missing value ratio** property is set to 0. This means that missing values are cleaned even if there is only one missing value. 

    > [!WARNING]
    > This condition must be met by each and every column in order for the specified operation to apply. For example, assume you selected three columns and then set the minimum ratio of missing values to .2 (20%), but only one column actually has 20% missing values. In this case, the cleanup operation would apply only to the column with over 20% missing values. Therefore, the other columns would be unchanged.
    > 
    > If you have any doubt about whether missing values were changed, select the option, **Generate missing value indicator column**. A column is appended to the dataset to indicate whether or not each column met the specified criteria for the minimum and maximum ranges.  
  
4. For **Maximum missing value ratio**, specify the maximum number of missing values that can be present for the operation to be performed.   
  
    For example, you might want to perform missing value substitution only if 30% or fewer of the rows contain missing values, but leave the values as-is if more than 30% of rows have missing values.  
  
    You define the number as the ratio of missing values to all values in the column. By default, the **Maximum missing value ratio** is set to 1. This means that missing values are cleaned even if 100% of the values in the column are missing.  
  
   
  
5. For **Cleaning Mode**, select one of the following options for replacing or removing missing values:  
  
  
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
  
3.  Espandere il gruppo **trasformazioni** nel riquadro a sinistra della finestra di progettazione. Individuare la trasformazione salvata e trascinarla nella pipeline.  

4.  Connettere la trasformazione salvata alla porta di input sinistra di [Apply Transformation](./apply-transformation.md). 

    Quando si applica una trasformazione salvata, non è possibile selezionare le colonne a cui viene applicata la trasformazione. Ciò è dovuto al fatto che la trasformazione è già stata definita e viene applicata automaticamente alle colonne specificate nell'operazione originale.

    Si supponga, tuttavia, che sia stata creata una trasformazione su un subset di colonne numeriche. È possibile applicare questa trasformazione a un set di dati di tipi di colonna misti senza generare un errore, in quanto i valori mancanti vengono modificati solo nelle colonne numeriche corrispondenti.

6.  Eseguire la pipeline.  

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 