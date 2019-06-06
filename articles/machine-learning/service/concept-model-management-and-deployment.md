---
title: 'MLOps: Gestire, distribuire e monitorare i modelli di Machine Learning'
titleSuffix: Azure Machine Learning service
description: 'Informazioni su come usare il servizio Azure Machine Learning per MLOps: distribuire, gestire e monitorare i modelli per migliorare costantemente le loro. È possibile distribuire i modelli di cui si è effettuato il training con il servizio Azure Machine Learning sul computer locale o da altre origini.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 0eaf48f57c3011222b71a63d703e1ccec7aca001
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692828"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Gestire e distribuire modelli con il servizio Azure Machine Learning

In questo articolo, informazioni su come usare servizio di Azure Machine Learning per gestire il ciclo di vita dei modelli. Azure Machine Learning Usa un approccio di operazioni di Machine Learning (MLOps), che consente di migliorare la qualità e la coerenza delle soluzioni di machine learning. Servizio di Azure Machine Learning offre le funzionalità MLOps seguenti:

* Integrazione con le pipeline di Azure. Definire continui flussi di lavoro di integrazione e distribuzione per i modelli.
* Un registro di sistema di modello che mantiene più versioni dei modelli con training.
* Convalida modello. Automaticamente la convalida dei modelli con training e selezionare la configurazione ottimale per distribuirli nell'ambiente di produzione.
* Distribuire i modelli come servizio web nel cloud, locale o su dispositivi IoT Edge.
* Monitorare le prestazioni del modello distribuito, in modo che è possibile guidare i miglioramenti nella prossima versione del modello.

Per sapere di più sui concetti alla base MLOps e come si applicano al servizio di Azure Machine Learning, guarda il video seguente.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="integration-with-azure-pipelines"></a>Integrazione con le pipeline di Azure

È possibile usare le pipeline di Azure per creare un processo di integrazione continua che esegue il training di un modello. In uno scenario tipico, quando un Data Scientist verifica una modifica nel repository Git per un progetto, la Pipeline di Azure avvierà un'esecuzione di training. I risultati dell'esecuzione quindi possono essere controllati per verificare le caratteristiche delle prestazioni del modello con training. È anche possibile creare una pipeline che consente di distribuire il modello come servizio web.

Il [estensione di Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) rende più facile lavorare con le pipeline di Azure. Fornisce i seguenti miglioramenti alle pipeline di Azure:

* Consente la selezione dell'area di lavoro quando si definisce una connessione al servizio.
* Consente di rilasciare le pipeline per essere attivata dai modelli di training creati in una pipeline di training.

Per altre informazioni sull'uso di pipeline di Azure con Azure Machine Learning, vedere la [integrazione continua e distribuzione di modelli di Machine Learning con le pipeline di Azure](/azure/devops/pipelines/targets/azure-machine-learning) articolo e [Azure Machine Learning servizio MLOps](https://aka.ms/mlops) repository.

## <a name="convert-and-optimize-models"></a>Convertire e ottimizzare i modelli

Conversione nel modello [scambio di rete neurale Open](https://onnx.ai) (ONNX) può migliorare le prestazioni. In Media, la conversione in ONNX consentono di ottenere un aumento delle prestazioni di 2x.

Per altre informazioni su ONNX con il servizio di Azure Machine Learning, vedere la [crea e accelerare i modelli di Machine Learning](concept-onnx.md) articolo.

## <a name="register-models"></a>Registrare i modelli

Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nel cloud di Azure, all'interno della propria area di lavoro. Il registro dei modelli consente di organizzare i modelli sottoposti a training e tenerne traccia con facilità.

> [!TIP]
> È anche possibile registrare i modelli sottoposti a training all'esterno del servizio di Azure Machine Learning.
 
I modelli registrati sono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro incrementa la versione. È possibile fornire tag di metadati aggiuntivi durante la registrazione che può essere usata quando si cercano i modelli. Il servizio di Azure Machine Learning supporta qualsiasi modello che può essere caricate usando Python 3.5.2 o versione successiva.

Non è possibile eliminare i modelli utilizzati in una distribuzione attiva.

Per altre informazioni, vedere la sezione relativa alla registrazione di un modello nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#registermodel).

Per un esempio di registrazione di un modello archiviato in formato pickle, vedere [Esercitazione: Eseguire il training di un modello di classificazione delle immagini](tutorial-deploy-models-with-aml.md).

## <a name="package-and-debug-models"></a>Modelli di pacchetto ed eseguire il debug

Prima di distribuire un modello nell'ambiente di produzione, si viene incluso nel pacchetto in un'immagine Docker. Nella maggior parte dei casi, la creazione delle immagini viene eseguita automaticamente in background durante la distribuzione. Per scenari avanzati, è possibile specificare manualmente l'immagine.

Se si verificano problemi con la distribuzione, è possibile distribuire nell'ambiente di sviluppo locale per la risoluzione dei problemi e il debug.

Per altre informazioni, vedere [distribuire i modelli](how-to-deploy-and-where.md#registermodel) e [risoluzione dei problemi di distribuzioni](how-to-troubleshoot-deployment.md).

## <a name="validate-and-profile-models"></a>Convalidare e modelli di profilo

Servizio Azure Machine Learning può usare profilatura per determinare le impostazioni della CPU e memoria ideale da utilizzare per la distribuzione del modello. Convalida del modello avviene come parte di questo processo, utilizzando i dati che viene fornito per il processo di profiling.

## <a name="use-models"></a>Usare i modelli

Sottoposto a training modelli di machine learning possono essere distribuito come servizi web nel cloud o in locale nell'ambiente di sviluppo. È anche possibile distribuire i modelli per dispositivi Azure IoT Edge. Distribuzioni possono usare CPU, GPU o matrici di campo-programmable gate (FPGA) per inferenza. È anche possibile usare i modelli da Power BI.

Quando si usa un modello come un servizio web o un dispositivo IoT Edge, è fornire gli elementi seguenti:

* Dei modelli che consentono di classificare i dati inviati al servizio/dispositivo.
* Script. Questo script accetta le richieste, utilizza dei modelli per classificare i dati e restituire una risposta.
* Un file di ambiente conda che descrive le dipendenze richieste dallo script dei modelli e voce.
* Gli asset aggiuntivi, ad esempio testo, dati e così via che vengono richiesti dallo script dei modelli e voce.

Questi asset vengono inseriti in un'immagine Docker e distribuiti come un servizio web o un modulo di IoT Edge.

Facoltativamente, è possibile utilizzare i parametri seguenti per ottimizzare ulteriormente la distribuzione:

* Abilitare la GPU: Consente di abilitare il supporto GPU nell'immagine Docker. L'immagine deve essere usata in servizi di Microsoft Azure, ad esempio istanze di contenitore di Azure, Azure Kubernetes Service, calcolo di Azure Machine Learning o macchine virtuali di Azure.
* Passaggi di file di docker aggiuntive: Un file che contiene ulteriori passaggi di Docker per eseguire durante la creazione dell'immagine Docker.
* Immagine di base: Un'immagine personalizzata da usare come immagine di base. Se non usi un'immagine personalizzata, l'immagine di base viene fornito dal servizio di Azure Machine Learning.

È anche possibile specificare la configurazione della piattaforma di distribuzione di destinazione. Ad esempio, la macchina virtuale tipo di famiglia, la memoria disponibile e numero di core durante la distribuzione in Azure Kubernetes Service.

Quando viene creata l'immagine, vengono aggiunti anche i componenti richiesti dal servizio Azure Machine Learning. Ad esempio, gli asset necessari per eseguire il servizio web e interagire con IoT Edge.

> [!NOTE]
> Impossibile modificare o cambiare il server web o componenti di IoT Edge usati nell'immagine Docker. Servizio Azure Machine Learning Usa una configurazione del server web e i componenti di IoT Edge che vengono testati e supportati da Microsoft.

### <a name="web-service"></a>Servizio Web

È possibile usare i modelli nel **servizi web** destinazioni di calcolo con il codice seguente:

* Istanza di contenitore di Azure
* Servizio Azure Kubernetes
* Ambiente di sviluppo locale

Per distribuire il modello come servizio web, è necessario fornire gli elementi seguenti:

* Il modello o un insieme di modelli.
* Dipendenze necessarie per utilizzare il modello. Ad esempio, uno script che accetta le richieste e richiama il modello, le dipendenze conda, e così via.
* Configurazione della distribuzione in cui viene descritto come e dove distribuire il modello.

Per altre informazioni, vedere [distribuire modelli](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Dispositivi IoT Edge


È possibile usare i modelli con i dispositivi IoT attraverso **moduli Azure IoT Edge**. Moduli di IoT Edge sono distribuiti in un dispositivo hardware, che abilita l'inferenza, o un modello di punteggio, nel dispositivo.

Per altre informazioni, vedere [distribuire modelli](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI supporta l'utilizzo di modelli di machine learning per analitica dei dati. Per altre informazioni, vedere [integrazione di Azure Machine Learning in Power BI (anteprima)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="monitor-and-collect-data"></a>Monitorare e raccogliere i dati

Il monitoraggio consente di comprendere quali dati inviati al modello e le stime che viene restituito.

Queste informazioni consentono di comprendere come viene utilizzato il modello. I dati di input raccolti possono anche essere utili nelle versioni future del training del modello.

Per altre informazioni, vedere [Come abilitare la raccolta dei dati dei modelli](how-to-enable-data-collection.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [come e dove è possibile distribuire modelli](how-to-deploy-and-where.md) con il servizio di Azure Machine Learning. Per un esempio di distribuzione, vedere [esercitazione: Distribuire un modello di classificazione di immagini in istanze di contenitore di Azure](tutorial-deploy-models-with-aml.md).

Informazioni su come creare [integrazione continua e distribuzione di modelli di Machine Learning con le pipeline di Azure](/azure/devops/pipelines/targets/azure-machine-learning). 

Informazioni su come creare servizi e applicazioni client che [utilizzano un modello distribuito come servizio Web](how-to-consume-web-service.md).
