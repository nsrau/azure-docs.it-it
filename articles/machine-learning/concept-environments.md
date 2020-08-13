---
title: Informazioni sugli ambienti Azure Machine Learning
titleSuffix: Azure Machine Learning
description: In questo articolo vengono illustrati i vantaggi degli ambienti di Machine Learning, che consentono le definizioni delle dipendenze riproducibili, controllabili e portatili di Machine Learning in un'ampia gamma di destinazioni di calcolo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 07/08/2020
ms.openlocfilehash: cc4c39cf26f3ab8d1037222f967789bfbeca05ba
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166774"
---
# <a name="what-are-azure-machine-learning-environments"></a>Che cosa sono gli ambienti Azure Machine Learning?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning ambienti sono incapsulati nell'ambiente in cui si verifica il training di machine learning. Specificano i pacchetti Python, le variabili di ambiente e le impostazioni software per gli script di training e di assegnazione dei punteggi. Specificano anche i tempi di esecuzione (Python, Spark o Docker). Gli ambienti sono entità gestite e con versione all'interno dell'area di lavoro di Machine Learning che consentono flussi di lavoro riproducibili, controllabili e portabili di apprendimento automatico in un'ampia gamma di destinazioni di calcolo.

È possibile usare un `Environment` oggetto nel calcolo locale per:
* Sviluppare lo script di training.
* Riutilizzare lo stesso ambiente nell'ambiente di calcolo di Machine Learning per il training dei modelli su larga scala.
* Distribuire il modello con lo stesso ambiente.
* Rivedere l'ambiente in cui è stato eseguito il training di un modello esistente.

Il diagramma seguente illustra come è possibile usare un singolo `Environment` oggetto sia nella configurazione di esecuzione (per il training) che nella configurazione dell'inferenza e della distribuzione (per le distribuzioni di servizi Web).

![Diagramma di un ambiente nel flusso di lavoro di Machine Learning](./media/concept-environments/ml-environment.png)

L'ambiente, la destinazione di calcolo e lo script di training formano la configurazione di esecuzione: la specifica completa di un'esecuzione di training.

## <a name="types-of-environments"></a>Tipi di ambienti

Gli ambienti possono essere suddivisi in tre categorie: *curato*, *gestito dall'utente*e gestito dal *sistema*.

Per impostazione predefinita, gli ambienti curati sono forniti da Azure Machine Learning e sono disponibili nell'area di lavoro. Da usare così come sono, contengono raccolte di pacchetti e impostazioni Python che consentono di iniziare a usare diversi framework di machine learning. Questi ambienti creati in precedenza consentono inoltre tempi di distribuzione più rapidi. Per un elenco completo, vedere l' [articolo ambienti curati](resource-curated-environments.md).

Negli ambienti gestiti dagli utenti, l'utente è responsabile della configurazione dell'ambiente e dell'installazione di tutti i pacchetti necessari allo script di training nella destinazione di calcolo. Conda non controlla l'ambiente o non installa alcun elemento. Se si sta definendo un ambiente personalizzato, è necessario elencare `azureml-defaults` con la versione `>= 1.0.45` come dipendenza PIP. Questo pacchetto contiene la funzionalità necessaria per ospitare il modello come servizio Web.

Usare ambienti gestiti dal sistema quando si vuole che [conda](https://conda.io/docs/) gestisca l'ambiente Python e le dipendenze di script. Un nuovo ambiente conda viene compilato in base all'oggetto dipendenze conda. Per impostazione predefinita, il servizio Azure Machine Learning presuppone questo tipo di ambiente, a causa dell'utilità delle destinazioni di calcolo remote che non sono configurabili manualmente.

## <a name="create-and-manage-environments"></a>Creare e gestire gli ambienti

Per creare gli ambienti, è possibile:

* Definizione `Environment` di nuovi oggetti usando un ambiente curato o definendo dipendenze personalizzate.
* Utilizzo `Environment` di oggetti esistenti dall'area di lavoro. Questo approccio consente la coerenza e la riproducibilità con le dipendenze.
* Importazione da una definizione di ambiente Anaconda esistente.
* Uso dell'interfaccia della riga di comando Azure Machine Learning
* [Utilizzo dell'estensione VS Code](how-to-manage-resources-vscode.md#create-environment)

Per esempi di codice specifici, vedere la sezione "creare un ambiente" di [come usare gli ambienti](how-to-use-environments.md#create-an-environment). Gli ambienti sono anche facilmente gestibili tramite l'area di lavoro. Sono incluse le funzionalità seguenti:

* Gli ambienti vengono registrati automaticamente nell'area di lavoro quando si invia un esperimento. Possono anche essere registrati manualmente.
* È possibile recuperare gli ambienti dall'area di lavoro da usare per il training o la distribuzione oppure per apportare modifiche alla definizione dell'ambiente.
* Con il controllo delle versioni, è possibile visualizzare le modifiche apportate agli ambienti nel tempo, garantendo la riproducibilità.
* È possibile creare automaticamente immagini Docker dagli ambienti.

Per esempi di codice, vedere la sezione "gestire gli ambienti" di [come usare gli ambienti](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Compilazione, memorizzazione nella cache e riutilizzo dell'ambiente

Il servizio Azure Machine Learning crea definizioni di ambiente in immagini Docker e ambienti conda. Consente inoltre di memorizzare nella cache gli ambienti, in modo che possano essere riutilizzati in esecuzioni di training successive e distribuzioni di endpoint di servizio. Per eseguire uno script di training in modalità remota, è necessario creare un'immagine Docker, mentre un'esecuzione locale può usare direttamente un ambiente conda. 

### <a name="submitting-a-run-using-an-environment"></a>Invio di un'esecuzione utilizzando un ambiente

Quando si invia per la prima volta un'esecuzione remota usando un ambiente, il servizio Azure Machine Learning richiama un' [attività di compilazione ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) nel container Registry di Azure (ACR) associata all'area di lavoro. L'immagine Docker compilata viene quindi memorizzata nella cache nell'area di lavoro di ACR. Gli ambienti curati sono supportati da immagini Docker memorizzate nella cache nell'ACR globale. All'inizio dell'esecuzione dell'esecuzione, l'immagine viene recuperata dalla destinazione di calcolo dall'ACR pertinente.

Per le esecuzioni locali, viene creato un ambiente Docker o conda in base alla definizione dell'ambiente. Gli script vengono quindi eseguiti nel computer di destinazione: un ambiente di runtime locale o un motore Docker locale.

### <a name="building-environments-as-docker-images"></a>Compilazione di ambienti come immagini Docker

Se la definizione dell'ambiente non esiste già nell'area di lavoro di ACR, verrà compilata una nuova immagine. La compilazione dell'immagine è costituita da due passaggi:

 1. Download di un'immagine di base ed esecuzione di qualsiasi passaggio di Docker
 2. Compilazione di un ambiente conda in base alle dipendenze conda specificate nella definizione dell'ambiente.

Il secondo passaggio viene omesso se si specificano le [dipendenze gestite dall'utente](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py). In questo caso si è responsabili dell'installazione di qualsiasi pacchetto python, inserendoli nell'immagine di base o specificando passaggi Docker personalizzati nel primo passaggio. Si è anche responsabili di specificare la posizione corretta per l'eseguibile di Python. È anche possibile usare un'immagine di [base Docker personalizzata](how-to-deploy-custom-docker-image.md).

### <a name="image-caching-and-reuse"></a>Memorizzazione nella cache e riutilizzo delle immagini

Se si usa la stessa definizione di ambiente per un'altra esecuzione, il servizio Azure Machine Learning riutilizza l'immagine memorizzata nella cache dall'area di lavoro ACR. 

Per visualizzare i dettagli di un'immagine memorizzata nella cache, usare il metodo [Environment. get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) .

Per determinare se riutilizzare un'immagine memorizzata nella cache o crearne una nuova, il servizio calcola [un valore hash](https://en.wikipedia.org/wiki/Hash_table) dalla definizione dell'ambiente e lo confronta con gli hash degli ambienti esistenti. L'hash è basato su:
 
 * Valore proprietà immagine di base
 * Valore della proprietà passaggi Docker personalizzati
 * Elenco di pacchetti Python nella definizione conda
 * Elenco di pacchetti nella definizione di Spark 

L'hash non dipende dal nome dell'ambiente o dalla versione. Se si rinomina l'ambiente o si crea un nuovo ambiente con le proprietà esatte e i pacchetti di uno esistente, il valore hash rimarrà invariato. Tuttavia, le modifiche alla definizione dell'ambiente, ad esempio l'aggiunta o la rimozione di un pacchetto python o la modifica della versione del pacchetto, determinano la modifica del valore hash. La modifica dell'ordine delle dipendenze o dei canali in un ambiente determinerà un nuovo ambiente e quindi richiederà una nuova compilazione dell'immagine. È importante notare che qualsiasi modifica apportata a un ambiente curato invalida l'hash e genera un nuovo ambiente "non curato".

Il valore hash calcolato viene confrontato con quelli presenti nell'area di lavoro e nel registro di ACR globale (o nella destinazione di calcolo per le esecuzioni locali). Se viene rilevata una corrispondenza, viene eseguito il pull dell'immagine memorizzata nella cache; in caso contrario, viene attivata una compilazione dell'immagine. La durata per eseguire il pull di un'immagine memorizzata nella cache include il tempo di download mentre la durata per il pull di un'immagine appena compilata include sia l'ora di compilazione che l'ora di download. 

Il diagramma seguente mostra tre definizioni di ambiente. Due di esse hanno nomi e versioni diversi, ma identici pacchetti di immagini di base e Python. Ma hanno lo stesso hash e quindi corrispondono alla stessa immagine memorizzata nella cache. Il terzo ambiente ha diversi pacchetti e versioni di Python e pertanto corrisponde a un'immagine memorizzata nella cache diversa.

![Diagramma della memorizzazione nella cache dell'ambiente come immagini Docker](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Se si crea un ambiente con una dipendenza del pacchetto sbloccata, ad esempio ```numpy``` , tale ambiente continuerà a utilizzare la versione del pacchetto installata _al momento della creazione dell'ambiente_. Inoltre, qualsiasi ambiente futuro con definizione di corrispondenza continuerà a utilizzare la versione precedente. 

Per aggiornare il pacchetto, specificare un numero di versione per forzare la ricompilazione dell'immagine, ad esempio ```numpy==1.18.1``` . Verranno installate nuove dipendenze, incluse quelle nidificate, che potrebbero interrompere uno scenario di lavoro precedente. 

> [!WARNING]
>  Il metodo [Environment. Build](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) ricreerà l'immagine memorizzata nella cache, con un possibile effetto collaterale dell'aggiornamento dei pacchetti sbloccati e la riproducibilità di rilievo per tutte le definizioni di ambiente corrispondenti a tale immagine memorizzata nella cache.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare e usare gli ambienti](how-to-use-environments.md) in Azure Machine Learning.
* Vedere la documentazione di riferimento di Python SDK per la [classe Environment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Vedere la documentazione di riferimento di R SDK per gli [ambienti](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).
