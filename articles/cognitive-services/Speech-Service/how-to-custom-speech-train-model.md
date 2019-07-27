---
title: Eseguire il training di un modello per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Il training di un riconoscimento vocale è necessario per migliorare l'accuratezza del riconoscimento per il modello di base di Microsoft o per un modello personalizzato che si sta pianificando di creare. Viene eseguito il training di un modello usando trascrizioni con etichetta umana e testo correlato. Questi set di dati, insieme ai dati audio caricati in precedenza, vengono usati per perfezionare ed eseguire il training del modello di riconoscimento vocale per riconoscere parole, frasi, acronimi, nomi e altri termini specifici del prodotto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b5893b4f07444b07bf142971a5df4776e549d307
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562807"
---
# <a name="train-a-model-for-custom-speech"></a>Eseguire il training di un modello per Riconoscimento vocale personalizzato

Il training di un riconoscimento vocale è necessario per migliorare l'accuratezza del riconoscimento per il modello di base di Microsoft o per un modello personalizzato che si sta pianificando di creare. Viene eseguito il training di un modello usando trascrizioni con etichetta umana e testo correlato. Questi set di dati, insieme ai dati audio caricati in precedenza, vengono usati per perfezionare ed eseguire il training del modello di riconoscimento vocale per riconoscere parole, frasi, acronimi, nomi e altri termini specifici del prodotto. Maggiore è il numero di set di dati nel dominio forniti, ovvero i dati correlati a ciò che gli utenti indicheranno e cosa si prevede di riconoscere, maggiore sarà l'accuratezza del modello, con conseguente miglioramento del riconoscimento. Tenere presente che, inserendo dati non correlati nel training, è possibile ridurre o danneggiare l'accuratezza del modello.

## <a name="use-training-to-resolve-accuracy-issues"></a>Usare il training per risolvere i problemi di accuratezza

Se si riscontrano problemi di riconoscimento con il modello, l'uso di trascrizioni con etichetta umana e dati correlati per un training aggiuntivo può contribuire a migliorare la precisione. Usare questa tabella per determinare il set di dati da usare per risolvere i problemi:

| Caso d'uso | Tipo di dati |
|----------|-----------|
| Migliorare l'accuratezza del riconoscimento sul vocabolario e sulla grammatica specifici del settore, ad esempio la terminologia medica o il gergo IT | Testo correlato (frasi/espressioni) |
| Definire il form fonetico e visualizzato di una parola o di un termine con una pronuncia non standard, ad esempio nomi di prodotto o acronimi. | Testo correlato (pronuncia) |
| Migliorare l'accuratezza del riconoscimento su stili di pronuncia, accenti o rumori di fondo specifici | Trascrizioni audio + con etichetta umana |
> [!IMPORTANT]
> Se non è stato caricato un set di dati, vedere [preparare e testare i dati](how-to-custom-speech-test-data.md). In questo documento vengono fornite istruzioni per il caricamento dei dati e le linee guida per la creazione di set di dati di alta qualità.

## <a name="train-and-evaluate-a-model"></a>Eseguire il training e valutare un modello

Il primo passaggio per eseguire il training di un modello consiste nel caricare i dati di training. Usare [preparare e testare i dati](how-to-custom-speech-test-data.md) per istruzioni dettagliate per preparare le trascrizioni con etichetta umana e il testo correlato (espressioni e pronunce). Dopo aver caricato i dati di training, seguire queste istruzioni per avviare il training del modello:

1. Passare a **riconoscimento vocale > Riconoscimento vocale personalizzato > Training**.
2. Fare clic su **Train Model**.
3. Successivamente, assegnare un **nome** e una **Descrizione**al training.
4. Dal menu a discesa **scenario e modello di base** selezionare lo scenario più adatto al dominio. Se non si è certi dello scenario da scegliere, selezionare **generale**. Il modello di base è il punto di partenza per il training. Se non si ha una preferenza, è possibile usare la versione più recente.
5. Dalla pagina **selezione dati di training** scegliere uno o più set di dati di trascrizione audio + con etichetta umana che si vuole usare per il training.
6. Una volta completato il training, è possibile scegliere di eseguire test di accuratezza sul modello appena sottoposto a training. Questo passaggio è facoltativo.
7. Selezionare **Crea** per compilare il modello personalizzato.

Nella tabella training viene visualizzata una nuova voce che corrisponde al modello appena creato. La tabella Visualizza anche lo stato:  Elaborazione, riuscita, non riuscita.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Valutare l'accuratezza di un modello sottoposto a training

È possibile esaminare i dati e valutare l'accuratezza del modello usando i documenti seguenti:

* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)


Se si sceglie l'accuratezza dei test, è importante selezionare un set di dati acustico diverso da quello usato con il modello per ottenere un senso realistico delle prestazioni del modello.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
