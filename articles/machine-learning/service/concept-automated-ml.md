---
title: Selezione e ottimizzazione dell'algoritmo di ML automatizzato
titleSuffix: Azure Machine Learning service
description: Informazioni su come il servizio Azure Machine Learning è in grado di scegliere automaticamente un algoritmo e, a partire da questo, generare un modello per consentire all'utente di risparmiare tempo. Tale scelta viene effettuata usando i parametri e i criteri specificati per selezionare l'algoritmo migliore per il modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: bf010e33a5ef77fcfde2506bfef9760a09667a9d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867253"
---
# <a name="what-is-automated-machine-learning"></a>Informazioni sulle funzionalità automatizzate di Machine Learning

Il processo di Machine Learning automatizzato consiste nell'acquisire dati di training con una determinata caratteristica e nell'eseguire più iterazioni con diverse combinazioni di algoritmi e selezioni di caratteristiche in modo da selezionare automaticamente il modello migliore per i dati in base ai punteggi di training. Il processo di sviluppo del modello di Machine Learning tradizionale richiede risorse ingenti, notevoli conoscenze settoriali e tempi estremamente lunghi per poter eseguire un elevato numero di modelli e confrontarne i risultati. Il Machine Learning automatizzato semplifica questo processo generando modelli ottimizzati in base agli obiettivi e ai vincoli definiti per l'esperimento, ad esempio il tempo necessario per l'esecuzione dell'esperimento o i modelli da inserire nella blacklist.

## <a name="how-it-works"></a>Funzionamento

1. Configurare il tipo di problema di Machine Learning che si sta tentando di risolvere. Sono supportate le categorie di apprendimento supervisionato seguenti:
   + classificazione
   + Regressione
   + Previsione

   Mentre il Machine Learning automatizzato è disponibile a livello generale, **la funzionalità di previsione è ancora in anteprima pubblica.**

   Vedere l'[elenco dei modelli](how-to-configure-auto-train.md#select-your-experiment-type) di Azure Machine Learning che è possibile provare durante il training.

1. Specificare l'origine e il formato per i dati di training. I dati devono essere etichettati e possono essere archiviati nell'ambiente di sviluppo o nell'archiviazione BLOB di Azure. Se i dati sono archiviati nell'ambiente di sviluppo, devono essere nella stessa directory degli script di training. Questa directory viene copiata nella destinazione di calcolo selezionata per il training.

    Nello script di training i dati possono essere letti in matrici Numpy o un dataframe Pandas.

    È possibile configurare le opzioni di divisione per la selezione di dati di training e di convalida oppure è possibile specificare set di dati di training e di convalida distinti.

1. Configurare la [destinazione di calcolo](how-to-set-up-training-targets.md) usata per il training del modello.

1. Configurare la configurazione delle funzionalità automatizzate di Machine Learning. In questo modo vengono controllati i parametri usati mentre Azure Machine Learning esegue l'iterazione su modelli diversi, le impostazioni degli iperparametri e le metriche da esaminare per stabilire qual è il modello migliore.

1. Inviare un'esecuzione di training.

Durante il training, il servizio Azure Machine Learning crea numerose pipeline che tentano di usare i diversi algoritmi e parametri. L'elaborazione viene interrotta quando viene raggiunto il limite di iterazione specificato o il valore di destinazione per la metrica desiderata.

[![Apprendimento automatico](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

È possibile esaminare le informazioni registrate sull'esecuzione, che contengono le metriche raccolte durante l'esecuzione. L'esecuzione di training genera anche un oggetto Python serializzato (file `.pkl`) che contiene il modello e la pre-elaborazione dei dati.

## <a name="model-explainability"></a>Spiegabilità del modello

Un tipico problema del Machine Learning automatizzato è rappresentato dall'impossibilità di visualizzare il processo end-to-end. Azure Machine Learning consente di visualizzare informazioni dettagliate sui modelli per rendere più trasparente il processo in esecuzione nel back-end. Alcuni modelli, ad esempio quello di regressione lineare, sono considerati piuttosto semplici e pertanto facili da comprendere. Tuttavia, con l'aggiunta di altre caratteristiche e l'uso di modelli di Machine Learning più complessi, la comprensione diventa sempre più difficile. Esistono due aspetti importanti correlati alla trasparenza nell'apprendimento automatico:

1. Consapevolezza della pipeline di apprendimento automatico e di tutti i passaggi correlati, tra cui la pre-elaborazione dei dati/definizione delle caratteristiche e i valori degli iperparametri.
1. Comprensione del rapporto esistente tra variabili di input (conosciute anche come "caratteristiche") e output del modello.  Conoscere sia la portata sia la direzione dell'impatto di ogni caratteristica sul valore previsto consente di comprendere e spiegare meglio il modello. Questo aspetto è noto come importanza delle caratteristiche.

È possibile abilitare funzionalità globale importanza post on demand di formazione per la pipeline di propria scelta, o abilitare la funzionalità per tutte le pipeline come parte di corsi di formazione automatizzati di machine learning. In settori molto soggetti a normative quali servizi sanitari e banche, questo è fondamentale per conformarsi alle normative e procedure consigliate.  Di seguito sono proposti alcuni scenari reali per illustrare:

1. Una società di produzione usando machine learning per prevedere un errore di strumentazione future, pertanto possono in modo proattivo eseguire attività di manutenzione. Dopo aver individuato che uno strumento deve avere esito negativo, ciò che è la causa più probabile verranno pertanto possibile eseguire rapidamente l'attività di manutenzione preventiva?
1. Un'istituzione finanziaria con machine learning per applicazioni di carta di credito o prestito di processo. Come si noto se il modello sta eseguendo la cosa giusta, e se un cliente chiede per altri dettagli sul motivo per cui l'applicazione è stata rifiutata, come risponderà a essi?
1. Un rivenditore online o un provider di software indipendenti tramite machine learning per prevedere la varianza del cliente. Quali sono i principali collaboratori al cliente di varianza e come può è impedire ai clienti di visitate?

Questa è una funzionalità di anteprima e continueremo a investire nel fornire informazioni più avanzate che consentono di comprendere meglio i modelli di machine learning. Seguire questo [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb) per sperimentare l'uso delle spiegazioni dei modelli in Azure Machine Learning.

## <a name="next-steps"></a>Passaggi successivi

Vedere esempi e informazioni su come creare modelli tramite le funzionalità automatizzate di Machine Learning:
+ [Esempi: Usare i notebook di Jupyter per esplorare il servizio Azure Machine Learning](samples-notebooks.md#automated-ml-setup)

+ [Esercitazione: Eseguire il training automatico di un modello di classificazione con Azure Automated Machine Learning](tutorial-auto-train-models.md)

+ [Usare il training automatico in una risorsa remota](how-to-auto-train-remote.md)

+ [Configurare le impostazioni per il training automatico](how-to-configure-auto-train.md)
