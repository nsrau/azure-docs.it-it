---
title: 'Passaggio 6: Accedere al servizio Web di Machine Learning | Documentazione Microsoft'
description: 'Passaggio 6 della procedura dettagliata Sviluppare una soluzione predittiva: Accedere a un servizio Web attivo di Azure Machine Learning.'
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 82e068ca3656b28e5e8dad19a31d6e5ff9c6f8b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Passaggio 6 della procedura dettagliata: Accedere al servizio Web di Azure Machine Learning

Questo è l'ultimo passaggio della procedura dettagliata [Sviluppare una soluzione di analisi predittiva in Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Creare un'area di lavoro di Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Caricare i dati esistenti](walkthrough-2-upload-data.md)
3. [Creare un nuovo esperimento](walkthrough-3-create-new-experiment.md)
4. [Eseguire il training e valutare i modelli](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuire il servizio Web](walkthrough-5-publish-web-service.md)
6. **Accedere al servizio Web**

- - -
Nel passaggio precedente di questa procedura dettagliata è stato distribuito un servizio Web che utilizza il modello di previsione del rischio di credito. Ora gli utenti sono in grado di inviare dati al servizio e ricevere risultati. 

Questo è un servizio Web di Azure che può ricevere e restituire dati tramite le API REST in due modi:  

* **Richiesta/risposta** : l'utente invia uno o più set di dati di credito al servizio usando un protocollo HTTP e il servizio risponde con uno o più set di risultati.
* **Esecuzione batch** : l'utente archivia una o più righe di dati di credito in un BLOB di Azure e invia il percorso del BLOB al servizio. Il servizio assegna un punteggio a tutte le righe di dati del BLOB di input, archivia i risultati in un altro BLOB e restituisce l'URL di quel contenitore.  

È il modo più rapido e semplice per accedere a un servizio Web classico tramite il [modello di app Web di richiesta-risposta di Azure Machine Learning ](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) o il [modello di app Web di esecuzione batch di Azure Machine Learning](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

I modelli di app Web consentono di compilare un'app Web personalizzata che riconosce i dati di input del servizio Web e i dati da restituire. È sufficiente concedere l'accesso al proprio servizio Web e ai dati e il modello farà il resto.

Per altre informazioni sull'utilizzo di modelli di app Web, vedere [Utilizzare un servizio Web di Azure Machine Learning con un modello di app Web](consume-web-service-with-web-app-template.md).

È possibile sviluppare anche un'applicazione personalizzata per accedere al servizio Web utilizzando il codice di avvio fornito automaticamente nei linguaggi di programmazione R, C# e Python.

Per informazioni complete, vedere [Come usare un servizio Web di Azure Machine Learning](consume-web-services.md).

