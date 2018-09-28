---
title: Informazioni sulle funzionalità automatizzate di Machine Learning - Azure Machine Learning
description: In questo articolo vengono fornite informazioni sulle funzionalità automatizzate di Machine Learning. Il servizio Azure Machine Learning può selezionare automaticamente un algoritmo utilizzabile per generare un modello. Le funzionalità automatizzate di Machine Learning consentono di risparmiare tempo usando i parametri e i criteri specificati dall'utente per selezionare l'algoritmo migliore per il modello.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: krishnan
author: krishnaanumalasetty
ms.date: 9/24/2018
ms.openlocfilehash: 2a9c05b68d05102fab80b2aa8fb1c1dad8a367ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960040"
---
# <a name="what-is-automated-machine-learning"></a>Informazioni sulle funzionalità automatizzate di Machine Learning

In questo articolo vengono fornite informazioni sulle funzionalità automatizzate di Machine Learning. Il servizio Azure Machine Learning può selezionare automaticamente un algoritmo utilizzabile per generare un modello. Le funzionalità automatizzate di Machine Learning consentono di risparmiare tempo usando i parametri e i criteri specificati dall'utente per selezionare l'algoritmo migliore per il modello.

## <a name="how-it-works"></a>Funzionamento

1. Configurare il tipo di problema di Machine Learning che si sta tentando di risolvere. Sono supportate due categorie di apprendimento supervisionato:
   + Classificazione
   + Regressione

   Vedere l'[elenco dei modelli](how-to-configure-auto-train.md#select-your-experiment-type) di Azure Machine Learning che è possibile provare durante il training.

1. Specificare l'origine e il formato per i dati di training. I dati devono essere etichettati e possono essere archiviati nell'ambiente di sviluppo o nell'archiviazione BLOB di Azure. Se i dati sono archiviati nell'ambiente di sviluppo, devono essere nella stessa directory degli script di training. Questa directory viene copiata nella destinazione di calcolo selezionata per il training.

    Nello script di training i dati possono essere letti in matrici Numpy o un dataframe Pandas.

    È possibile configurare le opzioni di divisione per la selezione di dati di training e di convalida oppure è possibile specificare set di dati di training e di convalida distinti.

1. Configurare la [destinazione di calcolo](how-to-set-up-training-targets.md) usata per il training del modello.

1. Configurare la configurazione delle funzionalità automatizzate di Machine Learning. Questa definisce i parametri usati da Azure Machine Learning durante l'iterazione con i diversi modelli, le impostazioni degli iperparametri e le metriche da esaminare per determinare il modello migliore. 

1. Inviare un'esecuzione di training.

Durante il training, il servizio Azure Machine Learning crea numerose pipeline che tentano di usare i diversi algoritmi e parametri. L'elaborazione viene interrotta quando viene raggiunto il limite di iterazione specificato o il valore di destinazione per la metrica desiderata.

[ ![Funzionalità automatizzate di Machine Learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

È possibile esaminare le informazioni registrate sull'esecuzione, che contengono le metriche raccolte durante l'esecuzione. L'esecuzione di training produce anche un oggetto serializzato Python (file con estensione PKL) che contiene il modello e i dati di pre-elaborazione.

## <a name="next-steps"></a>Passaggi successivi

Vedere esempi e informazioni su come creare modelli tramite le funzionalità automatizzate di Machine Learning:

+ [Tutorial: Automatically train a classification model with Azure Automated Machine Learning](tutorial-auto-train-models.md) (Esercitazione: Eseguire il training automatico di un modello di classificazione con Azure Automated Machine Learning)

+ [How to configure settings for automatic training](how-to-configure-auto-train.md) (Come configurare le impostazioni per il training automatico)

+ [How to use automatic training on a remote resource](how-to-auto-train-remote.md) (Come usare training automatico in una risorsa remota) 
