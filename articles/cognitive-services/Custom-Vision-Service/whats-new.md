---
title: Novità di Visione personalizzata
titleSuffix: Azure Cognitive Services
description: Questo articolo contiene le novità relative a Visione personalizzata.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 57f3cf0cb15243d054da0111366f3a1dc0fb5349
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95739758"
---
# <a name="whats-new-in-custom-vision"></a>Novità di Visione personalizzata

Ecco cosa c'è di nuovo nel servizio. Le novità possono essere note sulla versione, video, post di blog e altri tipi di informazioni. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati sul servizio.


## <a name="october-2020"></a>Ottobre 2020 

### <a name="custom-base-model"></a>Modello di base personalizzato

- Per alcune applicazioni è presente una grande quantità di dati di training comuni, ma è necessario ottimizzare separatamente i relativi modelli. In questo modo si ottiene un miglioramento delle prestazioni per le immagini di origini diverse con differenze minime. In questo caso, è possibile eseguire il training del primo modello come di consueto usando un volume elevato di dati di training. Chiamare quindi **TrainProject** nell'API di anteprima pubblica 3.4 con _CustomBaseModelInfo_ nel corpo della richiesta per usare il modello sottoposto a training della prima fase come modello di base per i progetti downstream. Se il progetto di origine e il progetto di destinazione downstream hanno caratteristiche simili per le immagini, è possibile prevedere prestazioni migliori. 

### <a name="new-domain-information"></a>Nuove informazioni sul dominio

- Le informazioni sul dominio restituite da **GetDomains** nell'API di anteprima pubblica Visione personalizzata 3.4 ora includono piattaforme esportabili supportate, una breve descrizione dell'architettura del modello e le dimensioni del modello per i domini compatti.

### <a name="training-divergence-feedback"></a>Feedback sulla divergenza di training

- L'API di Visione personalizzata 3.4 (anteprima pubblica) ora restituisce **TrainingErrorDetails** dalla chiamata **GetIteration**. In caso di iterazioni non riuscite indica se l'errore è stato causato dalla divergenza di training, che può essere risolta con una maggiore quantità di dati di training di qualità superiore.

## <a name="july-2020"></a>Luglio 2020

### <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure

* Visione personalizzata supporta il controllo degli accessi in base al ruolo di Azure, un sistema di autorizzazione per la gestione dei singoli accessi alle risorse di Azure. Per informazioni su come gestire l'accesso ai progetti di Visione personalizzata, vedere [Controllo degli accessi in base al ruolo di Azure](./role-based-access-control.md).

### <a name="subset-training"></a>Training su un subset

* Quando si esegue il training di un progetto di rilevamento oggetti, facoltativamente è possibile eseguire il training solo su un subset dei tag applicati. Questa operazione può essere eseguita se non è ancora stato applicato un numero sufficiente di determinati tag, ma si dispone di un numero sufficiente di altri. Per altre informazioni, seguire l'[argomento di avvio rapido relativo alla libreria client](./quickstarts/object-detection.md) per C# o Python.

### <a name="azure-storage-notifications"></a>Notifiche di archiviazione di Azure

* È possibile integrare il progetto di Visione personalizzata con una coda di archiviazione BLOB di Azure per ottenere notifiche push di attività di training/esportazione di progetto e copie di backup di modelli pubblicati. Questa funzionalità è utile per evitare di eseguire continuamente il polling del servizio per ottenere risultati durante l'esecuzione di operazioni lunghe. Al contrario, è possibile integrare le notifiche della coda di archiviazione nel flusso di lavoro. Per altre informazioni, vedere la guida all'[integrazione di Archiviazione](./storage-integration.md).

### <a name="copy-and-move-projects"></a>Copiare e spostare progetti

* È ora possibile copiare i progetti da un account Visione personalizzata ad altri. Potrebbe essere necessario spostare un progetto da un ambiente di sviluppo a un ambiente di produzione oppure eseguire il backup di un progetto in un account in un'area di Azure diversa per aumentare la sicurezza dei dati. Per altre informazioni, vedere la guida su come [copiare e spostare progetti](./copy-move-projects.md).

## <a name="september-2019"></a>Settembre 2019

### <a name="suggested-tags"></a>Tag consigliati

* Lo strumento Smart Label disponibile nel [sito Web di Visione personalizzata](https://www.customvision.ai/) genera tag consigliati per le immagini di training. In questo modo è possibile etichettare più rapidamente un numero elevato di immagini durante il training di un modello di Visione personalizzata. Per le istruzioni su come usare questa funzionalità, vedere [Tag consigliati](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Aggiornamenti dei Servizi cognitivi

[Annunci di aggiornamento di Azure per Servizi cognitivi](https://azure.microsoft.com/updates/?product=cognitive-services)