---
title: Creare un'applicazione per le mappe accessibile con Mappe di Azure | Microsoft Azure Maps
description: In questo articolo si apprenderà come creare un'applicazione con le funzionalità di accessibilità usando Mappe di Microsoft Azure.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 23f52e48c6a435678a01569d25e0072d9c8a3e28
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648567"
---
# <a name="building-an-accessible-application"></a>Compilazione di un'applicazione accessibile

Oltre il 20% degli utenti di Internet ha la necessità di avere applicazioni Web accessibili. Di conseguenza, è importante assicurarsi che l'applicazione sia progettata in modo che tutti gli utenti possano facilmente usarla. Si dovrebbe considerare l'accessibilità come una parte dell'esperienza utente nel suo complesso invece che come un insieme di attività da completare. Più accessibile è l'applicazione, maggiore è il numero di utenti che possono usarla. 

Ecco alcune considerazioni comuni sull'accessibilità in relazione ai contenuti interattivi ricchi come le mappe:
- Supportare l'utilità per la lettura dello schermo per gli utenti che hanno difficoltà a visualizzare le applicazioni Web.
- Prevedere più metodi per interagire ed esplorare l'applicazione Web quali il mouse, il tocco e la tastiera.
- Assicurarsi che il contrasto sia tale che i colori non si mescolino tra loro e diventi difficile distinguerli. 

L'SDK Web di Mappe di Azure è precompilato con numerose funzionalità di accessibilità, quali:
- Descrizioni dell'utilità per la lettura dello schermo quando la mappa si muove e quando l'utente si concentra su un controllo o un popup.
- Supporto per mouse, tocco e tastiera.
- Supporto per il contrasto dei colori accessibile nello stile della mappa stradale.
- Supporto per il contrasto elevato.

Per tutti i prodotti Microsoft è possibile trovare [qui](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) i dettagli completi sulla conformità dell'accessibilità. Cercare "Web di Mappe di Azure" per trovare il documento specifico relativo all'SDK Web di Mappe di Azure. 

## <a name="navigating-the-map"></a>Esplorazione della mappa

Esistono diversi modi in cui è possibile ingrandire, fare una panoramica, ruotare e inclinare la mappa. Di seguito vengono descritti tutti i diversi modi per esplorare la mappa.

**Zoom della mappa**

- Con un mouse, fare doppio clic sulla mappa per fare zoom avanti di un livello.
- Con un mouse, scorrere la rotellina per ingrandire la mappa.
- Con un touchscreen, toccare la mappa con due dita e avvicinarle per fare zoom indietro oppure allontanarle per fare zoom avanti.
- Con un touchscreen, effettuare un doppio tocco sulla mappa per fare zoom avanti di un livello.
- Con la mappa attiva, usare il segno più (`+`) o il segno uguale (`=`) per fare zoom avanti di un livello.
- Con la mappa attiva, usare il segno meno (`-`) o il carattere di sottolineatura (`_`) per fare zoom indietro di un livello.
- Usando il controllo zoom con un mouse, un tocco o il tasto TAB/INVIO della tastiera.
- Premere e tenere premuto il pulsante `Shift` insieme al pulsante sinistro del mouse sulla mappa e trascinare per disegnare un'area da ingrandire.

**Panoramica della mappa**

- Con il mouse, premere il pulsante sinistro del mouse sulla mappa e trascinare in una qualsiasi direzione.
- Con un touchscreen, toccare la mappa e trascinare in una qualsiasi direzione.
- Con la mappa attiva, usare i tasti di direzione per spostare la mappa.

**Rotazione della mappa**

- Con il mouse, premere il pulsante destro sulla mappa e trascinare verso sinistra o verso destra. 
- Con un touchscreen, toccare la mappa con due dita e ruotare.
- Con la mappa attiva, usare il tasto MAIUSC e i tasti di direzione verso destra o verso sinistra.
- Usando il controllo di rotazione con un mouse, un tocco o il tasto TAB/INVIO della tastiera.

**Inclinazione della mappa**

- Con il mouse, premere il pulsante destro sulla mappa e trascinare verso il basso o verso l'alto. 
- Con il touchscreen, toccare la mappa con due dita e trascinarle insieme verso l'alto o verso il basso.
- Con la mappa attiva, usare il tasto MAIUSC e i tasti di direzione verso l'alto o verso il basso. 
- Usando il controllo di inclinazione con un mouse, un tocco o il tasto TAB/INVIO della tastiera.

## <a name="change-the-map-style"></a>Modificare lo stile della mappa

Gli sviluppatori non sempre desiderano rendere disponibili tutti gli stili della mappa nell'applicazione. Se lo sviluppatore mostra il controllo selezione stile della mappa, l'utente può modificare lo stile della mappa usando il mouse, un tocco o la tastiera con i tasti TAB o INVIO. Lo sviluppatore può specificare gli stili della mappa che desidera rendere disponibili nel controllo di selezione stile per la mappa. Inoltre, lo sviluppatore può impostare e modificare lo stile della mappa a livello di codice.

**Uso del contrasto elevato**

- Quando il controllo mappa viene caricato, verifica che il contrasto elevato sia abilitato e che il browser lo supporti.
- Il controllo mappa non monitora la modalità di contrasto elevato del dispositivo. Se la modalità del dispositivo viene modificata, la modifica non si applica alla mappa. L'utente dovrà quindi ricaricare la mappa aggiornando la pagina.
- Quando viene rilevato un contrasto elevato, lo stile della mappa lo applica automaticamente e tutti i controlli predefiniti usano uno stile con contrasto elevato. Ad esempio, ZoomControl, PitchControl, CompassControl, StyleControl e altri controlli predefiniti useranno uno stile a contrasto elevato.
- Ci sono due tipi di contrasto elevato, chiaro e scuro. Se i controlli mappa rilevano il tipo di contrasto elevato, il comportamento della mappa viene modificato di conseguenza. Se è chiaro, viene caricato lo stile della mappa grayscale_light. Se non è possibile rilevarlo o è scuro, viene caricato lo stile high_contrast_dark.
- Se si creano controlli personalizzati, è utile sapere se i controlli predefiniti usano uno stile a contrasto elevato. Gli sviluppatori possono aggiungere una classe CSS nel div del contenitore della mappa che può essere controllata. Le classi CSS che vengono aggiunte sono `high-contrast-dark` e `high-contrast-light`. Per controllare con JavaScript, usare:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

in alternativa usare:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Tasti di scelta rapida

La mappa dispone di una serie di tasti di scelta rapida predefiniti che ne semplificano l'uso. Questi tasti di scelta rapida funzionano quando la mappa è attiva.

| Chiave      | Azione                            |
|----------|-----------------------------------|
| `Tab` | Spostarsi tra i controlli e i popup nella mappa. |
| `ESC` | Spostare lo stato attivo da un elemento qualsiasi della mappa all'elemento di livello superiore. |
| `Ctrl` + `Shift` + `D` | Consente di passare da un livello di dettaglio all'altro dell'utilità per la lettura dello schermo.  |
| Tasto freccia sinistra | Panoramica della mappa a sinistra di 100 pixel |
| Tasto freccia destra | Panoramica della mappa a destra di 100 pixel |
| Tasto freccia giù | Panoramica della mappa verso il basso di 100 pixel |
| Tasto freccia su | Panoramica della mappa verso l'alto di 100 pixel |
| `Shift` + freccia SU | Aumenta l'inclinazione della mappa di 10 gradi |
| `Shift` + freccia GIÙ | Riduce l'inclinazione della mappa di 10 gradi |
| `Shift` + freccia DESTRA | Ruota la mappa di 15 gradi in senso orario |
| `Shift` + freccia SINISTRA | Ruota la mappa di 15 gradi in senso antiorario |
| Segno più (`+`) o <sup>*</sup>segno di uguale (`=`) | Zoom avanti |
| Segno meno (`-`) o <sup>*</sup>carattere di sottolineatura (`_`) | Zoom indietro | 
| `Shift` + trascinare il mouse sulla mappa per disegnare un'area | Zoom nell'area |

<sup>*</sup> Questi tasti di scelta rapida condividono in genere lo stesso tasto su una tastiera. Questi tasti di scelta rapida sono stati aggiunti per migliorare l'esperienza utente. Inoltre, non è importante se l'utente usa il tasto MAIUSC o meno per i tasti di scelta rapida.

## <a name="screen-reader-support"></a>Supporto dell'utilità per la lettura dello schermo

Gli utenti possono spostarsi all'interno della mappa usando la tastiera. Se è in esecuzione un'utilità per la lettura dello schermo, la mappa notificherà all'utente le modifiche dello stato. Ad esempio, agli utenti vengono segnalate le modifiche apportate alla mappa quando si visualizza una panoramica o si fa zoom avanti nella mappa. Per impostazione predefinita, la mappa contiene descrizioni semplificate che escludono il livello di zoom e le coordinate del centro della mappa. L'utente può passare da un livello di dettaglio all'altro di queste descrizioni usando il tasto di scelta rapida `Ctrl` + `Shift` + `D`.

A eventuali informazioni aggiuntive inserite nella mappa di base devono corrispondere informazioni testuali per gli utenti dell'utilità di lettura dello schermo. Assicurarsi di aggiungere gli attributi [Accessible Rich Internet Applications (ARIA)](https://www.w3.org/WAI/standards-guidelines/aria/), alt e title, laddove appropriato. 

## <a name="make-popups-keyboard-accessible"></a>Rendere accessibile la tastiera di popup

Per rappresentare una posizione sulla mappa di solito viene usato un marcatore o un simbolo. Le informazioni aggiuntive sulla posizione vengono in genere visualizzate in un popup quando l'utente interagisce con il marcatore. Nella maggior parte delle applicazioni i popup vengono visualizzati quando un utente fa clic o tocca un marcatore. Tuttavia per fare clic o toccare l'utente deve usare, rispettivamente, un mouse e un touchscreen. È consigliabile rendere i popup accessibili quando si usa una tastiera. Questa funzionalità può essere ottenuta creando un popup per ogni punto dati e aggiungendolo alla mappa. 

L'esempio seguente carica i punti di interesse sulla mappa usando un livello di simbolo e aggiunge un popup alla mappa per ogni punto di interesse. Nelle proprietà di ogni punto dati viene archiviato un riferimento a ogni popup. È anche possibile recuperarlo per un marcatore, ad esempio quando si fa clic su di esso. Quando la mappa è attiva, premere il tasto TAB per passare da un popup all'altro sulla mappa.

<br/>

<iframe height='500' scrolling='no' title='Creare un applicazione accessibile' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Creare un applicazione accessibile</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Suggerimenti sull'accessibilità aggiuntivi

Ecco alcuni suggerimenti aggiuntivi per rendere l'applicazione di mapping Web più accessibile.

- Se si visualizzano molti dati del punto interattivo sulla mappa, provare a riordinarli usando il clustering. 
- Verificare che il rapporto di contrasto dei colori tra testo/simboli e colori di sfondo sia 4,5:1 o superiore.
- Fare in modo che i messaggi dell'utilità di lettura dello schermo (attributi ARIA, alt e title) siano brevi, descrittivi e significativi. Evitare di usare acronimi e gergo se non necessari.
- Provare a ottimizzare i messaggi inviati all'utilità di lettura dello schermo per creare informazioni significative brevi che l'utente possa facilmente comprendere. Ad esempio, se si desidera aggiornare l'utilità di lettura dello schermo a una frequenza elevata, come avviene quando la mappa si muove, provare a seguire questi punti:
    - Attendere che lo spostamento della mappa sia stato completato per aggiornare l'utilità di lettura dello schermo.
    - Limitare gli aggiornamenti a una frequenza di pochi secondi. 
    - Combinare i messaggi in modo logico. 
- Evitare di usare il colore come unico mezzo per comunicare le informazioni. Usare testo, icone o modelli per integrare o sostituire il colore. Alcune considerazioni:
    - Se si usa un livello a bolle per visualizzare il valore relativo tra i punti dati, provare a dimensionare il raggio di ogni bolla, a colorare la bolla o eseguire entrambe le azioni. 
    - Provare a usare un livello di simbolo con icone diverse per categorie di metriche diverse, ad esempio triangoli, stelle e quadrati. Il livello di simboli supporta anche la scalabilità delle dimensioni dell'icona. È anche possibile visualizzare un'etichetta di testo.
    - Se si visualizzano i dati con le linee, è possibile usare la larghezza per rappresentare il peso o le dimensioni. È possibile usare un modello a trattini per rappresentare diverse categorie di linee. È possibile usare un livello di simbolo insieme a una linea per sovrapporre le icone lungo la riga. L'uso di un'icona a freccia è utile per visualizzare il flusso o la direzione della linea.
    - Se vengono visualizzati i dati con i poligoni, è possibile usare un modello, ad esempio le strisce, come alternativa al colore. 
- Alcune visualizzazioni, ad esempio mappe termiche, livelli di riquadri e livelli di immagine, non sono accessibili agli utenti ipovedenti. Alcune considerazioni:
    - Fare in modo che l'utilità di lettura dello schermo descriva le informazioni mostrate dal livello quando viene aggiunto alla mappa. Se, ad esempio, viene visualizzato un livello di riquadro del radar meteorologico, fare in modo che l'utilità di lettura dello schermo indichi che "i dati del radar meteorologico sono sovrapposti sulla mappa".
- Limitare il numero di funzionalità che richiede l'uso del mouse. Queste funzionalità non potranno essere usate dagli utenti che usano una tastiera o un dispositivo touch per interagire con l'applicazione. Si noti che è comunque consigliabile prevedere l'uso del mouse per i contenuti interattivi ad esempio icone, collegamenti e pulsanti su cui è possibile fare clic.
- Provare a spostarsi nell'applicazione usando la tastiera. Assicurarsi che l'ordine di tabulazione sia logico.
- Se si creano tasti di scelta rapida, provare a limitarli a uno o due tasti. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'accessibilità nei moduli dell'SDK Web.

> [!div class="nextstepaction"]
> [Accessibilità degli strumenti di disegno](drawing-tools-interactions-keyboard-shortcuts.md)

Informazioni sullo sviluppo di app accessibili con Microsoft Learn:

> [!div class="nextstepaction"]
> [Accessibilità nel percorso di apprendimento sui badge digitali di intervento](https://ready.azurewebsites.net/learning/track/2940)

Esaminare questi strumenti di accessibilità utili:
> [!div class="nextstepaction"]
> [Sviluppo di app accessibili](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Panoramica di WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Web Accessibility Evaluation Tool (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Verifica del contrasto dei colori WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Simulatore per la visione NoCoffee](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
