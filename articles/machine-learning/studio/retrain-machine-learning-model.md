---
title: Ripetere il training di un modello di Machine Learning | Documentazione Microsoft
description: Informazioni su come ripetere il training di un modello e aggiornare il servizio Web per usare il modello appena sottoposto a training in Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: f02bf54cc8941d18c866183c9b7d12bbe7222d2b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="retrain-a-machine-learning-model"></a>Ripetere il training di un modello di Machine Learning
Come parte del processo di messa in funzione dei modelli di apprendimento automatico in Azure Machine Learning, è necessario sottoporre a training e salvare il modello. Lo si userà quindi per creare un servizio Web predicativo. Il servizio Web potrà quindi essere utilizzato in siti Web, dashboard e app per dispositivi mobili. 

I modelli creati con Machine Learning in genere non sono statici. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello. 

La ripetizione del training può avvenire di frequente. Con la funzionalità delle API di ripetizione del training a livello di codice, è possibile ripetere il training del modello a livello di codice usando le API di ripetizione del training e aggiornare il servizio Web con un modello di cui è stato appena eseguito il training. 

Questo documento descrive il processo di ripetizione del training e illustra come usare le API per la ripetizione del training.

## <a name="why-retrain-defining-the-problem"></a>Motivi per cui ripetere il training: definizione del problema
Durante il processo di training di Machine Learning viene eseguito il training di un modello usando un set di dati. I modelli creati con Machine Learning in genere non sono statici. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello.

In questi scenari un'API a livello di codice offre un modo pratico per consentire all'utente dell'API di creare un client in grado di ripetere il training del modello una tantum o periodicamente usando i propri dati. Sarà quindi possibile valutare i risultati della ripetizione del training e aggiornare l'API del servizio Web per l'uso del modello appena sottoposto a training.

> [!NOTE]
> Se si dispone di un esperimento di training esistente e di un nuovo servizio Web, è possibile che si preferisca fare riferimento all'articolo Ripetere il training di un servizio Web predittivo esistente anziché seguire la procedura dettagliata descritta nella sezione seguente.
> 
> 

## <a name="end-to-end-workflow"></a>Flusso di lavoro end-to-end
Per il processo sono necessari i componenti seguenti: un esperimento di training e un esperimento predittivo pubblicato come servizio Web. Per abilitare la ripetizione del training di un modello con training, l'esperimento di training deve essere pubblicato come servizio Web con l'output di un modello con training. In questo modo viene abilitato l'accesso dell'API al modello per la ripetizione del training. 

I passaggi seguenti sono validi sia per i nuovi servizi Web sia per i servizi Web classici:

Creare il servizio Web predittivo iniziale:

* Creare un esperimento di training
* Creare un esperimento Web predittivo
* Distribuire un servizio Web predittivo

Ripetere il training del servizio Web:

* Aggiornare l'esperimento di training per consentire la ripetizione del training
* Distribuire la ripetizione del training del servizio Web
* Usare il codice del servizio Esecuzione batch per ripetere il training del modello

Per una descrizione dettagliata dei passaggi precedenti, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](retrain-models-programmatically.md).

> [!NOTE] 
> Per distribuire un nuovo servizio Web è necessario disporre delle autorizzazioni sufficienti nella sottoscrizione a cui si sta distribuendo il servizio Web. Per altre informazioni, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md). 

Se è stato distribuito un servizio Web classico:

* Creare un nuovo endpoint nel servizio Web predittivo
* Ottenere l'URL della PATCH e il codice
* Usare l'URL della PATCH in modo da accedere al nuovo endpoint del modello nuovamente sottoposto a training 

Per una descrizione dettagliata dei passaggi precedenti, vedere [Ripetere il training di un servizio Web classico](retrain-a-classic-web-service.md).

Se si verificano problemi durante la ripetizione del training di un servizio Web classico, vedere [Risoluzione dei problemi relativi alla ripetizione del training di un servizio Web classico di Azure Machine Learning](troubleshooting-retraining-models.md).

Se è stato distribuito un nuovo servizio Web:

* Accedere con l'account di Azure Resource Manager
* Ottenere la definizione del servizio Web
* Esportare la definizione del servizio Web in un file in formato JSON
* Aggiornare il riferimento al BLOB `ilearner` nel file JSON
* Importare il file JSON in una definizione del servizio Web
* Aggiornare il servizio Web con la nuova definizione

Per una descrizione dettagliata dei passaggi precedenti, vedere [Ripetere il training di un nuovo servizio Web usando i cmdlet di gestione di PowerShell per Machine Learning](retrain-new-web-service-using-powershell.md).

Il processo per la configurazione della ripetizione del training per un servizio Web classico prevede i passaggi seguenti:

![Panoramica del processo di ripetizione del training][1]

Il processo per la configurazione della ripetizione del training per un nuovo servizio Web prevede i passaggi seguenti:

![Panoramica del processo di ripetizione del training][7]

## <a name="other-resources"></a>Altre risorse
* [Retraining and Updating Azure Machine Learning models with Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/) (Ripetere il training e aggiornare modelli di Azure Machine Learning con Azure Data Factory)
* [Creare più modelli di Machine Learning ed endpoint di servizio Web da un esperimento usando PowerShell](create-models-and-endpoints-with-powershell.md)
* Il video sulla [ripetizione del training di modelli AML tramite API](https://www.youtube.com/watch?v=wwjglA8xllg) illustra come ripetere il training di modelli di Machine Learning creati in Azure Machine Learning usando la ripetizione del training delle API e PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

