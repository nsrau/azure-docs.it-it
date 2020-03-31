---
title: 'Normalize Data: Riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Normalizza dati in Azure Machine Learning per trasformare un set di dati tramite *la normalizzazione*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a740c81aa165e221bca19987c7b3c62da56b0402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477528"
---
# <a name="normalize-data-module"></a>Normalizza modulo dati

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per trasformare un dataset tramite *la normalizzazione.*

La normalizzazione è una tecnica spesso applicata come parte della preparazione dei dati per l'apprendimento automatico. L'obiettivo della normalizzazione consiste nel modificare i valori delle colonne numeriche nel set di dati in modo da utilizzare una scala comune, senza distorcere le differenze negli intervalli di valori o perdere informazioni. La normalizzazione è necessaria anche per alcuni algoritmi per modellare correttamente i dati.

Si supponga, ad esempio, che il set di dati di input contenga una colonna con valori compresi tra 0 e 1 e un'altra colonna con valori compresi tra 10.000 e 100.000.For example, assume your input dataset contains one column with values ranging from 0 to 1, and another column with values ranging from 10,000 to 100,000. La grande differenza nella *scala* dei numeri potrebbe causare problemi quando si tenta di combinare i valori come feature durante la modellazione.

*La normalizzazione* evita questi problemi creando nuovi valori che mantengono la distribuzione generale e i rapporti nei dati di origine, mantenendo i valori all'interno di una scala applicata a tutte le colonne numeriche utilizzate nel modello.

Questo modulo offre diverse opzioni per la trasformazione dei dati numerici:

- È possibile modificare tutti i valori in una scala 0-1 o trasformare i valori rappresentandoli come ranghi percentili anziché come valori assoluti.
- È possibile applicare la normalizzazione a una singola colonna o a più colonne dello stesso set di dati.
- Se è necessario ripetere la pipeline o applicare gli stessi passaggi di normalizzazione ad altri dati, è possibile salvare i passaggi come trasformazione di normalizzazione e applicarli ad altri set di dati con lo stesso schema.

> [!WARNING]
> Alcuni algoritmi richiedono di normalizzare i dati prima di eseguire il training di un modello. Altri algoritmi scalano o normalizzano autonomamente i propri dati. Pertanto, quando si sceglie un algoritmo di apprendimento automatico da usare nella creazione di un modello predittivo, verificare i requisiti dei dati dell'algoritmo prima di applicare la normalizzazione ai dati di training.

##  <a name="configure-normalize-data"></a>Configurare Normalizza dati

È possibile applicare un solo metodo di normalizzazione alla volta utilizzando questo modulo. Pertanto, lo stesso metodo di normalizzazione viene applicato a tutte le colonne selezionate. Per utilizzare metodi di normalizzazione diversi, utilizzare una seconda istanza di **Normalizza dati**.

1. Aggiungere il modulo **Normalizza dati** alla pipeline. È possibile trovare il modulo in Azure Machine Learning, in **Trasformazione dati**, nella categoria **Scala e riduci.**

2. Connettere un set di dati che contiene almeno una colonna di tutti i numeri.

3. Utilizzare il Selettore colonne per scegliere le colonne numeriche da normalizzare. Se non si scelgono singole colonne, per impostazione predefinita vengono incluse **tutte le** colonne di tipo numerico nell'input e lo stesso processo di normalizzazione viene applicato a tutte le colonne selezionate. 

    Questo può portare a risultati strani se si includono colonne numeriche che non devono essere normalizzate! Controllare sempre le colonne con attenzione.

    Se non vengono rilevate colonne numeriche, controllare i metadati della colonna per verificare che il tipo di dati della colonna sia un tipo numerico supportato.

    > [!TIP]
    > Per assicurarsi che le colonne di un tipo specifico vengano fornite come input, provare a utilizzare il modulo [Seleziona colonne nel set di dati](./select-columns-in-dataset.md) prima di **normalizzare**i dati .

4. Usa 0 per le **colonne costanti se selezionata:** selezionare questa opzione quando una colonna numerica contiene un singolo valore non in scambiabile. Ciò garantisce che tali colonne non vengano utilizzate nelle operazioni di normalizzazione.

5. Dall'elenco a discesa **Metodo di trasformazione,** scegliere una singola funzione matematica da applicare a tutte le colonne selezionate. 
  
    - **:** Converte tutti i valori in un punteggio z.
    
      I valori nella colonna vengono trasformati usando la seguente formula:  
  
      ![normalizzazione utilizzando i punteggi&#45;z](media/module/aml-normalization-z-score.png)
  
      La deviazione media e quella standard vengono calcolate separatamente per ogni colonna. Viene usata la deviazione standard della popolazione.
  
    - **MinMax**: Il normalizzatore min-max ridimensiona linearmente ogni feature all'intervallo [0,1].
    
      Questa operazione viene eseguita scalando i valori di ogni funzione in modo che il valore minimo sia 0 e dividendo per il nuovo valore massimo, che è la differenza tra i valori massimo e minimo originali.
      
      I valori nella colonna vengono trasformati usando la seguente formula:  
  
      ![normalizzazione utilizzando la funzione min&#45;max](media/module/aml-normalization-minmax.png "AML_normalization minmax")  
  
    - **Logistico**: I valori nella colonna vengono trasformati utilizzando la seguente formula:

      ![formula della normalizzazione per funzione logistica](media/module/aml-normalization-logistic.png "AML_normalization-logistica")  
  
    - **LogNormal**: Questa opzione converte tutti i valori in una scala lognormale.
  
      I valori nella colonna vengono trasformati usando la seguente formula:
  
      ![registro delle formule&#45;distribuzione normale](media/module/aml-normalization-lognormal.png "AML_normalization-lognormale")
    
      Qui i parametri della distribuzione, calcolati empiricamente dai dati come stime di massima probabilità, per ogni colonna separatamente.  
  
    - **TanH**: Tutti i valori vengono convertiti in una tangente iperbolica.
    
      I valori nella colonna vengono trasformati usando la seguente formula:
    
      ![normalizzazione con funzione tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Inviare la pipeline oppure fare doppio clic sul modulo **Normalizza dati** e selezionare **Esegui selezionati**. 

## <a name="results"></a>Risultati

Il modulo **Normalizza dati** genera due output:

- Per visualizzare i valori trasformati, fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza**.

    Per impostazione predefinita, i valori vengono trasformati sul posto. Se si desidera confrontare i valori trasformati con i valori originali, utilizzare il modulo [Aggiungi colonne](./add-columns.md) per ricombinare i set di dati e visualizzare le colonne affiancate.

- Per salvare la trasformazione in modo da poter applicare lo stesso metodo di normalizzazione a un altro set di dati, selezionare il modulo e selezionare **Registra set di dati** nella scheda **Output** nel riquadro di destra.

    È quindi possibile caricare le trasformazioni salvate dal gruppo **Trasformazioni** del riquadro di spostamento sinistro e applicarle a un set di dati con lo stesso schema utilizzando [Applica trasformazione](apply-transformation.md).  


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 