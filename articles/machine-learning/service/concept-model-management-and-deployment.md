---
title: 'MLOps: Gestire, distribuire, & monitorare i modelli ML'
titleSuffix: Azure Machine Learning
description: 'Informazioni su come usare Azure Machine Learning per MLOps: distribuire, gestire e monitorare i modelli per migliorarli continuamente. È possibile distribuire i modelli di cui si è effettuato il training con Azure Machine Learning sul computer locale o da altre origini.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 98a3102d47504b40a6b62eb329b508468947ca79
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035477"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning"></a>MLOps: Gestione, distribuzione e monitoraggio dei modelli con Azure Machine Learning

Questo articolo illustra come usare Azure Machine Learning per gestire il ciclo di vita dei modelli. Azure Machine Learning usa un approccio per le operazioni di Machine Learning (MLOps), che migliora la qualità e la coerenza delle soluzioni di machine learning. 

Azure Machine Learning offre le funzionalità di MLOps seguenti:

- **Distribuisci progetti ML da qualsiasi posizione**
- **Monitorare le applicazioni ml per i problemi operativi e ml correlati** : confrontare gli input del modello tra training e inferenza, esplorare le metriche specifiche del modello e fornire il monitoraggio e gli avvisi nell'infrastruttura ml.
- **Acquisire i dati necessari per stabilire un audit trail end-to-end del ciclo di vita di ml**, inclusi quelli che pubblicano i modelli, perché vengono apportate modifiche e quando i modelli sono stati distribuiti o utilizzati nell'ambiente di produzione.
- **Automatizzare il ciclo di vita di Machine Learning end-to-end con Azure Machine Learning e Azure DevOps** per aggiornare spesso i modelli, testare nuovi modelli e implementare continuamente nuovi modelli ml insieme ad altre applicazioni e servizi.

Per saperne di più sui concetti alla base di MLOps e su come si applicano a Azure Machine Learning, guardare il video seguente.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>Distribuisci progetti ML da qualsiasi posizione

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Trasformare il processo di training in una pipeline riproducibile
Usare pipeline di ML da Azure Machine Learning per unire tutti i passaggi necessari per il processo di training del modello, dalla preparazione dei dati all'estrazione delle funzionalità all'ottimizzazione iperparametri alla valutazione del modello.

Per altre informazioni, vedere [pipeline di ml](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>Registrare e tenere traccia dei modelli di ML

Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nel cloud di Azure, all'interno della propria area di lavoro. Il registro dei modelli consente di organizzare i modelli sottoposti a training e tenerne traccia con facilità.

> [!TIP]
> Un modello registrato è un contenitore logico per uno o più file che costituiscono il modello. Se, ad esempio, si dispone di un modello archiviato in più file, è possibile registrarli come singolo modello nell'area di lavoro Azure Machine Learning. Dopo la registrazione, è possibile scaricare o distribuire il modello registrato e ricevere tutti i file registrati.
 
I modelli registrati sono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro incrementa la versione. È possibile fornire tag di metadati aggiuntivi durante la registrazione che può essere usata quando si cercano i modelli. Azure Machine Learning supporta qualsiasi modello che può essere caricato con Python 3.5.2 o versione successiva.

> [!TIP]
> È anche possibile registrare i modelli sottoposti a training all'esterno Azure Machine Learning.

Non è possibile eliminare un modello registrato utilizzato in una distribuzione attiva.
Per altre informazioni, vedere la sezione relativa alla registrazione di un modello nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Modelli di pacchetti ed debug

Prima di distribuire un modello in produzione, questo viene inserito in un pacchetto in un'immagine docker. Nella maggior parte dei casi, la creazione di un'immagine viene eseguita automaticamente in background durante la distribuzione. Per gli scenari avanzati, è possibile specificare manualmente l'immagine.

Se si verificano problemi con la distribuzione, è possibile eseguire la distribuzione nell'ambiente di sviluppo locale per la risoluzione dei problemi e il debug.

Per ulteriori informazioni, vedere Distribuzione di [modelli](how-to-deploy-and-where.md#registermodel) e [risoluzione dei problemi relativi alle distribuzioni](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Convalidare e profilare i modelli

Azure Machine Learning possibile utilizzare la profilatura per determinare le impostazioni della CPU e della memoria ideali da utilizzare durante la distribuzione del modello. La convalida del modello viene eseguita come parte di questo processo, usando i dati forniti per il processo di profilatura.

### <a name="convert-and-optimize-models"></a>Convertire e ottimizzare i modelli

La conversione del modello per l'apertura di ONNX ( [Neural Network Exchange](https://onnx.ai) ) può migliorare le prestazioni. In media, la conversione in ONNX può produrre un aumento delle prestazioni di 2x.

Per altre informazioni su ONNX con Azure Machine Learning, vedere l'articolo [creare e accelerare i modelli ml](concept-onnx.md) .

### <a name="use-models"></a>Usare i modelli

I modelli di apprendimento automatico con training possono essere distribuiti come servizi Web nel cloud o in locale nell'ambiente di sviluppo. È anche possibile distribuire modelli a dispositivi Azure IoT Edge. Le distribuzioni possono usare CPU, GPU o FPGA (Field-Programmable Gate Array) per l'inferenza. È anche possibile usare i modelli di Power BI.

Quando si usa un modello come servizio Web o IoT Edge dispositivo, vengono forniti gli elementi seguenti:

* Il modello o i modelli usati per assegnare un punteggio ai dati inviati al servizio o al dispositivo.
* Uno script di immissione. Questo script accetta le richieste, usa il modello o i modelli per assegnare un punteggio ai dati e restituire una risposta.
* Un file di ambiente conda che descrive le dipendenze richieste dai modelli e dallo script di immissione.
* Eventuali asset aggiuntivi, ad esempio testo, dati e così via, necessari per i modelli e lo script di immissione.

Questi asset vengono assemblati in un'immagine Docker e distribuiti come un servizio Web o un modulo IoT Edge.

Facoltativamente, è possibile usare i parametri seguenti per ottimizzare ulteriormente la distribuzione:

* Abilita GPU: Usato per abilitare il supporto GPU nell'immagine docker. L'immagine deve essere usata nei servizi di Microsoft Azure, ad esempio istanze di contenitore di Azure, servizio Azure Kubernetes, Azure Machine Learning calcolo o macchine virtuali di Azure.
* Passaggi aggiuntivi per il file docker: Un file che contiene altri passaggi Docker da eseguire durante la creazione dell'immagine docker.
* Immagine di base: Immagine personalizzata da usare come immagine di base. Se non si usa un'immagine personalizzata, l'immagine di base viene fornita da Azure Machine Learning.

Viene inoltre fornita la configurazione della piattaforma di distribuzione di destinazione. Ad esempio, il tipo di famiglia di VM, la memoria disponibile e il numero di core quando si esegue la distribuzione nel servizio Azure Kubernetes.

Quando viene creata l'immagine, vengono aggiunti anche i componenti necessari per Azure Machine Learning. Ad esempio, gli asset necessari per eseguire il servizio Web e interagire con IoT Edge.

> [!NOTE]
> Non è possibile modificare o modificare il server Web o i componenti IoT Edge usati nell'immagine docker. Azure Machine Learning usa una configurazione del server Web e componenti IoT Edge testati e supportati da Microsoft.

#### <a name="web-service"></a>Servizio Web

È possibile usare i modelli nei **servizi Web** con le destinazioni di calcolo seguenti:

* Istanza di contenitore di Azure
* Servizio Azure Kubernetes
* Ambiente di sviluppo locale

Per distribuire il modello come servizio Web, è necessario fornire gli elementi seguenti:

* Modello o insieme di modelli.
* Dipendenze necessarie per l'utilizzo del modello. Ad esempio, uno script che accetta richieste e richiama il modello, le dipendenze conda e così via.
* Configurazione della distribuzione che descrive come e dove distribuire il modello.

Per ulteriori informazioni, vedere [deploy Models](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Dispositivi IoT Edge

È possibile usare i modelli con i dispositivi Internet con i **moduli Azure IOT Edge**. I moduli IoT Edge vengono distribuiti in un dispositivo hardware, che consente l'inferenza o il punteggio del modello nel dispositivo.

Per ulteriori informazioni, vedere [deploy Models](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analisi

Microsoft Power BI supporta l'uso di modelli di apprendimento automatico per l'analisi dei dati. Per ulteriori informazioni, vedere [integrazione di Azure Machine Learning in Power BI (anteprima)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Monitorare le applicazioni ML per problemi operativi e ML correlati

Il monitoraggio consente di comprendere quali dati vengono inviati al modello e le stime che restituisce.

Queste informazioni consentono di comprendere la modalità di utilizzo del modello. I dati di input raccolti possono essere utili anche per il training di versioni future del modello.

Per altre informazioni, vedere [Come abilitare la raccolta dei dati dei modelli](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Acquisisci un audit trail end-to-end del ciclo di vita ML

Azure ML ti permette di tenere traccia del audit trail end-to-end di tutte le risorse ML. In particolare:

- Azure ML si [integra con git](how-to-set-up-training-targets.md#gitintegration) per tenere traccia delle informazioni che provengono da repository/Branch/commit del codice.
- I set di dati di [Azure ml](how-to-create-register-datasets.md) consentono di monitorare e verificare la versione dei dati.
- La cronologia di esecuzione di Azure ML archivia uno snapshot del codice, i dati e il calcolo usati per eseguire il training di un modello.
- Il registro di sistema del modello di Azure ML acquisisce tutti i metadati associati al modello (che sperimentano il training, la posizione in cui vengono distribuiti, se le distribuzioni sono integre).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>Automatizzare il ciclo di vita di ML end-to-end 

È possibile usare GitHub e Azure Pipelines per creare un processo di integrazione continua che esegue il training di un modello. In uno scenario tipico, quando un data scientist verifica una modifica nel repository Git per un progetto, la pipeline di Azure avvierà un'esecuzione di training. I risultati dell'esecuzione possono quindi essere controllati per visualizzare le caratteristiche di prestazioni del modello sottoposto a training. È anche possibile creare una pipeline che distribuisce il modello come servizio Web.

L' [estensione Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) facilita l'utilizzo di Azure Pipelines. Fornisce i miglioramenti seguenti per Azure Pipelines:

* Abilita la selezione dell'area di lavoro durante la definizione di una connessione al servizio.
* Consente di attivare le pipeline di rilascio da modelli sottoposti a training creati in una pipeline di training.

Per altre informazioni sull'uso di Azure Pipelines con Azure Machine Learning, vedere l'articolo relativo all' [integrazione continua e alla distribuzione di modelli di Machine Learning con Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) e il repository [Azure Machine Learning MLOps](https://aka.ms/mlops) .

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [come e dove è possibile distribuire i modelli](how-to-deploy-and-where.md) con Azure Machine Learning. Per un esempio di distribuzione, vedere [esercitazione: Distribuire un modello di classificazione delle immagini in istanze](tutorial-deploy-models-with-aml.md)di contenitore di Azure.

Informazioni su come creare l' [integrazione e la distribuzione continue dei modelli ml con Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning). 

Informazioni su come creare servizi e applicazioni client che [utilizzano un modello distribuito come servizio Web](how-to-consume-web-service.md).
