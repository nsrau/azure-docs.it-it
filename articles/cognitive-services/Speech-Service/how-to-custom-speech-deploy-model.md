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
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 15e2ce437a746d15622dc4e093a63c87448f100d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559663"
---
# <a name="deploy-a-custom-model"></a>Distribuire un modello personalizzato

Dopo aver caricato e ispezionato i dati, valutato l'accuratezza ed eseguito il training di un modello personalizzato, è possibile distribuire un endpoint personalizzato da usare con le app, gli strumenti e i prodotti. In questo documento si apprenderà come creare e distribuire un endpoint usando il portale di Riconoscimento vocale personalizzato.

## <a name="create-a-custom-endpoint"></a>Creare un endpoint personalizzato

Per creare un nuovo endpoint personalizzato, scegliere **distribuzione** dal menu riconoscimento vocale personalizzato nella parte superiore della pagina. Se si tratta della prima esecuzione, si noterà che nella tabella non sono elencati endpoint. Dopo aver creato un endpoint, usare questa pagina per tenere traccia di ogni endpoint distribuito.

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
