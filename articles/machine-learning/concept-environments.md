---
title: Informazioni sugli ambienti Azure Machine Learning
titleSuffix: Azure Machine Learning
description: In questo articolo vengono illustrati i vantaggi degli ambienti di Machine Learning, che consentono le definizioni delle dipendenze riproducibili, controllabili e portatili di Machine Learning in un'ampia gamma di destinazioni di calcolo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: 8906299cc9e2c000dab2ac9d2a345d9aaf238260
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045852"
---
# <a name="what-are-azure-machine-learning-environments"></a>Che cosa sono gli ambienti Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Gli ambienti Azure Machine Learning specificano i pacchetti Python, le variabili di ambiente e le impostazioni software per gli script di training e di assegnazione dei punteggi. Specificano anche i tempi di esecuzione (Python, Spark o Docker). Sono entità gestite e con controllo delle versioni all'interno dell'area di lavoro Machine Learning che consentono flussi di lavoro riproducibili, controllabili e portabili di apprendimento automatico in un'ampia gamma di destinazioni di calcolo.

È possibile usare un oggetto `Environment` nel calcolo locale per:
* Sviluppare uno script di training.
* Riutilizzare lo stesso ambiente in Azure Machine Learning calcolo per il training del modello su larga scala.
* Distribuire il modello con lo stesso ambiente.

Il diagramma seguente illustra come è possibile usare un singolo oggetto `Environment` sia nella configurazione di esecuzione, per il training, sia nella configurazione dell'inferenza e della distribuzione per le distribuzioni di servizi Web.

![Diagramma di un ambiente nel flusso di lavoro di Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipi di ambienti

Gli ambienti possono essere suddivisi in tre categorie: *curato*, *gestito dall'utente*e gestito dal *sistema*.

Per impostazione predefinita, gli ambienti curati sono forniti da Azure Machine Learning e sono disponibili nell'area di lavoro. Contengono raccolte di pacchetti e impostazioni Python che consentono di iniziare a usare diversi framework di machine learning. 

Negli ambienti gestiti dagli utenti, l'utente è responsabile della configurazione dell'ambiente e dell'installazione di tutti i pacchetti necessari allo script di training nella destinazione di calcolo. Conda non controlla l'ambiente o non installa alcun elemento. Se si sta definendo un ambiente personalizzato, è necessario elencare `azureml-defaults` con la versione `>= 1.0.45` come dipendenza PIP. Questo pacchetto contiene la funzionalità necessaria per ospitare il modello come servizio Web.

Usare ambienti gestiti dal sistema quando si vuole che [conda](https://conda.io/docs/) gestisca l'ambiente Python e le dipendenze di script. Per impostazione predefinita, il servizio presuppone questo tipo di ambiente, a causa dell'utilità per le destinazioni di calcolo remote che non possono essere configurate manualmente.

## <a name="create-and-manage-environments"></a>Creare e gestire gli ambienti

Per creare gli ambienti, è possibile:

* Definizione di nuovi oggetti di `Environment`, usando un ambiente curato o definendo dipendenze personalizzate.
* Uso di oggetti `Environment` esistenti dall'area di lavoro. Questo approccio consente la coerenza e la riproducibilità con le dipendenze.
* Importazione da una definizione di ambiente Anaconda esistente.
* Uso dell'interfaccia della riga di comando Azure Machine Learning

Per esempi di codice specifici, vedere la sezione "creare un ambiente" di [riutilizzo degli ambienti per il training e la distribuzione](how-to-use-environments.md#create-an-environment). Gli ambienti sono anche facilmente gestibili tramite l'area di lavoro. Sono incluse le funzionalità seguenti:

* Gli ambienti vengono registrati automaticamente nell'area di lavoro quando si invia un esperimento. Possono anche essere registrati manualmente.
* È possibile recuperare gli ambienti dall'area di lavoro da usare per il training o la distribuzione oppure per apportare modifiche alla definizione dell'ambiente.
* Con il controllo delle versioni, è possibile visualizzare le modifiche apportate agli ambienti nel tempo, garantendo la riproducibilità.
* È possibile creare automaticamente immagini Docker dagli ambienti.

Per esempi di codice, vedere la sezione "gestire gli ambienti" di [riutilizzo degli ambienti per il training e la distribuzione](how-to-use-environments.md#manage-environments).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare e usare gli ambienti](how-to-use-environments.md) in Azure Machine Learning.
* Vedere la documentazione di riferimento di Python SDK per la [classe Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Vedere la documentazione di riferimento di R SDK per gli [ambienti](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
