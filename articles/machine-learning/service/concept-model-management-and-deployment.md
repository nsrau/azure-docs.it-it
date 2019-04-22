---
title: Gestire, registrare, distribuire e monitorare modelli di Machine Learning
titleSuffix: Azure Machine Learning service
description: Informazioni sull'uso del servizio Azure Machine Learning per distribuire, gestire e monitorare i modelli per garantirne un miglioramento costante. È possibile distribuire i modelli di cui si è effettuato il training con il servizio Azure Machine Learning sul computer locale o da altre origini.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 1/23/2019
ms.custom: seodec18
ms.openlocfilehash: 2cd2d328d33744854bc525e5ecf1dfa3b6e4bcc8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59275443"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Gestire e distribuire modelli con il servizio Azure Machine Learning

Questo articolo offre informazioni su come usare il servizio Azure Machine Learning per distribuire, gestire e monitorare i modelli per garantirne un miglioramento costante. È possibile distribuire i modelli di cui si è effettuato il training con Azure Machine Learning sul computer locale o da altre origini. 

Il diagramma seguente illustra il flusso di lavoro di distribuzione completo: [![Flusso di lavoro di distribuzione per Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Il flusso di lavoro di distribuzione comprende i passaggi seguenti:
1. **Registrazione del modello** in un registro ospitato nell'area di lavoro del servizio Azure Machine Learning
1. **Registrazione di un'immagine** che associa un modello a uno script di punteggio e alle dipendenze in un contenitore portabile 
1. **Distribuzione** dell'immagine come un servizio Web nel cloud o nei dispositivi perimetrali
1. **Monitoraggio e raccolta dati**
1. **Aggiornamento** di una distribuzione per usare una nuova immagine

Ogni passaggio può essere eseguito in modo indipendente o come parte di un comando di distribuzione singolo. Inoltre, è possibile integrare la distribuzione in un **flusso di lavoro CI/CD** come illustrato in questo grafico.

[!['Azure Machine Learning/integrazione continua (CI/CD) ciclo di distribuzione'](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>Passaggio 1: Registrare il modello

Con la registrazione dei modelli è possibile archiviare i modelli e creare le relative versioni nel cloud di Azure, all'interno della propria area di lavoro. Il registro dei modelli consente di organizzare i modelli sottoposti a training e tenerne traccia con facilità.
 
I modelli registrati sono identificati dal nome e dalla versione. Ogni volta che si registra un modello con lo stesso nome di uno esistente, il registro incrementa la versione. È possibile fornire tag di metadati aggiuntivi durante la registrazione che può essere usata quando si cercano i modelli. Il servizio Azure Machine Learning supporta qualsiasi modello che possa essere caricato con Python 3. 

Non è possibile eliminare i modelli attualmente in uso da un'immagine.

Per altre informazioni, vedere la sezione relativa alla registrazione di un modello nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#registermodel).

Per un esempio di registrazione di un modello archiviato in formato pickle, vedere [Esercitazione: Eseguire il training di un modello di classificazione delle immagini](tutorial-deploy-models-with-aml.md).

Per informazioni sull'uso di modelli ONNX, vedere il documento [ONNX e Azure Machine Learning](how-to-build-deploy-onnx.md).

## <a name="step-2-register-image"></a>Passaggio 2: Registrare l'immagine

Le immagini consentono una distribuzione affidabile del modello, insieme a tutti i componenti necessari per l'uso del modello. Un'immagine contiene i seguenti elementi:

* Il modello
* Il motore dei punteggi
* Il file di assegnazione dei punteggi o l'applicazione
* Tutte le dipendenze necessarie per assegnare un punteggio al modello

L'immagine può anche includere componenti SDK per la registrazione e il monitoraggio. I dati di log SDK possono essere utilizzati per ottimizzare o ripetere il training dei modelli, compresi l'input e l'output del modello.

Azure Machine Learning supporta i framework più diffusi, ma in genere può funzionare qualsiasi framework in cui si può installare il pip.

Quando l'area di lavoro è stata creata, diverse altre risorse di Azure sono state usate da quell'area di lavoro.
Tutti gli oggetti utilizzati per creare l'immagine predefinita vengono archiviati nell'account di archiviazione di Azure nell'area di lavoro. È possibile specificare altri tag di metadati durante la creazione dell'immagine. I tag dei metadati vengono inoltre archiviati nel registro delle immagini e consentono l'esecuzione di query per trovare l'immagine.

È anche possibile usare immagini personalizzate, che possono essere caricate in Registro contenitori di Azure e utilizzate dal servizio Azure Machine Learning.

Per altre informazioni, vedere la sezione relativa alla configurazione e alla registrazione di un'immagine nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#configureimage).

## <a name="step-3-deploy-image"></a>Passaggio 3: Distribuire l'immagine

È possibile distribuire immagini registrate nel cloud o nei dispositivi perimetrali. Il processo di distribuzione crea tutte le risorse necessarie per il monitoraggio, il bilanciamento del carico e la scalabilità automatica del modello. È possibile proteggere l'accesso ai servizi distribuiti con l'autenticazione basata su certificati, fornendo gli asset di sicurezza durante la distribuzione. È inoltre possibile aggiornare una distribuzione esistente per usare un'immagine più recente.

È anche possibile cercare le distribuzioni dei servizi Web. Ad esempio, è possibile cercare tutte le distribuzioni di un modello o immagini specifici.

[![Destinazioni di inferenza](media/concept-model-management-and-deployment/inferencing-targets.png)](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

È possibile distribuire le immagini nelle destinazioni di distribuzione seguenti nel cloud:

* Istanza di contenitore di Azure
* Servizio Azure Kubernetes
* Computer Azure FPGA
* Dispositivi Azure IoT Edge

Una volta distribuito il servizio, il carico della richiesta di inferenza è automaticamente bilanciato e il cluster viene ridimensionato per soddisfare qualsiasi richiesta. I [dati di telemetria relativi al servizio](how-to-enable-app-insights.md) possono essere acquisiti dal servizio Azure Application Insights.

Per altre informazioni, vedere la sezione relativa alla distribuzione nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#deploy).

## <a name="step-4-monitor-models-and-collect-data"></a>Passaggio 4: Monitorare i modelli e raccogliere i dati

È disponibile un SDK per la registrazione dei modelli e l'acquisizione dei dati per monitorare input, output e altri dati pertinenti del modello. I dati vengono archiviati come un BLOB nell'account di Archiviazione di Microsoft Azure per l'area di lavoro.

Per usare SDK con il modello, si importa SDK nello script dei punteggi o nell'applicazione. È quindi possibile usare SDK per registrare i dati, ad esempio parametri, risultati o dettagli di input.

Se si decide di abilitare la raccolta dati del modello ogni volta che si distribuisce l'immagine, viene effettuato automaticamente il provisioning dei dettagli necessari per l'acquisizione dei dati, ad esempio le credenziali per l'archivio BLOB personale.

> [!Important]
> Microsoft non vede i dati raccolti dal modello. I dati vengono inviati direttamente all'account di archiviazione di Azure per la propria area di lavoro.

Per altre informazioni, vedere [Come abilitare la raccolta dei dati dei modelli](how-to-enable-data-collection.md).

## <a name="step-5-update-the-deployment"></a>Passaggio 5: Aggiornare la distribuzione

Gli aggiornamenti al modello non vengono registrati automaticamente. In modo analogo, la registrazione di una nuova immagine non aggiorna automaticamente le distribuzioni che sono state create da una versione precedente dell'immagine. In alternativa, è necessario registrare manualmente il modello, registrare l'immagine e quindi aggiornare il modello. Per altre informazioni, vedere la sezione relativa all'aggiornamento nell'articolo [Distribuire modelli](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [come e dove è possibile distribuire modelli](how-to-deploy-and-where.md) con il servizio di Azure Machine Learning. Per un esempio di distribuzione, vedere [esercitazione: Distribuire un modello di classificazione di immagini in istanze di contenitore di Azure](tutorial-deploy-models-with-aml.md).

Informazioni su come creare servizi e applicazioni client che [utilizzano un modello distribuito come servizio Web](how-to-consume-web-service.md).
