---
title: Eseguire il training e la distribuzione di un modello di Riconoscimento vocale personalizzato-servizio vocale
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come eseguire il training e la distribuzione di modelli Riconoscimento vocale personalizzato. Il training di un modello di sintesi vocale può migliorare l'accuratezza del riconoscimento per il modello di base di Microsoft o per un modello personalizzato. Viene eseguito il training di un modello usando trascrizioni con etichetta umana e testo correlato.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 9193da2f3841bb94aa395399c31d7fe826c395e0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025600"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Eseguire il training e distribuire un modello di Riconoscimento vocale personalizzato

Questo articolo illustra come eseguire il training e la distribuzione di modelli Riconoscimento vocale personalizzato. Il training di un modello di sintesi vocale può migliorare l'accuratezza del riconoscimento per il modello di base di Microsoft. Viene eseguito il training di un modello usando trascrizioni con etichetta umana e testo correlato. Questi DataSet insieme ai dati audio caricati in precedenza vengono usati per perfezionare ed eseguire il training del modello di riconoscimento vocale.

## <a name="use-training-to-resolve-accuracy-issues"></a>Usare il training per risolvere i problemi di accuratezza

Se si riscontrano problemi di riconoscimento con un modello di base, l'uso di trascrizioni con etichetta umana e dati correlati per eseguire il training di un modello personalizzato può contribuire a migliorare la precisione. Usare questa tabella per determinare il set di dati da usare per risolvere i problemi:

| Caso d'uso | Tipo di dati |
| -------- | --------- |
| Migliorare l'accuratezza del riconoscimento su vocabolario e grammatica specifici del settore, ad esempio la terminologia medica o il gergo IT. | Testo correlato (frasi/espressioni) |
| Definire il form fonetico e visualizzato di una parola o di un termine con una pronuncia non standard, ad esempio nomi di prodotto o acronimi. | Testo correlato (pronuncia) |
| Migliorare l'accuratezza del riconoscimento su stili di pronuncia, accenti o rumori di fondo specifici. | Trascrizioni audio + con etichetta umana |

## <a name="train-and-evaluate-a-model"></a>Eseguire il training di un modello e valutarlo

Il primo passaggio per eseguire il training di un modello consiste nel caricare i dati di training. Usare [preparare e testare i dati](./how-to-custom-speech-test-and-train.md) per istruzioni dettagliate per preparare le trascrizioni con etichetta umana e il testo correlato (espressioni e pronunce). Dopo aver caricato i dati di training, seguire queste istruzioni per avviare il training del modello:

1. Accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **> vocale Riconoscimento vocale personalizzato > [nome del progetto] > Training**.
3. Fare clic su **Train Model**.
4. Successivamente, assegnare un **nome** e una **Descrizione** al training.
5. Dal menu a discesa **scenario e modello di base** selezionare lo scenario più adatto al dominio. Se non si è certi dello scenario da scegliere, selezionare **generale**. Il modello di base è il punto di partenza per il training. Il modello più recente è in genere la scelta migliore.
6. Dalla pagina **selezione dati di training** scegliere uno o più set di dati di trascrizione audio + con etichetta umana che si vuole usare per il training.
7. Una volta completato il training, è possibile scegliere di eseguire test di accuratezza sul modello appena sottoposto a training. Questo passaggio è facoltativo.
8. Selezionare **Crea** per compilare il modello personalizzato.

Nella tabella training viene visualizzata una nuova voce che corrisponde al modello appena creato. La tabella Visualizza anche lo stato: elaborazione, operazione riuscita, non riuscita.

Vedere le [procedure per](how-to-custom-speech-evaluate-data.md) valutare e migliorare riconoscimento vocale personalizzato accuratezza del modello. Se si sceglie l'accuratezza dei test, è importante selezionare un set di dati acustico diverso da quello usato con il modello per ottenere un senso realistico delle prestazioni del modello.

## <a name="deploy-a-custom-model"></a>Distribuire un modello personalizzato

Dopo aver caricato e ispezionato i dati, valutato l'accuratezza ed eseguito il training di un modello personalizzato, è possibile distribuire un endpoint personalizzato da usare con le app, gli strumenti e i prodotti. 

Per creare un nuovo endpoint personalizzato, accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) e selezionare **distribuzione** dal menu riconoscimento vocale personalizzato nella parte superiore della pagina. Se si tratta della prima esecuzione, si noterà che nella tabella non sono elencati endpoint. Dopo aver creato un endpoint, è possibile usare questa pagina per tenere traccia di ogni endpoint distribuito.

Selezionare quindi **Aggiungi endpoint** e immettere un **nome** e una **Descrizione** per l'endpoint personalizzato. Selezionare quindi il modello personalizzato che si desidera associare a questo endpoint. Da questa pagina è anche possibile abilitare la registrazione. La registrazione consente di monitorare il traffico dell'endpoint. Se disabilitato, il traffico non verrà archiviato.

![Come distribuire un modello](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Non dimenticare di accettare le condizioni per l'utilizzo e i dettagli relativi ai prezzi.

Quindi selezionare **Crea**. Questa azione consente di tornare alla pagina **distribuzione** . La tabella include ora una voce che corrisponde all'endpoint personalizzato. Lo stato dell'endpoint Mostra lo stato corrente. Possono essere necessari fino a 30 minuti per creare un'istanza di un nuovo endpoint usando i modelli personalizzati. Quando lo stato della distribuzione diventa **completato**, l'endpoint è pronto per l'utilizzo.

Dopo la distribuzione dell'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Fare clic sul collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, l'URL dell'endpoint e il codice di esempio.

## <a name="view-logging-data"></a>Visualizzare i dati di registrazione

I dati di registrazione sono disponibili per il download in **Endpoint > dettagli**.
> [!NOTE]
>I dati di registrazione sono disponibili per 30 giorni nell'archiviazione di proprietà di Microsoft e verranno rimossi in seguito. Se un account di archiviazione di proprietà del cliente è collegato alla sottoscrizione di servizi cognitivi, i dati di registrazione non verranno eliminati automaticamente.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come usare il modello personalizzato](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Preparare e testare i dati](./how-to-custom-speech-test-and-train.md)
- [Esaminare i dati](how-to-custom-speech-inspect-data.md)
- [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
