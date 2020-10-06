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
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 019a9264beddafbd4585810967551e064592e94d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602524"
---
# <a name="whats-new-in-custom-vision"></a>Novità di Visione personalizzata

Ecco cosa c'è di nuovo nel servizio. Le novità possono essere note sulla versione, video, post di blog e altri tipi di informazioni. Aggiungere un segnalibro a questa pagina per rimanere sempre aggiornati sul servizio.

## <a name="july-2020"></a>Luglio 2020

### <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

* Visione personalizzata supporta il controllo degli accessi in base al ruolo di Azure, un sistema di autorizzazione per la gestione dei singoli accessi alle risorse di Azure. Per informazioni su come gestire l'accesso ai progetti di Visione personalizzata, vedere [Controllo degli accessi in base al ruolo](./role-based-access-control.md).

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