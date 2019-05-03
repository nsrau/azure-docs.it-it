---
title: 'Normalizzare i dati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modulo Normalize Data nel servizio Azure Machine Learning per trasformare un set di dati attraverso *normalizzazione*...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029446"
---
# <a name="normalize-data-module"></a>Normalize Data module

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per trasformare un set di dati attraverso *normalizzazione*.

Normalizzazione è una tecnica spesso applicata come parte della preparazione dei dati per machine learning. L'obiettivo di normalizzazione consiste nel modificare i valori delle colonne numeriche nel set di dati per usare una scala comune, senza distorsione differenze negli intervalli di valori o informazioni non confermate. Normalizzazione è necessaria anche per alcuni algoritmi modellare i dati in modo corretto.

Si supponga, ad esempio, che il set di dati di input contiene una colonna con valori compresi tra 0 e 1 e un'altra colonna con valori compresi tra 10.000 e 100.000. Il grande differenza di *scalabilità* dei numeri può causare problemi quando si tenta di combinare i valori come funzioni durante la modellazione.

*Normalizzazione* consente di evitare questi problemi tramite la creazione di nuovi valori che gestiscono la distribuzione generale e rapporti nei dati di origine, mantenendo i valori all'interno di una scala applicate a tutte le colonne numeriche utilizzate nel modello.

Questo modulo offre diverse opzioni per la trasformazione dei dati numerici:

- È possibile modificare tutti i valori per un 0-1 scalare o trasformare i valori che li rappresentano come classificazione percentile anziché valori assoluti.
- È possibile applicare la normalizzazione a una singola colonna o a più colonne nel set di dati stesso.
- Se è necessario ripetere il test o si applicano gli stessi passaggi di normalizzazione ad altri dati, è possibile salvare i passaggi come una trasformazione di normalizzazione e applicarla ad altri set di dati che hanno lo stesso schema.

> [!WARNING]
> Alcuni algoritmi richiedono di normalizzare i dati prima del training di un modello. Altri algoritmi consentono di eseguire i propri dati scalano o normalizzano autonomamente. Pertanto, quando si sceglie un algoritmo di machine learning da usare nella creazione di un modello predittivo, assicurarsi di esaminare i requisiti di dati dell'algoritmo prima di applicare la normalizzazione dei dati di training.

##  <a name="configure-normalize-data"></a>Configurare Normalize Data

È possibile applicare un solo metodo di normalizzazione alla volta usando questo modulo. Pertanto, lo stesso metodo di normalizzazione viene applicato a tutte le colonne selezionate. Per usare i metodi di normalizzazione diversa, usare una seconda istanza di **Normalize Data**.

1. Aggiungere il **Normalize Data** modulo nell'esperimento. È possibile trovare il modulo In Azure Machine Learning, sotto **Data Transformation**, nella **Scale and Reduce** categoria.

2. Connettere un set di dati che contiene almeno una colonna di tutti i numeri.

3. Usare il selettore di colonna per scegliere le colonne numeriche da normalizzare. Se non si sceglie di singole colonne, per impostazione predefinita **tutti** sono incluse colonne di tipo numerico nell'input e lo stesso processo di normalizzazione viene applicato a tutte le colonne selezionate. 

    Questo può causare risultati imprevisti se include colonne numeriche che non devono essere normalizzate. Verificare sempre con attenzione le colonne.

    Se non vengono rilevate le colonne numeriche, controllare i metadati della colonna per verificare che il tipo di dati della colonna è un tipo numerico supportato.

    > [!TIP]
    > Per garantire che le colonne di un tipo specifico vengono fornite come input, provare a usare il [Select Columns in Dataset](./select-columns-in-dataset.md) modulo prima **Normalize Data**.

4. **Usare 0 per le colonne costante se selezionata**:  Selezionare questa opzione quando qualsiasi colonna numerica contiene un singolo valore rimane invariato. Ciò garantisce che tali colonne non vengono usate nelle operazioni di normalizzazione.

5. Dal **metodo di trasformazione** elenco a discesa scegliere una singola funzione matematica da applicare a tutte le colonne selezionate. 
  
    - **Zscore**: Converte tutti i valori in un punteggio z.
    
      I valori nella colonna vengono trasformati usando la formula seguente:  
  
      ![normalizzazione con z&#45;punteggi](media/module/aml-normalization-z-score.png)
  
      Deviazione media e standard vengono calcolate separatamente per ogni colonna. Deviazione standard della popolazione viene utilizzata.
  
    - **MinMax**: Il normalizzatore min-max scala di nuovo linearmente tutte le funzionalità all'intervallo [0,1].
    
      Ridimensionamento all'intervallo [0,1] viene eseguita scalando i valori di ogni funzionalità in modo che il valore minimo è 0, e quindi dividendo per il nuovo valore massimo (che è la differenza tra i valori massimo e minimi originali).
      
      I valori nella colonna vengono trasformati usando la formula seguente:  
  
      ![normalizzazione con il numero minimo di&#45;funzione max](media/module/aml-normalization-minmax.png "AML_normalization minmax")  
  
    - **Logistica**: I valori nella colonna vengono trasformati usando la formula seguente:

      ![formula della normalizzazione per funzione logistica](media/module/aml-normalization-logistic.png "AML_normalization logistica")  
  
    - **LogNormal**: Questa opzione converte tutti i valori in una scala lognormale.
  
      I valori nella colonna vengono trasformati usando la formula seguente:
  
      ![formula log&#45;distribuzione normale](media/module/aml-normalization-lognormal.png "AML_normalization lognormale")
    
      In questo caso μg e σ sono i parametri della distribuzione, calcolata in modo empirico dai dati come stime della probabilità massima per ogni colonna separatamente.  
  
    - **TanH**: Tutti i valori vengono convertiti in una tangente iperbolica.
    
      I valori nella colonna vengono trasformati usando la formula seguente:
    
      ![normalizzazione con funzione tanh](media/module/aml-normalization-tanh.png "AML_normalization tanh")

6. Eseguire l'esperimento, oppure fare doppio clic il **Normalize Data** modulo e selezionare **Esegui selezione**. 

## <a name="results"></a>Risultati

Il **Normalize Data** modulo genera due output:

- Per visualizzare i valori trasformati, il pulsante destro del modulo, selezionare **set di dati trasformato**, fare clic su **Visualize**.

    Per impostazione predefinita, i valori vengono trasformati sul posto. Se si desidera confrontare i valori trasformati per i valori originali, usare il [Add Columns](./add-columns.md) modulo per ricombinare i set di dati e visualizzare le colonne di fianco a fianco.

- Per salvare la trasformazione in modo che sia possibile applicare lo stesso metodo di normalizzazione a un altro set di dati simili, il pulsante destro del modulo, selezionare **funzione di trasformazione**, fare clic su **Salva come trasformazione**.

    È quindi possibile caricare le trasformazioni salvate dal **trasforma** gruppo del riquadro di spostamento a sinistra e applicarlo a un set di dati con lo stesso schema utilizzando [. / Apply Transformation](apply-transformation.md).  


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 