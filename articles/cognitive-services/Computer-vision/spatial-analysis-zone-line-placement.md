---
title: Posizione della zona e della linea di analisi spaziale
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare zone e linee con l'analisi spaziale
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939412"
---
# <a name="zone-and-line-placement-guide"></a>Guida al posizionamento di zone e linee

Questo articolo fornisce linee guida su come definire le zone e le linee per le operazioni di analisi spaziale per ottenere un'analisi accurata dei movimenti dei popoli in uno spazio. Si applica a tutte le operazioni. 

Le zone e le linee vengono definite utilizzando il parametro JSON SPACEANALYSIS_CONFIG. Per ulteriori informazioni, vedere l'articolo relativo alle [operazioni di analisi spaziale](spatial-analysis-operations.md) .

## <a name="guidelines-for-drawing-zones"></a>Linee guida per la creazione di zone

Tenere presente che ogni spazio è diverso; è necessario aggiornare la posizione o le dimensioni a seconda delle esigenze.

Se si desidera visualizzare una sezione specifica della visualizzazione della fotocamera, creare la zona più grande che è possibile, coprendo l'area del piano specifica a cui si è interessati, ma senza includere altre aree a cui non si è interessati. Ciò consente di aumentare l'accuratezza dei dati raccolti e di evitare falsi positivi dalle aree di cui non si vuole tenere traccia. Prestare attenzione quando si posizionano gli angoli del poligono e assicurarsi che non si trovino all'esterno dell'area di cui si vuole tenere traccia.  

### <a name="example-of-a-well-shaped-zone"></a>Esempio di una zona ben formata

La zona deve essere sufficientemente grande da contenere tre persone che si trovino lungo ogni perimetro e si concentrano sull'area di interesse. L'analisi spaziale identificherà le persone i cui piedi sono posizionati nella zona. Pertanto, quando si disegnano le zone nell'immagine 2D, si supponga che la zona venga considerata una moquette al pavimento.

![Area ben formata](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Esempi di zone che non sono ben formate

Negli esempi seguenti vengono illustrate le zone a forma di scarsa qualità. In questi esempi, l'area di interesse è lo spazio davanti alla visualizzazione del *tempo di gioco* .

**La zona non si trova sul pavimento.**

![Zona con forma non corretta](./media/spatial-analysis/zone-not-on-floor.png) 

**La zona è troppo piccola.**

![la zona è troppo piccola](./media/spatial-analysis/zone-too-small.png)

**La zona non acquisisce completamente l'area attorno alla visualizzazione.**

![la zona non acquisisce completamente l'area attorno alla chiusura](./media/spatial-analysis/zone-bad-capture.png)

**La zona è troppo vicina al bordo dell'immagine della fotocamera e non acquisisce la visualizzazione a destra.**

![la zona è troppo vicina al bordo dell'immagine della fotocamera e non acquisisce la visualizzazione a destra](./media/spatial-analysis/zone-edge.png)

**La zona è parzialmente bloccata dallo scaffale, pertanto le persone e il piano non sono completamente visibili.**

![la zona è parzialmente bloccata, quindi gli utenti non sono completamente visibili](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Esempio di riga ben formata

La riga deve essere sufficientemente grande da contenere l'intera entrata. L'analisi spaziale identificherà le persone i cui piedi attraversano la linea, quindi, quando si disegnano linee nell'immagine 2D, si supponga di disegnarle come se si trovassero al pavimento. 

Se possibile, estendere la linea più ampia rispetto all'ingresso effettivo. Se questa operazione non comporterà incroci aggiuntivi, come nell'immagine seguente quando la riga si trova su un muro, estenderla.

![Linea ben formata](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Esempi di linee non ben formate

Negli esempi seguenti vengono illustrate righe definite in modo non corretto.

**La linea non copre l'intero metodo di ingresso al piano.**

![La linea non copre l'intero punto di ingresso al piano](./media/spatial-analysis/zone-line-bad-coverage.png)

**La linea è troppo alta e non copre l'intero sportello.**

![La linea è troppo alta e non copre l'intero sportello](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Web per il conteggio delle persone](spatial-analysis-web-app.md)
* [Configurare le operazioni di analisi spaziale](./spatial-analysis-operations.md)
* [Registrazione e risoluzione dei problemi](spatial-analysis-logging.md)
* [Guida al posizionamento della fotocamera](spatial-analysis-camera-placement.md)
