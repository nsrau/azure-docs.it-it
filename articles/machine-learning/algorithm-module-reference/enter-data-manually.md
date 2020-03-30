---
title: 'Inserimento manuale dei dati: riferimento del modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Immetti dati manualmente in Azure Machine Learning per creare un set di dati di piccole dimensioni digitando i valori. Il set di dati può avere più colonne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367517"
---
# <a name="enter-data-manually-module"></a>Modulo Immetti dati manualmente

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare il modulo **Immetti dati manualmente** per creare un set di dati di piccole dimensioni digitando i valori. Il set di dati può avere più colonne.
  
Questo modulo può essere utile in scenari quali:This module can be helpful in scenarios such as:  
  
- Generazione di un piccolo set di valori per il test.  
- Creazione di un breve elenco di etichette.  
- Digitazione di un elenco di nomi di colonna da inserire in un set di dati.

## <a name="create-a-dataset"></a>Creare un set di dati 
  
1. Aggiungere il modulo [Immettere i dati manualmente](./enter-data-manually.md) alla pipeline. È possibile trovare questo modulo nella categoria **Input e output dei dati** in Azure Machine Learning.You can find this module in the Data Input and Output category in Azure Machine Learning. 
  
1. Per **DataFormat**, selezionare una delle opzioni seguenti. Queste opzioni determinano il modo in cui i dati forniti devono essere analizzati. I requisiti per ogni formato differiscono notevolmente, quindi assicurati di leggere gli argomenti correlati.  
  
   - **ARFF**: Formato di file di relazione tra attributi utilizzato da Weka.   
   - **CSV**: Formato dei valori delimitati da virgole. Per ulteriori informazioni, consultate [Convertire in CSV.](./convert-to-csv.md)    
   - **SVMLight:** formato utilizzato da Vowpal Wabbit e da altri framework di apprendimento automatico.    
   - **TSV**: formato di valori delimitati da tabulazioni.

   Se si sceglie un formato e non si forniscono dati che soddisfano le specifiche di formato, si verifica un errore di runtime.
  
1. Fare clic all'interno della casella di testo **Dati** per iniziare a immettere i dati. I seguenti formati richiedono particolare attenzione:  
  
   - **CSV**: Per creare più colonne, incollare testo delimitato da virgole o digitare più colonne utilizzando virgole tra i campi.
  
     Se si seleziona l'opzione **HasHeader,** è possibile utilizzare la prima riga di valori come intestazione di colonna.  
  
     Se si deseleziona questa opzione, vengono utilizzati i nomi delle colonne (Col1, Col2 e così via). È possibile aggiungere o modificare i nomi delle colonne in un secondo momento utilizzando [Modifica metadati](./edit-metadata.md).  
  
   - **TSV**: Per creare più colonne, incollare testo separato da tabulazioni o digitare più colonne utilizzando tabulazioni tra i campi.  
  
     Se si seleziona l'opzione **HasHeader,** è possibile utilizzare la prima riga di valori come intestazione di colonna.  
  
     Se si deseleziona questa opzione, vengono utilizzati i nomi delle colonne (Col1, Col2 e così via). È possibile aggiungere o modificare i nomi delle colonne in un secondo momento utilizzando [Modifica metadati](./edit-metadata.md).  
  
   - **ARFF**: Incollare in un file in formato ARFF esistente. Se si digitano direttamente i valori, assicurarsi di aggiungere l'intestazione facoltativa e i campi degli attributi obbligatori all'inizio dei dati. 

     Ad esempio, le seguenti righe di intestazione e attributo possono essere aggiunte a un elenco semplice. L'intestazione `SampleText`di colonna sarebbe . Si noti che il tipo String non è supportato.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: digitare o incollare i valori utilizzando il formato SVMLight.  
  
     Ad esempio, l'esempio seguente rappresenta le prime due righe del set di dati Blood Donation, in formato SVMLight:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Quando si esegue il modulo [Immetti dati manualmente,](./enter-data-manually.md) queste righe vengono convertite in un set di dati di colonne e valori di indice come segue:  
  
     |Col1|Col2|Col3|Col4|Etichette|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. Selezionare il tasto Invio dopo ogni riga per iniziare una nuova riga.      
     
   Se si seleziona Inserisci più volte per aggiungere più righe finali vuote, le righe vuote verranno rimosse o tagliate.  
  
   Se si creano righe con valori mancanti, è sempre possibile filtrarle in un secondo momento.  
  
1. Connettere la porta di output ad altri moduli ed eseguire la pipeline.  
  
   Per visualizzare il dataset, fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza**.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 