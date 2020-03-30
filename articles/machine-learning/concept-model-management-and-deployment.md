---
title: 'MLOps: gestione del modello ML'
titleSuffix: Azure Machine Learning
description: 'Informazioni sulla gestione dei modelli con Azure Machine Learning (MLOps). Distribuisci, gestisci e monitora i tuoi modelli per migliorarli continuamente. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 40e335ee0377c560b15a588269cbdb39cdebca82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477358"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps: gestione, distribuzione e monitoraggio dei modelli con Azure Machine Learning

In questo articolo vengono fornite informazioni su come usare Azure Machine Learning per gestire il ciclo di vita dei modelli. Azure Machine Learning usa un approccio alle operazioni di Machine Learning (MLOps). MLOps migliora la qualità e la coerenza delle soluzioni di apprendimento automatico. 

## <a name="what-is-mlops"></a>Che cos'è MLOps?

Machine Learning Operations (MLOps) si basa su principi e procedure [DevOps](https://azure.microsoft.com/overview/what-is-devops/) che aumentano l'efficienza dei flussi di lavoro. Ad esempio, integrazione continua, recapito e distribuzione. MLOps applica questi principi al processo di apprendimento automatico, con l'obiettivo di:

* Sperimentazione e sviluppo più rapidi dei modelli
* Implementazione più rapida dei modelli nell'ambiente di produzione
* Controllo qualità

Azure Machine Learning offre le funzionalità MLOps seguenti:Azure Machine Learning provides the following MLOps capabilities:

- **Creare pipeline ML riproducibili**. Le pipeline di Machine Learning consentono di definire passaggi ripetibili e riutilizzabili per i processi di preparazione, formazione e assegnazione dei punteggi dei dati.
- **Creare ambienti software riutilizzabili** per la formazione e la distribuzione di modelli.
- **Registra, crea pacchetti e distribuisci modelli da qualsiasi luogo.** È inoltre possibile tenere traccia dei metadati associati necessari per utilizzare il modello.
- Acquisire i dati di governance per il ciclo di **vita end-to-end ML**. Le informazioni registrate possono includere gli utenti che pubblicano modelli, perché sono state apportate modifiche e quando i modelli sono stati distribuiti o utilizzati nell'ambiente di produzione.
- **Notifica e avviso sugli eventi nel ciclo di vita di ML.** Ad esempio, il completamento dell'esperimento, la registrazione del modello, la distribuzione del modello e il rilevamento della deriva dei dati.
- **Monitorare le applicazioni ML per i problemi operativi e correlati a ML**. Confronta gli input del modello tra training e inferenza, esplora le metriche specifiche del modello e fornisci monitoraggio e avvisi sull'infrastruttura di ML.
- Automatizza il ciclo di vita di **ML end-to-end con Azure Machine Learning e le pipeline**di Azure. L'utilizzo delle pipeline consente di aggiornare frequentemente i modelli, testare nuovi modelli e implementare continuamente nuovi modelli di Machine Learning insieme alle altre applicazioni e servizi.

## <a name="create-reproducible-ml-pipelines"></a>Creare pipeline ML riproducibiliCreate reproducible ML pipelines

Usare le pipeline di Machine Learning di Azure per unire tutti i passaggi coinvolti nel processo di formazione del modello.

Una pipeline di ML può contenere passaggi dalla preparazione dei dati all'estrazione delle funzionalità, dall'ottimizzazione degli iperparametri alla valutazione del modello. Per ulteriori informazioni, vedere [Pipeline di ML](concept-ml-pipelines.md).

Se si utilizza la finestra di [progettazione](concept-designer.md) per creare le pipeline di Machine Manager, è possibile fare clic in qualsiasi momento su **"..."** nella parte superiore destra della pagina Designer e quindi selezionare **Clona**. La clonazione della pipeline consente di eseguire l'iterazione della progettazione della pipeline senza perdere le versioni precedenti.  

## <a name="create-reusable-software-environments"></a>Creare ambienti software riutilizzabili

Gli ambienti di Azure Machine Learning consentono di tenere traccia e riprodurre le dipendenze software dei progetti man mano che si evolvono. Gli ambienti consentono di garantire che le compilazioni siano riproducibili senza configurazioni software manuali.

Gli ambienti descrivono le dipendenze pip e Conda per i progetti e possono essere usati sia per la formazione che per la distribuzione di modelli. Per altre informazioni, vedere Che cosa sono gli [ambienti di Azure Machine Learning.](concept-environments.md)

## <a name="register-package-and-deploy-models-from-anywhere"></a>Registra, crea pacchetti e distribuisci modelli ovunque ti trovi

### <a name="register-and-track-ml-models"></a>Registrare e tenere traccia dei modelli ML

Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nel cloud di Azure, all'interno della propria area di lavoro. Il registro dei modelli consente di organizzare i modelli sottoposti a training e tenerne traccia con facilità.

> [!TIP]
> Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Ad esempio, se si dispone di un modello archiviato in più file, è possibile registrarli come un singolo modello nell'area di lavoro di Azure Machine Learning.For example, if you have a model that is stored in multiple files, you can register them as a single model in your Azure Machine Learning workspace. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.

I modelli registrati sono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro incrementa la versione. Durante la registrazione possono essere forniti tag di metadati aggiuntivi. Questi tag vengono quindi utilizzati durante la ricerca di un modello. Azure Machine Learning supporta qualsiasi modello che può essere caricato con Python 3.5.2 o versione successiva.

> [!TIP]
> È anche possibile registrare modelli formati all'esterno di Azure Machine Learning.You can also register models trained outside Azure Machine Learning.

Non è possibile eliminare un modello registrato utilizzato in una distribuzione attiva.
Per altre informazioni, vedere la sezione relativa alla registrazione di un modello nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#registermodel).

### <a name="profile-models"></a>Modelli di profilo

Azure Machine Learning consente di comprendere i requisiti di CPU e memoria del servizio che verrà creato quando si distribuisce il modello. La profilatura verifica il servizio che esegue il modello e restituisce informazioni quali l'utilizzo della CPU, l'utilizzo della memoria e la latenza di risposta. Fornisce inoltre una raccomandazione per CPU e memoria in base all'utilizzo delle risorse.
Per ulteriori informazioni, vedere la sezione relativa alla profilatura di [Deploy models](how-to-deploy-and-where.md#profilemodel).

### <a name="package-and-debug-models"></a>Modelli di pacchetto e debugPackage and debug models

Prima di distribuire un modello nell'ambiente di produzione, viene incluso in un pacchetto in un'immagine Docker.Before deploying a model into production, it is packaged into a Docker image. Nella maggior parte dei casi, la creazione di immagini avviene automaticamente in background durante la distribuzione. È possibile specificare manualmente l'immagine.

Se si verificano problemi con la distribuzione, è possibile eseguire la distribuzione nell'ambiente di sviluppo locale per la risoluzione dei problemi e il debug.

Per ulteriori informazioni, vedere [Distribuire modelli](how-to-deploy-and-where.md#registermodel) e [Risoluzione dei problemi relativi alle distribuzioni](how-to-troubleshoot-deployment.md).

### <a name="convert-and-optimize-models"></a>Convertire e ottimizzare i modelli

La conversione del modello in [Open Neural Network Exchange](https://onnx.ai) (ONNX) può migliorare le prestazioni. In media, la conversione in ONNX può produrre un aumento delle prestazioni di 2 volte.

Per altre informazioni su ONNX con Azure Machine Learning, vedere l'articolo Creare e accelerare i modelli di Machine Learning.For more information on ONNX with Azure Machine Learning, see [the Create and accelerate ML models](concept-onnx.md) article.

### <a name="use-models"></a>Usare i modelli

I modelli di apprendimento automatico addestrati vengono distribuiti come servizi Web nel cloud o in locale. È anche possibile distribuire modelli nei dispositivi Azure IoT Edge.You can also deploy models to Azure IoT Edge devices. Le distribuzioni utilizzano CPU, GPU o FPGA (Field-programmable gate array) per l'inferenza. È anche possibile usare modelli da Power BI.

Quando si usa un modello come servizio Web o dispositivo IoT Edge, è necessario specificare gli elementi seguenti:

* I modelli utilizzati per assegnare un punteggio ai dati inviati al servizio/dispositivo.
* Uno script di avvio. Questo script accetta le richieste, usa i modelli per assegnare un punteggio ai dati e restituire una risposta.
* Un ambiente Azure Machine Learning che descrive le dipendenze pip e Conda richieste dai modelli e dallo script di immissione.
* Eventuali risorse aggiuntive, ad esempio testo, dati e così via, richieste dai modelli e dallo script di immissione.

Si fornisce anche la configurazione della piattaforma di distribuzione di destinazione. Ad esempio, il tipo di famiglia di macchine virtuali, la memoria disponibile e il numero di core durante la distribuzione nel servizio Azure Kubernetes.For example, the VM family type, available memory, and number of cores when deploying to Azure Kubernetes Service.

Quando viene creata l'immagine, vengono aggiunti anche i componenti richiesti da Azure Machine Learning.When the image is created, components required by Azure Machine Learning are also added. Ad esempio, le risorse necessarie per eseguire il servizio Web e interagire con IoT Edge.

#### <a name="batch-scoring"></a>Assegnazione dei punteggi batch
Il punteggio batch è supportato tramite le pipeline di ML. Per altre informazioni, vedere [Stime batch su Big Data](how-to-use-parallel-run-step.md).For more information, see Batch predictions on big data .

#### <a name="real-time-web-services"></a>Servizi web in tempo reale

È possibile usare i modelli nei servizi Web con le destinazioni di calcolo seguenti:You can use your models in **web services** with the following compute targets:

* Istanza di contenitore di Azure
* Servizio Azure Kubernetes
* Ambiente di sviluppo locale

Per distribuire il modello come servizio Web, è necessario fornire gli elementi seguenti:

* Il modello o l'insieme di modelli.
* Dipendenze necessarie per utilizzare il modello. Ad esempio, uno script che accetta le richieste e richiama il modello, le dipendenze conda e così via.
* Configurazione di distribuzione che descrive come e dove distribuire il modello.

Per ulteriori informazioni, vedere [Distribuire modelli](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Dispositivi IoT Edge

È possibile usare i modelli con i dispositivi IoT tramite i **moduli di Azure IoT Edge.** I moduli IoT Edge vengono distribuiti in un dispositivo hardware, che consente l'inferenza, o il punteggio del modello, nel dispositivo.

Per ulteriori informazioni, vedere [Distribuire modelli](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI supporta l'uso di modelli di apprendimento automatico per l'analisi dei dati. Per altre informazioni, vedere Integrazione di [Azure Machine Learning in Power BI (anteprima).](https://docs.microsoft.com/power-bi/service-machine-learning-integration)

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Acquisire i dati di governance necessari per acquisire il ciclo di vita di ML end-to-end

Azure ML offre la possibilità di tenere traccia dell'audit trail end-to-end di tutti gli asset di ML. In particolare:

- Azure ML [si integra con Git](how-to-set-up-training-targets.md#gitintegration) per tenere traccia delle informazioni su quale repository/branch / commit del codice proviene.
- I set di dati di [Azure ML](how-to-create-register-datasets.md) consentono di tenere traccia, profilare e versione i dati. 
- La cronologia di esecuzione di Azure ML archivia uno snapshot del codice, dei dati e dei calcoli usati per eseguire il training di un modello.
- Il Registro di sistema del modello di Azure ML acquisisce tutti i metadati associati al modello (quale esperimento ne ha eseguito il training, dove viene distribuito, se le distribuzioni sono integre).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Notifica, automatizza e avvisa gli eventi nel ciclo di vita di ML
Azure ML pubblica gli eventi chiave in Azure EventGrid, che possono essere usati per notificare e automatizzare gli eventi nel ciclo di vita di ML. Per ulteriori informazioni, consultare [questo documento](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Monitorare i problemi operativi & ML

Il monitoraggio consente di comprendere quali dati vengono inviati al modello e le stime restituite.

Queste informazioni consentono di comprendere l'utilizzo del modello. I dati di input raccolti possono anche essere utili per il training di versioni future del modello.

Per altre informazioni, vedere [Come abilitare la raccolta dei dati dei modelli](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Riqualificare il modello sui nuovi dati

Spesso, ti consigliamo di aggiornare il modello, o anche riqualificarlo da zero, come si ricevono nuove informazioni. A volte, la ricezione di nuovi dati è una parte prevista del dominio. Altre volte, come descritto in Rilevare la deriva dei [dati (anteprima) sui set di dati](how-to-monitor-datasets.md), le prestazioni del modello possono peggiorare di fronte a elementi quali modifiche a un particolare sensore, modifiche naturali dei dati come effetti stagionali o caratteristiche che si spostano nella loro relazione con altre caratteristiche. 

Non esiste una risposta universale a "Come faccio a sapere se devo riqualificarmi?" ma gli strumenti di monitoraggio e eventi di Azure ML precedentemente discussi sono buoni punti di partenza per l'automazione. Una volta che hai deciso di riqualificare, dovresti: 

- Pre-elaborare i dati utilizzando un processo ripetibile e automatizzato
- Allena il tuo nuovo modello
- Confrontare gli output del nuovo modello con quelli del vecchio modello
- Utilizzare criteri predefiniti per scegliere se sostituire il vecchio modello 

Un tema dei passaggi precedenti è che la riqualificazione deve essere automatizzata, non ad hoc. [Le pipeline](concept-ml-pipelines.md) di Azure Machine Learning sono una buona risposta per la creazione di flussi di lavoro relativi alla preparazione, alla formazione, alla convalida e alla distribuzione dei dati. Leggi I modelli di riqualificazione con la finestra [di progettazione di Azure Machine Learning (anteprima)](how-to-retrain-designer.md) per vedere come le pipeline e la finestra di progettazione di Azure Machine Learning si inseriscono in uno scenario di riqualificazione. 

## <a name="automate-the-ml-lifecycle"></a>Automatizzare il ciclo di vita di ML 

È possibile usare GitHub e le pipeline di Azure per creare un processo di integrazione continua che esegue il training di un modello. In uno scenario tipico, quando un Data Scientist archivia una modifica nel repository Git per un progetto, la pipeline di Azure avvierà un'esecuzione del training. I risultati dell'esecuzione possono quindi essere controllati per visualizzare le caratteristiche delle prestazioni del modello sottoposto a training. È inoltre possibile creare una pipeline che distribuisce il modello come servizio Web.You can also create a pipeline that deploys the model as a web service.

[L'estensione di Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) semplifica l'utilizzo delle pipeline di Azure.The Azure Machine Learning extension makes it easier to work with Azure Pipelines. Fornisce i miglioramenti seguenti alle pipeline di Azure:It provides the following enhancements to Azure Pipelines:

* Consente la selezione dell'area di lavoro durante la definizione di una connessione al servizio.
* Consente alle pipeline di rilascio di essere attivate da modelli sottoposti a training creati in una pipeline di training.

Per altre informazioni sull'uso delle pipeline di Azure con Azure Machine Learning, vedere i collegamenti seguenti:For more information on using Azure Pipelines with Azure Machine Learning, see the following links:

* [Integrazione e distribuzione continua dei modelli di Machine Learning con le pipeline di AzureContinuous integration and deployment of ML models with Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) 
* [Repository MLOps di Azure Machine Learning.Azure Machine Learning MLOps](https://aka.ms/mlops) repository.
* [Archivio MLOpsPython di Azure Machine Learning.Azure Machine Learning MLOpsPython](https://github.com/Microsoft/MLOpspython) repository.

È anche possibile usare Azure Data Factory per creare una pipeline di inserimento dati che prepara i dati per l'uso con il training. Per ulteriori informazioni, vedere Pipeline di [inserimento dati](how-to-cicd-data-ingestion.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, leggere ed esplorare le risorse seguenti:

+ [Come & dove distribuire i modelli](how-to-deploy-and-where.md) con Azure Machine LearningHow how is where to deploy models with Azure Machine Learning

+ [Esercitazione: Distribuire un modello di classificazione delle immagini in ACI](tutorial-deploy-models-with-aml.md).

+ [Repo di esempi di MLOps end-to-end](https://github.com/microsoft/MLOps)

+ [CI/CD of ML models with Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Creare client che [utilizzano un modello distribuitoCreate](how-to-consume-web-service.md) clients that consume a deployed model

+ [Apprendimento automatico su larga scala](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Architetture di riferimento dell'iA di Azure & procedure consigliate](https://github.com/microsoft/AI)
