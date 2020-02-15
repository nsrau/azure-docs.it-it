---
title: Creare un'applicazione mappa accessibile con mappe di Azure | Mappe Microsoft Azure
description: In questo articolo si apprenderà come compilare un'applicazione con le funzionalità di accessibilità usando Microsoft Azure maps.
services: azure-maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2019
ms.topic: conceptual
ms.service: azure-maps
manager: cpendleton
ms.openlocfilehash: 2ae84b59cd70a5b27ad3e501db6cfae110d90fbd
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209784"
---
# <a name="building-an-accessible-application"></a>Compilazione di un'applicazione accessibile

Verso l'alto del 20% degli utenti Internet è necessario disporre di applicazioni Web accessibili. Di conseguenza, è importante assicurarsi che l'applicazione sia progettata in modo che qualsiasi utente possa utilizzarla facilmente. Invece di considerare l'accessibilità come un set di attività da completare, è possibile considerarlo come parte dell'esperienza utente complessiva. Più accessibile è l'applicazione, maggiore è il numero di utenti che possono usarlo. 

Quando si tratta di contenuti interattivi avanzati come una mappa, alcune considerazioni comuni sull'accessibilità sono:
- Supportare l'applicazione per la lettura dello schermo per gli utenti che hanno difficoltà a visualizzare l'applicazione Web.
- Sono disponibili più metodi per interagire con ed esplorare l'applicazione Web, ad esempio il mouse, il tocco e la tastiera.
- Assicurarsi che il contrasto dei colori sia tale che i colori non si mescolino tra loro e diventano difficili da distinguere tra loro. 

Azure Maps Web SDK è precompilato con numerose funzionalità di accessibilità, ad esempio:
- Descrizioni dello screen reader quando la mappa viene spostata e quando l'utente si concentra su un controllo o un popup.
- Supporto per mouse, tocco e tastiera.
- Supporto per il contrasto dei colori accessibile nello stile della mappa stradale.

I dettagli completi sulla conformità dell'accessibilità per tutti i prodotti Microsoft sono disponibili [qui](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/). Cercare "Azure Maps web" per trovare il documento specifico per Azure Maps Web SDK. 

## <a name="navigating-the-map"></a>Esplorazione della mappa
Esistono diversi modi in cui è possibile eseguire lo zoom, la panoramica, la rotazione e il pitch della mappa. Di seguito vengono descritti in dettaglio tutti i modi per esplorare la mappa.

**Zoom della mappa**
- Utilizzando un mouse, fare doppio clic sulla mappa per ingrandire un livello.
- Utilizzando un mouse, scorrere la rotellina per ingrandire la mappa.
- Utilizzando un touch screen, toccare la mappa con due dita e pizzicare insieme per eseguire lo zoom indietro o dividere le dita per eseguire lo zoom avanti.
- Utilizzando un touch screen, toccare la mappa per ingrandire un livello.
- Con lo stato attivo della mappa, usare il segno più (`+`) o il segno * uguale (`=`) per ingrandire un livello.
- Con lo stato attivo della mappa, usare il segno meno, il trattino (`-`) o il carattere di sottolineatura (`_`) per eseguire lo zoom indietro di un livello.
- Utilizzare il controllo zoom con un mouse, un tocco o una scheda tastiera/invio.
- Premere e tenere premuto il pulsante `Shift` e premere il pulsante sinistro del mouse sulla mappa e trascinare per creare un'area in cui ingrandire la mappa.

**Panoramica della mappa**
- Con il mouse, premere il pulsante sinistro del mouse sulla mappa e trascinare in qualsiasi direzione.
- Utilizzando un touchscreen, toccare la mappa e trascinarla in qualsiasi direzione.
- Con lo stato attivo della mappa, utilizzare i tasti di direzione per spostare la mappa.

**Ruotare la mappa**
- Con il mouse, premere il pulsante destro del mouse sulla mappa e trascinare verso sinistra o verso destra. 
- Utilizzando un touchscreen, toccare la mappa con due dita e ruotare.
- Con lo stato attivo della mappa, utilizzare il tasto MAIUSC e i tasti freccia sinistra o destra.
- Uso del controllo di rotazione con i tasti di scelta del mouse, di tocco o di tastiera/invio.

**Pitch mappa**
- Usando il mouse, premere il pulsante destro del mouse sulla mappa e trascinare verso l'alto o verso il basso. 
- Utilizzando un touchscreen, toccare la mappa con due dita e trascinarle insieme.
- Con lo stato attivo della mappa, utilizzare il tasto MAIUSC più i tasti freccia su o giù. 
- Utilizzando il controllo pitch con un mouse, un tocco o una scheda tastiera/invio.

**Modificare lo stile della mappa** Non tutti gli sviluppatori desiderano che tutti gli stili della mappa siano disponibili nell'applicazione. Lo sviluppatore può impostare e modificare a livello di codice lo stile della mappa. Se lo sviluppatore Visualizza il controllo selezione stile della mappa, l'utente può modificare lo stile della mappa utilizzando il mouse, un tocco o la tastiera con la scheda o il tasto INVIO. Lo sviluppatore può specificare gli stili della mappa che si desidera rendere disponibili nel controllo selezione stile mappa. 

## <a name="keyboard-shortcuts"></a>Tasti di scelta rapida

La mappa dispone di una serie di tasti di scelta rapida incorporati che semplificano l'uso della mappa. Questi tasti di scelta rapida funzionano quando la mappa ha lo stato attivo.

| Chiave      | Azione                            |
|----------|-----------------------------------|
| `Tab` | Spostarsi tra i controlli e i popup nella mappa. |
| `ESC` | Spostare lo stato attivo da qualsiasi elemento della mappa all'elemento della mappa di primo livello. |
| `Ctrl` + `Shift` + `D` | Consente di impostare il livello di dettaglio lettore schermo.  |
| Tasto freccia sinistra | Panoramica della mappa a sinistra di 100 pixel |
| Tasto freccia destra | Panoramica della mappa a destra di 100 pixel |
| Tasto freccia giù | Panoramica della mappa in basso 100 pixel |
| Tasto freccia su | Panoramica della mappa su 100 pixel |
| `Shift` + freccia su | Aumenta il pitch della mappa di 10 gradi |
| `Shift` + freccia giù | Riduci il pitch della mappa di 10 gradi |
| `Shift` + freccia destra | Ruotare la mappa di 15 gradi in senso orario |
| `Shift` + freccia sinistra | Ruotare la mappa di 15 gradi in senso antiorario |
| Segno più (`+`) o <sup>*</sup>segno di uguale (`=`) | Eseguire lo zoom avanti |
| Segno meno, trattino (`-`) o carattere di sottolineatura <sup>*</sup>(`_`) | Eseguire lo zoom indietro | 
| `Shift` + trascina il mouse sulla mappa per l'area di estrazione | Ingrandisci area |

<sup>*</sup> Questi tasti di scelta rapida della chiave condividono in genere la stessa chiave su una tastiera. Questi tasti di scelta rapida sono stati aggiunti per migliorare l'esperienza utente. Inoltre, non è importante se l'utente utilizza il tasto MAIUSC o meno per questi tasti di scelta rapida.

## <a name="screen-reader-support"></a>Supporto per la lettura dello schermo

Gli utenti possono spostarsi all'interno della mappa usando la tastiera. Se è in esecuzione un'utilità per la lettura dello schermo, la mappa notificherà all'utente le modifiche dello stato. Ad esempio, agli utenti vengono segnalate le modifiche apportate alla mappa quando si visualizza una panoramica o si fa zoom avanti nella mappa. Per impostazione predefinita, la mappa fornisce descrizioni semplificate che escludono il livello di zoom e le coordinate del centro della mappa. L'utente può abilitare o disabilitare il livello di dettaglio di queste descrizioni usando la scorciatoia da tastiera `Ctrl` + `Shift` + `D`.

A eventuali informazioni aggiuntive inserite nella mappa di base devono corrispondere informazioni testuali per gli utenti dell'utilità di lettura dello schermo. Assicurarsi di aggiungere ad [Internet Applications (aria)](https://www.w3.org/WAI/standards-guidelines/aria/), ALT e attributi di titolo accessibili laddove appropriato. 

## <a name="make-popups-keyboard-accessible"></a>Rendere accessibile la tastiera popup

Un marcatore o un simbolo viene spesso utilizzato per rappresentare una posizione sulla mappa. Informazioni aggiuntive sulla posizione vengono in genere visualizzate in un popup quando l'utente interagisce con il marcatore. Nella maggior parte delle applicazioni, i popup vengono visualizzati quando un utente fa clic o tocca un marcatore. Tuttavia, facendo clic e toccando, è necessario che l'utente usi rispettivamente un mouse e un touchscreen. È consigliabile fare in modo che i popup siano accessibili quando si usa una tastiera. Questa funzionalità può essere eseguita creando un popup per ogni punto dati e aggiungendolo alla mappa. 

Nell'esempio seguente vengono caricati i punti di interesse sulla mappa utilizzando un livello di simbolo e viene aggiunto un popup alla mappa per ogni punto di interesse. Un riferimento a ogni popup viene archiviato nelle proprietà di ogni punto dati. Può anche essere recuperato per un marcatore, ad esempio quando si fa clic su un marcatore. Quando si è concentrati sulla mappa, premere il tasto TAB per consentire all'utente di scorrere ogni popup sulla mappa.

<br/>

<iframe height='500' scrolling='no' title='Creare un applicazione accessibile' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Vedere l'elemento Pen <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Creare un applicazione accessibile</a> di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>. </iframe>

<br/>

## <a name="additional-accessibility-tips"></a>Suggerimenti sull'accessibilità aggiuntivi

Ecco alcuni suggerimenti aggiuntivi per rendere più accessibile l'applicazione di mapping Web.

- Se si visualizzano molti dati punto interattivi sulla mappa, provare a ridurre il disordine e a usare il clustering. 
- Verificare che il rapporto contrasto colori tra testo/simboli e colori di sfondo sia 4.5:1 o più.
- Mantieni i messaggi (attributi ARIA, ALT e titolo) per la lettura dello schermo, brevi, descrittivi e significativi. Evitare gli acronimi e il gergo superflui.
- Provare ad ottimizzare i messaggi inviati all'utilità per la lettura dello schermo per fornire informazioni significative brevi che è facile da digerire per l'utente. Ad esempio, se si desidera aggiornare la lettura dello schermo a frequenza elevata, ad esempio quando la mappa è in fase di trasferimento, provare a eseguire i punti seguenti:
    - Attendere il completamento del passaggio della mappa per aggiornare la lettura dello schermo.
    - Limitare gli aggiornamenti a ogni pochi secondi. 
    - Combinare i messaggi in modo logico. 
- Evitare di usare il colore come unico mezzo per fornire informazioni. Usare testo, icone o modelli per integrare o sostituire il colore. Alcune considerazioni:
    - Se si usa un livello Bubble per visualizzare il valore relativo tra i punti dati, provare a ridimensionare il raggio di ogni bolla, colorare la bolla o entrambi. 
    - Prendere in considerazione l'uso di un livello di simbolo con icone diverse per diverse categorie di metriche, ad esempio triangoli, stelle e quadrati. Il livello dei simboli supporta anche la scalabilità delle dimensioni dell'icona. È anche possibile visualizzare un'etichetta di testo.
    - Se si visualizzano i dati della riga, è possibile usare la larghezza per rappresentare il peso o le dimensioni. Un modello di matrice Dash può essere utilizzato per rappresentare diverse categorie di righe. Un livello di simbolo può essere usato in combinazione con una linea per sovrapporre le icone lungo la riga. L'uso di un'icona a freccia è utile per visualizzare il flusso o la direzione della linea.
    - Se vengono visualizzati dati poligoni, è possibile usare un modello, ad esempio strisce, come alternativa al colore. 
- Alcune visualizzazioni, ad esempio Heatmaps, livelli di sezione e livelli di immagine, non sono accessibili per gli utenti con problemi di visione. Alcune considerazioni:
    - Fare in modo che l'lettore della schermata descriva le informazioni visualizzate dal livello quando viene aggiunto alla mappa. Se, ad esempio, viene visualizzato un livello sezione del radar meteorologico, fare in maniera che la lettura dello schermo includa i dati radar meteorologici sovrapposti sulla mappa.
- Limitare la quantità di funzionalità che richiede il passaggio del mouse. Queste funzionalità non saranno accessibili agli utenti che usano una tastiera o un dispositivo touch per interagire con l'applicazione. Si noti che è comunque consigliabile avere uno stile hover per contenuto interattivo, ad esempio icone, collegamenti e pulsanti selezionabili.
- Provare a spostarsi nell'applicazione usando la tastiera. Assicurarsi che l'ordinamento delle schede sia logico.
- Se si creano tasti di scelta rapida, provare a limitarlo a due chiavi o meno. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'accessibilità nei moduli Web SDK.

> [!div class="nextstepaction"]
> [Accessibilità degli strumenti di disegno](drawing-tools-interactions-keyboard-shortcuts.md)

Informazioni sullo sviluppo di app accessibili con Microsoft Learn:

> [!div class="nextstepaction"]
> [Accessibilità nel percorso di apprendimento delle notifiche digitali azione](https://ready.azurewebsites.net/learning/track/2940)

Esaminare questi utili strumenti di accessibilità:
> [!div class="nextstepaction"]
> [Sviluppo di app accessibili](https://developer.microsoft.com/windows/accessible-apps)

> [!div class="nextstepaction"]
> [Panoramica di WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/)

> [!div class="nextstepaction"]
> [Strumento di valutazione dell'accessibilità Web (WAVE)](https://wave.webaim.org/)

> [!div class="nextstepaction"]
> [Controllo contrasto colori WebAim](https://webaim.org/resources/contrastchecker/)

> [!div class="nextstepaction"]
> [Nessun simulatore per la visione del caffè](https://chrome.google.com/webstore/detail/nocoffee/jjeeggmbnhckmgdhmgdckeigabjfbddl?hl=en-US)
