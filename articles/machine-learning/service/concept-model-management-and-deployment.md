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
ms.openlocfilehash: 416bebc070cfcad52c6180e65f0066c46c826cbe
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849653"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Gestire e distribuire modelli con il servizio Azure Machine Learning

Questo articolo offre informazioni su come usare il servizio Azure Machine Learning per distribuire, gestire e monitorare i modelli per garantirne un miglioramento costante. È possibile distribuire i modelli di cui si è effettuato il training con Azure Machine Learning sul computer locale o da altre origini. 

Il diagramma seguente illustra il flusso di lavoro di distribuzione completo: [![Flusso di lavoro di distribuzione per Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Il MLOps / flusso di lavoro di distribuzione include i passaggi seguenti:
1. **Registrazione del modello** in un registro ospitato nell'area di lavoro del servizio Azure Machine Learning
1. **Usare** il modello in un servizio web nel cloud, in un dispositivo IoT o per analitica con Power BI.
1. **Monitoraggio e raccolta dati**
1. **Aggiornamento** di una distribuzione per usare una nuova immagine

Ogni passaggio può essere eseguita in modo indipendente o come parte di un singolo comando. Inoltre, è possibile creare un **flusso di lavoro di integrazione continua/recapito Continuo** come illustrato in questo grafico.

[!['Azure Machine Learning/integrazione continua (CI/CD) ciclo di distribuzione'](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="step-1-register-model"></a>Passaggio 1: Registrare il modello

Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nel cloud di Azure, all'interno della propria area di lavoro. Il registro dei modelli consente di organizzare i modelli sottoposti a training e tenerne traccia con facilità.

> [!TIP]
> È anche possibile registrare i modelli sottoposti a training all'esterno del servizio di Azure Machine Learning.
 
I modelli registrati sono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro incrementa la versione. È possibile fornire tag di metadati aggiuntivi durante la registrazione che può essere usata quando si cercano i modelli. Il servizio di Azure Machine Learning supporta qualsiasi modello che può essere caricate usando Python 3.5.2 o versione successiva.

Non è possibile eliminare i modelli utilizzati in una distribuzione attiva.

Per altre informazioni, vedere la sezione relativa alla registrazione di un modello nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#registermodel).

Per un esempio di registrazione di un modello archiviato in formato pickle, vedere [Esercitazione: Eseguire il training di un modello di classificazione delle immagini](tutorial-deploy-models-with-aml.md).

## <a name="step-2-use-the-model"></a>Passaggio 2: Usare il modello

Modelli di Machine learning possono essere utilizzati come un servizio web, in dispositivi perimetrali IoT o per analitica dai servizi, ad esempio Power BI.

### <a name="web-service"></a>Servizio Web

È possibile usare i modelli nel **servizi web** destinazioni di calcolo con il codice seguente:

* Istanza di contenitore di Azure
* Servizio Azure Kubernetes

Per distribuire il modello come servizio web, è necessario predisporre quanto segue:

* Il modello o un insieme di modelli.
* Dipendenze necessarie per utilizzare il modello. Ad esempio, uno script che accetta le richieste e richiama il modello, le dipendenze conda, e così via.
* Configurazione della distribuzione in cui viene descritto come e dove distribuire il modello.

Per altre informazioni, vedere [distribuire modelli](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Dispositivi IoT Edge

È possibile usare i modelli con i dispositivi IoT attraverso **moduli Azure IoT Edge**. Moduli di IoT Edge vengono distribuiti nei dispositivi hardware, che abilita l'inferenza, o un modello di punteggio, nel dispositivo.

Per altre informazioni, vedere [distribuire modelli](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analisi

Microsoft Power BI supporta l'utilizzo di modelli di machine learning per analitica dei dati. Per altre informazioni, vedere [integrazione di Azure Machine Learning in Power BI (anteprima)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="step-3-monitor-models-and-collect-data"></a>Passaggio 3: Monitorare i modelli e raccogliere i dati

Il monitoraggio consente di comprendere quali dati inviati al modello e le stime che viene restituito.

Queste informazioni consentono di comprendere come viene utilizzato il modello. I dati di input raccolti possono anche essere utili nelle versioni future del training del modello.

Per altre informazioni, vedere [Come abilitare la raccolta dei dati dei modelli](how-to-enable-data-collection.md).

## <a name="step-4-update-the-deployment"></a>Passaggio 4: Aggiornare la distribuzione

Le distribuzioni devono essere aggiornate in modo esplicito. Per altre informazioni, vedere la sezione relativa all'aggiornamento nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [come e dove è possibile distribuire modelli](how-to-deploy-and-where.md) con il servizio di Azure Machine Learning. Per un esempio di distribuzione, vedere [esercitazione: Distribuire un modello di classificazione di immagini in istanze di contenitore di Azure](tutorial-deploy-models-with-aml.md).

Informazioni su come creare [integrazione continua e distribuzione di modelli di Machine Learning con le pipeline di Azure](/azure/devops/pipelines/targets/azure-machine-learning). 

Informazioni su come creare servizi e applicazioni client che [utilizzano un modello distribuito come servizio Web](how-to-consume-web-service.md).
