---
title: Moduli personalizzati di Cortana Intelligence Gallery | Documentazione Microsoft
description: Scoprire moduli personalizzati di Machine Learning in Cortana Intelligence Gallery.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: roopalik;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 56c308643ad6d20170174725f76f6ddbc76b3e22
ms.contentlocale: it-it
ms.lasthandoff: 04/15/2017

---
# <a name="discover-custom-machine-learning-modules-in-cortana-intelligence-gallery"></a>Scoprire moduli personalizzati di Machine Learning in Cortana Intelligence Gallery
[!INCLUDE [machine-learning-gallery-item-selector](../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Moduli personalizzati per Machine Learning Studio
In Cortana Intelligence Gallery sono disponibili vari [moduli personalizzati](https://gallery.cortanaintelligence.com/customModules) che espandono le funzionalità di Azure Machine Learning Studio. È possibile importare questi moduli per usarli nei propri esperimenti e sviluppare così soluzioni di analisi predittiva ancora più avanzate.

Attualmente, Gallery offre moduli per *analisi della serie temporale*, *regole di associazione*, *algoritmi di clustering* (oltre K-means), *visualizzazioni* e altri moduli di utilità.


## <a name="discover"></a>Scoprire
Per esplorare i moduli personalizzati [in Gallery](http://gallery.cortanaintelligence.com), selezionare **Moduli personalizzati** in **Altro**.

![Selezionare Moduli personalizzati dalla home page di Gallery](media/machine-learning-gallery-custom-modules/select-custom-modules-in-gallery.png)

Nella pagina **[Moduli personalizzati](https://gallery.cortanaintelligence.com/customModules)** è disponibile l'elenco dei moduli più recenti e di quelli più visualizzati. Per visualizzare tutti i moduli personalizzati, selezionare il pulsante **Visualizza tutto**. Per cercare un modulo personalizzato specifico, selezionare **Visualizza tutto** e quindi selezionare i criteri di filtro. È anche possibile immettere i termini di ricerca nella casella **Ricerca** nella parte superiore della pagina di Gallery.

![Selezionare "Visualizza tutto" per visualizzare tutti i moduli personalizzati](media/machine-learning-gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>Comprendere

Per comprendere il funzionamento di un modulo personalizzato pubblicato, selezionare il modulo da aprire nella pagina dei dettagli. La pagina dei dettagli garantisce un'esperienza di apprendimento coerente e informativa. Ad esempio, la pagina evidenzia lo scopo del modulo ed elenca input, output e parametri previsti. La pagina contiene anche un collegamento al codice sorgente sottostante, che è possibile esaminare e personalizzare.

### <a name="comment-and-share"></a>Commentare e condividere
Nella pagina dei dettagli di un modulo personalizzato, è possibile fornire commenti e suggerimenti oppure fare domande sul modulo nella sezione **Commenti**. È anche possibile condividere il modulo con amici o colleghi su Twitter o LinkedIn e invitare altri utenti a visualizzare la pagina di dettagli del modulo inviando loro un collegamento tramite posta elettronica.

![Condividere l'elemento con amici](media/machine-learning-gallery-how-to-use-contribute-publish/share-links.png)

![Aggiungere un commento](media/machine-learning-gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>Importa
Negli esperimenti è possibile importare qualsiasi modulo personalizzato presente in Gallery.

Cortana Intelligence Gallery offre due modi per importare la copia di un modulo:

* **Da Gallery**. Quando si importa un modulo personalizzato da Gallery, è possibile scaricare anche un esperimento di esempio che illustra come usarlo.
* **Da Machine Learning Studio**. È possibile importare qualsiasi modulo personalizzato mentre si lavora in Machine Learning Studio; in questo caso, non è disponibile l'esperimento di esempio.

### <a name="from-the-gallery"></a>Da Gallery

1. Aprire la pagina dei dettagli del modulo in Gallery. 
2. Selezionare **Apri in Studio**.
   
    ![Aprire il modulo personalizzato da Gallery](media/machine-learning-gallery-custom-modules/open-custom-module-from-gallery.png)
   
Con ogni modulo personalizzato viene fornito un esperimento di esempio che illustra come usare il modulo. Quando si seleziona **Apri in Studio**, l'esperimento di esempio viene aperto nell'area di lavoro di Machine Learning Studio. Se non è già stato effettuato l'accesso a Studio, sarà necessario accedere con l'account Microsoft.

Oltre all'esperimento di esempio, anche il modulo personalizzato viene copiato nell'area di lavoro e viene inserito nella tavolozza dei moduli, insieme a tutti i moduli di Machine Learning Studio predefiniti o personalizzati. Può essere usato negli esperimenti come qualsiasi altro modulo presente nella propria area di lavoro.

### <a name="from-within-machine-learning-studio"></a>Da Machine Learning Studio

1. In Machine Learning Studio selezionare **NUOVO**.
2. Selezionare **Modulo**. È possibile scegliere da un elenco dei moduli di Gallery o trovare un modulo specifico usando la casella **Ricerca**.
3. Spostare il mouse sopra un modulo, quindi selezionare **Importa modulo**. Per ottenere informazioni sul modulo, selezionare **Visualizza in Gallery**. Verrà aperta la pagina di dettagli del modulo in Gallery.
   
    ![Importare un modulo personalizzato in Machine Learning Studio](media/machine-learning-gallery-custom-modules/add-custom-module-in-studio.png)

Il modulo personalizzato viene copiato nell'area di lavoro e inserito nella tavolozza dei moduli insieme a tutti gli altri moduli predefiniti o personalizzati presenti in Machine Learning Studio. Può essere usato negli esperimenti come qualsiasi altro modulo presente nella propria area di lavoro.

## <a name="use"></a>Uso

Indipendentemente dal metodo scelto per importare un modulo personalizzato, quest'ultimo viene inserito nella tavolozza dei moduli in Machine Learning Studio. Dalla tavolozza dei moduli è possibile usare i moduli personalizzati in qualsiasi esperimento creato nella propria area di lavoro come qualsiasi altro modulo.

Per usare un modulo importato:

1. Creare un nuovo esperimento o aprirne uno esistente.
2. Per espandere l'elenco di moduli personalizzati nell'area di lavoro, selezionare **Personalizzato** nella tavolozza dei moduli. La tavolozza si trova a sinistra dell'area di disegno dell'esperimento.
   
    ![Elenco di moduli personalizzati nella tavolozza di Studio](media/machine-learning-gallery-custom-modules/custom-module-in-studio-palette.png)
3. Selezionare il modulo importato e trascinarlo nell'esperimento.


**[Passare a Gallery](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


