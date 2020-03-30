---
title: Creare un'applicazione mappa accessibile con Azure Maps . Mappe di Microsoft Azure
description: In this article, you'll learn how to build an application with accessibility features using Microsoft Azure Maps.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 5fa9e159fa0ac76ce8c585936059fb7f3151c7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473320"
---
# <a name="building-an-accessible-application"></a>Compilazione di un'applicazione accessibile

Più di 20% degli utenti di Internet hanno bisogno di applicazioni web accessibili. Di conseguenza, è importante assicurarsi che l'applicazione sia progettata in modo che qualsiasi utente possa utilizzarla facilmente. Invece di pensare all'accessibilità come a un insieme di attività da completare, considerala come parte della tua esperienza utente complessiva. Più accessibile è l'applicazione, più persone possono utilizzarla. 

Quando si tratta di contenuti interattivi avanzati come una mappa, alcune considerazioni comuni sull'accessibilità sono:When it comes to rich interactive content like a map, some common accessibility considerations are:
- Supportare l'utilità per la lettura dello schermo per gli utenti che hanno difficoltà a visualizzare l'applicazione Web.
- Dispone di più metodi per interagire con l'applicazione Web ed esplorare l'applicazione Web, ad esempio mouse, tocco e tastiera.
- Assicurarsi che il contrasto dei colori sia tale che i colori non si fondono e diventino difficili da distinguere l'uno dall'altro. 

Azure Maps Web SDK è predefinito con molte funzionalità di accessibilità, ad esempio:The Azure Maps Web SDK comes prebuilt with many accessibility features such as:
- Descrizioni dell'utilità per la lettura dello schermo quando la mappa si sposta e quando l'utente si concentra su un controllo o un popup.
- Supporto per mouse, tocco e tastiera.
- Supporto accessibile per il contrasto dei colori nello stile della mappa stradale.

I dettagli di conformità completa all'accessibilità per tutti i prodotti Microsoft sono disponibili [qui](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Cercare "Azure Maps web" per trovare il documento specifico per Azure Maps Web SDK. 

## <a name="navigating-the-map"></a>Navigazione nella mappa

Esistono diversi modi in cui la mappa può essere ingrandita, panoramica, ruotata e inclinata. Di seguito vengono descritti in dettaglio tutti i diversi modi per spostarsi all'interno della mappa.

**Ingrandire la mappa**

- Utilizzando il mouse, fare doppio clic sulla mappa per ingrandire un livello.
- Con il mouse, scorrere la rotellina per ingrandire la mappa.
- Utilizzando un touch screen, toccare la mappa con due dita e pizzicare insieme per ridurre o allontanare le dita per ingrandire.
- Usando un touch screen, tocca due volte la mappa per ingrandire un livello.
- Con la mappa attivata,`+`utilizzare il segno`=`più ( ) o il segno di uguale ( ) per ingrandire un livello.
- Con la mappa attiva, utilizzare il`-`segno meno,`_`il trattino ( ) o il carattere di sottolineatura ( ) per ridurre di un livello.
- Utilizzando il controllo zoom con un mouse, toccare o tastiera tab/inserire i tasti.
- Tenere premuto `Shift` il pulsante e premere il pulsante sinistro del mouse sulla mappa e trascinare per disegnare un'area in cui ingrandire la mappa.

**Eseguire la panoramica della mappa**

- Utilizzando il mouse, premere verso il basso con il pulsante sinistro del mouse sulla mappa e trascinare in qualsiasi direzione.
- Usando un touch screen, tocca la mappa e trascina in qualsiasi direzione.
- Con la mappa attiva, utilizzare i tasti freccia per spostare la mappa.

**Ruotare la mappa**

- Utilizzando il mouse, premere verso il basso con il pulsante destro del mouse sulla mappa e trascinare verso sinistra o destra. 
- Usando un touch screen, tocca la mappa con due dita e ruota.
- Con la mappa attiva, usa il tasto MAIUSC e i tasti freccia sinistra o destra.
- Utilizzando il controllo di rotazione con un mouse, toccare o tastiera tab/inserire i tasti.

**Pitch della mappa**

- Utilizzando il mouse, premere verso il basso con il tasto destro del mouse sulla mappa e trascinare verso l'alto o verso il basso. 
- Usando un touch screen, tocca la mappa con due dita e trascinale verso l'alto o verso il basso insieme.
- Con la mappa messa a fuoco, usa il tasto MAIUSC più i tasti freccia su o giù. 
- Utilizzando il controllo passo con un mouse, toccare o tastiera tab/inserire i tasti.

## <a name="change-the-map-style"></a>Modifica dello stile della mappa

Non tutti gli sviluppatori desiderano che tutti gli stili di mappa possibili siano disponibili nella propria applicazione. Se lo sviluppatore visualizza il controllo selezione stile della mappa, l'utente può modificare lo stile della mappa utilizzando il mouse, un tocco o la tastiera con la scheda o il tasto INVIO. Lo sviluppatore può specificare gli stili della mappa che desidera rendere disponibili nel controllo selezione stili mappa. Inoltre, lo sviluppatore può impostare e modificare lo stile della mappa a livello di codice.

**Contrasto elevato**

- Quando il controllo mappa viene caricato, controlla se il contrasto elevato è abilitato e il browser lo supporta.
- Il controllo mappa non monitora la modalità di contrasto elevato del dispositivo. Se la modalità del dispositivo cambia, la mappa non lo farà. Pertanto, l'utente dovrà ricaricare la mappa aggiornando la pagina.
- Quando viene rilevato un contrasto elevato, lo stile mappa passa automaticamente al contrasto elevato e tutti i controlli incorporati utilizzeranno uno stile di contrasto elevato. Ad esempio, lo stile di contrasto elevato, lo strumento , PitchControl, CompassControl, StyleControl e altri controlli incorporati.
- Ci sono due tipi di contrasto elevato, chiaro e scuro. Se il tipo di contrasto elevato può essere rilevato dai controlli mappa, il comportamento della mappa si adatterà di conseguenza. Se la luce, verrà caricato lo stile della mappa grayscale_light. Se il tipo non può essere rilevato o è scuro, verrà caricato lo stile high_contrast_dark.
- Se si creano controlli personalizzati, è utile sapere se i controlli incorporati utilizzano uno stile di contrasto elevato. Gli sviluppatori possono aggiungere una classe css sul div del contenitore della mappa per controllare. Le classi css che `high-contrast-dark` verrebbero aggiunte sono e `high-contrast-light`. Per verificare l'utilizzo di JavaScript, utilizzare:

```javascript
map.getMapContainer().classList.contains("high-contrast-dark")
```

oppure, utilizzare:

```javascript
map.getMapContainer().classList.contains("high-contrast-light")
```

## <a name="keyboard-shortcuts"></a>Tasti di scelta rapida

La mappa ha una serie di scorciatoie da tastiera integrate che semplificano l'uso della mappa. Questi tasti di scelta rapida funzionano quando la mappa ha lo stato attivo.

| Chiave      | Azione                            |
|----------|-----------------------------------|
| `Tab` | Spostarsi tra i controlli e i popup nella mappa. |
| `ESC` | Spostare lo stato attivo da qualsiasi elemento della mappa all'elemento della mappa di primo livello. |
| `Ctrl` + `Shift` + `D` | Attiva/disattiva il livello di dettaglio dell'utilità per la lettura dello schermo.  |
| Tasto freccia sinistra | Panoramica della mappa a sinistra di 100 pixel |
| Tasto freccia destra | Eseguire la panoramica della mappa a destra di 100 pixel |
| Tasto freccia giù | Eseguire una panoramica della mappa verso il basso di 100 pixel |
| Tasto freccia su | Eseguire una panoramica della mappa fino a 100 pixel |
| `Shift`Freccia su | Aumentare l'altezza della mappa di 10 gradi |
| `Shift`Freccia giù | Diminuire l'altezza della mappa di 10 gradi |
| `Shift`Freccia destra | Ruotare la mappa di 15 gradi in senso orario |
| `Shift`: freccia SINISTRA | Ruotare la mappa di 15 gradi in senso antiorario |
| Segno più`+`( <sup>*</sup>) o`=`segno di uguale ( ) | Fare zoom avanti |
| Segno meno,`-`trattino <sup>*</sup>(`_`) o carattere di sottolineatura ( ) | Fare zoom indietro | 
| `Shift`trascinamento del mouse sulla mappa per disegnare l'area | Ingrandire l'area |

<sup>*</sup>Questi tasti di scelta rapida in genere condividono lo stesso tasto su una tastiera. Questi tasti di scelta rapida sono stati aggiunti per migliorare l'esperienza utente. Inoltre, non importa se l'utente utilizza il tasto MAIUSC o meno per queste scorciatoie.

## <a name="screen-reader-support"></a>Supporto per Screen Reader

Gli utenti possono spostarsi all'interno della mappa usando la tastiera. Se è in esecuzione un'utilità per la lettura dello schermo, la mappa notificherà all'utente le modifiche dello stato. Ad esempio, agli utenti vengono segnalate le modifiche apportate alla mappa quando si visualizza una panoramica o si fa zoom avanti nella mappa. Per impostazione predefinita, la mappa fornisce descrizioni semplificate che escludono il livello di zoom e le coordinate del centro della mappa. L'utente può attivare o disattivare il `Ctrl`  +  `Shift`  +  `D`livello di dettaglio di queste descrizioni utilizzando lo short short short della tastiera.

A eventuali informazioni aggiuntive inserite nella mappa di base devono corrispondere informazioni testuali per gli utenti dell'utilità di lettura dello schermo. Assicurarsi di aggiungere gli attributi [ARIA (Accessible Rich Internet Applications),](https://www.w3.org/WAI/standards-guidelines/aria/)alt e title, se necessario. 

## <a name="make-popups-keyboard-accessible"></a>Rendere accessibili i popup dalla tastiera

Un marcatore o un simbolo viene spesso utilizzato per rappresentare una posizione sulla mappa. Ulteriori informazioni sulla posizione vengono in genere visualizzate in un popup quando l'utente interagisce con il marcatore. Nella maggior parte delle applicazioni, i popup vengono visualizzati quando un utente fa clic o tocca un marcatore. Tuttavia, facendo clic e toccando richiedono all'utente di utilizzare rispettivamente un mouse e un touch screen. È buona norma rendere i popup accessibili quando si utilizza una tastiera. Questa funzionalità può essere ottenuta creando un popup per ogni punto dati e aggiungendolo alla mappa. 

L'esempio seguente carica i punti di interesse sulla mappa utilizzando un layer di simboli e aggiunge un popup alla mappa per ogni punto di interesse. Un riferimento a ogni popup viene archiviato nelle proprietà di ogni punto dati. Può anche essere recuperato per un marcatore, ad esempio quando si fa clic su un marcatore. Quando si è concentrati sulla mappa, premendo il tasto Tab si permetterà all'utente di scorrere ogni popup sulla mappa.

<br/>

<iframe height='500' scrolling='no' title='Creare un applicazione accessibile' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Creare un applicazione accessibile</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Ulteriori suggerimenti per l'accessibilità

Ecco alcuni suggerimenti aggiuntivi per rendere più accessibile l'applicazione di mappatura Web.

- Se si visualizzano molti dati punto interattivi sulla mappa, è consigliabile ridurre il disordine e utilizzare il clustering. 
- Assicurarsi che il rapporto di contrasto dei colori tra testo/simboli e colori di sfondo sia 4.5:1 o più.
- Mantenere i messaggi dell'utilità per la lettura dello schermo (ARIA, alt e title) brevi, descrittivi e significativi. Evitare inutili gergo e acronimi.
- Provare a ottimizzare i messaggi inviati all'utilità per la lettura dello schermo per fornire brevi informazioni significative che è facile per l'utente di digerire. Ad esempio, se si desidera aggiornare l'utilità per la lettura dello schermo a frequenza elevata, ad esempio quando la mappa è in movimento, è consigliabile eseguire le operazioni seguenti:
    - Attendere che la mappa abbia terminato lo spostamento per aggiornare l'utilità per la lettura dello schermo.
    - Limitare gli aggiornamenti a una volta ogni pochi secondi. 
    - Combinare i messaggi in modo logico. 
- Evitare di trasmettere le informazioni solo tramite il colore. Utilizzare testo, icone o motivi per integrare o sostituire il colore. Alcune considerazioni:
    - Se si utilizza un livello bolla per mostrare il valore relativo tra i punti dati, è consigliabile scalare il raggio di ogni bolla, colorare la bolla o entrambe. 
    - È consigliabile usare un layer di simboli con icone diverse per diverse categorie di metriche, ad esempio triangoli, stelle e quadrati. Il livello simbolo supporta anche il ridimensionamento delle dimensioni dell'icona. È anche possibile visualizzare un'etichetta di testo.
    - Se si visualizzano i dati della linea, la larghezza può essere utilizzata per rappresentare lo spessore o le dimensioni. Un modello dash-array può essere utilizzato per rappresentare diverse categorie di linee. Un layer simbolo può essere utilizzato in combinazione con una linea per sovrapporre le icone lungo la linea. L'uso di un'icona a freccia è utile per mostrare il flusso o la direzione della linea.
    - Se si visualizzano dati poligonali, un motivo, ad esempio strisce, può essere utilizzato come alternativa al colore. 
- Alcune visualizzazioni, ad esempio heatmap, tile layer e layer di immagini, non sono accessibili agli utenti con problemi di vista. Alcune considerazioni:
    - Chiedi all'utilità per la lettura dello schermo di descrivere ciò che il layer sta visualizzando quando viene aggiunto alla mappa. Ad esempio, se viene visualizzato un livello di riquadro radar meteo, chiedere all'utilità per la lettura dello schermo di pronunciare "I dati radar meteo sono sovrapposti sulla mappa".
- Limitare la quantità di funzionalità che richiede il passaggio del mouse. Queste funzionalità saranno inaccessibili agli utenti che utilizzano una tastiera o un dispositivo touch per interagire con l'applicazione. Si noti che è comunque consigliabile disporre di uno stile al passaggio del mouse per il contenuto interattivo, ad esempio icone, collegamenti e pulsanti selezionabili.
- Provare a esplorare l'applicazione utilizzando la tastiera. Assicurarsi che l'ordine di tabulazione sia logico.
- Se si creano tasti di scelta rapida, provare a limitarlo a due o meno tasti. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'accessibilità nei moduli Web SDK.

> [!div class="nextstepaction"]
> [Accessibilità degli strumenti di disegno](drawing-tools-interactions-keyboard-shortcuts.md)

Scopri di più sullo sviluppo di app accessibili con Microsoft Learn:

> [!div class="nextstepaction"]
> [Accessibilità nel percorso di apprendimento dei badge digitali d'azione](https://ready.azurewebsites.net/learning/track/2940)

Dai un'occhiata a questi utili strumenti di accessibilità:
> [!div class="nextstepaction"]
> [Sviluppo di app accessibili](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Panoramica di WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Strumento di valutazione dell'accessibilità Web (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Controllo del contrasto dei colori WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Nessun simulatore di visione del caffè](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
