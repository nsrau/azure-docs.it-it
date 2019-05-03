---
title: 'Immettere i dati manualmente: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo immettere manualmente i dati nel servizio Azure Machine Learning per creare un set di dati di piccole dimensioni digitando valori. Il set di dati può avere più colonne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028606"
---
# <a name="enter-data-manually-module"></a>Immettere manualmente i dati di modulo

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per creare un set di dati di piccole dimensioni digitando i valori. Il set di dati può avere più colonne.
  
Questo modulo può essere utile nei seguenti scenari:  
  
- Generazione di un piccolo set di valori per i test  
  
- Creazione di un breve elenco di etichette
  
- Digitare un elenco di nomi di colonna da inserire in un set di dati

## <a name="enter-data-manually"></a>Immettere manualmente i dati 
  
1.  Aggiungere il [Immetti manualmente i dati](./enter-data-manually.md) modulo nell'esperimento. È possibile trovare questo modulo nel **Data Input and Output** categoria in Azure Machine Learning. 
  
2.  Per la **DataFormat**, selezionare una delle opzioni seguenti. Queste opzioni determinano la modalità devono essere analizzati i dati forniti. I requisiti per ogni formato sono molto diversi, pertanto assicurarsi di leggere gli argomenti correlati.  
  
    -   **ARFF**. Formato di file attributi-relazione, usato da Weka.   
  
    -   **CSV**. Formato con valori delimitati da virgole. Per altre informazioni, vedere [Convert to CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Il formato Vowpal Wabbit e altri framework di machine learning.  
  
    -   **TSV**. Formato con valori delimitati da tabulazioni.

     Se si sceglie un formato e non forniscono i dati che soddisfano le specifiche di formato, si verifica un errore di run-time.
  
3.  Fare clic all'interno di **dati** casella di testo per iniziare a immettere dati. I formati seguenti richiedono un'attenzione particolare:  
  
    - **CSV**:  Per creare più colonne, incollare testo delimitato da virgole, o più colonne con una virgola tra i campi di tipo.
  
        Se si seleziona il **HasHeader** opzione, è possibile usare la prima riga di valori come intestazione di colonna.  
  
        Se si deseleziona questa opzione, i nomi delle colonne, Col1, Col2 e così via, vengono usati. È possibile aggiungere o modificare le colonne nomi in un secondo momento tramite [Edit Metadata](./edit-metadata.md).  
  
    - **TSV**: Per creare più colonne, incollare testo delimitati da tabulazioni, o più colonne usando le schede tra i campi di tipo.  
  
        Se si seleziona il **HasHeader** opzione, è possibile usare la prima riga di valori come intestazione di colonna.  
  
        Se si deseleziona questa opzione, i nomi delle colonne, Col1, Col2 e così via, vengono usati. È possibile aggiungere o modificare le colonne nomi in un secondo momento tramite [Edit Metadata](./edit-metadata.md).  
  
    -   **ARFF**:  Incollare in un file di formato ARFF esistente. Se si digita valori direttamente, assicurarsi di aggiungere l'intestazione facoltativa e i campi attributo obbligatorio all'inizio dei dati. 
    
        Ad esempio, le righe di intestazione e di attributo seguente è stato possibile aggiungere a un elenco semplice. L'intestazione di colonna sarebbe `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Digitare o incollare i valori usando il formato SVMLight.  
  
        Ad esempio, l'esempio seguente rappresenta le prime due righe del set di dati Blood Donation, nel formato SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Quando si esegue la [Immetti manualmente i dati](./enter-data-manually.md) modulo, queste righe vengono convertite in un set di dati delle colonne e indicizzare i valori come indicato di seguito:  
  
        |Col1|Col2|Col3|Col4|Etichette|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Premere INVIO dopo ogni riga, per avviare una nuova riga.  
  
     **Assicurarsi di premere INVIO dopo la riga finale.** 
     
     Se si preme INVIO più volte per aggiungere più vuote finali righe, una riga vuota finale viene rimosso troncate, ma altre righe vuote vengono trattati come valori mancanti.  
  
     Se si creano le righe con valori mancanti, è possibile sempre filtrare tali in un secondo momento.  
  
5.  Il modulo e scegliere **Esegui selezionati** per analizzare i dati e caricarli nell'area di lavoro come set di dati.  
  
     Per visualizzare il set di dati, fare clic sulla porta di output e selezionare **Visualize**.  
## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 