---
title: Linee guida per le esperienze di ancoraggio efficace che usano Azure Anchor spaziali | Microsoft Docs
description: Linee guida e considerazioni per creare e individuare in modo efficace gli ancoraggi tramite gli ancoraggi spaziale di Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e77dcd96ffa0fbd57aa0ed1b4f857279ca768a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60566030"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Creare un'esperienza efficace ancoraggio usando gli ancoraggi spaziale di Azure

Questo articolo vengono fornite linee guida e considerazioni che consentono in modo efficace creare e individuare gli ancoraggi tramite gli ancoraggi spaziale.

## <a name="good-anchors"></a>Ancoraggi buona

Gli ancoraggi spaziali consente di creare ancoraggi buona. È importante investire tempo nella formazione o dissuadere gli utenti durante l'utilizzo di utente (UX) per creare punti di ancoraggio validi. Investendo nella creazione fin dall'inizio gli ancoraggi ottimali, consentire agli utenti finali per individuare in modo affidabile gli ancoraggi:

- Su dispositivi diversi.
- In momenti diversi.
- In condizioni di illuminazione diversi.
- Da una prospettiva desiderato all'interno dello spazio.

## <a name="static-and-dynamic-locations"></a>Indirizzi statici e dinamici

Parte della progettazione dell'esperienza di ancoraggio della selezione di percorsi. I percorsi sarà definita da un amministratore dello spazio e statici? O sarà dinamico e definito dall'utente?

Un gestore dell'archivio delle vendite al dettaglio potrebbe essere necessario un'esperienza in negozio statica a indurre gli utenti di visitare. Ma lo sviluppatore di un gioco da tavolo realtà mista probabile che si voglia consentire agli utenti di scegliere dove eseguire la riproduzione.

Per i percorsi statici, consentono di apprendere rapidamente agli amministratori di dedicare tempo curando lo spazio con punti di ancoraggio validi.

Per i percorsi dinamici, è opportuno valutare come tenute o guidare gli utenti nell'esperienza utente per creare punti di ancoraggio validi.

## <a name="stable-visual-features"></a>Funzionalità di visual stabile

Sistemi di rilevamento Visual usati nella realtà mista e i dispositivi di realtà aumentata si basano sulle funzionalità dell'ambiente di visual. Per ottenere l'esperienza più affidabile:  

- *Eseguire operazioni* creare ancoraggi nelle posizioni con funzionalità visive stabile (vale a dire, caratteristiche che non vengono modificati spesso).

- *Non* creare ancoraggi su superfici vuote che non dispongono di alcun caratteristiche distintive.

- *Non* creare ancoraggi sulle prime altamente riflettente.

- *Non* creare ancoraggi nelle aree in cui il modello si ripete, ad esempio carpet o lo sfondo.

![Esempi di un ambiente ottimo per gli ancoraggi e un ambiente non corretto per gli ancoraggi](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Diverse prospettive di visualizzazione

Quando si crea un ancoraggio, considerare le persone che in un secondo momento tenterà di individuare il punto di ancoraggio.

Si consideri, ad esempio, un ancoraggio all'interno di una chat con due porte. Probabilmente si desidera consentire agli utenti di accedere alla chat da uno sportello. Quando si crea il punto di ancoraggio, è necessario analizzare la posizione da entrambe le porte. È modificare prospettive per acquisire dati di ambiente attorno all'ancoraggio in modo che gli utenti possano individuare il punto di ancoraggio da uno sportello.

In generale, quando si crea un ancoraggio, eseguirne la scansione le prospettive di persone che verranno effettuato un tentativo per consentirne l'individuazione. Pertanto, se si desidera inserire contenuto virtuale su un scultura esterni, è opportuno a camminare scultura, durante l'analisi, come si crea il punto di ancoraggio. Se l'ancoraggio è l'angolo di una chat, è presente solo una direzione per affrontarlo, da. Quando si crea questo ancoraggio, è possibile digitalizzarlo solo da questa prospettiva.

## <a name="multiple-anchors"></a>Più punti di ancoraggio

Illuminazione può fare la differenza nelle funzionalità visual che rileva un'app. Gli ancoraggi creati in luce naturale forte potrebbero essere difficili da individuare in luce artificiale e viceversa.  

Se si verifica questo problema, può essere utile per creare due punti di ancoraggio. Nella stessa posizione, creare un ancoraggio in luce del giorno e l'altro in luce artificiale. L'app può quindi eseguire una query per entrambi gli ancoraggi. Quando l'ancoraggio viene individuato, l'app avrà una posa per il punto di ancoraggio. 

Allo stesso modo, in ambienti in cui la funzionalità di visual cambiano in quanto la maggior parte degli oggetti vengono spostati, può consentire più punti di ancoraggio. Quando un ancoraggio diventa troppo difficile da individuare a causa di modifiche significative nell'ambiente, è possibile sostituire il punto di ancoraggio con uno nuovo. È possibile eseguire questa operazione, ad esempio, in un negozio al dettaglio in cui il layout viene aggiornato ogni pochi mesi.

## <a name="targets-and-rooms"></a>Le destinazioni e chat

In molti casi, un ancoraggio è un punto di ingresso per l'esperienza dell'app. È opportuno iniziare questo passaggio rapidamente e in modo affidabile pertanto gli utenti possono immettere l'esperienza. Dedicare tempo alla modalità con cui gli utenti troveranno i punti di ancoraggio è un passaggio importante. È utile pensare a ricerca di punti di ancoraggio in termini di due scenari di vasto: *destinazioni* e *chat*.

### <a name="targets"></a>Destinazioni

Nello scenario di destinazione, la posizione di ancoraggio è ben nota. In un'applicazione fittizia disegno di realtà mista, ad esempio, un utente inserisce un'area di disegno virtuale su una parete. Anna indica gli altri utenti nella chat room in modo da puntare i propri dispositivi nella stessa posizione su una parete per individuare il punto di ancoraggio e iniziare l'esperienza.  

Un altro esempio di uno scenario di destinazione potrebbe essere un segno di un bar con il testo "Cercare trattative". Bar o ha inserito un ancoraggio qui. Come gli utenti di analizzare il segno, essi individuare il punto di ancoraggio e immettere l'esperienza di realtà aumentata per trovare offerte sulla bar.

Nello scenario di destinazione, possono aiutare foto. Se si visualizza agli utenti una foto della destinazione prevista nel proprio dispositivo, è possibile identificare rapidamente gli elementi da analizzare nel mondo reale. Ad esempio, si potrebbero aiutare gli utenti perviene entro l'area generale di una destinazione desiderata tramite GPS. Quando si arriva all'utente, l'app Mostra una foto della destinazione. L'utente cerca tutto lo spazio, consente di trovare la destinazione e cerca il punto di ancoraggio.

![Illustrazione di un punto di ancoraggio, che illustra una foto della destinazione in un dispositivo dell'utente per dispositivi mobili](./media/start-here-edit.png)

### <a name="rooms"></a>chat room

Nello scenario di spazio, gli utenti immettono un spazio è sufficiente conoscere che è un ancoraggio qui un punto qualsiasi. Gli utenti analizza lo spazio con il dispositivo e di individuare rapidamente il punto di ancoraggio.

Questa esperienza in genere è necessario creare ancoraggi ben curati, come illustrato nelle varie prospettive di visualizzazione. Se sottoposto a scansione chat room da molte angolazioni quando è stato creato il punto di ancoraggio, gli utenti possono analizzare quasi ovunque quando tentano di individuarla.

![Illustrazione di come un utente consente di analizzare una chat room per trovare un ancoraggio](./media/scan-room.png)

In pratica, si dedica più tempo l'analisi dello spazio quando si crea il punto di ancoraggio in modo che gli utenti successivi possono analizzare e individuare rapidamente il punto di ancoraggio. Come si crea l'esperienza, è necessario tenere presente questo importante compromesso.

L'esempio di app di realtà mista disegno cui abbiamo discusso in precedenza non funziona anche come scenario di spazio. In questo caso, l'utente posiziona il punto di ancoraggio richiede ad altri utenti l'esperienza di aggiunta rapida. Gli utenti non desiderano attendere prima di iniziare l'esperienza fino a quando non viene anche analizzata chat room. Poiché tutti gli utenti sappiano esattamente dove scegliere il dispositivo per individuare gli ancoraggi, questo esempio funziona meglio come scenario di destinazione.

## <a name="anchor-location"></a>Posizione di ancoraggio

I sistemi di rilevamento Visual si basano sulle funzionalità di visual in un ambiente. Le funzionalità più visiva che include un'analisi, maggiore sarà la probabilità di trovare un ancoraggio.

Seguire le linee guida generali in questa sezione per creare un'esperienza utente che invoglino un'utile analisi dell'ambiente.

In primo luogo, se l'utente non consente di trovare un ancoraggio entro pochi secondi, l'app deve incoraggiare gli utenti per spostare il dispositivo per acquisire più prospettive. L'app può anche incoraggiare gli utenti a se stessi spostarsi all'ambiente di analisi per l'ancoraggio da più prospettive. Le altre prospettive di funzionalità visibili per il dispositivo, è preferibile.

Per gli scenari di destinazione, chiedere all'utente per spostarsi all'interno di destinazione per la visualizzazione da prospettive diverse. In altre parole, chiedere all'utente di acquisire la destinazione di nuove prospettive fino a quando non si trova il punto di ancoraggio.

Per gli scenari di chat, chiedere all'utente di lentamente analizza stanza. Ad esempio, chiedere all'utente di impostare in modo da acquisire 180 gradi o persino a 360 gradi della chat room. O chiedere all'utente di visualizzare una prospettiva nuova chat room. 

Il metodo più significativo è a analizza stanza. Un'analisi tutta la stanza acquisisce altre funzionalità visive dell'ambiente di un'analisi di una parete nelle vicinanze, ad esempio. Un'analisi di una parete nelle vicinanze non acquisire tante funzionalità visive utile dell'ambiente.

Non è utile spostare più volte da lato a lato dispositivo durante la ricerca di un ancoraggio. Ciò si limita ad acquisire gli stessi punti dal punto di vista stessa.

## <a name="experience-tests"></a>Esperienza di test

In questo articolo si sono affermato linee guida generali. Con gli ancoraggi spaziali, si scrivono applicazioni che interagiscono con il mondo reale. Per questo motivo, è consigliabile dedicare tempo per testare gli scenari di ancoraggio dell'app in ambienti reali. Ciò vale soprattutto per gli ambienti che rappresentano in cui si prevede che gli utenti usino l'app.
