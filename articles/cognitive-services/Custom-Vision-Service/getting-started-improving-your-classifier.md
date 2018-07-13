---
title: Migliorare il classificatore con il servizio Visione personalizzata - Servizi cognitivi di Azure | Microsoft Docs
description: Informazioni su come migliorare la qualità del classificatore del servizio Visione personalizzata.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374724"
---
# <a name="how-to-improve-your-classifier"></a>Come migliorare il classificatore

Informazioni su come migliorare la qualità del classificatore del servizio Visione personalizzata. La qualità del classificatore dipende dalla qualità dei dati con etichette forniti in esso. 

## <a name="train-more-varied-images"></a>Eseguire il training di immagini più variegate

Se si forniscono immagini con tag con diverse angolazioni, sfondi, dimensioni degli oggetti, gruppi di foto e altre varianti, la funzione di classificazione migliora. Le foto nel contesto sono preferibili rispetto alle foto con sfondi neutri. Includere immagini rappresentative di ciò che verrà inviato al classificatore durante il normale utilizzo.

Per altre informazioni sull'aggiunta di immagini, vedere il documento [Creare un classificatore](getting-started-build-a-classifier.md).

> [!IMPORTANT]
> Ricordare di eseguire il training del classificatore dopo aver aggiunto le immagini.

## <a name="use-images-submitted-for-prediction"></a>Usare le immagini inviate per la stima

Il servizio Visione personalizzata archivia le immagini inviate all'endpoint di stima. Per usare queste immagini per migliorare il classificatore procedere come segue:

1. Per visualizzare le immagini inviate al classificatore aprire la [pagina Web di Visione personalizzata](https://customvision.ai) e selezionare la scheda __Predictions__ (Stime).

    ![Immagine della scheda predictions (stime)](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > La visualizzazione predefinita mostra le immagini dall'iterazione corrente. È possibile usare l'elenco a discesa __Iterazione__ per visualizzare le immagini inviate durante le iterazioni precedenti.

2. Passare il mouse su un'immagine per visualizzare i tag stimati dal classificatore.

    > [!TIP]
    > Le immagini vengono classificate in modo che quelle più vantaggiose per il classificatore siano al primo posto. Per selezionare un ordinamento diverso usare la sezione __Ordinamento__.

    Per aggiungere un'immagine ai dati di training, scegliere l'immagine, selezionare il tag e quindi __Salva e chiudi__. L'immagine viene rimossa da __Predictions__ (Stime) e aggiunta alle immagini di training. È possibile visualizzarla selezionando la scheda __Training Images__ (Immagini di training).

    ![Immagine della pagina dei tag](./media/getting-started-improving-your-classifier/tag-image.png)

3. Usare il pulsante __Train__ (Eseguire il training) per ripetere il training del classificatore.

## <a name="visually-inspect-predictions"></a>Controllare visivamente le stime

Per controllare le stime delle immagini, selezionare la scheda __Training Images__ (Immagini di training) e quindi selezionare __Iteration History__ (Cronologia iterazioni). Le immagini che vengono delineate con una casella rossa sono state stimate in modo errato.

![Immagine della cronologia delle iterazioni](./media/getting-started-improving-your-classifier/iteration-history.png)

A volte l'ispezione visiva può identificare i modelli che è possibile correggere aggiungendo altri dati di training. Ad esempio, un classificatore per rose e margherite può etichettare erroneamente tutte le rose bianche come margherite. Si potrebbe risolvere il problema aggiungendo e fornendo dati di training che contengono immagini con tag delle rose bianche.

## <a name="unexpected-classification"></a>Classificazione imprevista

In alcuni casi il classificatore apprende le caratteristiche che le immagini hanno in comune. Ad esempio, si intende creare un classificatore per rose e tulipani. Si forniscono immagini di tulipani nei campi e di rose in un vaso rosso davanti a una parete blu. Con questi dati, il classificatore potrebbe eseguire il training per il campo e la parete + il vaso anziché per le rose e i tulipani.

Per risolvere il problema, usare il materiale sussidiario sul training con più immagini differenti: fornire immagini con diverse angolazioni, sfondi, dimensioni degli oggetti, gruppi e altre varianti.

## <a name="negative-image-handling"></a>Gestione delle immagini negative

Il servizio Visione personalizzata supporta alcune operazioni di gestione automatica delle immagine negative. Se si sta compilando un classificatore di cani e gatti e per la stima si invia l'immagine di una scarpa, il classificatore deve segnare un punteggio per quell'immagine più vicino possibile allo 0% per cane e gatto. 

> [!WARNING]
> L'approccio automatico funziona per le immagini chiaramente negative. Potrebbe non funzionare bene nei casi in cui le immagini negative siano solo una variante delle immagini usate nel training. 
>
> Per esempio, se si ha un classificatore di cani Husky e Corgi, e si inserisce l'immagine di un Pomerano, può valutare il Pomerano come Husky. In presenza di immagini negative di questo tipo, creare un nuovo tag (ad esempio, "Altro") e applicarlo alle immagini di training negative.

## <a name="next-steps"></a>Passaggi successivi

[Usare l'API delle stime](use-prediction-api.md)