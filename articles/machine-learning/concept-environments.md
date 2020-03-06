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
ms.openlocfilehash: 036efa27fb8d22c32f2f6bce1efe9dea300a3972
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302772"
---
# <a name="what-are-azure-machine-learning-environments"></a>Che cosa sono gli ambienti Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Gli ambienti Azure Machine Learning specificano i pacchetti Python, le variabili di ambiente e le impostazioni software per gli script di training e di assegnazione dei punteggi. Specificano anche i tempi di esecuzione (Python, Spark o Docker). Gli ambienti sono entità gestite e con versione all'interno dell'area di lavoro di Machine Learning che consentono flussi di lavoro riproducibili, controllabili e portabili di apprendimento automatico in un'ampia gamma di destinazioni di calcolo.

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

## <a name="environment-building-caching-and-reuse"></a>Compilazione, memorizzazione nella cache e riutilizzo dell'ambiente

Il servizio Azure Machine Learning crea definizioni di ambiente in immagini Docker e ambienti conda. Consente inoltre di memorizzare nella cache gli ambienti, in modo che possano essere riutilizzati in esecuzioni di training successive e distribuzioni di endpoint di servizio.

### <a name="building-environments-as-docker-images"></a>Compilazione di ambienti come immagini Docker

In genere, quando si invia un'esecuzione usando un ambiente, il servizio Azure Machine Learning richiama un' [attività di compilazione ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) nel container Registry di Azure (ACR) associata all'area di lavoro. L'immagine Docker compilata viene quindi memorizzata nella cache nell'area di lavoro di ACR. All'inizio dell'esecuzione dell'esecuzione, l'immagine viene recuperata dalla destinazione di calcolo.

La compilazione dell'immagine è costituita da due passaggi:

 1. Download di un'immagine di base ed esecuzione di qualsiasi passaggio di Docker
 2. Compilazione di un ambiente conda in base alle dipendenze conda specificate nella definizione dell'ambiente.

Il secondo passaggio viene omesso se si specificano le [dipendenze gestite dall'utente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). In questo caso si è responsabili dell'installazione di qualsiasi pacchetto python, inserendoli nell'immagine di base o specificando passaggi Docker personalizzati nel primo passaggio. Si è anche responsabili di specificare la posizione corretta per l'eseguibile di Python.

### <a name="image-caching-and-reuse"></a>Memorizzazione nella cache e riutilizzo delle immagini

Se si usa la stessa definizione di ambiente per un'altra esecuzione, il servizio Azure Machine Learning riutilizza l'immagine memorizzata nella cache dall'area di lavoro ACR. 

Per visualizzare i dettagli di un'immagine memorizzata nella cache, usare il metodo [Environment. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) .

Per determinare se riutilizzare un'immagine memorizzata nella cache o crearne una nuova, il servizio calcola [un valore hash](https://en.wikipedia.org/wiki/Hash_table) dalla definizione dell'ambiente e lo confronta con gli hash degli ambienti esistenti. L'hash è basato su:
 
 * Valore proprietà immagine di base
 * Valore della proprietà passaggi Docker personalizzati
 * Elenco di pacchetti Python nella definizione conda
 * Elenco di pacchetti nella definizione di Spark 

L'hash non dipende dal nome dell'ambiente o dalla versione. Le modifiche alla definizione dell'ambiente, ad esempio l'aggiunta o la rimozione di un pacchetto python o la modifica della versione del pacchetto, determinano la modifica del valore hash e attivano la ricompilazione dell'immagine. Tuttavia, se si rinomina semplicemente l'ambiente o si crea un nuovo ambiente con le proprietà esatte e i pacchetti di uno esistente, il valore hash rimane invariato e viene utilizzata l'immagine memorizzata nella cache.

Vedere il diagramma seguente che mostra tre definizioni di ambiente. Due di esse hanno un nome e una versione diversi, ma un'immagine di base identica e pacchetti Python. Hanno lo stesso hash e quindi corrispondono alla stessa immagine memorizzata nella cache. Il terzo ambiente ha diversi pacchetti e versioni di Python e pertanto corrisponde a un'immagine memorizzata nella cache diversa.

![Diagramma della memorizzazione nella cache dell'ambiente come immagini Docker](./media/concept-environments/environment-caching.png)

Se si crea un ambiente con una dipendenza del pacchetto sbloccata, ad esempio ```numpy```, tale ambiente continuerà a utilizzare la versione del pacchetto installata al momento della creazione dell'ambiente. Inoltre, qualsiasi ambiente futuro con definizione di corrispondenza continuerà a utilizzare la versione precedente. Per aggiornare il pacchetto, specificare un numero di versione per forzare la ricompilazione dell'immagine, ad esempio ```numpy==1.18.1```. Si noti che verranno installate nuove dipendenze, incluse quelle nidificate, che potrebbero interrompere uno scenario di lavoro precedente

> [!WARNING]
>  Il metodo [Environment. Build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace-) ricreerà l'immagine memorizzata nella cache, con un possibile effetto collaterale dell'aggiornamento dei pacchetti sbloccati e la riproducibilità di rilievo per tutte le definizioni di ambiente corrispondenti a tale immagine memorizzata nella cache.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare e usare gli ambienti](how-to-use-environments.md) in Azure Machine Learning.
* Vedere la documentazione di riferimento di Python SDK per la [classe Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Vedere la documentazione di riferimento di R SDK per gli [ambienti](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
