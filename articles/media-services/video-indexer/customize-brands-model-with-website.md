---
title: Usare il sito Web di Video Indexer per personalizzare un modello di Marchi - Azure
titlesuffix: Azure Media Services
description: Questo articolo illustra come personalizzare un modello di Marchi con il sito Web di Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 2522ede85c290fa238c0d5a5604d2dfbab984cdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60535547"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Personalizzare un modello di Marchi con il sito Web di Video Indexer

Video Indexer supporta il rilevamento del marchio dal parlato e dal testo visivo durante l'indicizzazione e la reindicizzazione di contenuto audio e video. La funzionalità di rilevamento del marchio identifica citazioni di prodotti, servizi e aziende suggerite dal database dei marchi di Bing. Se, ad esempio, Microsoft viene citata in un contenuto audio o video o se appare nel testo visivo di un video, Video Indexer lo rileva come un marchio. Un modello dei marchi personalizzato consente di selezionare se Video Indexer dovrà fare riferimento al database dei marchi di Bing, escludere determinati marchi dal rilevamento (essenzialmente creando un elenco di marchi non consentiti) e includere i marchi che devono far parte del modello ma che potrebbero non essere nel database dei marchi di Bing (essenzialmente creando un elenco di marchi consentiti).

Per una panoramica dettagliata, vedere [Panoramica](customize-brands-model-overview.md).

È possibile usare il sito Web di Video Indexer per creare, usare e modificare modelli di Marchi personalizzati rilevati in un video, come descritto in questo argomento. È anche possibile usare l'API, come descritto in [Personalizzare il modello di Marchi usando le API](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Modificare le impostazioni del modello di Marchi  

È possibile scegliere se rilevare o meno i marchi dal database dei marchi di Bing. A tale scopo, è necessario modificare le impostazioni del modello di Marchi.

1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
2. Per personalizzare un modello nel proprio account, fare clic sul pulsante **Content model customization** (Personalizzazione del modello di contenuto) nell'angolo in alto a destra della pagina.
 
   ![Personalizzare il modello di contenuto](./media/content-model-customization/content-model-customization.png) 
3. Per modificare i marchi, selezionare la scheda **Marchi**.

    ![Personalizzare il modello di marchi](./media/customize-brand-model/customize-brand-model.png)
4. Spuntare l'opzione **Show brands suggested by Bing** (Mostra i marchi suggeriti da Bing) perché Video Indexer includa i marchi suggeriti da Bing. Lasciare deselezionata l'opzione se non si desidera che Video Indexer rilevi i marchi suggeriti da Bing nel contenuto. 

## <a name="include-brands-in-the-model"></a>Includere i marchi nel modello

La sezione **Includi marchi** rappresenta i marchi personalizzati che Video Indexer deve rilevare, anche se non vengono offerti suggerimenti da Bing.  

### <a name="add-a-brand"></a>Aggiungere un marchio

1. Fare clic su "+ Aggiungi marchio".

    ![Personalizzare il modello di marchi](./media/customize-brand-model/add-brand.png)

    Specificare un nome (obbligatorio), una categoria (facoltativa), una descrizione (facoltativa) e un URL di riferimento (facoltativo).
    Il campo categoria è pensato per facilitare l'aggiunta di tag ai marchi. Questo campo viene visualizzato come *tag* del marchio quando si usano le API di Video Indexer. Ad esempio, il marchio "Azure" può essere contrassegnato o classificato come "Cloud".

    Il campo URL di riferimento può contenere qualsiasi sito Web di riferimento per il marchio, ad esempio un collegamento alla relativa pagina di Wikipedia.
2. Facendo clic su "Aggiungi marchio", si noterà che il marchio è stato aggiunto all'elenco **Includi marchi**.

### <a name="edit-a-brand"></a>Modificare un marchio

1. Fare clic sull'icona a forma di matita accanto al marchio da modificare.

    È possibile aggiornare la categoria, la descrizione o l'URL di riferimento di un marchio. Non è possibile modificare il nome di un marchio perché i nomi di marchio sono univoci. Se è necessario modificare il nome del marchio, eliminare l'intero marchio (vedere la sezione successiva) e creare un nuovo marchio con il nuovo nome.
2. Fare clic sul pulsante **Aggiorna** per aggiornare il marchio con le nuove informazioni.

### <a name="delete-a-brand"></a>Eliminare un marchio

1. Fare clic sull'icona del cestino accanto al marchio da eliminare.
2. Facendo clic su "Elimina", il marchio non verrà più visualizzato nell'elenco *Includi marchi*.

## <a name="exclude-brands-from-the-model"></a>Escludere un marchio dal modello

La sezione **Escludi marchi** rappresenta i marchi che Video Indexer non deve rilevare.

### <a name="add-a-brand"></a>Aggiungere un marchio

1. Fare clic su "+ Aggiungi marchio".

    Specificare un nome (obbligatorio) e una categoria (facoltativa).
2. Facendo clic su "Aggiungi marchio", si noterà che il marchio è stato aggiunto all'elenco *Escludi marchi*.

### <a name="edit-a-brand"></a>Modificare un marchio

1. Fare clic sull'icona a forma di matita accanto al marchio da modificare.

    È possibile aggiornare solo la categoria di un marchio. Non è possibile modificare il nome di un marchio perché i nomi di marchio sono univoci. Se è necessario modificare il nome del marchio, eliminare l'intero marchio (vedere la sezione successiva) e creare un nuovo marchio con il nuovo nome.
2. Fare clic sul pulsante **Aggiorna** per aggiornare il marchio con le nuove informazioni.

### <a name="delete-a-brand"></a>Eliminare un marchio

1. Fare clic sull'icona del cestino accanto al marchio da eliminare.
2. Facendo clic su "Elimina", il marchio non verrà più visualizzato nell'elenco *Escludi marchi*.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare il modello di Marchi tramite API](customize-brands-model-with-api.md)
