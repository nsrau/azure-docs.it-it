---
title: Che cosa sono gli ambienti ML
titleSuffix: Azure Machine Learning
description: In questo articolo vengono illustrati i vantaggi degli ambienti di Machine Learning, che consentono le definizioni di dipendenza riproducibili, controllabili e portatili di Machine Learning tra destinazioni di calcolo diverse.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: ad520c7e6503f28de0bd5538662c223575a078fa
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692732"
---
# <a name="what-are-azure-machine-learning-environments"></a>Che cosa sono gli ambienti Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Gli ambienti specificano i pacchetti Python, le variabili di ambiente e le impostazioni software per gli script di training e di assegnazione dei punteggi e i tempi di esecuzione (Python, Spark o Docker). Sono entità gestite e con versione all'interno dell'area di lavoro Azure Machine Learning che consentono flussi di lavoro riproducibili, controllabili e portatili di Machine Learning tra destinazioni di calcolo diverse.

È possibile usare un oggetto ambiente nel calcolo locale per sviluppare lo script di training, riutilizzare lo stesso ambiente in Azure Machine Learning calcolo per il training dei modelli su larga scala e persino distribuire il modello con lo stesso ambiente.

Nell'esempio seguente viene illustrato che lo stesso oggetto ambiente può essere utilizzato sia nella configurazione di esecuzione per il training sia nella configurazione dell'inferenza e della distribuzione per le distribuzioni di servizi Web.

![Diagramma dell'ambiente nel flusso di lavoro di Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipi di ambienti

Gli ambienti possono essere suddivisi in tre categorie: **curato**, **gestito dall'utente** e **gestito dal sistema**.

Per impostazione predefinita, gli ambienti curati sono forniti da Azure Machine Learning e sono disponibili nell'area di lavoro. Contengono raccolte di pacchetti e impostazioni Python che consentono di iniziare a usare diversi framework di machine learning. 

Per un ambiente gestito dall'utente è necessario configurare l'ambiente e installare ogni pacchetto richiesto dallo script di training nella destinazione di calcolo. Conda non controllerà l'ambiente e non istallerà alcun elemento. Si noti che se si definisce un ambiente personalizzato, è necessario elencare `azureml-defaults` con la versione `>= 1.0.45` come dipendenza PIP. Questo pacchetto contiene la funzionalità necessaria per ospitare il modello come servizio Web.

Gli ambienti gestiti dal sistema vengono usati quando si vuole [conda](https://conda.io/docs/) per gestire l'ambiente Python e le dipendenze di script. Per impostazione predefinita, il servizio presuppone questo tipo di ambiente, a causa dell'utilità delle destinazioni di calcolo remote che non possono essere configurate manualmente.

## <a name="creating-and-managing-environments"></a>Creazione e gestione di ambienti

Gli ambienti possono essere creati da:

* Definizione di nuovi oggetti di `Environment`, usando un ambiente curato o definendo dipendenze personalizzate
* Uso di oggetti `Environment` esistenti dall'area di lavoro. Questo consente la coerenza e la riproducibilità con le dipendenze
* Importazione da una definizione di ambiente Anaconda esistente.

Vedere le [procedure](how-to-use-environments.md#create-an-environment) per esempi di codice specifici. Gli ambienti sono anche facilmente gestibili tramite l'area di lavoro e includono le funzionalità seguenti:

* Gli ambienti vengono registrati automaticamente nell'area di lavoro quando si invia un esperimento. Possono anche essere registrati manualmente
* Recuperare gli ambienti dall'area di lavoro e usarli per il training, la distribuzione o apportare modifiche alla definizione dell'ambiente
* Il controllo delle versioni consente di visualizzare le modifiche apportate agli ambienti nel tempo e garantisce la riproducibilità
* Crea automaticamente immagini Docker dagli ambienti

Vedere la sezione [gestire gli ambienti](how-to-use-environments.md#manage-environments) degli esempi di codice.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare e usare gli ambienti](how-to-use-environments.md) in Azure Machine Learning
* Vedere la documentazione di riferimento di Python SDK per la [classe Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Vedere la documentazione di riferimento di R SDK per gli [ambienti](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).