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
ms.openlocfilehash: 87b9e4a3ca7151b3666928b00add175eddeea050
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409383"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selezionare un dominio per un progetto di Visione personalizzata

Dal pannello impostazioni per il progetto di Visione personalizzata, è possibile selezionare un dominio per il progetto. Scegliere il dominio più vicino allo scenario.

## <a name="image-classification"></a>Classificazione immagini

|Dominio|Scopo|
|---|---|
|__Generico__| Ottimizzato per un'ampia gamma di attività di classificazione di immagini. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico.|
|__Food__|Ottimizzato per fotografie di piatti come nel menù di un ristorante. Se si vogliono classificare fotografie di singoli frutti o verdure, usare il dominio Food (Cibo).|
|__Punti di riferimento__|Ottimizzato per i luoghi di interesse riconoscibili, sia naturali che artificiali. Il dominio offre i migliori risultati quando il luogo di interesse è chiaramente visibile nella fotografia. Il dominio è efficace anche se il luogo è leggermente nascosto da utenti posti davanti.|
|__Retail__|Ottimizzato per le immagini che si trovano in un catalogo di vendita o in un sito Web di vendita. Se si vogliono classificare con alta precisione vestiti, pantaloni e magliette o camicie, usare questo dominio.|
|__Domini compatti__| Ottimizzato per i vincoli di classificazione in tempo reale nei dispositivi perimetrali.|

## <a name="object-detection"></a>Rilevamento di oggetti

|Dominio|Scopo|
|---|---|
|__Generale__| Ottimizzato per un'ampia gamma di attività di rilevamento oggetti. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico.|
|__Logo__|Ottimizzato per il rilevamento di logo dei marchi nelle immagini.|
|__Prodotti sugli scaffali__|Ottimizzato per il rilevamento e la classificazione dei prodotti sugli scaffali.|
|__Domini compatti__| Ottimizzato per i vincoli del rilevamento di oggetti in tempo reale nei dispositivi perimetrali.|

## <a name="compact-domains"></a>Domini compatti

I modelli generati da domini compatti possono essere esportati per l'esecuzione in locale. Nell'API di anteprima pubblica di Visione personalizzata 3,4 è possibile ottenere un elenco delle piattaforme esportabili per i domini Compact chiamando l'API getdomains.

Le prestazioni del modello variano in base al dominio selezionato. Nella tabella seguente vengono segnalate le dimensioni e il tempo di inferenza del modello su Intel Desktop CPU e NVidia GPU \[ 1 \] . Questi numeri non includono la pre-elaborazione e il tempo di postelaborazione.

|Attività|Dominio|Dimensioni del modello|Tempo di inferenza CPU|Tempo di inferenza GPU|
|---|---|---|---|---|
|Classificazione|General (compact) (Generale - compatto)|5 MB|13 ms|5 ms|
|Rilevamento di oggetti|General (compact) (Generale - compatto)|45 MB|35 ms|5 ms|
|Rilevamento di oggetti|Generale (Compact) [S1]|14 MB|27 ms|7 ms|

>[!NOTE]
>Il dominio __generale (Compact)__ per il rilevamento di oggetti richiede una logica di postelaborazione speciale. Per informazioni dettagliate, vedere uno script di esempio nel pacchetto zip esportato. Se è necessario un modello senza la logica di postelaborazione, usare __General (Compact) [S1]__.

>[!IMPORTANT]
>Non vi è alcuna garanzia che i modelli esportati forniscano esattamente lo stesso risultato dell'API di stima nel cloud. Una lieve differenza nella piattaforma in esecuzione o l'implementazione della pre-elaborazione può causare una differenza maggiore negli output del modello. Per informazioni dettagliate sulla logica di pre-elaborazione, vedere [questo documento](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
