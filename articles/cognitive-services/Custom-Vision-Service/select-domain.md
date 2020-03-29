---
title: Selezionare un dominio per un progetto Di visione personalizzata - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Questo articolo ti mostrerà come selezionare un dominio per il tuo progetto nel Servizio visione artificiale personalizzato.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1569b6081adad4cae0855f9adfb4e14e910bf819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899451"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selezionare un dominio per un progetto Vision personalizzato

Dal pannello delle impostazioni per il progetto Visione personalizzata, è possibile selezionare un dominio per il progetto. Scegliere il dominio più simile allo scenario.

## <a name="image-classification"></a>Classificazione immagini

|Dominio|Scopo|
|---|---|
|__Generico__| Ottimizzato per un'ampia gamma di attività di classificazione di immagini. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico.|
|__Food__|Ottimizzato per fotografie di piatti come nel menù di un ristorante. Se si vogliono classificare fotografie di singoli frutti o verdure, usare il dominio Food (Cibo).|
|__Luoghi d' interesse__|Ottimizzato per i luoghi di interesse riconoscibili, sia naturali che artificiali. Il dominio offre i migliori risultati quando il luogo di interesse è chiaramente visibile nella fotografia. Il dominio è efficace anche se il luogo è leggermente nascosto da utenti posti davanti.|
|__Vendita al dettaglio__|Ottimizzato per le immagini che si trovano in un catalogo di vendita o in un sito Web di vendita. Se si vogliono classificare con alta precisione vestiti, pantaloni e magliette o camicie, usare questo dominio.|
|__Domini compatti__| Ottimizzato per i vincoli di classificazione in tempo reale sui dispositivi perimetrali.|

## <a name="object-detection"></a>Rilevamento di oggetti

|Dominio|Scopo|
|---|---|
|__Generale__| Ottimizzato per un'ampia gamma di attività di rilevamento oggetti. Se nessuno degli altri domini risulta appropriato o si è in dubbio sul dominio da scegliere, selezionare il dominio generico.|
|__Logo__|Ottimizzato per il rilevamento di logo dei marchi nelle immagini.|
|__Prodotti sugli scaffali__|Ottimizzato per rilevare e classificare i prodotti sugli scaffali.|
|__Domini compatti__| Ottimizzato per i vincoli del rilevamento di oggetti in tempo reale sui dispositivi perimetrali.|

## <a name="compact-domains"></a>Domini compatti

I modelli generati da domini compatti possono essere esportati per l'esecuzione in locale. Le prestazioni del modello variano in base al dominio selezionato. Nella tabella seguente, segnaliamo le dimensioni del modello e il tempo di \[inferenza sulla CPU Intel Desktop e NVidia GPU 1\]. 

> [!NOTE]
> Questi numeri non includono il tempo di pre-elaborazione e post-elaborazione.

|Attività|Dominio|Dimensioni del modello|Tempo di inferenza della CPU|Tempo di inferenza GPU|
|---|---|---|---|---|
|Classificazione|General (compact) (Generale - compatto)|5 MB|13 ms|5 ms|
|Rilevamento di oggetti|General (compact) (Generale - compatto)|45 MB|35 ms|5 ms|
|Rilevamento di oggetti|Generale (compatto) [S1]|14 MB (in più)|27 ms|7 ms|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK (Vision AI Dev Kit)

Quando viene selezionato un dominio compatto, viene fornita un'opzione aggiuntiva "Funzionalità di esportazione" che consente di distinguere tra "Piattaforme base" e "Vision AI Dev Kit".

In Funzionalità di _esportazione_ le due opzioni sono:

- Piattaforme di base (Tensorflow, CoreML, ONNX e così via)
- Kit di sviluppo Vision AI.

Quando _Vision AI Dev Kit_ è selezionato _generico_, _Punti di riferimento_e vendita al _dettaglio,_ ma non i domini _food_ compact sono disponibili per la classificazione delle immagini, mentre sia _Generale (compatta)_ che _Generale (compatta) [S1]_ sono disponibili per il rilevamento degli oggetti.

>[!NOTE]
>Il dominio __generale (compatto)__ per il rilevamento degli oggetti richiede una logica di post-elaborazione speciale. Per i dettagli, vedere uno script di esempio nel pacchetto zip esportato. Se è necessario un modello senza la logica di post-elaborazione, utilizzare __Generale (compatta) [S1]__.

>[!IMPORTANT]
>Non vi è alcuna garanzia che i modelli esportati foro esattamente lo stesso risultato dell'API di stima nel cloud. Una leggera differenza nella piattaforma in esecuzione o nell'implementazione di pre-elaborazione può causare una differenza maggiore negli output del modello. Per i dettagli della logica di pre-elaborazione, vedere [questo documento](python-tutorial.md).

\[1\] CPU Intel Xeon E5-2690 e NVIDIA Tesla M60
