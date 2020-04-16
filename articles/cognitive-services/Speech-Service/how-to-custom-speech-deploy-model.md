---
title: Distribuire un modello per il riconoscimento vocale personalizzato - Servizio di riconoscimento vocaleDeploy a model for Custom Speech - Speech service
titleSuffix: Azure Cognitive Services
description: In questo documento verrà illustrato come creare e distribuire un endpoint tramite il portale di riconoscimento vocale personalizzato.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 9d48f09fe5d5c736f65b6a76211dd3fec85479ea
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402186"
---
# <a name="deploy-a-custom-model"></a>Distribuire un modello personalizzato

Dopo aver caricato e analizzato i dati, valutato l'accuratezza ed eseguito il training di un modello personalizzato, è possibile distribuire un endpoint personalizzato da usare con app, strumenti e prodotti. In questo documento verrà illustrato come creare e distribuire un endpoint utilizzando il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Creare un endpoint personalizzatoCreate a custom endpoint

Per creare un nuovo endpoint personalizzato, accedere al [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) e selezionare **Distribuzione** dal menu Riconoscimento vocale personalizzato nella parte superiore della pagina. Se si tratta della prima esecuzione, si noterà che non sono presenti endpoint elencati nella tabella. Dopo aver creato un endpoint, si userà questa pagina per tenere traccia di ogni endpoint distribuito.

Selezionare quindi **Aggiungi endpoint** e immettere un **Nome** e una **Descrizione** per l'endpoint personalizzato. Selezionare quindi il modello personalizzato che si desidera associare a questo endpoint. Da questa pagina è anche possibile abilitare la registrazione. La registrazione consente di monitorare il traffico degli endpoint. Se disabilitata, il traffico non verrà archiviato.

![Come distribuire un modelloHow to deploy a model](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Non dimenticare di accettare le condizioni per l'utilizzo e i dettagli sui prezzi.

Quindi selezionare **Crea**. Questa azione consente di tornare alla pagina **Distribuzione.This** action returns you to the Deployment page. La tabella include ora una voce che corrisponde all'endpoint personalizzato. Lo stato dell'endpoint mostra lo stato corrente. Possono essere nuovi minuti per creare un'istanza di un nuovo endpoint usando i modelli personalizzati. Quando lo stato della distribuzione diventa **Completato**, l'endpoint è pronto per l'uso.

Dopo la distribuzione dell'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Fare clic sul collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, l'URL dell'endpoint e il codice di esempio.

## <a name="view-logging-data"></a>Visualizzare i dati di registrazione

I dati di registrazione sono disponibili per il download in **Endpoint > Details**.

## <a name="next-steps"></a>Passaggi successivi

* Scopri come usare il modello personalizzato [qui](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Ispezionare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
