---
title: 'Immettere i dati manualmente: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Enter data manually in Azure Machine Learning per creare un set di dati di piccole dimensioni digitando values. Il set di dati può contenere più colonne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3bff9a91f06649487560faef3ab554b2a3d56af3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908082"
---
# <a name="enter-data-manually-module"></a>Immettere manualmente i dati del modulo

Questo articolo descrive un modulo in Azure Machine Learning Designer.

Usare il modulo **Enter data manually** per creare un set di dati di piccole dimensioni digitando values. Il set di dati può contenere più colonne.
  
Questo modulo può essere utile in scenari come i seguenti:  
  
- Generazione di un piccolo set di valori per i test.  
- Creazione di un breve elenco di etichette.  
- Immissione di un elenco di nomi di colonna da inserire in un set di dati.

## <a name="create-a-dataset"></a>Creare un set di dati 
  
1. Aggiungere il modulo [immettere manualmente i dati](./enter-data-manually.md) alla pipeline. È possibile trovare questo modulo nella categoria **input e output dei dati** in Azure Machine Learning. 
  
1. Per **DataFormat**, selezionare una delle opzioni seguenti. Queste opzioni determinano la modalità di analisi dei dati forniti. Poiché i requisiti per ogni formato sono molto diversi, assicurarsi di leggere gli argomenti correlati.  
  
   - **ARFF**: formato di file di relazione tra attributi usato da weka.   
   - **CSV**: formato con valori delimitati da virgole. Per altre informazioni, vedere [Convert to CSV (Converti in CSV](./convert-to-csv.md)).    
   - **SVMLight**: formato usato da Vowpal Wabbit e altri Framework di machine learning.    
   - **TSV**: formato con valori delimitati da tabulazioni.

   Se si sceglie un formato e non si forniscono dati che soddisfano le specifiche di formato, si verificherà un errore di Runtime.
  
1. Fare clic all'interno della casella di testo **dati** per iniziare a immettere i dati. I formati seguenti richiedono particolare attenzione:  
  
   - **CSV**: per creare più colonne, incollare un testo delimitato da virgole o digitare più colonne usando le virgole tra i campi.
  
     Se si seleziona l'opzione **HasHeader** , è possibile utilizzare la prima riga di valori come intestazione di colonna.  
  
     Se si deseleziona questa opzione, vengono utilizzati i nomi delle colonne (col1, Col2 e così via). È possibile aggiungere o modificare i nomi delle colonne in un secondo momento usando [Modifica metadati](./edit-metadata.md).  
  
   - **TSV**: per creare più colonne, incollare il testo separato da tabulazioni oppure digitare più colonne usando le schede tra i campi.  
  
     Se si seleziona l'opzione **HasHeader** , è possibile utilizzare la prima riga di valori come intestazione di colonna.  
  
     Se si deseleziona questa opzione, vengono utilizzati i nomi delle colonne (col1, Col2 e così via). È possibile aggiungere o modificare i nomi delle colonne in un secondo momento usando [Modifica metadati](./edit-metadata.md).  
  
   - **ARFF**: incollare un file di formato ARFF esistente. Se i valori vengono digitati direttamente, assicurarsi di aggiungere i campi intestazione facoltativa e attributo obbligatorio all'inizio dei dati. 

     È ad esempio possibile aggiungere le righe di intestazione e di attributo seguenti a un elenco semplice. L'intestazione di colonna è `SampleText` . Si noti che il tipo stringa non è supportato.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: digitare o incollare i valori usando il formato SVMLight.  
  
     Ad esempio, l'esempio seguente rappresenta la prima coppia di righe del set di dati di donazione del sangue, nel formato SVMLight:  
  
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
  
1. Selezionare il tasto INVIO dopo ogni riga per avviare una nuova riga.      
     
   Se si seleziona INVIO più volte per aggiungere più righe finali vuote, le righe vuote verranno rimosse o tagliate.  
  
   Se si creano righe con valori mancanti, è sempre possibile filtrarle in un secondo momento.  
  
1. Connettere la porta di output ad altri moduli ed eseguire la pipeline.  
  
   Per visualizzare il set di dati, fare clic con il pulsante destro del mouse sul modulo e scegliere **Visualizza**.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 