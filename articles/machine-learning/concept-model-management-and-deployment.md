---
title: 'Gestione modelli MLOps: ML'
titleSuffix: Azure Machine Learning
description: 'Informazioni sulla gestione dei modelli con Azure Machine Learning (MLOps). Consente di distribuire, gestire e monitorare i modelli per migliorarli continuamente. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: d1d14fa9730e3ddd47378a45ff7a1442bdee69ac
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543385"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: gestione dei modelli, distribuzione e monitoraggio con Azure Machine Learning

Questo articolo illustra come usare Azure Machine Learning per gestire il ciclo di vita dei modelli. Azure Machine Learning usa un approccio per le operazioni di Machine Learning (MLOps). MLOps migliora la qualità e la coerenza delle soluzioni di machine learning. 

## <a name="what-is-mlops"></a>Che cos'è MLOps?

Machine Learning Operations (MLOps) si basa sui principi [DevOps](https://azure.microsoft.com/overview/what-is-devops/) e sulle procedure che aumentano l'efficienza dei flussi di lavoro. Ad esempio, integrazione continua, recapito e distribuzione. MLOps applica questi principi al processo di Machine Learning, con l'obiettivo di:

* Sperimentazione e sviluppo più veloci dei modelli
* Distribuzione più rapida dei modelli nell'ambiente di produzione
* Controllo di qualità

Azure Machine Learning offre le funzionalità di MLOps seguenti:

- **Creare pipeline di ml riproducibili**. Machine Learning pipeline consentono di definire passaggi ripetibili e riutilizzabili per la preparazione dei dati, il training e l'assegnazione di punteggi ai processi.
- **Creare ambienti software riutilizzabili** per il training e la distribuzione dei modelli.
- **Registra, assembla e Distribuisci i modelli ovunque ti trovi**. È inoltre possibile tenere traccia dei metadati associati richiesti per l'utilizzo del modello.
- **Acquisire i dati di governance per il ciclo di vita di Machine Learning end-to-end**. Le informazioni registrate possono includere chi sta pubblicando modelli, perché sono state apportate modifiche e quando i modelli sono stati distribuiti o utilizzati nell'ambiente di produzione.
- **Inviare notifiche e avvisi sugli eventi nel ciclo di vita di ml**. Ad esempio, il completamento di un esperimento, la registrazione del modello, la distribuzione del modello e il rilevamento della tendenza dei dati.
- **Monitorare le applicazioni ml per i problemi operativi e ml correlati**. Confrontare gli input del modello tra il training e l'inferenza, esplorare le metriche specifiche del modello e fornire il monitoraggio e gli avvisi nell'infrastruttura ML.
- **Automatizzare il ciclo di vita end-to-end per ml con Azure Machine Learning e Azure Pipelines**. L'uso di pipeline consente di aggiornare i modelli di frequente, testare nuovi modelli e implementare continuamente nuovi modelli ML insieme ad altre applicazioni e servizi.

## <a name="create-reproducible-ml-pipelines"></a>Creare pipeline di ML riproducibili

Usare pipeline ML da Azure Machine Learning per unire tutti i passaggi necessari per il processo di training del modello.

Una pipeline ML può contenere passaggi dalla preparazione dei dati all'estrazione delle funzionalità all'ottimizzazione dell'iperparametro alla valutazione del modello. Per altre informazioni, vedere [pipeline di ml](concept-ml-pipelines.md).

Se si usa la [finestra di progettazione](concept-designer.md) per creare le pipeline di Machine Learning, è possibile in qualsiasi momento fare clic su **"..."** in alto a destra nella pagina della finestra di progettazione e quindi selezionare **Clone**. La clonazione della pipeline consente di eseguire l'iterazione della progettazione della pipeline senza perdere le versioni precedenti.  

## <a name="create-reusable-software-environments"></a>Creare ambienti software riutilizzabili

Gli ambienti Azure Machine Learning consentono di rilevare e riprodurre le dipendenze del software dei progetti man mano che si evolvono. Gli ambienti consentono di garantire che le compilazioni siano riproducibili senza configurazioni software manuali.

Gli ambienti descrivono le dipendenze PIP e conda per i progetti e possono essere usati sia per il training sia per la distribuzione dei modelli. Per ulteriori informazioni, vedere [che cosa sono gli ambienti Azure Machine Learning](concept-environments.md).

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registrare, creare pacchetti e distribuire modelli ovunque ci si trovi

### <a name="register-and-track-ml-models"></a>Registrare e tenere traccia dei modelli di ML

Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nel cloud di Azure, all'interno della propria area di lavoro. Il registro dei modelli consente di organizzare i modelli sottoposti a training e tenerne traccia con facilità.

> [!TIP]
> Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro di Azure Machine Learning. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

I modelli registrati sono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro incrementa la versione. Durante la registrazione è possibile specificare tag di metadati aggiuntivi. Questi tag vengono quindi utilizzati durante la ricerca di un modello. Azure Machine Learning supporta qualsiasi modello che può essere caricato con Python 3.5.2 o versione successiva.

> [!TIP]
> È anche possibile registrare i modelli sottoposti a training all'esterno Azure Machine Learning.

Non è possibile eliminare un modello registrato utilizzato in una distribuzione attiva.
Per altre informazioni, vedere la sezione relativa alla registrazione di un modello nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Modelli di profilo

Azure Machine Learning possono essere utili per comprendere i requisiti di CPU e memoria del servizio che verrà creato durante la distribuzione del modello. Il profiling testa il servizio che esegue il modello e restituisce informazioni quali l'utilizzo della CPU, l'utilizzo della memoria e la latenza della risposta. Fornisce inoltre una raccomandazione per CPU e memoria basata sull'utilizzo delle risorse.
Per ulteriori informazioni, vedere la sezione relativa alla profilatura di [deploy Models](how-to-deploy-profile-model.md).

### <a name="package-and-debug-models"></a>Modelli di pacchetti ed debug

Prima di distribuire un modello in produzione, questo viene inserito in un pacchetto in un'immagine docker. Nella maggior parte dei casi, la creazione di un'immagine viene eseguita automaticamente in background durante la distribuzione. È possibile specificare manualmente l'immagine.

Se si verificano problemi con la distribuzione, è possibile eseguire la distribuzione nell'ambiente di sviluppo locale per la risoluzione dei problemi e il debug.

Per ulteriori informazioni, vedere Distribuzione di [modelli](how-to-deploy-and-where.md#registermodel) e [risoluzione dei problemi relativi alle distribuzioni](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Convertire e ottimizzare i modelli

La conversione del modello per l'apertura di ONNX ( [Neural Network Exchange](https://onnx.ai) ) può migliorare le prestazioni. In media, la conversione in ONNX può produrre un aumento delle prestazioni di 2x.

Per altre informazioni su ONNX con Azure Machine Learning, vedere l'articolo [creare e accelerare i modelli ml](concept-onnx.md) .

### <a name="use-models"></a>Usare i modelli

I modelli di apprendimento automatico sottoposti a training vengono distribuiti come servizi Web nel cloud o localmente. È anche possibile distribuire modelli a dispositivi Azure IoT Edge. Le distribuzioni usano CPU, GPU o FPGA (Field-Programmable Gate Array) per l'inferenza. È anche possibile usare i modelli di Power BI.

Quando si usa un modello come servizio Web o dispositivo IoT Edge, vengono forniti gli elementi seguenti:

* Il modello o i modelli usati per assegnare un punteggio ai dati inviati al servizio o al dispositivo.
* Uno script di avvio. Questo script accetta le richieste, usa il modello o i modelli per assegnare un punteggio ai dati e restituire una risposta.
* Un ambiente Azure Machine Learning che descrive le dipendenze PIP e conda richieste dai modelli e dallo script di immissione.
* Eventuali asset aggiuntivi, ad esempio testo, dati e così via, necessari per i modelli e lo script di immissione.

Viene inoltre specificata la configurazione della piattaforma di distribuzione di destinazione. Ad esempio, il tipo di famiglia di VM, la memoria disponibile e il numero di core quando si esegue la distribuzione nel servizio Azure Kubernetes.

Quando viene creata l'immagine vengono aggiunti anche i componenti richiesti da Azure Machine Learning. Ad esempio, gli asset necessari per eseguire il servizio Web e interagire con IoT Edge.

#### <a name="batch-scoring"></a>Assegnazione dei punteggi batch
Il Punteggio batch è supportato tramite pipeline ML. Per ulteriori informazioni, vedere [stime batch in Big Data](how-to-use-parallel-run-step.md).

#### <a name="real-time-web-services"></a>Servizi Web in tempo reale

È possibile usare i modelli nei **servizi Web** con le destinazioni di calcolo seguenti:

* Istanza di contenitore di Azure
* Servizio Azure Kubernetes
* Ambiente di sviluppo locale

Per distribuire il modello come servizio Web, è necessario fornire gli elementi seguenti:

* Modello o insieme di modelli.
* Dipendenze necessarie per l'utilizzo del modello. Ad esempio, uno script che accetta richieste e richiama il modello, le dipendenze conda e così via.
* Configurazione della distribuzione che descrive come e dove distribuire il modello.

Per ulteriori informazioni, vedere [deploy Models](how-to-deploy-and-where.md).

#### <a name="controlled-rollout"></a>Implementazione controllata

Quando si esegue la distribuzione nel servizio Azure Kubernetes, è possibile usare l'implementazione controllata per abilitare gli scenari seguenti:

* Creare più versioni di un endpoint per una distribuzione
* Eseguire un test A/B eseguendo il routing del traffico a versioni diverse dell'endpoint.
* Passare da una versione all'altra dell'endpoint aggiornando la percentuale di traffico nella configurazione dell'endpoint.

Per altre informazioni, vedere [implementazione controllata dei modelli ml](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview).

#### <a name="iot-edge-devices"></a>Dispositivi IoT Edge

È possibile usare i modelli con i dispositivi Internet con i **moduli Azure IOT Edge**. I moduli IoT Edge vengono distribuiti in un dispositivo hardware, che consente l'inferenza o il punteggio del modello nel dispositivo.

Per ulteriori informazioni, vedere [deploy Models](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI supporta l'uso di modelli di apprendimento automatico per l'analisi dei dati. Per ulteriori informazioni, vedere [integrazione di Azure Machine Learning in Power BI (anteprima)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Acquisire i dati di governance necessari per l'acquisizione del ciclo di vita di Machine Learning end-to-end

Azure ML offre la possibilità di tenere traccia dell'audit trail end-to-end di tutte le risorse ML usando i metadati.

- Azure ML si [integra con git](how-to-set-up-training-targets.md#gitintegration) per tenere traccia delle informazioni relative a repository/Branch/commit da cui proviene il codice.
- I set di dati di [Azure ml](how-to-create-register-datasets.md) consentono di monitorare, profilare e la versione dei dati.
- L' [interpretazione](how-to-machine-learning-interpretability.md) consente di spiegare i modelli, rispettare la conformità alle normative e comprendere il modo in cui i modelli arrivano a un risultato per l'input specificato.
- La cronologia di esecuzione di Azure ML archivia uno snapshot del codice, dei dati e dei calcoli usati per il training di un modello.
- Il registro di sistema del modello di Azure ML acquisisce tutti i metadati associati al modello (che sperimentano il training, la posizione in cui vengono distribuiti, se le distribuzioni sono integre).
- L' [integrazione con Azure](how-to-use-event-grid.md) consente di agire sugli eventi nel ciclo di vita di ml. Ad esempio, la registrazione del modello, la distribuzione, la deviazione dati e gli eventi di training (esecuzione).

> [!TIP]
> Sebbene alcune informazioni sui modelli e i set di dati vengano acquisiti automaticamente, è possibile aggiungere altre informazioni usando i __tag__. Quando si cercano i modelli e i set di impostazioni registrati nell'area di lavoro, è possibile usare i tag come filtro.
>
> L'associazione di un set di dati a un modello registrato è un passaggio facoltativo. Per informazioni sul riferimento a un set di dati durante la registrazione di un modello, vedere il riferimento alla classe del [modello](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py) .


## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Notifica, automazione e avviso per gli eventi nel ciclo di vita ML
Azure ML pubblica gli eventi chiave in Azure EventGrid, che possono essere usati per notificare e automatizzare gli eventi nel ciclo di vita di ML. Per ulteriori informazioni, consultare [questo documento](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitorare i problemi operativi di & ML

Il monitoraggio consente di comprendere quali dati vengono inviati al modello e le stime che restituisce.

Queste informazioni consentono di comprendere la modalità di utilizzo del modello. I dati di input raccolti possono essere utili anche per il training di versioni future del modello.

Per altre informazioni, vedere [Come abilitare la raccolta dei dati dei modelli](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Ripetere il training del modello con nuovi dati

Spesso è necessario convalidare il modello, aggiornarlo o anche ripetere il training da zero, quando si ricevono nuove informazioni. In alcuni casi, la ricezione di nuovi dati è una parte prevista del dominio. In altri casi, come illustrato in [rilevare la tendenza dei dati (anteprima) sui set di dati](how-to-monitor-datasets.md), le prestazioni del modello possono peggiorare in modo simile alle modifiche apportate a un particolare sensore, modifiche ai dati naturali, ad esempio gli effetti stagionali, o funzionalità che si spostano in relazione ad altre funzionalità. 

Non esiste alcuna risposta universale a "Ricerca per categorie sa se devo ripetere il training?" Tuttavia, gli strumenti di monitoraggio e di eventi di Azure ML descritti in precedenza sono ottimi punti di partenza per l'automazione. Dopo aver deciso di ripetere il training, è necessario: 

- Pre-elabora i dati usando un processo ripetibile e automatizzato
- Eseguire il training del nuovo modello
- Confrontare gli output del nuovo modello con quelli del vecchio modello
- Usare i criteri predefiniti per scegliere se sostituire il modello precedente 

Un tema dei passaggi precedenti consiste nel fatto che la ripetizione del training dovrebbe essere automatizzata, non ad hoc. [Azure Machine Learning pipeline](concept-ml-pipelines.md) rappresentano una valida soluzione per la creazione di flussi di lavoro relativi alla preparazione, al training, alla convalida e alla distribuzione dei dati. Leggere ripetere il training dei [modelli con Azure Machine Learning Designer (anteprima)](how-to-retrain-designer.md) per vedere come le pipeline e il Azure machine learning designer rientrino in uno scenario di ripetizione del training. 

## <a name="automate-the-ml-lifecycle"></a>Automatizzare il ciclo di vita del ML 

È possibile usare GitHub e Azure Pipelines per creare un processo di integrazione continua che esegue il training di un modello. In uno scenario tipico, quando un data scientist verifica una modifica nel repository Git per un progetto, la pipeline di Azure avvierà un'esecuzione di training. I risultati dell'esecuzione possono quindi essere controllati per visualizzare le caratteristiche di prestazioni del modello sottoposto a training. È anche possibile creare una pipeline che distribuisce il modello come servizio Web.

L' [estensione Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) facilita l'utilizzo di Azure Pipelines. Fornisce i miglioramenti seguenti per Azure Pipelines:

* Abilita la selezione dell'area di lavoro durante la definizione di una connessione al servizio.
* Consente di attivare le pipeline di rilascio da modelli sottoposti a training creati in una pipeline di training.

Per ulteriori informazioni sull'utilizzo di Azure Pipelines con Azure Machine Learning, vedere i collegamenti seguenti:

* [Integrazione continua e distribuzione dei modelli ML con Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Azure Machine Learning repository MLOps](https://aka.ms/mlops) .
* [Azure Machine Learning repository MLOpsPython](https://github.com/Microsoft/MLOpspython) .

È anche possibile usare Azure Data Factory per creare una pipeline di inserimento dati che prepara i dati per l'uso con il training. Per altre informazioni, vedere [pipeline](how-to-cicd-data-ingestion.md)di inserimento dati.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, leggere ed esplorare le risorse seguenti:

+ [Come & dove distribuire i modelli](how-to-deploy-and-where.md) con Azure Machine Learning

+ [Esercitazione: distribuire un modello di classificazione delle immagini in ACI](tutorial-deploy-models-with-aml.md).

+ [Repository degli esempi MLOps end-to-end](https://github.com/microsoft/MLOps)

+ [Modelli CI/CD di ML con Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Creare client che [utilizzano un modello distribuito](how-to-consume-web-service.md)

+ [Apprendimento automatico su larga scala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Architetture di riferimento di intelligenza artificiale di Azure & Best Practices Rep](https://github.com/microsoft/AI)
