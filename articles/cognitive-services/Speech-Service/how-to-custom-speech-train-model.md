---
title: Eseguire il training di un modello per il riconoscimento vocale personalizzato - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La formazione di un modello di sintesi vocale può migliorare l'accuratezza del riconoscimento per il modello di base di Microsoft o un modello personalizzato. Il training di un modello viene eseguito utilizzando trascrizioni con etichetta umana e testo correlato.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77137761"
---
# <a name="train-a-model-for-custom-speech"></a>Eseguire il training di un modello per Riconoscimento vocale personalizzato

La formazione di un modello di sintesi vocale può migliorare l'accuratezza del riconoscimento per il modello di base di Microsoft.Training a speech-to-text model can improve recognition accuracy for Microsoft's baseline model. Il training di un modello viene eseguito utilizzando trascrizioni con etichetta umana e testo correlato. Questi set di dati, insieme ai dati audio caricati in precedenza, vengono usati per perfezionare e addestrare il modello di sintesi vocale per riconoscere parole, frasi, acronimi, nomi e altri termini specifici del prodotto. Più set di dati nel dominio vengono forniti (dati correlati a ciò che gli utenti diranno e a ciò che ci si aspetta di riconoscere), più accurato sarà il modello, con conseguente miglioramento del riconoscimento. Tenere presente che inserendo dati non correlati nel training, è possibile ridurre o compromettere l'accuratezza del modello.

## <a name="use-training-to-resolve-accuracy-issues"></a>Utilizzare la formazione per risolvere i problemi di accuratezza

Se si verificano problemi di riconoscimento con il modello, l'uso di trascrizioni con etichettatura umana e dati correlati per un addestramento aggiuntivo può contribuire a migliorare l'accuratezza. Utilizzare questa tabella per determinare il set di dati da utilizzare per risolvere i problemi:Use this table to determine which dataset to use to address your issue(s):

| Caso d'uso | Tipo di dati |
| -------- | --------- |
| Migliora l'accuratezza del riconoscimento su vocabolario e grammatica specifici del settore, come la terminologia medica o il gergo IT. | Testo correlato (frasi/espressioni) |
| Definire la forma fonetica e visualizzata di una parola o di un termine con pronuncia non standard, ad esempio nomi di prodotti o acronimi. | Testo correlato (pronuncia) |
| Migliora la precisione del riconoscimento su stili di conversazione, accenti o rumori di fondo specifici. | Audio - Trascrizioni con etichetta tura umana |

> [!IMPORTANT]
> Se non è stato caricato un set di dati, vedere [Preparare e testare i dati](how-to-custom-speech-test-data.md). Questo documento fornisce istruzioni per il caricamento di dati e linee guida per la creazione di set di dati di alta qualità.

## <a name="train-and-evaluate-a-model"></a>Eseguire il training di un modello e valutarlo

Il primo passaggio per eseguire il training di un modello consiste nel caricare i dati di training. Utilizzare [Preparare e testare i dati](how-to-custom-speech-test-data.md) per istruzioni dettagliate per preparare trascrizioni con etichetta umana e testo correlato (espressioni e pronunce). Dopo aver caricato i dati di training, seguire queste istruzioni per iniziare il training del modello:After you've uploaded training data, follow these instructions to start training your model:

1. Accedere al [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **Formazione > > sintesi vocale > sintesi vocale personalizzata**.
3. Fare clic su **Formato modello**.
4. Assegnare quindi un **Nome** e **una Descrizione**alla formazione.
5. Dal menu a discesa **Modello scenario e modello di base,** selezionare lo scenario più adatto al dominio. Se non si è certi dello scenario da scegliere, selezionare **Generale**. Il modello di base è il punto di partenza per il training. Se non si dispone di una preferenza, è possibile utilizzare la più recente.
6. Nella pagina **Seleziona dati** di training scegliere uno o più set di dati di trascrizione con etichetta umana che si vuole usare per il training.
7. Una volta completato il training, è possibile scegliere di eseguire test di accuratezza sul modello appena sottoposto a training. Questo passaggio è facoltativo.
8. Selezionare **Crea** per creare il modello personalizzato.

Nella tabella Training viene visualizzata una nuova voce che corrisponde al modello appena creato. Nella tabella viene inoltre visualizzato lo stato: Elaborazione, Operazione riuscita, Operazione non riuscita.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Valutare l'accuratezza di un modello sottoposto a training

È possibile controllare i dati e valutare l'accuratezza del modello utilizzando questi documenti:

- [Ispezionare i dati](how-to-custom-speech-inspect-data.md)
- [Valutare i dati](how-to-custom-speech-evaluate-data.md)

Se si sceglie di testare l'accuratezza, è importante selezionare un set di dati acustico diverso da quello usato con il modello per ottenere un'idea realistica delle prestazioni del modello.

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Preparare e testare i dati](how-to-custom-speech-test-data.md)
- [Ispezionare i dati](how-to-custom-speech-inspect-data.md)
- [Valutare i dati](how-to-custom-speech-evaluate-data.md)
- [Eseguire il training del modello](how-to-custom-speech-train-model.md)
