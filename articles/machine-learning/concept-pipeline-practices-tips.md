---
title: Iterazione e evoluzione di pipeline di Machine Learning
titleSuffix: Azure Machine Learning
description: Modelli, procedure e suggerimenti per lo sviluppo rapido
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858189"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Iterazione e evoluzione di pipeline di Machine Learning

Azure Machine Learning pipeline forniscono un modo efficiente per modularizzare il codice, riutilizzare i risultati e ottimizzare le risorse di calcolo. Ecco alcuni suggerimenti pratici e procedure per lavorare con le pipeline.

## <a name="how-do-you-get-started-with-pipelines"></a>Come iniziare a usare le pipeline?

Sono disponibili diverse opzioni per iniziare se non si ha familiarità con le pipeline:

* Se si impara meglio leggendo e ricreando il processo di costruzione, l'articolo [creare ed eseguire pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md) è una soluzione ottimale 
* Se si desidera imparare in modo interattivo in un notebook di Jupyter, la pipeline sviluppata in [Azure Machine Learning pipeline: introduzione](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) notebook potrebbe essere adatta alle proprie proprie
* Se si preferisce una situazione di codice per la prima volta, la clonazione del repository [Demo Azure Machine Learning Pipelines](https://github.com/microsoft/aml-pipelines-demo) rappresenta un valido punto di partenza

## <a name="how-do-you-modularize-pipeline-code"></a>Come si modularizzare il codice della pipeline? 

I moduli e `ModuleStep` la classe offrono un'ottima opportunità per modularizzare il codice ml. Tuttavia, è necessario tenere presente che il passaggio tra i passaggi della pipeline è molto più costoso rispetto a una chiamata di funzione. La domanda che è necessario porre non è così tanto che le funzioni e i dati sono concettualmente diversi da quelli in questa altra sezione? ma "Voglio che queste funzioni e i dati si evolvono separatamente?" o "questo calcolo è costoso ed è possibile riutilizzarne l'output?" Per altre informazioni, vedere thisn'tebook [come creare un modulo, ModuleVersion e usarle in una pipeline con ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Come illustrato in precedenza, la separazione della preparazione dei dati dal training è spesso un'opportunità di questo tipo. Talvolta la preparazione dei dati è complessa e richiede molto tempo che è possibile suddividere il processo in passaggi di pipeline distinti. Altre opportunità includono test e analisi post-training. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Come è possibile velocizzare l'iterazione della pipeline? 

Le tecniche comuni per l'iterazione rapida delle pipeline includono: 

- Clonazione rapida della pipeline (creazione di una copia) e riesecuzione rapida della pipeline
- Mantenere l'istanza di calcolo in esecuzione, in modo da evitare tempi di avvio
- La configurazione dei dati e dei passaggi per consentire il riutilizzo consente alla pipeline di ignorare il ricalcolo dei dati non modificabili

Quando si vuole eseguire rapidamente l'iterazione, è possibile clonare la pipeline, creare una pipeline ed eseguire di nuovo la pipeline. Un'altra tecnica utile è che se si mantiene il calcolo caldo, non si incorrerà sul costo della filatura del nuovo calcolo. Se si configura il passaggio per consentire il riutilizzo del risultato di un'esecuzione, l'esecuzione ripetuta riutilizzerà i risultati laddove possibile (in assenza di modifiche nei passaggi).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Come si collabora con le pipeline ML? 

Le pipeline separate sono linee naturali per suddividere il lavoro. Più sviluppatori o anche più team possono lavorare su passaggi diversi, purché i dati e gli argomenti che scorrono tra i passaggi siano concordati. 

Durante lo sviluppo attivo, è possibile `PipelineRun` recuperare `StepRun` ed eseguire i risultati dall'area di lavoro, usare questi oggetti per scaricare l'output finale e intermedio e usare tali artefatti per il proprio lavoro modulare.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Usare pipeline per testare le tecniche in isolamento

Le soluzioni ML reali comportano in genere una considerevole personalizzazione di ogni passaggio. Spesso i dati non elaborati devono essere preparati in qualche modo: filtrati, trasformati e potenziati. I processi di training possono avere diverse architetture potenziali e, per un apprendimento approfondito, molte possibili varianti per le dimensioni dei livelli e le funzioni di attivazione. Anche con un'architettura coerente, la ricerca di iperparametri può produrre vittorie significative.

Oltre a strumenti come [AutoML](concept-automated-ml.md) e la [ricerca automatizzata di iperparametri](how-to-tune-hyperparameters.md), le pipeline possono essere uno strumento importante per le soluzioni di test A/B. Se sono disponibili diverse varianti dei passaggi della pipeline, è facile generare esecuzioni separate, provando le varianti: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

