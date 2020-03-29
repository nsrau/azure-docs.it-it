---
title: Ridimensionare e ritagliare le anteprime delle immagini - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Alcune risposte delle API di Ricerca Bing includono gli URL delle immagini di anteprima servite da Bing, che è possibile ridimensionare e ritagliare e contenere parametri di query.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110630"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ridimensionare e ritagliare le immagini di anteprima

Alcune risposte delle API di Ricerca Bing includono gli URL delle immagini di anteprima servite da Bing, che è possibile ridimensionare e ritagliare e contenere parametri di query. Ad esempio:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Se visualizzate un sottoinsieme di queste miniature, fornite un'opzione per visualizzare le immagini rimanenti.

> [!NOTE]
> Assicurarsi che il ritaglio e il ridimensionamento delle immagini di anteprima forniscano uno scenario di ricerca che rispetti i diritti di terze parti, come richiesto dall'API Ricerca Bing e dai [requisiti di visualizzazione.](use-display-requirements.md)

## <a name="resize-a-thumbnail"></a>Ridimensionare una miniatura 

Per ridimensionare un'anteprima, Bing consiglia di `w` specificare `h` solo uno dei parametri di query (larghezza) o (altezza) nell'URL dell'anteprima. Specificando solo l'altezza o la larghezza, Bing può mantenere l'aspetto originale dell'immagine. Specificare la larghezza e l'altezza in pixel. 

Ad esempio, se la miniatura originale è 480x620:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

E si desidera ridurne le `w` dimensioni, impostare il `336`parametro `h` su un nuovo valore (ad esempio ) e rimuovere il parametro:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Se si specifica solo l'altezza o la larghezza di una miniatura, le proporzioni originali dell'immagine verranno mantenute. Se si specificano entrambi i parametri e le proporzioni non vengono mantenute, Bing aggiungerà spaziatura interna bianca al bordo dell'immagine.

Ad esempio, se si ridimensiona un'immagine 480x359 a 200x200 senza ritaglio, l'intera larghezza conterrà l'immagine ma l'altezza conterrà 25 pixel di riempimento bianco nella parte superiore e inferiore dell'immagine. Se l'immagine era 359x480, i bordi sinistro e destro conterrebbero la spaziatura interna bianca. Se si ritaglia l'immagine, la spaziatura interna bianca non viene aggiunta.  

La figura seguente illustra le dimensioni originali di un'immagine di anteprima (480x300).  
  
![Immagine orizzontale originale](./media/resize-crop/bing-resize-crop-landscape.png)  
  
La figura seguente illustra l'immagine ridimensionata a 200x200. Le proporzioni vengono mantenute e i bordi superiore e inferiore sono imbottiti di bianco (il bordo nero qui è incluso per mostrare l'imbottitura).  
  
![Immagine orizzontale ridimensionata](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Se si specificano dimensioni maggiori della larghezza e dell'altezza originali dell'immagine, Bing aggiungerà una spaziatura interna bianca ai bordi sinistro e superiore.  

## <a name="request-different-thumbnail-sizes"></a>Richiedere diverse dimensioni delle miniature

Per richiedere una dimensione dell'immagine di anteprima diversa, rimuovere `id` tutti `pid` i parametri di query dall'URL dell'anteprima, ad eccezione dei parametri e . Aggiungere quindi `&w` il parametro `&h` di query (larghezza) o (altezza) con le dimensioni dell'immagine desiderate in pixel, ma non entrambi. Bing manterrà le proporzioni originali dell'immagine. 

Per aumentare la larghezza dell'immagine specificata dall'URL precedente a 165 pixel, è necessario utilizzare il seguente URL:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Se si richiede un'immagine più grande delle dimensioni originali dell'immagine, Bing aggiunge una spaziatura interna bianca intorno all'immagine in base alle esigenze. Ad esempio, se le dimensioni originali dell'immagine sono 474x316 e si imposta `&w` su 500, Bing restituirà un'immagine 500x333. Questa immagine avrà 8,5 pixel di spaziatura bianca lungo i bordi superiore e inferiore e 13 pixel di riempimento sui bordi sinistro e destro.

Per impedire a Bing di aggiungere una spaziatura interna bianca se `&p` la dimensione richiesta è maggiore delle dimensioni originali dell'immagine, impostare il parametro di query su 0. Ad esempio, se `&p=0` si include il parametro nell'URL precedente, Bing restituirà un'immagine 474x316 anziché un'immagine 500x333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Se si `&w` specificano entrambi i parametri e `&h` i parametri di query, Bing manterrà le proporzioni dell'immagine e aggiungerà la spaziatura interna bianca in base alle esigenze. Ad esempio, se le dimensioni originali dell'immagine sono 474x316 e si impostano i`&w=200&h=200`parametri width e height su 200x200 ( ), Bing restituisce un'immagine che contiene 33 pixel di spaziatura bianca nella parte superiore e inferiore. Se si `&p` include il parametro di query, Bing restituisce un'immagine 200x134.

## <a name="crop-a-thumbnail"></a>Ritagliare un'anteprima 

Per ritagliare un'immagine, includere il `c` parametro di query (ritagliare). È possibile usare i seguenti valori:
  
- `4`&mdash; Rapporto cieco  
- `7`&mdash; Rapporto intelligente  

### <a name="smart-ratio-cropping"></a>Ritaglio Smart Ratio

Se si richiede il ritaglio `c` Smart `7`Ratio (impostando il parametro su ), Bing ritaglierà un'immagine dal centro dell'area di interesse verso l'esterno, mantenendo le proporzioni dell'immagine. L'area di interesse è la parte dell'immagine che secondo Bing contiene gli elementi più importanti. Il seguente è un esempio di area di interesse.  
  
![Area di interesse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Se si ridimensiona un'immagine e si richiede il ritaglio Smart Ratio, Bing riduce l'immagine alle dimensioni richieste mantenendo le proporzioni. Bing ritaglia quindi l'immagine in base alle dimensioni ridimensionate. Ad esempio, se la larghezza ridimensionata è minore o uguale all'altezza, Bing ritaglierà l'immagine a sinistra e a destra del centro dell'area di interesse. In caso contrario, Bing lo ritaglierà nella parte superiore e inferiore del centro dell'area di interesse.  
  
 
La seguente è l'immagine ridotta a 200x200 usando il ritaglio con proporzioni intelligenti. Poiché Bing misura l'immagine dall'angolo superiore sinistro, la parte inferiore dell'immagine viene ritagliata. 
  
![Immagine orizzontale ritagliata a 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
La seguente è l'immagine ridotta a 200x100 usando il ritaglio con proporzioni intelligenti. Poiché Bing misura l'immagine dall'angolo superiore sinistro, la parte inferiore dell'immagine viene ritagliata. 
   
![Immagine orizzontale ritagliata a 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
La seguente è l'immagine ridotta a 100x200 usando il ritaglio con proporzioni intelligenti. Poiché Bing misura l'immagine dal centro, le parti sinistra e destra dell'immagine vengono ritagliate.
  
![Immagine orizzontale ritagliata a 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Se Bing non è in grado di determinare l'area di interesse dell'immagine, il servizio utilizzerà il ritaglio Blind Ratio.  

### <a name="blind-ratio-cropping"></a>Ritaglio blind Ratio

Se si richiede il ritaglio `c` Blind `4`Ratio (impostando il parametro su ), Bing utilizza le regole seguenti per ritagliare l'immagine.  
  
- Se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, l'immagine viene misurata dall'angolo superiore sinistro e ritagliata in basso.  
- Se `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, l'immagine viene misurata dal centro e ritagliata a sinistra e a destra.  

La seguente è un'immagine verticale 225x300.  
  
![Immagine di girasole originale](./media/resize-crop/bing-resize-crop-sunflower.png)
  
La seguente è l'immagine ridotta a 200x200 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dall'angolo superiore sinistro, con la parte inferiore dell'immagine ritagliata.  
  
![Immagine di girasole ritagliata a 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
La seguente è l'immagine ridotta a 200x100 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dall'angolo superiore sinistro, con la parte inferiore dell'immagine ritagliata.  
  
![Immagine di girasole ritagliata a 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
La seguente è l'immagine ridotta a 100x200 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dal centro e di conseguenza ne vengono ritagliate le parti sinistra e destra.  
  
![Immagine di girasole ritagliata a 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sulle API di ricerca Bing](bing-api-comparison.md)
* [Requisiti per l'uso e la visualizzazione delle API di ricerca Bing](use-display-requirements.md)
