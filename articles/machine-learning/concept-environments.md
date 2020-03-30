---
title: Informazioni sugli ambienti Di Azure Machine Learning
titleSuffix: Azure Machine Learning
description: In questo articolo vengono appesi i vantaggi degli ambienti di apprendimento automatico, che consentono definizioni delle dipendenze di Machine Learning riproducibili, controllabili e portatili in un'ampia gamma di destinazioni di calcolo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 03/18/2020
ms.openlocfilehash: 50ddbffd00e0cbbd0641089613aaa40d03658c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064200"
---
# <a name="what-are-azure-machine-learning-environments"></a>Che cosa sono gli ambienti di Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Gli ambienti Di Azure Machine Learning specificano i pacchetti Python, le variabili di ambiente e le impostazioni software relative agli script di training e punteggio. Specificano anche i tempi di esecuzione (Python, Spark o Docker). Gli ambienti sono entità gestite e con controllo delle versioni all'interno dell'area di lavoro di Machine Learning che consentono flussi di lavoro di Machine Learning riproducibili, controllabili e portatili in un'ampia gamma di destinazioni di calcolo.

È possibile `Environment` utilizzare un oggetto sul calcolo locale per:You can use an object on your local compute to:
* Sviluppare il copione di allenamento.
* Riutilizzare lo stesso ambiente in Azure Machine Learning Compute per il training dei modelli su larga scala.
* Distribuire il modello con lo stesso ambiente.

Il diagramma seguente illustra come usare `Environment` un singolo oggetto sia nella configurazione di esecuzione, per il training e la configurazione di inferenza e distribuzione, per le distribuzioni di servizi Web.

![Diagramma di un ambiente nel flusso di lavoro di apprendimento automatico](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Tipi di ambienti

Gli ambienti possono essere suddivisi in tre categorie: *curated*, *user-managed*e *system-managed*.

Gli ambienti curati sono forniti da Azure Machine Learning e sono disponibili nell'area di lavoro per impostazione predefinita. Contengono raccolte di pacchetti Python e impostazioni che consentono di iniziare a utilizzare vari framework di apprendimento automatico. 

Negli ambienti gestiti dagli utenti, l'utente è responsabile della configurazione dell'ambiente e dell'installazione di ogni pacchetto necessario per lo script di training nella destinazione di calcolo. Conda non controlla il tuo ambiente o non installa nulla per te. Se si definisce il proprio ambiente, è necessario elencare `azureml-defaults` con la versione `>= 1.0.45` come dipendenza pip. Questo pacchetto contiene la funzionalità necessaria per ospitare il modello come servizio Web.This package contains the functionality that's needed to host the model as a web service.

Si usano ambienti gestiti dal sistema quando si desidera che [Conda](https://conda.io/docs/) gestisca l'ambiente Python e le dipendenze di script per l'utente. Il servizio presuppone questo tipo di ambiente per impostazione predefinita, per la sua utilità sulle destinazioni di calcolo remote che non sono configurabili manualmente.

## <a name="create-and-manage-environments"></a>Creare e gestire ambienti

È possibile creare ambienti:You can create environments by:

* Definizione `Environment` di nuovi oggetti, utilizzando un ambiente curato o definendo le proprie dipendenze.
* Utilizzo `Environment` di oggetti esistenti dal workspace. Questo approccio consente la coerenza e la riproducibilità con le dipendenze.
* Importazione da una definizione di ambiente Anaconda esistente.
* Uso della riga di comando di Azure Machine LearningUsing the Azure Machine Learning CLI

Per esempi di codice specifici, vedere la sezione "Creare un ambiente" di Ambienti di [riutilizzo per la formazione e](how-to-use-environments.md#create-an-environment)la distribuzione . Gli ambienti sono anche facilmente gestiti attraverso l'area di lavoro. Includono le seguenti funzionalità:

* Gli ambienti vengono registrati automaticamente nell'area di lavoro quando si invia un esperimento. Possono anche essere registrati manualmente.
* È possibile recuperare gli ambienti dall'area di lavoro da utilizzare per la formazione o la distribuzione o per apportare modifiche alla definizione dell'ambiente.
* Con il controllo delle versioni, è possibile visualizzare le modifiche apportate agli ambienti nel tempo, in modo da garantire la riproducibilità.
* È possibile creare automaticamente immagini Docker dagli ambienti.

Per esempi di codice, vedere la sezione "Gestione degli ambienti" in Riutilizzo degli ambienti per la formazione e la [distribuzione.](how-to-use-environments.md#manage-environments)

## <a name="environment-building-caching-and-reuse"></a>Creazione, memorizzazione nella cache e riutilizzo dell'ambiente

Il servizio Azure Machine Learning crea definizioni di ambiente in immagini Docker e ambienti conda. Memorizza inoltre nella cache gli ambienti in modo che possano essere riutilizzati nelle esecuzioni di training successive e nelle distribuzioni di endpoint del servizio.

### <a name="building-environments-as-docker-images"></a>Creazione di ambienti come immagini Docker

In genere, quando si invia per la prima volta un'esecuzione usando un ambiente, il servizio Azure Machine Learning richiama un'attività di compilazione [ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) nel Registro di sistema contenitore di Azure (ACR) associata all'area di lavoro. L'immagine Docker compilata viene quindi memorizzata nella cache dell'aCR area di lavoro. All'inizio dell'esecuzione, l'immagine viene recuperata dalla destinazione di calcolo.

La compilazione dell'immagine è costituita da due passaggi:The image build consists of two steps:

 1. Download di un'immagine di base ed esecuzione di qualsiasi passaggio di Docker
 2. Creazione di un ambiente conda in base alle dipendenze di conda specificate nella definizione dell'ambiente.

Il secondo passaggio viene omesso se si specificano [dipendenze gestite dall'utente.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) In questo caso sei responsabile dell'installazione di qualsiasi pacchetto Python, includendoli nell'immagine di base o specificando passaggi Docker personalizzati entro il primo passaggio. Sei anche responsabile di specificare il percorso corretto per l'eseguibile Python.

### <a name="image-caching-and-reuse"></a>Memorizzazione nella cache e riutilizzo delle immagini

Se si usa la stessa definizione di ambiente per un'altra esecuzione, il servizio Azure Machine Learning riutilizza l'immagine memorizzata nella cache dal controllo di controllo utente dell'area di lavoro. 

Per visualizzare i dettagli di un'immagine memorizzata nella cache, utilizzare il metodo [Environment.get_image_details.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-)

Per determinare se riutilizzare un'immagine memorizzata nella cache o crearne una nuova, il servizio calcola [un valore hash](https://en.wikipedia.org/wiki/Hash_table) dalla definizione dell'ambiente e lo confronta con gli hash degli ambienti esistenti. L'hash è basato su:
 
 * Valore della proprietà dell'immagine di base
 * Valore della proprietà Passaggi docker personalizzati
 * Elenco dei pacchetti Python nella definizione di Conda
 * Elenco dei pacchetti nella definizione Spark 

L'hash non dipende dal nome o dalla versione dell'ambiente. Le modifiche alla definizione dell'ambiente, ad esempio l'aggiunta o la rimozione di un pacchetto Python o la modifica della versione del pacchetto, determinano la modifica del valore hash e attivano la ricompilazione di un'immagine. Tuttavia, se è sufficiente rinominare l'ambiente o creare un nuovo ambiente con le proprietà esatte e pacchetti di uno esistente, quindi il valore hash rimane lo stesso e viene utilizzata l'immagine memorizzata nella cache.

Vedere il diagramma seguente che mostra tre definizioni di ambiente. Due di loro hanno nome e versione diversi, ma l'immagine di base identica e i pacchetti Python. Hanno lo stesso hash e quindi corrispondono alla stessa immagine memorizzata nella cache. Il terzo ambiente ha diversi pacchetti e versioni Python e pertanto corrisponde a un'immagine memorizzata nella cache diversa.

![Diagramma della memorizzazione nella cache dell'ambiente come immagini Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Se si crea un ambiente con una dipendenza ```numpy```del pacchetto non bloccata, ad esempio , tale ambiente continuerà a utilizzare la versione del pacchetto installata _al momento della creazione dell'ambiente._ Inoltre, qualsiasi ambiente futuro con definizione corrispondente continuerà a utilizzare la versione precedente. 

Per aggiornare il pacchetto, specificare un numero ```numpy==1.18.1```di versione per forzare la ricostruzione dell'immagine, ad esempio . Si noti che verranno installate nuove dipendenze, incluse quelle annidate che potrebbero interrompere uno scenario funzionante in precedenza.

> [!WARNING]
>  Il [metodo Environment.build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) ricompila l'immagine memorizzata nella cache, con il possibile effetto collaterale dell'aggiornamento dei pacchetti non bloccati e l'interruzione della riproducibilità per tutte le definizioni di ambiente corrispondenti a tale immagine memorizzata nella cache.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come creare e usare ambienti in Azure Machine Learning.Learn how to [create and use environments](how-to-use-environments.md) in Azure Machine Learning.
* Vedere la documentazione di riferimento di Python SDK per la [classe di ambiente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Vedere la documentazione di riferimento di R SDK per [gli ambienti](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
