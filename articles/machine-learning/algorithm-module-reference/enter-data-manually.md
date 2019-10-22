---
title: 'Immettere i dati manualmente: riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Enter data Manual in Azure Machine Learning Service per creare un set di dati di piccole dimensioni digitando i valori. Il set di dati può contenere più colonne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: bd5fb636dbf79534e84a21a461f8912ceb6d9633
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693152"
---
# <a name="enter-data-manually-module"></a>Immettere manualmente i dati del modulo

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio Azure Machine Learning.

Usare questo modulo per creare un set di dati di piccole dimensioni digitando i valori. Il set di dati può contenere più colonne.
  
Questo modulo può essere utile in scenari come i seguenti:  
  
- Generazione di un piccolo set di valori per il test  
  
- Creazione di un breve elenco di etichette
  
- Immissione di un elenco di nomi di colonna da inserire in un set di dati

## <a name="enter-data-manually"></a>Immettere i dati manualmente 
  
1.  Aggiungere il modulo [immettere manualmente i dati](./enter-data-manually.md) alla pipeline. È possibile trovare questo modulo nella categoria **input e output dei dati** in Azure Machine Learning. 
  
2.  Per **DataFormat**, selezionare una delle opzioni seguenti. Queste opzioni determinano la modalità di analisi dei dati forniti. Poiché i requisiti per ogni formato sono molto diversi, assicurarsi di leggere gli argomenti correlati.  
  
    -   **ARFF**. Formato di file di relazione tra attributi, utilizzato da weka.   
  
    -   **CSV**. Formato con valori delimitati da virgole. Per altre informazioni, vedere [Convert to CSV (Converti in CSV](./convert-to-csv.md)).  
  
    -   **SVMLight**. Formato usato da Vowpal Wabbit e altri Framework di machine learning.  
  
    -   **TSV**. Formato con valori delimitati da tabulazioni.

     Se si sceglie un formato e non si forniscono dati che soddisfano le specifiche di formato, si verificherà un errore di run-time.
  
3.  Fare clic all'interno della casella di testo **dati** per iniziare a immettere i dati. I formati seguenti richiedono particolare attenzione:  
  
    - **CSV**: per creare più colonne, incollare un testo delimitato da virgole o digitare più colonne usando le virgole tra i campi.
  
        Se si seleziona l'opzione **HasHeader** , è possibile utilizzare la prima riga di valori come intestazione di colonna.  
  
        Se si deseleziona questa opzione, vengono utilizzati i nomi delle colonne, col1, Col2 e così via. È possibile aggiungere o modificare i nomi delle colonne in un secondo momento usando [Modifica metadati](./edit-metadata.md).  
  
    - **TSV**: per creare più colonne, incollare il testo separato da tabulazioni oppure digitare più colonne usando le schede tra i campi.  
  
        Se si seleziona l'opzione **HasHeader** , è possibile utilizzare la prima riga di valori come intestazione di colonna.  
  
        Se si deseleziona questa opzione, vengono utilizzati i nomi delle colonne, col1, Col2 e così via. È possibile aggiungere o modificare i nomi delle colonne in un secondo momento usando [Modifica metadati](./edit-metadata.md).  
  
    -   **ARFF**: incollare un file di formato ARFF esistente. Se si digitano direttamente i valori, assicurarsi di aggiungere i campi intestazione facoltativa e attributo obbligatorio all'inizio dei dati. 
    
        Ad esempio, è possibile aggiungere le righe di intestazione e di attributo seguenti a un elenco semplice. L'intestazione di colonna verrebbe `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: digitare o incollare i valori usando il formato SVMLight.  
  
        Ad esempio, l'esempio seguente rappresenta le prime due righe del set di dati di donazione del sangue, in formato SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Quando si esegue il modulo [Enter data manually](./enter-data-manually.md) , queste righe vengono convertite in un set di dati di colonne e valori di indice come indicato di seguito:  
  
        |Col1|Col2|Col3|Col4|Etichette|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1|  
        |0|0,004|0,999955|0,008615|1|  
  
4.  Premere INVIO dopo ogni riga per avviare una nuova riga.  
  
     **Assicurarsi di premere INVIO dopo la riga finale.** 
     
     Se si preme INVIO più volte per aggiungere più righe finali vuote, la riga vuota finale viene rimossa, ma le altre righe vuote vengono considerate come valori mancanti.  
  
     Se si creano righe con valori mancanti, è sempre possibile filtrarle in un secondo momento.  
  
5.  Fare clic con il pulsante destro del mouse sul modulo e scegliere **Esegui selezionati** per analizzare i dati e caricarli nell'area di lavoro come set di dati.  
  
     Per visualizzare il set di dati, fare clic sulla porta di output e selezionare **Visualizza**.  
## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning servizio. 