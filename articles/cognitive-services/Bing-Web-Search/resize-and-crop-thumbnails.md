---
title: Ridimensionare e ritagliare le anteprime delle immagini - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come ridimensionare e ritagliare le anteprime fornite dalle API di ricerca di Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 6a5b2dada254a0bfc7fa60172f56221ba67ad279
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867934"
---
# <a name="resize-and-crop-thumbnail-images"></a>Ridimensionare e ritagliare le immagini di anteprima

Alcune risposte dalle API di ricerca Bing includono gli URL delle immagini di anteprima servite da Bing, che è possibile ridimensionare e ritagliare e possono contenere parametri di query. Ad esempio:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Se si visualizza un sottoinsieme delle anteprime, offrono un'opzione per visualizzare le immagini rimanenti.

> [!NOTE]
> Assicurarsi che il ritaglio e ridimensionamento delle immagini di anteprima fornirà uno scenario di ricerca che rispettino i diritti di terze parti, come richiesto dall'API di ricerca Bing [utilizzare e visualizzare i requisiti](use-display-requirements.md).

## <a name="resize-a-thumbnail"></a>Ridimensionare un'immagine di anteprima 

Per ridimensionare un'immagine di anteprima, Bing consiglia di specificare un solo il `w` (larghezza) o `h` i parametri di query (altezza) nell'URL dell'anteprima. Specificando solo l'altezza o larghezza consente a Bing di mantenere l'aspetto originale dell'immagine. Specificare la larghezza e l'altezza in pixel. 

Ad esempio, se l'anteprima originale è 620 x 480:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

E si vuole ridurre la dimensione, impostare il `w` parametro per un nuovo valore (ad esempio `336`) e rimuovere il `h` parametro:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Se si specifica solo l'altezza o larghezza di un'anteprima, verranno mantenute le proporzioni originali dell'immagine. Se si specificano entrambi i parametri e non sono mantenute le proporzioni, Bing aggiungerà spaziatura interna bianca al bordo dell'immagine.

Ad esempio, se si ridimensiona un' 480x359 immagine a 200x200 senza ritagliarla, la larghezza completa contiene l'immagine ma l'altezza contiene 25 pixel di spaziatura interna bianca nella parte superiore e inferiore dell'immagine. Se l'immagine è stata 359 x 480, i bordi sinistro e destro conterrebbe spaziatura interna bianca. Se si ritaglia l'immagine, la spaziatura interna bianca non viene aggiunta.  

La figura seguente illustra le dimensioni originali di un'immagine di anteprima (480x300).  
  
![Immagine orizzontale originale](./media/resize-crop/bing-resize-crop-landscape.png)  
  
La figura seguente illustra l'immagine ridimensionata a 200x200. Le proporzioni vengono mantenute e i bordi superiore e inferiore vengono riempiti di bianco (in questo caso il bordo nero è incluso per mostrare la spaziatura interna).  
  
![Immagine orizzontale ridimensionata](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Se si specificano dimensioni maggiori di larghezza e altezza originali dell'immagine, Bing aggiunge un riempimento bianco per i bordi sinistro e superiore.  

## <a name="request-different-thumbnail-sizes"></a>Richiedere le diverse dimensioni delle anteprime

Per richiedere una dimensione diversa immagine di anteprima, rimuovere tutti i parametri di query dall'URL dell'anteprima, ad eccezione di `id` e `pid` parametri. Quindi aggiungere il `&w` (larghezza) o `&h` parametro di query (altezza) con le dimensioni desiderate relativa all'immagine in pixel, ma non entrambi. Bing manterrà le proporzioni originali dell'immagine. 

Per aumentare la larghezza dell'immagine specificato dall'URL precedente a 165 pixel di distanza, usare l'URL seguente:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Se si richiede un'immagine di dimensioni maggiore rispetto alle dimensioni originali dell'immagine, Bing aggiunge spaziatura interna bianca intorno all'immagine in base alle esigenze. Ad esempio, se l'immagine originale del dimensione è 474 x 316 e si imposta `&w` su 500, Bing restituirà un'immagine di 500 x 333. Questa immagine avrà 8,5 pixel di spaziatura interna bianca lungo i bordi superiore e inferiore e 13 pixel di spaziatura interna ai bordi sinistro e destro.

Per impedire l'aggiunta di spaziatura interna bianca se la dimensione richiesta è superiore alle dimensioni originali dell'immagine di Bing, impostare il `&p` query parametro su 0. Ad esempio, se si include il `&p=0` parametro nell'URL precedente, Bing restituirà un'immagine di 474 x 316 anziché un'immagine di 500 x 333:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Se si specificano entrambe `&w` e `&h` manterrà le proporzioni dell'immagine di parametri di query, Bing e aggiunge spaziatura interna bianca in base alle esigenze. Ad esempio, se l'immagine originale del dimensioni sono 474 x 316 e impostare i parametri width e height a 200x200 (`&w=200&h=200`), Bing restituisce un'immagine che contiene 33 pixel di spaziatura interna bianca nella parte superiore e inferiore. Se si include il `&p` parametro di query Bing restituisce un'immagine di 200 x 134.

## <a name="crop-a-thumbnail"></a>Ritagliare un'immagine di anteprima 

Per ritagliare un'immagine, includere il `c` parametro di query (ritaglio). È possibile usare i valori seguenti:
  
- `4` &mdash; Proporzioni  
- `7` &mdash; Proporzioni intelligenti  

### <a name="smart-ratio-cropping"></a>Smart ritaglio con proporzioni

Se si richiede di ritaglio con proporzioni intelligenti (impostando il `c` parametro per `7`), Bing verrà ritagliare un'immagine dal centro del relativa area di interesse verso l'esterno, mantenendo le proporzioni dell'immagine. L'area di interesse è la parte dell'immagine che secondo Bing contiene gli elementi più importanti. Il seguente è un esempio di area di interesse.  
  
![Area di interesse](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Se si ridimensiona un'immagine e richiedere ritaglio con proporzioni intelligenti, Bing riduce l'immagine a dimensioni richieste mantenendo le proporzioni. Bing è quindi Ritaglia l'immagine in base alle dimensioni modificate. Ad esempio, se la larghezza ridimensionata è minore o uguale all'altezza, Bing verrà ritagliare l'immagine a sinistra e a destra del centro dell'area di interesse. In caso contrario, Bing verrà ritagliarla nella parte superiore e inferiore del centro dell'area di interesse.  
  
 
La seguente è l'immagine ridotta a 200x200 usando il ritaglio con proporzioni intelligenti. Poiché Bing misura l'immagine nell'angolo in alto a sinistra, la parte inferiore dell'immagine viene ritagliata. 
  
![Immagine orizzontale ritagliata a 200x200](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
La seguente è l'immagine ridotta a 200x100 usando il ritaglio con proporzioni intelligenti. Poiché Bing misura l'immagine nell'angolo in alto a sinistra, la parte inferiore dell'immagine viene ritagliata. 
   
![Immagine orizzontale ritagliata a 200x100](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
La seguente è l'immagine ridotta a 100x200 usando il ritaglio con proporzioni intelligenti. Poiché l'immagine dal centro di misure di Bing, vengono ritagliate le parti sinistra e destra dell'immagine.
  
![Immagine orizzontale ritagliata a 100x200](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Se Bing non è possibile determinare l'area di interesse dell'immagine, il servizio userà ritaglio con proporzioni casuali.  

### <a name="blind-ratio-cropping"></a>Ritaglio con proporzioni nascosta

Se si richiede di ritaglio con proporzioni casuali (impostando il `c` parametro per `4`), Bing Usa le regole seguenti per ritagliare l'immagine.  
  
- Se `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`, l'immagine viene misurata dall'angolo in alto a sinistra e ritagliata in basso.  
- Se `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, l'immagine viene misurata dal centro e ritagliata a sinistra e a destra.  

La seguente è un'immagine verticale 225x300.  
  
![Immagine di girasole originale](./media/resize-crop/bing-resize-crop-sunflower.png)
  
La seguente è l'immagine ridotta a 200x200 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dall'angolo superiore sinistro risultante nella parte inferiore dell'immagine viene ritagliata.  
  
![Immagine di girasole ritagliata a 200x200](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
La seguente è l'immagine ridotta a 200x100 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dall'angolo superiore sinistro risultante nella parte inferiore dell'immagine viene ritagliata.  
  
![Immagine di girasole ritagliata a 200x100](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
La seguente è l'immagine ridotta a 100x200 usando il ritaglio con proporzioni casuali. L'immagine viene misurata dal centro e di conseguenza ne vengono ritagliate le parti sinistra e destra.  
  
![Immagine di girasole ritagliata a 100x200](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Passaggi successivi

* [Quali sono le API di ricerca Bing?](bing-api-comparison.md)
* [API ricerca Bing utilizzare e visualizzare i requisiti](use-display-requirements.md)
