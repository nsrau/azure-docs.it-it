---
title: Posizionamento della fotocamera di analisi spaziale
titleSuffix: Azure Cognitive Services
description: Informazioni su come configurare una fotocamera per l'uso con l'analisi spaziale
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: 8e951e6cb18596b19f49bf42179297f656e3fa5f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91304296"
---
# <a name="camera-placement-guide"></a>Guida al posizionamento della fotocamera

Questo articolo fornisce consigli sulla selezione host per l'analisi spaziale (anteprima pubblica). Include linee guida generali, oltre a raccomandazioni specifiche per l'altezza, l'angolo e la distanza dal punto di vista della fotocamera per tutte le operazioni incluse. 

> [!NOTE]
> Questa guida è progettata per la videocamera AXIS M3045-V. Questa fotocamera utilizzerà la risoluzione 1920x1080, il campo di visualizzazione orizzontale di 106 gradi, il campo di visualizzazione verticale di 59 gradi e una lunghezza focale di 2,8 mm fissa. I principi riportati di seguito si applicano a tutte le fotocamere, ma è necessario modificare le linee guida specifiche relative all'altezza della fotocamera e alla distanza dal punto da fotocamera a focale per l'uso con altre fotocamere. 

## <a name="general-guidelines"></a>Linee guida generali

Quando si posizionano le fotocamere per l'analisi spaziale, tenere presenti le linee guida generali seguenti:

* **Altezza luce.** Posizionare le fotocamere sotto le fixture di illuminazione in modo che le fixture non blocchino le fotocamere.
* **Presenza limitazioni.** Per evitare di ostacolare le visualizzazioni della fotocamera, prendere nota degli ostacoli, ad esempio pali, firme, scaffalature, pareti e fotocamere LP esistenti.
* **Retroilluminazione ambientale.** La retroilluminazione esterna influiscono sulla qualità dell'immagine della fotocamera. Per evitare gravi condizioni di backlighting, evitare di indirizzare le telecamere alle porte esterne e vetrate.
* **Regole e normative sulla privacy locali.** Le normative locali possono limitare le fotocamere che possono acquisire. Prima di inserire le fotocamere, assicurarsi di comprendere le regole e le normative locali.
* **Compilazione della struttura.** HVAC, sprinkler e cablaggio esistente possono limitare il montaggio hardware delle fotocamere.
* **Gestione dei cavi.** Verificare che sia possibile instradare un cavo Ethernet da percorsi di montaggio della fotocamera pianificati al Commuter Power over Internet (PoE).

## <a name="height-focal-point-distance-and-angle"></a>Altezza, distanza del punto focale e angolo

Quando si decide come installare una fotocamera per l'analisi spaziale, è necessario prendere in considerazione tre elementi:
- Altezza della fotocamera
- Distanza da fotocamera a punto focale
- Angolo della fotocamera rispetto al piano del piano

È anche importante comprendere la direzione della maggior parte delle persone (direzione di camminamento della persona) in relazione al campo di visualizzazione della fotocamera, se possibile. Questa direzione è importante per le prestazioni del sistema.

![Immagine di una persona che cammina in una direzione](./media/spatial-analysis/person-walking-direction.png)

Nella figura seguente viene illustrata la visualizzazione dell'elevazione per la direzione di esplorazione della persona.

![Innalzamento di livello e vista piano](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Altezza della fotocamera

In genere, le fotocamere devono essere montate 12-14 metri dalla superficie. Quando si pianifica il montaggio della fotocamera in questo intervallo, prendere in considerazione gli ostacoli (ad esempio, la scaffale, le luci appese, la disconnessione e le visualizzazioni) che potrebbero influire sulla visualizzazione della fotocamera, quindi regolare l'altezza in modo necessario.

## <a name="camera-to-focal-point-distance"></a>Distanza da fotocamera a punto focale

La _distanza da fotocamera a punto focale_ è la distanza lineare tra il punto focale (o il centro dell'immagine della fotocamera) e la fotocamera misurata.

![Da fotocamera a focale-punto-distanza](./media/spatial-analysis/camera-focal-point.png)

Questa distanza viene misurata sul piano del piano.

![Modalità di misurazione della distanza dal punto di partenza dalla fotocamera al punto focale](./media/spatial-analysis/camera-focal-point-floor-plane.png)

Dal precedente, l'aspetto è simile al seguente:

![Modalità di misurazione della distanza dal punto di partenza dalla fotocamera al punto focale](./media/spatial-analysis/camera-focal-point-above.png)

Usare la tabella seguente per determinare la distanza della fotocamera dal punto focale in base a altezze di montaggio specifiche. Queste distanze sono per la selezione host ottimale. Si noti che la tabella fornisce indicazioni sotto la raccomandazione 12'-14' poiché alcuni massimali possono limitare l'altezza.

| Altezza della fotocamera | Distanza da fotocamera a punto focale (min/max) |  
| ------------- | ---------------------------------------- |  
| 8'            | 10'-13'                                  |  
| 10           | 7'-13'                                   |  
| 12           | 10'-17'                                  |  
| 14           | 11'-18'                                  |  
| 16           | 12'-22'                                  |  
| 20           | 15'-30'                                  |  

La figura seguente simula le visualizzazioni della fotocamera dalle distanze più vicine tra la fotocamera e il punto focale.

| Più vicino                                      | Meno aggiornata                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Distanza tra la fotocamera e il punto focale più vicina](./media/spatial-analysis/focal-point-closest.png) | ![Distanza da fotocamera a punto focale più lontana](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Intervalli di montaggio angolo fotocamera

Questa sezione descrive gli intervalli di montaggio dell'angolo della fotocamera accettabile. Questi intervalli di montaggio mostrano l'intervallo accettabile per la selezione host ottimale.

### <a name="line-configuration"></a>Configurazione linea

La tabella seguente illustra le raccomandazioni per le fotocamere configurate per l'operazione **cognitiveservices. Vision. spatialanalysis-personcrossingline** . 

| Altezza della fotocamera | Distanza da fotocamera a punto focale | Angolo di montaggio della fotocamera ottimale (min/max) |  
| ------------- | ------------------------------ | ------------------------------------------ |  
| 8'            | 9                             | +/-40 °                                     |  
| 10           | 10                            | +/-30 °                                     |  
| 12           | 13                            | +/-20 °                                     |  
| 16           | 18                            | +/-10 °                                     |  
| 20           | 22                            | +/-10 °                                     |  

La figura seguente simula le visualizzazioni della fotocamera usando le raccomandazioni per gli angoli di montaggio più a sinistra (-) e a destra (+) per l'uso di **cognitiveservices. Vision. spatialanalysis-personcrossingline** per eseguire il conteggio delle entrate in modo portabile.

| Visualizzazione più a sinistra                | Visualizzazione a destra                |  
| ---------------------------- | ----------------------------- |  
| ![Angolo della fotocamera più a sinistra](./media/spatial-analysis/camera-angle-left.png) | ![Angolo della fotocamera più a destra](./media/spatial-analysis/camera-angle-right.png) |  

La figura seguente Mostra gli angoli di posizionamento e montaggio della fotocamera da una visualizzazione Bird-Eye.

![Visualizzazione panoramica](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Configurazione zona

Per garantire che l'area coperta sia sufficientemente grande, è consigliabile posizionare le fotocamere a 10 metri o più sopra. 

Quando la zona è vicina a un ostacolo come un muro o un scaffale, montare le fotocamere nella distanza specificata dalla destinazione all'interno dell'intervallo di angolo di 120 gradi accettabile, come illustrato nella figura seguente.

![Angolo della fotocamera accettabile](./media/spatial-analysis/camera-angle-acceptable.png)

Nella figura seguente vengono illustrate le simulazioni per le visualizzazioni della fotocamera a destra e a sinistra di un'area accanto a uno scaffale.

| Visualizzazione a sinistra        | Visualizzazione a destra        |  
| ---------------- | ----------------- |  
| ![Visualizzazione a sinistra](./media/spatial-analysis/end-cap-left.png) | ![Visualizzazione a destra](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Code

Per monitorare le code, è possibile usare le competenze **cognitiveservices. Vision. spatialanalysis-PersonCount**, **cognitiveservices. Visio. spatialanalysis-persondistance**e **cognitiveservices. Vision. spatialanalysis-personcrossingpolygon** . Per ottimizzare la qualità dei dati della coda, è preferibile ridurre al minimo l'occlusione delle persone nella coda e garantire che la posizione delle code sia coerente nel tempo.

![Coda cinture ritirabile](./media/spatial-analysis/retractable-belt-queue.png)

Questo tipo di barriera è preferibile rispetto alle barriere opache per la formazione della coda per ottimizzare l'accuratezza delle informazioni dal sistema.

Esistono due tipi di code: lineare e zig-zag.

Nella figura seguente vengono illustrati i consigli per le code lineari:

![Indicazione coda lineare](./media/spatial-analysis/camera-angle-linear-queue.png)

Nella figura seguente vengono illustrate le simulazioni per le visualizzazioni della fotocamera a destra e a sinistra delle code lineari. Si noti che è possibile montare la fotocamera sul lato opposto della coda.

| Visualizzazione a sinistra                          | Visualizzazione a destra                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Angolo sinistro per coda lineare](./media/spatial-analysis/camera-angle-linear-left.png) | ![Angolo retto per la coda lineare](./media/spatial-analysis/camera-angle-linear-right.png) |  

Per le code zig-zag, è preferibile evitare di posizionare la fotocamera direttamente verso la direzione della riga della coda, come illustrato nella figura seguente. Si noti che ognuna delle quattro posizioni della fotocamera di esempio nell'illustrazione fornisce la visualizzazione ideale con una deviazione accettabile di +/-15 gradi in ogni direzione.

Le illustrazioni seguenti simulano la visualizzazione da una fotocamera posizionata nelle posizioni ideali per una coda zig-zag.

| Visualizzazione 1        | Visualizzazione 2        |  
| ------------- | ------------- |  
| ![Visualizzazione 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Visualizzazione 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| Visualizzazione 3 |  Visualizzazione 4 |  
| ---- | ----  |
| ![Visualizzazione 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![Visualizzazione 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Code organiche

Le linee di coda organiche formano organicamente. Questo stile di accodamento è accettabile se le code non si formano oltre 2-3 persone e i form della linea nella definizione della zona. Se la lunghezza della coda è in genere superiore a 2-3 persone, è consigliabile usare una barriera della cinghia ritirabile per guidare la direzione della coda e garantire i moduli linea all'interno della definizione della zona.

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire un'applicazione Web per il conteggio delle persone](spatial-analysis-web-app.md)
* [Configurare le operazioni di analisi spaziale](./spatial-analysis-operations.md)
* [Registrazione e risoluzione dei problemi](spatial-analysis-logging.md)
* [Guida al posizionamento di zone e linee](spatial-analysis-zone-line-placement.md)
