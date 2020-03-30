---
title: Personalizzare un modello di Marchi con il sito Web di Video Indexer
titleSuffix: Azure Media Services
description: Scopri come personalizzare un modello Brands con il sito Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128049"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalizzare un modello di Marchi con il sito Web di Video Indexer

Video Indexer supporta il rilevamento del marchio dal parlato e dal testo visivo durante l'indicizzazione e la reindicizzazione di contenuto audio e video. La funzionalità di rilevamento del marchio identifica citazioni di prodotti, servizi e aziende suggerite dal database dei marchi di Bing. Ad esempio, se Microsoft è menzionata nei contenuti video o audio o se viene visualizzata nel testo visivo di un video, Video Indexer lo rileva come un marchio nel contenuto.

Un modello di Brand personalizzato consente di:

- selezionare questa opzione se si desidera che Video Indexer rilevi le marche dal database dei marchi Bing.
- Seleziona se desideri che L'Indicizzatore Video escluda determinate marche dal momento che vengono rilevate (essenzialmente creando un elenco di marche come se si desidera).
- Selezionare questa opzione se si desidera che Video Indexer includa i brand che devono far parte del modello che potrebbero non essere inclusi nel database dei marchi di Bing (essenzialmente creando un elenco accetta di marchi).

Per una panoramica dettagliata, vedere questa [panoramica](customize-brands-model-overview.md).

È possibile usare il sito Web di Video Indexer per creare, usare e modificare modelli di Marchi personalizzati rilevati in un video, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [Personalizzare il modello di Marchi usando le API](customize-brands-model-with-api.md).

## <a name="edit-brands-model-settings"></a>Modificare le impostazioni del modello Brands

È possibile scegliere se rilevare o meno i marchi dal database dei marchi di Bing. Per impostare questa opzione, devi modificare le impostazioni del tuo modello Brands. A tale scopo, seguire questa procedura:

1. Accedi al sito Web [Indicizzatore video.](https://www.videoindexer.ai/)
2. Per personalizzare un modello nel tuo account, seleziona il pulsante **di personalizzazione del modello** di contenuto nell'angolo in alto a destra della pagina.

   ![Personalizzare il modello di contenuto nell'indicizzatore video](./media/content-model-customization/content-model-customization.png)

3. Per modificare i marchi, selezionare la scheda **Marchi**.

    ![Personalizzare il modello di brand nell'indicizzatore video](./media/customize-brand-model/customize-brand-model.png)

4. Seleziona l'opzione **Mostra brand suggeriti da Bing** se desideri che L'Indicizzatore Video rilevi i brand suggeriti da Bing: lascia l'opzione deselezionata se non lo fai.

## <a name="include-brands-in-the-model"></a>Includere i marchi nel modello

La sezione **Includi brand** rappresenta i brand personalizzati che l'indicizzatore video deve rilevare, anche se non sono suggeriti da Bing.  

### <a name="add-a-brand-to-include-list"></a>Aggiungere un marchio per includere l'elenco

1. Selezionare **: Aggiungi marchio**.

    ![Personalizzare il modello di brand nell'indicizzatore video](./media/customize-brand-model/add-brand.png)

    Specificare un nome (obbligatorio), una categoria (facoltativa), una descrizione (facoltativa) e un URL di riferimento (facoltativo).
    Il campo categoria è pensato per facilitare l'aggiunta di tag ai marchi. Questo campo viene visualizzato come *tag* del marchio quando si usano le API di Video Indexer. Ad esempio, il marchio "Azure" può essere contrassegnato o classificato come "Cloud".

    Il campo URL di riferimento può essere qualsiasi sito web di riferimento per il brand (come un link alla sua pagina di Wikipedia).

2. Seleziona **Aggiungi marchio** e vedrai che il brand è stato aggiunto all'elenco Includi **marchi.**

### <a name="edit-a-brand-on-the-include-list"></a>Modificare un marchio nell'elenco dei componenti inclusi

1. Seleziona l'icona a matita accanto al brand che desideri modificare.

    È possibile aggiornare la categoria, la descrizione o l'URL di riferimento di un marchio. Non è possibile modificare il nome di un marchio perché i nomi di marchio sono univoci. Se è necessario modificare il nome del marchio, eliminare l'intero marchio (vedere la sezione successiva) e creare un nuovo marchio con il nuovo nome.

2. Seleziona il pulsante **Aggiorna** per aggiornare il brand con le nuove informazioni.

### <a name="delete-a-brand-on-the-include-list"></a>Eliminare un brand nell'elenco delle inclusioni

1. Seleziona l'icona del cestino accanto al brand che desideri eliminare.
2. Seleziona **Elimina** e il brand non verrà più visualizzato nell'elenco *Includi marchi.*

## <a name="exclude-brands-from-the-model"></a>Escludere un marchio dal modello

La sezione **Escludi marchi** rappresenta i marchi che non vuoi che l'Indicizzatore Video rilevi.

### <a name="add-a-brand-to-exclude-list"></a>Aggiungere un marchio per escludere l'elenco

1. Seleziona **: Aggiungi marca.**

    Specificare un nome (obbligatorio) e una categoria (facoltativa).

2. Seleziona **Aggiungi marchio** e vedrai che il brand è stato aggiunto all'elenco *Escludi marchi.*

### <a name="edit-a-brand-on-the-exclude-list"></a>Modificare un marchio nell'elenco di esclusione

1. Seleziona l'icona a matita accanto al brand che desideri modificare.

    È possibile aggiornare solo la categoria di un marchio. Non è possibile modificare il nome di un marchio perché i nomi di marchio sono univoci. Se è necessario modificare il nome del marchio, eliminare l'intero marchio (vedere la sezione successiva) e creare un nuovo marchio con il nuovo nome.

2. Seleziona il pulsante **Aggiorna** per aggiornare il brand con le nuove informazioni.

### <a name="delete-a-brand-on-the-exclude-list"></a>Eliminare un marchio nell'elenco di esclusione

1. Seleziona l'icona del cestino accanto al brand che desideri eliminare.
2. Seleziona **Elimina** e il brand non verrà più visualizzato nell'elenco *Escludi marchi.*

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello di Marchi tramite API](customize-brands-model-with-api.md)
