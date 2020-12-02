---
title: Eseguire il training e la distribuzione di un modello di Riconoscimento vocale personalizzato-servizio vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire il training e distribuire modelli di Riconoscimento vocale personalizzato. Il training di un modello di sintesi vocale può migliorare l'accuratezza del riconoscimento per il modello Baseline Microsoft o per un modello personalizzato.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 130cd643856b38471eac6d6869cdc1ed8b0bcd2e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499153"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Eseguire il training e distribuire un modello di Riconoscimento vocale personalizzato

In questo articolo si apprenderà come eseguire il training e la distribuzione di Riconoscimento vocale personalizzato modelli. Il training di un modello di sintesi vocale può migliorare l'accuratezza del riconoscimento per il modello Baseline Microsoft. Per eseguire il training di un modello, si utilizzano trascrizioni con etichetta umana e testo correlato. Questi set di dati, insieme ai dati audio caricati in precedenza, vengono usati per perfezionare ed eseguire il training del modello di riconoscimento vocale.

## <a name="use-training-to-resolve-accuracy-problems"></a>Usare il training per risolvere i problemi di accuratezza

Se si riscontrano problemi di riconoscimento con un modello di base, è possibile usare trascrizioni con etichetta umana e dati correlati per eseguire il training di un modello personalizzato e migliorare la precisione. Usare questa tabella per determinare il set di dati da usare per risolvere i problemi:

| Caso d'uso | Tipo di dati |
| -------- | --------- |
| Migliorare l'accuratezza del riconoscimento su vocabolario e grammatica specifici del settore, come la terminologia medica o il gergo IT | Testo correlato (frasi/espressioni) |
| Definire il form fonetico e visualizzato di una parola o di un termine con una pronuncia non standard, ad esempio nomi di prodotto o acronimi | Testo correlato (pronuncia) |
| Migliorare l'accuratezza del riconoscimento su stili di pronuncia, accenti o rumori di fondo specifici | Trascrizioni audio + con etichetta umana |

## <a name="train-and-evaluate-a-model"></a>Eseguire il training di un modello e valutarlo

Il primo passaggio per eseguire il training di un modello consiste nel caricare i dati di training. Per istruzioni dettagliate su come preparare le trascrizioni con etichetta umana e il testo correlato (espressioni e pronunce), vedere [preparare e testare i dati](./how-to-custom-speech-test-and-train.md) . Dopo aver caricato i dati di training, seguire queste istruzioni per avviare il training del modello:

1. Accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **riconoscimento vocale**  >  **riconoscimento vocale personalizzato**  >  **[nome del progetto]**  >  **formazione**.
3. Selezionare **Train Model**.
4. Assegnare un **nome** e una **Descrizione** al training.
5. Nell'elenco **scenario e modello di base** selezionare lo scenario più adatto al dominio. Se non si è certi dello scenario da scegliere, selezionare **generale**. Il modello di base è il punto di partenza per il training. Il modello più recente è in genere la scelta migliore.
6. Nella pagina **selezione dati di training** , scegliere uno o più set di dati di trascrizione audio e con etichetta umana che si desidera utilizzare per il training.
7. Al termine del training, è possibile eseguire test di accuratezza sul modello appena sottoposto a training. Questo passaggio è facoltativo.
8. Selezionare **Crea** per compilare il modello personalizzato.

Nella tabella **Training** viene visualizzata una nuova voce che corrisponde al nuovo modello. La tabella Visualizza anche lo stato: **elaborazione**, **esito positivo** o **non riuscito**.

Vedere le [procedure per](how-to-custom-speech-evaluate-data.md) valutare e migliorare riconoscimento vocale personalizzato accuratezza del modello. Se si sceglie di verificare l'accuratezza, è importante selezionare un set di dati acustico diverso da quello usato con il modello per ottenere un senso realistico delle prestazioni del modello.

## <a name="deploy-a-custom-model"></a>Distribuire un modello personalizzato

Al termine del caricamento e del controllo dei dati, della valutazione dell'accuratezza e del training di un modello personalizzato, è possibile distribuire un endpoint personalizzato da usare con le app, gli strumenti e i prodotti. 

Per creare un endpoint personalizzato, accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). Selezionare **distribuzione** nel menu **riconoscimento vocale personalizzato** nella parte superiore della pagina. Se si tratta della prima esecuzione, si noterà che nella tabella non sono elencati endpoint. Dopo aver creato un endpoint, è possibile utilizzare questa pagina per tenere traccia di ogni endpoint distribuito.

Selezionare quindi **Aggiungi endpoint** e immettere un **nome** e una **Descrizione** per l'endpoint personalizzato. Selezionare quindi il modello personalizzato che si desidera associare all'endpoint.  È anche possibile abilitare la registrazione da questa pagina. La registrazione consente di monitorare il traffico dell'endpoint. Se la registrazione è disabilitata, il traffico non verrà archiviato.

![Screenshot che mostra la pagina nuovo endpoint.](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Non dimenticare di accettare le condizioni per l'utilizzo e i dettagli relativi ai prezzi.

Quindi selezionare **Crea**. Questa azione consente di tornare alla pagina **distribuzione** . La tabella include ora una voce che corrisponde all'endpoint personalizzato. Lo stato dell'endpoint Mostra lo stato corrente. Possono essere necessari fino a 30 minuti per creare un'istanza di un nuovo endpoint usando i modelli personalizzati. Quando lo stato della distribuzione diventa **completato**, l'endpoint è pronto per l'utilizzo.

Dopo la distribuzione dell'endpoint, il nome dell'endpoint viene visualizzato come collegamento. Selezionare il collegamento per visualizzare informazioni specifiche per l'endpoint, ad esempio la chiave dell'endpoint, l'URL dell'endpoint e il codice di esempio.

## <a name="view-logging-data"></a>Visualizzare i dati di registrazione

I dati di registrazione sono disponibili per **Endpoint** il download in  >  **Dettagli** endpoint.
> [!NOTE]
>I dati di registrazione sono disponibili per 30 giorni nell'archiviazione di proprietà di Microsoft. Verrà rimosso in seguito. Se un account di archiviazione di proprietà del cliente è collegato alla sottoscrizione di servizi cognitivi, i dati di registrazione non verranno eliminati automaticamente.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come usare il modello personalizzato](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Preparare e testare i dati](./how-to-custom-speech-test-and-train.md)
- [Esaminare i dati](how-to-custom-speech-inspect-data.md)
- [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
