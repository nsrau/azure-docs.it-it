---
title: Selezionare un dominio per un progetto di Visione personalizzata-Visione artificiale
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come selezionare un dominio per il progetto nella Servizio visione artificiale personalizzato.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899451"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selezionare un dominio per un progetto di Visione personalizzata

Dal pannello impostazioni per il progetto di Visione personalizzata, è possibile selezionare un dominio per il progetto. Scegliere il dominio più vicino allo scenario.

## <a name="image-classification"></a>Classificazione immagini

|Dominio|Scopo|
|---|---|
|__Domande generiche__| Ottimizzato per un'ampia gamma di attività di classificazione di immagini. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico.|
|__Food__ (Cibo)|Ottimizzato per fotografie di piatti come nel menù di un ristorante. Se si vogliono classificare fotografie di singoli frutti o verdure, usare il dominio Food (Cibo).|
|__Landmarks__ (Luoghi di interesse)|Ottimizzato per i luoghi di interesse riconoscibili, sia naturali che artificiali. Il dominio offre i migliori risultati quando il luogo di interesse è chiaramente visibile nella fotografia. Il dominio è efficace anche se il luogo è leggermente nascosto da utenti posti davanti.|
|__Retail__ (Vendita)|Ottimizzato per le immagini che si trovano in un catalogo di vendita o in un sito Web di vendita. Se si vogliono classificare con alta precisione vestiti, pantaloni e magliette o camicie, usare questo dominio.|
|__Domini compatti__| Ottimizzato per i vincoli di classificazione in tempo reale nei dispositivi perimetrali.|

## <a name="object-detection"></a>Rilevamento di oggetti

|Dominio|Scopo|
|---|---|
|__Generale__| Ottimizzato per un'ampia gamma di attività di rilevamento oggetti. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico.|
|__Logo__|Ottimizzato per il rilevamento di logo dei marchi nelle immagini.|
|__Prodotti su scaffali__|Ottimizzato per il rilevamento e la classificazione dei prodotti negli scaffali.|
|__Domini compatti__| Ottimizzato per i vincoli del rilevamento di oggetti in tempo reale nei dispositivi perimetrali.|

## <a name="compact-domains"></a>Domini Compact

I modelli generati da domini compatti possono essere esportati per l'esecuzione in locale. Le prestazioni del modello variano in base al dominio selezionato. Nella tabella seguente vengono segnalate le dimensioni e il tempo di inferenza del modello su Intel Desktop CPU e NVidia GPU \[1\]. 

> [!NOTE]
> Questi numeri non includono la pre-elaborazione e il tempo di postelaborazione.

|Attività|Dominio|Dimensioni del modello|Tempo di inferenza CPU|Tempo di inferenza GPU|
|---|---|---|---|---|
|Classificazione|Generale (Compact)|5 MB|13 ms|5 ms|
|Rilevamento di oggetti|Generale (Compact)|45 MB|35 ms|5 ms|
|Rilevamento di oggetti|Generale (Compact) [S1]|14 MB|27 ms|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

Quando viene selezionato un dominio compatto, viene fornita un'opzione aggiuntiva "funzionalità di esportazione", che consente di distinguere tra "piattaforme di base" e "visione artificiale Dev Kit".

In _funzionalità di esportazione_ le due opzioni sono:

- Piattaforme di base (Tensorflow, CoreML, ONNX e così via)
- Vision AI Dev Kit.

Quando si seleziona _Vision ai Dev Kit_ , i domini _generici_, i _punti di riferimento_e la _vendita al dettaglio_ , ma non i domini compatti _alimentari_ sono disponibili per la classificazione delle immagini, mentre sono disponibili sia _generale (Compact)_ che _generale (Compact) [S1]_ per il rilevamento degli oggetti.

>[!NOTE]
>Il dominio __generale (Compact)__ per il rilevamento di oggetti richiede una logica di postelaborazione speciale. Per informazioni dettagliate, vedere uno script di esempio nel pacchetto zip esportato. Se è necessario un modello senza la logica di postelaborazione, usare __General (Compact) [S1]__ .

>[!IMPORTANT]
>Non vi è alcuna garanzia che i modelli esportati forniscano esattamente lo stesso risultato dell'API di stima nel cloud. Una lieve differenza nella piattaforma in esecuzione o l'implementazione della pre-elaborazione può causare una differenza maggiore negli output del modello. Per informazioni dettagliate sulla logica di pre-elaborazione, vedere [questo documento](python-tutorial.md).

\[1\] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
