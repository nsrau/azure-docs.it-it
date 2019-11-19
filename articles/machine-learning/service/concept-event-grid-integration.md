---
title: USA eventi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Questo articolo illustra come usare griglia di eventi di Azure per sottoscrivere, reagire e annullare la sottoscrizione di eventi generati da Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: 2fe2d07b29b8799712d59cdf21aeb3ce989ca3b2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158469"
---
# <a name="consume-azure-machine-learning-events-preview"></a>USA eventi Azure Machine Learning (anteprima)

Azure Machine Learning gestisce l'intero ciclo di vita del processo di Machine Learning, tra cui il training del modello, la distribuzione del modello e il monitoraggio. Azure Machine Learning eventi consentono alle applicazioni di reagire agli eventi durante il ciclo di vita di Machine Learning, ad esempio il completamento delle esecuzioni di training, la registrazione e la distribuzione dei modelli e il rilevamento della deviazione dei dati, tramite l'uso di server moderni architetture. 

Questi eventi vengono pubblicati tramite [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/). Con portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure, i clienti possono facilmente sottoscrivere gli eventi [specificando uno o più tipi di evento e le condizioni di filtro](/azure/event-grid/event-filtering). I clienti hanno anche la possibilità di scegliere di creare un'ampia gamma di gestori di eventi, ad esempio funzioni di Azure, app per la logica di Azure o webhook generici. Azure Machine Learning, insieme a griglia di eventi di Azure, offre una piattaforma di recapito degli eventi a disponibilità elevata, affidabile e a tolleranza di errore per la creazione di applicazioni basate su eventi.

Per informazioni sull'uso di Azure Machine Learning con griglia di eventi, vedere [creare flussi di lavoro basati su eventi Machine Learning (anteprima)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>Modello di eventi 

Griglia di eventi di Azure legge gli eventi da origini, ad esempio Azure Machine Learning e altri servizi di Azure. Questi eventi vengono quindi inviati ai gestori eventi, ad esempio hub eventi di Azure, funzioni di Azure, app per la logica e altri. Il diagramma seguente illustra il modo in cui griglia di eventi connette le origini e i gestori, ma non è un elenco completo delle integrazioni supportate.

![Modello funzionale di Griglia di eventi di Azure](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Per ulteriori informazioni sulle origini eventi e i gestori eventi, vedere informazioni su [griglia](/azure/event-grid/overview)di eventi.

## <a name="azure-machine-learning-event-types"></a>Tipi di evento Azure Machine Learning

Azure Machine Learning fornisce eventi nei vari punti del ciclo di vita di Machine Learning: 

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Generato quando viene completata l'esecuzione di un esperimento di Machine Learning |
| `Microsoft.MachineLearningServices.ModelRegistered` | Generato quando un modello di apprendimento automatico viene registrato nell'area di lavoro |
| `Microsoft.MachineLearningServices.ModelDeployed` | Generato quando viene completata una distribuzione di un servizio di inferenza con uno o più modelli |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Generato quando viene completato un processo di rilevamento della deriva dei dati per due set di dati |

## <a name="subscribe-to-machine-learning-events"></a>Sottoscrivere eventi di Machine Learning

Le sottoscrizioni per gli eventi Azure Machine Learning sono protette dal controllo degli accessi in base al ruolo (RBAC). Solo il [collaboratore o il proprietario](how-to-assign-roles.md#default-roles) di un'area di lavoro può creare, aggiornare ed eliminare sottoscrizioni di eventi.

È possibile filtrare le sottoscrizioni di eventi in base a una serie di condizioni. È possibile applicare filtri alle sottoscrizioni di eventi durante la [creazione](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) della sottoscrizione stessa o [in un secondo momento](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). 

### <a name="filter-by-event-type"></a>Filtrare in base al tipo di evento
Una sottoscrizione di eventi può specificare uno o più Azure Machine Learning tipi di evento.

### <a name="filter-by-event-subject"></a>Filtra per oggetto evento
Griglia di eventi di Azure supporta i filtri oggetto basati su __inizia con__ e __termina con__ corrispondenze, in modo che gli eventi con un oggetto corrispondente vengano recapitati al Sottoscrittore. Diversi eventi di Machine Learning hanno un formato soggetto diverso.

| Tipo evento | Formato oggetto | Oggetto di esempio |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |

### <a name="advanced-filtering"></a>Filtro avanzato

Griglia di eventi di Azure supporta anche l'applicazione di filtri avanzati in base allo schema di eventi pubblicati. Azure Machine Learning dettagli dello schema di evento sono reperibili nello [schema di eventi di griglia di eventi di Azure per Azure Machine Learning](../../event-grid/event-schema-machine-learning.md).

Alcuni esempi di filtri avanzati che è possibile eseguire includono:

* Per `Microsoft.MachineLearningServices.ModelRegistered` evento, per filtrare il valore del tag del modello:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Per altre informazioni su come applicare i filtri, vedere [filtrare gli eventi per griglia di eventi](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>USA eventi Machine Learning

Le applicazioni che gestiscono gli eventi di Machine Learning devono seguire alcune procedure consigliate:

> [!div class="checklist"]
> * Poiché è possibile configurare più sottoscrizioni per indirizzare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi provengano da un'origine specifica, ma per controllare l'argomento del messaggio per assicurarsi che provenga dall'area di lavoro di Machine Learning prevista.
> * Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
> * Dal momento che i messaggi non arrivano in ordine e giungono con un po' di ritardo, usare i campi etag per verificare se le informazioni disponibili sugli oggetti sono ancora aggiornate.  È possibile usare anche i campi del sequencer per comprendere l'ordine degli eventi relativi a un oggetto specifico.
> * Ignorare i campi che non si conoscono. Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
> * Operazione non riuscita o annullata Azure Machine Learning non attiverà alcun evento. Se, ad esempio, la distribuzione di un modello ha esito negativo, Microsoft. MachineLearningServices. ModelDeployed non verrà attivato. Prendere in considerazione tale modalità di errore durante la progettazione delle applicazioni. È sempre possibile usare Azure Machine Learning SDK, l'interfaccia della riga di comando o il portale per verificare lo stato di un'operazione e comprendere i motivi di errore dettagliati.

Griglia di eventi di Azure consente ai clienti di creare gestori di messaggi disaccoppiati, che possono essere attivati da eventi Azure Machine Learning. Di seguito sono riportati alcuni esempi significativi di gestori di messaggi:
* Funzioni di Azure
* App per la logica di Azure
* Hub eventi di Azure
* Pipeline Azure Data Factory
* Webhook generici, che possono essere ospitati nella piattaforma Azure o altrove

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su griglia di eventi e concedi agli eventi Azure Machine Learning un tentativo:

- [Informazioni sulla griglia di eventi](../../event-grid/overview.md)
- [Schema di eventi di griglia di eventi di Azure per Azure Machine Learning](../../event-grid/event-schema-machine-learning.md)
- [Creare flussi di lavoro basati su eventi con Azure Machine Learning](how-to-use-event-grid.md)
