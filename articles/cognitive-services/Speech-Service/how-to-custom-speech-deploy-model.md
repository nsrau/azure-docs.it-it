---
title: Distribuire un modello per il riconoscimento vocale personalizzato - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: In questo documento si apprenderà come creare e distribuire un endpoint tramite il portale di riconoscimento vocale personalizzato.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025871"
---
# <a name="deploy-a-custom-model"></a>Distribuire un modello personalizzato

Dopo aver caricato e controllato i dati, valutare l'accuratezza ed eseguito il training di un modello personalizzato, è possibile distribuire un endpoint personalizzato da usare con l'App, strumenti e prodotti. In questo documento si apprenderà come creare e distribuire un endpoint tramite il portale di riconoscimento vocale personalizzato.

## <a name="create-a-custom-endpoint"></a>Creare un endpoint personalizzato

Per creare un nuovo endpoint personalizzati, selezionare **distribuzione** dal menu di riconoscimento vocale personalizzato nella parte superiore della pagina. Se questa è la prima esecuzione, si noterà che non sono presenti endpoint elencati nella tabella. Dopo aver creato un endpoint, è possibile utilizzare questa pagina per tenere traccia di ogni endpoint distribuito.

Successivamente, selezionare **Aggiungi endpoint** e immettere una **Name** e **descrizione** per l'endpoint personalizzato. Selezionare il modello personalizzato che si desidera associare a questo endpoint. In questa pagina è anche possibile abilitare la registrazione. La registrazione consente di monitorare il traffico di endpoint. Se disabilitato, non verrà archiviato il traffico.

![Come distribuire un modello](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Non dimenticare di accettare le condizioni d'uso e i dettagli sui prezzi.

Quindi selezionare **Crea**. Questa azione verrà nuovamente visualizzato il **distribuzione** pagina. La tabella include ora una voce corrispondente al tuo endpoint personalizzato. Lo stato dell'endpoint Mostra lo stato corrente. Possono volerci fino a 30 minuti per creare un'istanza di un nuovo endpoint usando i tuoi modelli personalizzati. Quando lo stato della distribuzione diventa **completa**, l'endpoint è pronto per l'uso.

Dopo aver distribuito l'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Fare clic sul collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, URL dell'endpoint e codice di esempio.

## <a name="view-logging-data"></a>Visualizzare i dati di registrazione

I dati di registrazione sono disponibili per il download nel **Endpoint > Dettagli**.

## <a name="next-steps"></a>Passaggi successivi

* Usare l'endpoint personalizzato con il [Speech SDK](speech-sdk.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
