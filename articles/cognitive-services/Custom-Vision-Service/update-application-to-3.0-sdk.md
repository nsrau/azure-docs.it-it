---
title: Come eseguire la migrazione del progetto per la 3.0 API
titlesuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione di progetti di visione artificiale personalizzato dalla versione precedente dell'API per la 3.0 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044007"
---
# <a name="migrate-to-the-30-api"></a>Eseguire la migrazione alla 3.0 API

Servizio visione artificiale personalizzato è ora a livello generale e ha subito un aggiornamento di API.
Questo aggiornamento include alcune nuove funzionalità e, inoltre, alcune modifiche di rilievo:

* L'API Prediction ora è suddiviso in due parti in base al tipo di progetto.
* L'opzione di esportazione di visione artificiale per intelligenza artificiale Developer Kit (VAIDK) richiede la creazione di un progetto in modo specifico.
* Iterazioni predefinite sono state rimosse a favore di pubblicare / annullare la pubblicazione di un'iterazione denominata.

Questa Guida illustrerà la procedura per aggiornare i progetti per lavorare con la nuova versione dell'API. Vedere le [note sulla versione](release-notes.md) per un elenco completo delle modifiche.

## <a name="use-the-updated-prediction-api"></a>Usare l'API Prediction aggiornata

2.x API usata la stessa chiamata di stima per classificatori di immagini e i progetti di rilevatore di oggetto. Entrambi i tipi di progetto sono stati accettabili per la **PredictImage** e **PredictImageUrl** chiamate. A partire da 3.0, è stato suddiviso questa API in modo che è necessario associare il tipo di progetto per la chiamata:

* Uso **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** per ottenere stime per i progetti di classificazione di immagini.
* Uso **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** per ottenere stime per i progetti di rilevamento di oggetti.

## <a name="use-the-new-iteration-publishing-workflow"></a>Usare il nuovo flusso di lavoro iterazione

2.x API utilizzata iterazione predefinita o un ID di iterazione specificato per scegliere l'iterazione da usare per la stima. A partire da 3.0, abbiamo adottato un flusso di pubblicazione in base al quale è prima di tutto pubblicare un'iterazione in un nome specificato dall'API di training. Quindi possibile passare il nome per i metodi di stima per specificare quali iterazione da usare.

> [!IMPORTANT]
> In 3.0 API non utilizzano la funzionalità di iterazione predefinito. Fino a quando non viene resa obsoleta le API precedenti, è possibile continuare a usare l'API versione 2.x per attivare o disattivare un'iterazione come impostazione predefinita. Queste API verranno mantenute per un periodo di tempo ed è possibile chiamare il **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metodo per contrassegnare un'iterazione come predefinito.

### <a name="publish-an-iteration"></a>Pubblicare un'iterazione

Una volta che viene eseguito il training di un'iterazione, è possibile renderla disponibile per la stima utilizzando la **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** (metodo). Per pubblicare un'iterazione, è necessario l'ID di risorsa di stima, disponibile nella pagina Impostazioni del sito Web CustomVision.

![La pagina Impostazioni sito Web visione artificiale personalizzato con l'ID di risorsa di stima descritto.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> È anche possibile ottenere queste informazioni dal [portale di Azure](https://portal.azure.com) accedendo alla risorsa di stima di visione artificiale personalizzato e selezionando **proprietà**.

Dopo la pubblicazione dell'iterazione, le app possono usarlo per la stima, specificando il nome nella loro chiamata API per le stime. Per rendere disponibile per le chiamate di stima un'iterazione, usare il **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="additional-export-options"></a>Altre opzioni di esportazione

Con 3.0 API si sta esponendo altre due destinazioni di esportazione: L'architettura ARM e Kit per sviluppatori di intelligenza artificiale per visione artificiale.

* Per usare ARM, è sufficiente selezionare un dominio Compact e quindi scegliere file DockerFile e quindi ARM come le opzioni di esportazione.
* Per visione artificiale per intelligenza artificiale Dev Kit, è necessario creare il progetto con il __generali (Compact)__ dominio, nonché specificando VAIDK nella destinazione di esportazione argomento piattaforme.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento API Training (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentazione di riferimento API per le stime (REST)](https://go.microsoft.com/fwlink/?linkid=865445)