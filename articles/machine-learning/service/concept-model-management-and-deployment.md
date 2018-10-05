---
title: Gestione e distribuzione dei modelli con Azure Machine Learning
description: Informazioni sull'uso di Azure Machine Learning per distribuire, gestire e monitorare i modelli al fine di un miglioramento costante. È possibile distribuire i modelli di cui si è effettuato il training con Azure Machine Learning sul computer locale o da altre origini.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hjerez
ms.author: hjerez
ms.date: 09/24/2018
ms.openlocfilehash: d3e0b63d42ad8c6d4765f5120c26c5dfdf5ad6fb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166538"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning"></a>Gestione, distribuzione e monitoraggio dei modelli con Azure Machine Learning

In questo articolo è possibile ottenere informazioni su come usare Azure Machine Learning per distribuire, gestire e monitorare i modelli per un miglioramento costante. È possibile distribuire i modelli di cui si è effettuato il training con Azure Machine Learning sul computer locale o da altre origini. 

Il diagramma seguente illustra il flusso di lavoro completo di distribuzione: [ ![Flusso di lavoro di distribuzione per Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Il flusso di lavoro di distribuzione comprende i passaggi seguenti:
1. **Registrazione del modello** in un registro ospitato nell'area di lavoro di Azure Machine Learning
1. **Registrazione di un'immagine** che associa un modello a uno script di punteggio e alle dipendenze in un contenitore portabile 
1. **Distribuzione** dell'immagine come un servizio Web nel cloud o nei dispositivi perimetrali
1. **Monitoraggio e raccolta dati**

Ogni passaggio può essere eseguito in modo indipendente o come parte di un comando di distribuzione singolo. Inoltre, è possibile integrare la distribuzione in un **flusso di lavoro CI/CD** come illustrato in questo grafico.

[ !['Ciclo di integrazione continua/distribuzione continua (CI/CD) di Azure Machine Learning'](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)


## <a name="step-1-register-model"></a>Passaggio 1: Registrazione del modello

Il registro di modello tiene traccia di tutti i modelli nell'area di lavoro di Azure Machine Learning.
I modelli vengono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro incrementa la versione. È possibile fornire tag di metadati aggiuntivi durante la registrazione che può essere usata quando si cercano i modelli.

Non è possibile eliminare i modelli attualmente in uso da un'immagine.

## <a name="step-2-register-image"></a>Passaggio 2: Registrare un'immagine

Le immagini consentono una distribuzione affidabile del modello, insieme a tutti i componenti necessari per l'uso del modello. Un'immagine contiene i seguenti elementi:

* Il modello
* Il motore dei punteggi
* Il file di assegnazione dei punteggi o l'applicazione
* Tutte le dipendenze necessarie per assegnare un punteggio al modello

L'immagine può anche includere componenti SDK per la registrazione e il monitoraggio. I dati di registro SDK possono essere utilizzati per ottimizzare o ripetere il training dei modelli, compresi l'input e l'output del modello.

Azure Machine Learning supporta i framework più diffusi, ma in genere può funzionare qualsiasi framework in cui si può installare il pip.

Quando l'area di lavoro è stata creata, diverse altre risorse di Azure sono state usate da quell'area di lavoro.
Tutti gli oggetti usati per creare l'immagine vengono archiviati nell'account di archiviazione di Azure nell'area di lavoro. L'immagine viene creata e archiviata nel Registro contenitori di Azure. È possibile fornire tag di metadati aggiuntivi quando si crea l'immagine, che vengono archiviati anche nel registro di immagini e di cui è possibile eseguire query per trovare l'immagine.

## <a name="step-3-deploy-image"></a>Passaggio 3: Distribuzione dell'immagine

È possibile distribuire immagini registrate nel cloud o nei dispositivi perimetrali. Il processo di distribuzione crea tutte le risorse necessarie per il monitoraggio, il bilanciamento del carico e la scalabilità automatica del modello. È possibile proteggere l'accesso ai servizi distribuiti con l'autenticazione basata su certificati, fornendo gli asset di sicurezza durante la distribuzione. È inoltre possibile aggiornare una distribuzione esistente per usare un'immagine più recente.

È anche possibile cercare le distribuzioni dei servizi Web. Ad esempio, è possibile cercare tutte le distribuzioni di un modello o immagini specifici.

[ ![Destinazioni di inferenza](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

È possibile distribuire le immagini nelle seguenti [destinazioni di distribuzione](how-to-deploy-and-where.md) nel cloud:

* Istanza di contenitore di Azure
* Azure Kubernetes Service
* Computer Azure FPGA
* Dispositivi Azure IoT Edge

Una volta distribuito il servizio, il carico della richiesta di inferenza è automaticamente bilanciato e il cluster viene ridimensionato per soddisfare qualsiasi richiesta. I [Dati di telemetria relativi al servizio](https://docs.microsoft.com/python/api/azureml-telemetry/azureml.telemetry?view=azure-ml-py) possono essere acquisiti dal servizio di Application Insights di Azure associato all'area di lavoro.

## <a name="step-4-monitor-models-and-collect-data"></a>Passaggio 4: Monitoraggio dei modelli e raccolta dati

È disponibile un SDK per la registrazione dei modelli e l'acquisizione dei dati per monitorare input, output e altri dati pertinenti del modello. I dati vengono archiviati come un BLOB nell'account di Archiviazione di Microsoft Azure per l'area di lavoro.

Per usare SDK con il modello, si importa SDK nello script dei punteggi o nell'applicazione. È quindi possibile usare SDK per registrare i dati, ad esempio parametri, risultati o dettagli di input.

Se si decide di [abilitare la raccolta dati del modello](how-to-enable-data-collection.md) ogni volta che si distribuisce l'immagine, viene effettuato automaticamente il provisioning dei dettagli necessari per l'acquisizione dei dati, ad esempio le credenziali per l'archivio BLOB personale.

> [!Important]
> Microsoft non vede i dati raccolti dal modello. I dati vengono inviati direttamente all'account di archiviazione di Azure per la propria area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [come e dove è possibile distribuire modelli](how-to-deploy-and-where.md) con il servizio di Azure Machine Learning.
