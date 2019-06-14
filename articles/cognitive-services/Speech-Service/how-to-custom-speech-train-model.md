---
title: Eseguire il training di un modello per il riconoscimento vocale personalizzato - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Training di un riconoscimento vocale-riconoscimento è necessario per migliorare la precisione del riconoscimento per entrambi i modelli di base di Microsoft o un modello personalizzato che si prevede di creare. Un modello viene eseguito mediante un'etichetta umane trascrizioni e testo correlato. Questi set di dati insieme ai dati audio caricati in precedenza, vengono usati per rifinire ed eseguire il training del modello di riconoscimento vocale per riconoscere le parole, frasi, gli acronimi, nomi e altre condizioni specifiche del prodotto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: e12cef052db6aabad94b47283eda11f60f3b2b13
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063924"
---
# <a name="train-a-model-for-custom-speech"></a>Eseguire il training di un modello per il riconoscimento vocale personalizzato

Training di un riconoscimento vocale-riconoscimento è necessario per migliorare la precisione del riconoscimento per entrambi i modelli di base di Microsoft o un modello personalizzato che si prevede di creare. Un modello viene eseguito mediante un'etichetta umane trascrizioni e testo correlato. Questi set di dati insieme ai dati audio caricati in precedenza, vengono usati per rifinire ed eseguire il training del modello di riconoscimento vocale per riconoscere le parole, frasi, gli acronimi, nomi e altre condizioni specifiche del prodotto. I più nel dominio set di dati specificata dall'utente (i dati relativi a ciò che verranno indicato che gli utenti e ciò che si prevede di riconoscere), il più accurati che sarà il modello, con conseguente miglioramento riconoscimento. Tenere presente, inserendo i dati non correlati il training, è possibile ridurre o ridurre l'accuratezza del modello.

## <a name="use-training-to-resolve-accuracy-issues"></a>Usare set di training per risolvere i problemi di accuratezza

Se si potrebbero verificarsi problemi di riconoscimento con il modello, usando un'etichetta umane trascrizioni e i relativi dati di training aggiuntivi possono contribuire a migliorare la precisione. Usare questa tabella per determinare quale set di dati da utilizzare per risolvere i problemi:

| Caso d'uso | Tipo di dati | Quantità di dati |
|----------|-----------|---------------|
| I nomi propri sono riconosciuta correttamente | Testo (le frasi o espressioni) | 10 MB a 500 MB |
| Le parole sono riconosciuta correttamente a causa di una distinzione tra caratteri accentati | Testo correlato (pronuncia) | Specificare le parole misrecognized |
| Le parole comuni vengono eliminate o utente | Trascrizioni audio + con etichetta umane | ore di trascrizione di 10 a 1.000 |

> [!IMPORTANT]
> Se è stato ancora caricato un set di dati, vedere [prepara e i dati di test](how-to-custom-speech-test-data.md). Questo documento fornisce istruzioni per il caricamento dei dati e le linee guida per la creazione di set di dati di alta qualità.

## <a name="train-and-evaluate-a-model"></a>Eseguire il training e valutare un modello

Il primo passaggio per addestrare un modello consiste nel caricare i dati di training. Uso [prepara e i dati di test](how-to-custom-speech-test-data.md) per istruzioni dettagliate per preparare un'etichetta umane trascrizioni e il testo correlato (espressioni e le pronunce). Dopo aver caricato i dati di training, seguire queste istruzioni per avviare il modello di training:

1. Passare a **vocale-> riconoscimento vocale personalizzato > formazione**.
2. Fare clic su **Train model**.
3. Successivamente, offrono la formazione una **Name** e **descrizione**.
4. Dal **Scenario e linea di base modello** dal menu a discesa, selezionare lo scenario che meglio si adatta al dominio. Se è certi di quale scenario di scegliere, selezionare **generali**. Il modello di linea di base è il punto di partenza per il training. Se non si ha una preferenza, è possibile usare la versione più recente.
5. Dal **selezionare i dati di training** pagina, scegliere uno o più trascrizione audio + con etichetta umane i set di dati che si desidera usare per il training.
6. Una volta completato il training, è possibile scegliere di eseguire test sul modello appena sottoposto a training di accuratezza. Questo passaggio è facoltativo.
7. Selezionare **Create** per compilare il modello personalizzato.

La tabella di formazione consente di visualizzare una nuova voce che corrisponde a questo nuovo modello. La tabella viene anche visualizzato lo stato:  L'elaborazione, completato, non è riuscita.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Valutare l'accuratezza di un modello con training

È possibile esaminare i dati e valutare l'accuratezza del modello di utilizzo di questi documenti:

* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)


Se si sceglie di testare l'accuratezza, è importante selezionare un set di dati acustici che è diverso da quello utilizzato con il modello e farsi un'idea realistica delle prestazioni del modello.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Il training del modello](how-to-custom-speech-train-model.md)
