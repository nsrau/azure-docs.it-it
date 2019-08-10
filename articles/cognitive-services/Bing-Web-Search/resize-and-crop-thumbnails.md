---
title: Ridimensionare e ritagliare le anteprime delle immagini - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come ridimensionare e ritagliare le anteprime fornite dal API di ricerca Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: ecc6eb86e7115143fa63b44f9191b1fe8d3703b8
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881808"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ridimensionare e ritagliare le immagini di anteprima

Alcune risposte dalla API di ricerca Bing includono gli URL per le immagini di anteprima gestite da Bing, che è possibile ridimensionare e ritagliare e possono contenere parametri di query. Ad esempio:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Se si visualizza un subset di queste anteprime, specificare un'opzione per visualizzare le immagini rimanenti.

> [!NOTE]
> Assicurarsi che le immagini di anteprima di ritaglio e ridimensionamento forniscano uno scenario di ricerca che rispetta i diritti di terze parti, come richiesto dai [requisiti di utilizzo e visualizzazione](use-display-requirements.md)dell'API ricerca Bing.

## <a name="resize-a-thumbnail"></a>Ridimensionare un'anteprima 

Per ridimensionare un'anteprima, Bing consiglia di specificare solo un `w` parametro di query (width) o `h` (Height) nell'URL dell'anteprima. Se si specifica solo l'altezza o la larghezza, Bing mantiene l'aspetto originale dell'immagine. Specificare la larghezza e l'altezza in pixel. 

Ad esempio, se l'anteprima originale è 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

E si desidera ridurne le dimensioni, impostare il `w` parametro su un nuovo valore (ad esempio `336`) e rimuovere il `h` parametro:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Se si specifica solo l'altezza o la larghezza di un'anteprima, le proporzioni originali dell'immagine verranno mantenute. Se si specificano entrambi i parametri e le proporzioni non vengono mantenute, Bing aggiungerà spaziatura bianca al bordo dell'immagine.

Se ad esempio si ridimensiona un'immagine 480x359 in 200x200 senza ritaglio, l'intera larghezza conterrà l'immagine, ma l'altezza conterrà 25 pixel di riempimento bianco nella parte superiore e inferiore dell'immagine. Se l'immagine è 359x480, i bordi sinistro e destro contengono spaziatura interna bianca. Se si ritaglia l'immagine, la spaziatura interna bianca non viene aggiunta.  

La figura seguente illustra le dimensioni originali di un'immagine di anteprima (480x300).  
  
![Immagine orizzontale originale](./media/resize-crop/bing-resize-crop-landscape.png)  
  
La figura seguente illustra l'immagine ridimensionata a 200x200. Le proporzioni vengono mantenute e i bordi superiore e inferiore vengono riempiti con il bianco (il bordo nero è incluso per mostrare la spaziatura interna).  
  
![Immagine orizzontale ridimensionata](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Se si specificano dimensioni maggiori della larghezza e dell'altezza originali dell'immagine, in Bing verrà aggiunta la spaziatura bianca ai bordi sinistro e superiore.  

## <a name="request-different-thumbnail-sizes"></a>Richiedi dimensioni di anteprima diverse

Per richiedere una dimensione diversa dell'immagine di anteprima, rimuovere tutti i parametri di query dall'URL dell'anteprima `id` , `pid` eccetto i parametri e. Quindi, aggiungere il `&w` parametro di query ( `&h` Width) o (Height) con la dimensione dell'immagine desiderata in pixel, ma non entrambi. Bing manterrà le proporzioni originali dell'immagine. 

Per aumentare la larghezza dell'immagine specificata dall'URL precedente a 165 pixel, usare l'URL seguente:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Se si richiede un'immagine di dimensioni superiori a quelle originali dell'immagine, Bing aggiunge la spaziatura bianca intorno all'immagine in base alle esigenze. Se, ad esempio, le dimensioni originali dell'immagine sono 474x316 e si `&w` imposta su 500, Bing restituirà un'immagine 500x333. Questa immagine avrà 8,5 pixel di riempimento bianco lungo i bordi superiore e inferiore e 13 pixel di riempimento sui bordi sinistro e destro.

Per impedire a Bing di aggiungere spaziatura interna bianca se le dimensioni richieste sono maggiori delle dimensioni originali dell'immagine, `&p` impostare il parametro di query su 0. Ad esempio, se si include il `&p=0` parametro nell'URL precedente, Bing restituirà un'immagine 474x316 anziché un'immagine 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Se si specificano `&w` entrambi `&h` i parametri di query e, Bing manterrà le proporzioni dell'immagine e aggiungerà la spaziatura interna in base alle esigenze. Se, ad esempio, le dimensioni originali dell'immagine sono 474x316 e si impostano i parametri width e Height`&w=200&h=200`su 200x200 (), Bing restituisce un'immagine che contiene 33 pixel di riempimento bianco nella parte superiore e inferiore. Se si include il `&p` parametro di query, Bing restituisce un'immagine 200x134.

## <a name="crop-a-thumbnail"></a>Ritagliare un'anteprima 

Per ritagliare un'immagine, includere `c` il parametro di query (crop). È possibile utilizzare i valori seguenti:
  
- `4`&mdash; Percentuale di cecità  
- `7`&mdash; Rapporto intelligente  

### <a name="smart-ratio-cropping"></a>Ritaglio con proporzioni intelligenti

Se si richiede il ritaglio con proporzioni intelligenti `c` , impostando il parametro su `7`, Bing ridurrà un'immagine dal centro dell'area di interesse verso l'esterno, mantenendo al tempo stesso le proporzioni dell'immagine. L'area di interesse è la parte dell'immagine che secondo Bing contiene gli elementi più importanti. Il seguente è un esempio di area di interesse.  
  
![Area di interesse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Se si ridimensiona un'immagine e si richiede il ritaglio con proporzioni intelligenti, Bing riduce l'immagine alla dimensione richiesta mantenendo le proporzioni. Bing quindi ritaglia l'immagine in base alle dimensioni ridimensionate. Se, ad esempio, la larghezza ridimensionata è minore o uguale all'altezza, Bing ridurrà l'immagine a sinistra e a destra del centro dell'area di interesse. In caso contrario, Bing lo Ritaglia nella parte superiore e inferiore del centro dell'area di interesse.  
  
 
La seguente è l'immagine ridotta a 200x200 usando il ritaglio con proporzioni intelligenti. Poiché Bing misura l'immagine dall'angolo superiore sinistro, la parte inferiore dell'immagine viene ritagliata. 
  
![Immagine orizzontale ritagliata a 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
La seguente è l'immagine ridotta a 200x100 usando il ritaglio con proporzioni intelligenti. Poiché Bing misura l'immagine dall'angolo superiore sinistro, la parte inferiore dell'immagine viene ritagliata. 
   
![Immagine orizzontale ritagliata a 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
La seguente è l'immagine ridotta a 100x200 usando il ritaglio con proporzioni intelligenti. Poiché Bing misura l'immagine dal centro, le parti sinistra e destra dell'immagine vengono ritagliate.
  
![Immagine orizzontale ritagliata a 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Se Bing non è in grado di determinare l'area di interesse dell'immagine, il servizio utilizzerà il ritaglio con proporzioni cieche.  

### <a name="blind-ratio-cropping"></a>Ritaglio con proporzioni cieche

Se si richiede il ritaglio con proporzioni cieche ( `4`impostando il `c` parametro su), Bing usa le regole seguenti per ritagliare l'immagine.  
  
- Se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, l'immagine viene misurata dall'angolo superiore sinistro e ritagliata in basso.  
- Se `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, l'immagine viene misurata dal centro e ritagliata a sinistra e a destra.  

La seguente è un'immagine verticale 225x300.  
  
![Immagine di girasole originale](./media/resize-crop/bing-resize-crop-sunflower.png)
  
La seguente è l'immagine ridotta a 200x200 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dall'angolo superiore sinistro e la parte inferiore dell'immagine viene ritagliata.  
  
![Immagine di girasole ritagliata a 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
La seguente è l'immagine ridotta a 200x100 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dall'angolo superiore sinistro e la parte inferiore dell'immagine viene ritagliata.  
  
![Immagine di girasole ritagliata a 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
La seguente è l'immagine ridotta a 100x200 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dal centro e di conseguenza ne vengono ritagliate le parti sinistra e destra.  
  
![Immagine di girasole ritagliata a 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Passaggi successivi

* [Quali sono i API di ricerca Bing?](bing-api-comparison.md)
* [Requisiti per l'uso e la visualizzazione dell'API Ricerca Bing](use-display-requirements.md)
