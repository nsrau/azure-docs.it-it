---
title: Distribuire un modello per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: In questo documento si apprenderà come creare e distribuire un endpoint usando il portale di Riconoscimento vocale personalizzato.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806114"
---
# <a name="deploy-a-custom-model"></a>Distribuire un modello personalizzato

Dopo aver caricato e ispezionato i dati, valutato l'accuratezza ed eseguito il training di un modello personalizzato, è possibile distribuire un endpoint personalizzato da usare con le app, gli strumenti e i prodotti. In questo documento si apprenderà come creare e distribuire un endpoint usando il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Creare un endpoint personalizzato

Per creare un nuovo endpoint personalizzato, accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) e selezionare **distribuzione** dal menu riconoscimento vocale personalizzato nella parte superiore della pagina. Se si tratta della prima esecuzione, si noterà che nella tabella non sono elencati endpoint. Dopo aver creato un endpoint, usare questa pagina per tenere traccia di ogni endpoint distribuito.

Selezionare quindi **Aggiungi endpoint** e immettere un **nome** e una **Descrizione** per l'endpoint personalizzato. Selezionare quindi il modello personalizzato che si desidera associare a questo endpoint. Da questa pagina è anche possibile abilitare la registrazione. La registrazione consente di monitorare il traffico dell'endpoint. Se disabilitato, il traffico non verrà archiviato.

![Come distribuire un modello](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Non dimenticare di accettare le condizioni per l'utilizzo e i dettagli relativi ai prezzi.

Quindi selezionare **Crea**. Questa azione consente di tornare alla pagina **distribuzione** . La tabella include ora una voce che corrisponde all'endpoint personalizzato. Lo stato dell'endpoint Mostra lo stato corrente. Possono essere necessari fino a 30 minuti per creare un'istanza di un nuovo endpoint usando i modelli personalizzati. Quando lo stato della distribuzione diventa **completato**, l'endpoint è pronto per l'utilizzo.

Dopo la distribuzione dell'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Fare clic sul collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, l'URL dell'endpoint e il codice di esempio.

## <a name="view-logging-data"></a>Visualizzare i dati di registrazione

I dati di registrazione sono disponibili per il download in **Endpoint > dettagli**.

## <a name="next-steps"></a>Passaggi successivi

* Usare l'endpoint personalizzato con l' [SDK di riconoscimento vocale](speech-sdk.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
