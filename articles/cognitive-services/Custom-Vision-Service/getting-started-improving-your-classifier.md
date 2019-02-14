---
title: Miglioramento del classificatore - Servizio visione artificiale personalizzato
titlesuffix: Azure Cognitive Services
description: Informazioni su come migliorare la qualità del classificatore.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: pafarley
ms.openlocfilehash: ae8a96c07467a4c8617b8e7983e8dd7ad6e70be4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883458"
---
# <a name="how-to-improve-your-classifier"></a>Come migliorare il classificatore

Informazioni su come migliorare la qualità del classificatore del servizio Visione personalizzata. La qualità del classificatore dipende dalla quantità, qualità e varietà dei dati con etichetta forniti in esso e da come è bilanciato il set di dati. Un buon classificatore ha in genere un set di dati di training bilanciato rappresentativo di ciò che verrà inviato al classificatore. Il processo di compilazione di un classificatore di questo tipo è spesso iterativo. È comune effettuare alcuni cicli di training per raggiungere i risultati previsti.

Ecco i passaggi comuni eseguiti per migliorare un classificatore. Questi passaggi non sono regole rigide e veloci, ma euristica che consentirà di creare un classificatore migliore.

1. Primo ciclo di training
1. Aggiungere altre immagini e bilanciare i dati
1. Ripetere il training
1. Aggiungere immagini con sfondi, illuminazione, dimensioni degli oggetti, angolazioni e stili diversi
1. Ripetere il training e riacquisire le immagini per la stima
1. Esaminare i risultati della stima
1. Modificare i dati di training esistenti

## <a name="data-quantity-and-data-balance"></a>Quantità e bilanciamento dei dati

La cosa più importante consiste nel caricare immagini sufficienti per il training del classificatore. Come inizio, si consiglia di avere per il set di training almeno 50 immagini per etichetta. Con un numero inferiore di immagini, sussiste un alto rischio di overfitting. Mentre i numeri relativi alle prestazioni potrebbero segnalare una buona qualità, si potrebbero incontrare delle difficoltà con i dati reali. Il training del classificatore con un maggior numero di immagini aumenterà l'accuratezza generale dei risultati della stima.

Tenere anche presente che è necessario assicurarsi che i dati siano bilanciati. Ad esempio, disporre di 500 immagini per un'etichetta e di 50 immagini per un'altra etichetta produrrà un set di dati di training non bilanciato e il modello effettuerà una previsione più accurata di una etichetta rispetto all'altra. È possibile ottenere risultati migliori se si mantiene un rapporto di almeno 1:2 tra l'etichetta con il minor numero di immagini e l'etichetta con il maggior numero di immagini. Ad esempio, se l'etichetta con il maggior numero di immagini contiene 500 immagini, l'etichetta con il numero inferiore di immagini deve disporre per il training di almeno 250 immagini.

## <a name="train-more-diverse-images"></a>Eseguire il training di immagini diverse tra loro

Fornire immagini rappresentative di ciò che verrà inviato al classificatore durante l'uso normale. Ad esempio, se si sta eseguendo il training di un classificatore di "mele", questo potrebbe non risultare particolarmente accurato se si esegue il training solo di foto di mele in piatti e si effettuano stime su foto di mele sugli alberi. Includere una varietà di immagini per evitare valutazioni parziali del classificatore e ottenere una buona generalizzazione. Di seguito sono illustrati alcuni modi in cui è possibile diversificare il set di training:

__Contesto__: fornire immagini dell'oggetto con diversi sfondi (ovvero frutta sul piatto rispetto a frutta nella borsa della spesa). Le foto in contesto sono migliori delle foto con sfondi neutri in quanto forniscono informazioni aggiuntive al classificatore.

![Immagine di esempi di sfondo](./media/getting-started-improving-your-classifier/background.png)

__Illuminazione:__ fornire immagini con diversi tipi di illuminazione, ad esempio eseguite con flash, con un'esposizione alta e così via, soprattutto se le immagini usate per la stima hanno illuminazioni diverse. È inoltre utile includere le immagini con saturazione, tonalità e luminosità varie.

![Immagine di esempi di illuminazione](./media/getting-started-improving-your-classifier/lighting.png)

__Dimensioni dell'oggetto:__ fornire immagini in cui gli oggetti sono di dimensioni diverse con acquisizione di parti diverse dell'oggetto. Ad esempio, una foto di un casco di banane e un primo piano di una banana. Le diverse dimensioni consentono una migliore generalizzazione da parte del classificatore.

![Immagine di esempi di dimensioni](./media/getting-started-improving-your-classifier/size.png)

__Angolazione:__ fornire immagini scattate con diverse angolazioni. Se tutte le foto sono state eseguite con un set di telecamere fisse, ad esempio telecamere di sorveglianza, assicurarsi di assegnare un'etichetta diversa a ciascuna delle telecamere, anche se acquisiscono gli stessi oggetti per evitare l'overfitting: modellazione di oggetti non correlati, ad esempio lampioni come funzionalità chiave.

![Immagine di esempi di angolazione](./media/getting-started-improving-your-classifier/angle.png)

__Stile:__ fornire immagini con stili diversi della stessa classe, ad esempio diversi tipi di agrumi. Tuttavia, se si dispone di immagini di oggetti con stili drasticamente diversi (vale a dire, un'immagine di Mickey Mouse rispetto a un topo vero), è consigliabile etichettarle come classi separate in modo da rappresentare meglio le caratteristiche distinte.

![Immagine di esempi di stile](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-images-submitted-for-prediction"></a>Usare le immagini inviate per la stima

Il servizio Visione personalizzata archivia le immagini inviate all'endpoint di stima. Per usare queste immagini per migliorare il classificatore procedere come segue:

1. Per visualizzare le immagini inviate al classificatore aprire la [pagina Web di Visione personalizzata](https://customvision.ai), andare al progetto e selezionare la scheda __Predictions__ (Stime). La visualizzazione predefinita mostra le immagini dall'iterazione corrente. È possibile usare l'elenco a discesa __Iteration__ (Iterazione) per visualizzare le immagini inviate durante le iterazioni precedenti.

    ![Immagine della scheda predictions (stime)](./media/getting-started-improving-your-classifier/predictions.png)

2. Passare il mouse su un'immagine per visualizzare i tag previsti dal classificatore. Le immagini vengono classificate in modo visualizzare in alto quelle più vantaggiose per il classificatore. Per selezionare un ordinamento diverso, usare la sezione __Sort__ (Ordinamento). Per aggiungere un'immagine ai dati di training esistenti, scegliere l'immagine, selezionare il tag corretto e quindi fare clic su __Salva e chiudi__. L'immagine verrà rimossa da __Predictions__ (Stime) e aggiunta alle immagini di training. È possibile visualizzarla selezionando la scheda __Training Images__ (Immagini di training).

    ![Immagine della pagina dei tag](./media/getting-started-improving-your-classifier/tag.png)

3. Usare il pulsante __Train__ (Eseguire il training) per ripetere il training del classificatore.

## <a name="visually-inspect-predictions"></a>Controllare visivamente le stime

Per controllare le stime delle immagini, selezionare la scheda __Training Images__ (Immagini di training) e quindi selezionare __Iteration History__ (Cronologia iterazioni). Le immagini che vengono delineate con una casella rossa sono state stimate in modo errato.

![Immagine della cronologia delle iterazioni](./media/getting-started-improving-your-classifier/iteration.png)

A volte l'ispezione visiva può identificare modelli che è possibile correggere aggiungendo altri dati di training o modificando quelli esistenti. Un classificatore di mele e lime potrebbe ad esempio etichettare erroneamente tutte le mele verdi come lime. Si potrebbe risolvere il problema aggiungendo e fornendo dati di training che contengono immagini con tag di mele verdi.

## <a name="unexpected-classification"></a>Classificazione imprevista

In alcuni casi il classificatore apprende in modo errato le caratteristiche che le immagini hanno in comune. Ad esempio, se si sta creando un classificatore per mele e agrumi e vengono fornite immagini di mele in mano e di agrumi su piatti bianchi, il classificatore potrebbe eseguire il training di mani e di piatti bianchi anziché di mele e agrumi.

![Immagine di classificazione imprevista](./media/getting-started-improving-your-classifier/unexpected.png)

Per risolvere il problema, usare il materiale sussidiario precedente sul training con più immagini differenti: fornire immagini con diverse angolazioni, sfondi, dimensioni degli oggetti, gruppi e altre varianti.

## <a name="negative-image-handling"></a>Gestione delle immagini negative

Il servizio Visione personalizzata supporta alcune operazioni di gestione automatica delle immagine negative. Se si sta compilando un classificatore di acini d'uva e banane e per la stima si invia l'immagine di una scarpa, il classificatore deve segnare un punteggio per quell'immagine più vicino possibile allo 0%, sia per gli acini d'uva che per le banane.

D'altra parte, nei casi in cui le immagini negative sono soltanto una variazione delle immagini usate nel training, è probabile che il modello classificherà le immagini negative come una classe con etichetta a causa delle molte analogie. Ad esempio, se si dispone di un classificatore di arance e di pompelmi e si invia un'immagine di una clementina, è possibile che la clementina sia classificata come una arancia. Questo problema può verificarsi poiché molte caratteristiche della clementina (il colore, la consistenza e l'habitat naturale) sono simili a quelle delle arance.  Se le immagini negative sono di questo tipo, è consigliabile creare uno o più tag separati ("Altro") e assegnare un'etichetta alle immagini negative con questo tag durante il training per consentire al modello di distinguere meglio queste classi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come eseguire il test delle immagini a livello di codice inviandole all'API delle stime.

> [!div class="nextstepaction"]
[Usare l'API delle stime](use-prediction-api.md)
