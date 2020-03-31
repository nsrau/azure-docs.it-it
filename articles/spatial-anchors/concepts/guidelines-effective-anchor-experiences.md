---
title: Linee guida per esperienze di ancoraggio efficaci
description: Linee guida e considerazioni per creare e individuare gli ancoraggi in modo efficace usando gli ancoraggi spaziali di Azure.Guidelines and considerations to create and locate anchors effectively by using Azure Spatial Anchors.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270512"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Creare un'esperienza di ancoraggio efficace usando gli ancoraggi spaziali di AzureCreate an effective anchor experience by using Azure Spatial Anchors

In questo articolo vengono fornite linee guida e considerazioni che consentono di creare e individuare in modo efficace gli ancoraggi utilizzando gli ancoraggi spaziali.

## <a name="good-anchors"></a>Buone ancore

Ancore spaziali consente di creare ancore buone. È importante investire tempo nell'educazione o nella guida degli utenti nella tua esperienza utente (UX) per creare ancore buone. Investendo nella creazione di buoni ancoraggi in anticipo, si aiutano gli utenti finali a trovare gli ancoraggi in modo affidabile:

- Su diversi dispositivi.
- In vari momenti.
- In diverse condizioni di illuminazione.
- Dalle prospettive desiderate all'interno dello spazio.

## <a name="static-and-dynamic-locations"></a>Posizioni statiche e dinamiche

Parte della progettazione dell'esperienza di ancoraggio è la scelta delle posizioni. Le posizioni saranno statiche e definite da un amministratore dello spazio? O saranno dinamici e definiti dall'utente?

Un responsabile del punto vendita al dettaglio potrebbe volere un'esperienza statica in negozio per invogliare gli utenti a visitare. Ma lo sviluppatore di un gioco da tavolo a realtà mista probabilmente vorrebbe consentire agli utenti di scegliere dove giocare.

Per le posizioni statiche, puoi insegnare agli amministratori a dedicare del tempo a curare lo spazio con buoni ancoraggi.

Per le posizioni dinamiche, dovresti pensare a come insegni o guidi gli utenti dell'esperienza utente a creare ancore buoni.

## <a name="stable-visual-features"></a>Caratteristiche visive stabili

I sistemi di tracciamento visivo utilizzati su dispositivi a realtà mista e a realtà aumentata si basano sulle caratteristiche visive dell'ambiente. Per ottenere l'esperienza più affidabile:

- *Creare* ancoraggi in posizioni con feature visive stabili, ovvero feature che non cambiano spesso.

- *Non* creare ancoraggi su superfici vuote di grandi dimensioni che non hanno caratteristiche distintive.

- *Non* creare ancore su materiali altamente riflettenti.

- *Non* creare ancoraggi su superfici in cui il motivo si ripete, ad esempio tappeto o carta da parati.

![Esempi di un buon ambiente per le ancore e un ambiente non valido per le ancore](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Varie prospettive di visione

Quando crei un'ancora, pensa alle persone che in seguito cercheranno di localizzare l'ancora.

Si consideri, ad esempio, un'ancora al centro di una stanza che dispone di due porte. Probabilmente si desidera consentire agli utenti di entrare nella stanza da entrambe le porte. Quando crei l'ancora, dovrai scansionare la sua posizione da entrambe le porte. È possibile modificare le prospettive per acquisire i dati di ambiente intorno all'ancoraggio in modo che gli utenti possano individuare l'ancoraggio da entrambe le porte.

In generale, quando si crea un'ancora, eseguire la scansione dalle prospettive delle persone che cercheranno di individuarlo. Quindi, se stai posizionando contenuti virtuali su una scultura all'aperto, ha senso camminare intorno alla scultura, mentre la scansione, mentre crei l'ancora. Se l'ancora si trova nell'angolo di una stanza, c'è solo una direzione da cui avvicinarsi. Quando si crea questo ancoraggio, è possibile eseguire la scansione solo da questa prospettiva.

## <a name="multiple-anchors"></a>Ancoraggi multipli

L'illuminazione può fare la differenza nelle funzionalità visive rilevate da un'app. Le ancore create con una forte luce naturale potrebbero essere difficili da individuare nella luce artificiale e viceversa.

Se si dispone di questo problema, può aiutare a creare due ancoraggi. Nello stesso punto, creare un'ancora alla luce del giorno e un'altra in luce artificiale. L'app può quindi eseguire query per entrambi gli ancoraggi. Quando si trova un ancoraggio, l'app avrà una posa per l'ancoraggio.

Analogamente, negli ambienti in cui le funzionalità visive cambiano perché la maggior parte degli oggetti si sposta, più ancoraggi possono essere utili. Quando un'ancora diventa troppo difficile da trovare a causa di cambiamenti significativi nell'ambiente, è possibile sostituire l'ancoraggio con uno nuovo. È possibile eseguire questa operazione, ad esempio, in un punto vendita al dettaglio in cui il layout viene aggiornato ogni pochi mesi.

## <a name="targets-and-rooms"></a>Obiettivi e locali

In molti casi, un ancoraggio è un punto di ingresso all'esperienza dell'app. Ti consigliamo di ottenere attraverso questo passaggio in modo rapido e affidabile in modo che gli utenti possono inserire la tua esperienza. Trascorrere del tempo su come gli utenti troveranno le ancore è un importante passo di progettazione. È utile pensare di trovare ancore in termini di due scenari generali: *obiettivi* e *camere*.

### <a name="targets"></a>Server di destinazione

Nello scenario di destinazione, la posizione di un ancoraggio è ben nota. Ad esempio, in un'app fittizia di pittura di realtà mista, un utente posiziona una tela virtuale sul muro. Indica agli altri utenti della stanza di puntare i loro dispositivi nello stesso punto della parete per individuare l'ancora e iniziare l'esperienza.

Un altro esempio di uno scenario di destinazione potrebbe essere un cartello in un bar che dice "Scansione per le offerte". La caffetteria ha posto un'ancora qui. Mentre gli utenti scansionano il cartello, individuano l'ancora e entrano nell'esperienza di realtà aumentata per trovare offerte sul caffè.

Nello scenario di destinazione, le foto possono essere utili. Se mostri agli utenti una foto del target previsto sul proprio dispositivo, questi possono identificare rapidamente cosa eseguire la scansione nel mondo reale. Ad esempio, potresti aiutare gli utenti ad arrivare all'interno dell'area generale di un obiettivo previsto utilizzando il GPS. Quando l'utente arriva, l'app mostra una foto della destinazione. L'utente guarda intorno allo spazio, trova la destinazione e cerca l'ancoraggio.

![Illustrazione di un'ancora che mostra una foto del target sul dispositivo mobile di un utente](./media/start-here-edit.png)

### <a name="rooms"></a>Club

Nello scenario della stanza, gli utenti entrano in uno spazio semplicemente sapendo che c'è un'ancora qui da qualche parte. Gli utenti scansionano lo spazio con il proprio dispositivo e individuano rapidamente l'ancora.

Questa esperienza richiede in genere la creazione di ancoraggi ben curati, come descritto in varie prospettive di visualizzazione. Se la stanza è stata scansionata da molte prospettive quando è stata creata l'ancoraggio, gli utenti possono eseguire la scansione quasi ovunque quando tentano di individuarla.

![Illustrazione di come un utente può eseguire la scansione di una stanza per trovare un'ancora](./media/scan-room.png)

Essenzialmente, si impiega più tempo a eseguire la scansione dello spazio quando si crea l'ancoraggio in modo che gli utenti successivi possano eseguire la scansione e individuare rapidamente l'ancoraggio. Quando crei la tua esperienza, dovrai considerare questo importante compromesso.

L'esempio dell'app di pittura a realtà mista di cui abbiamo parlato in precedenza non funziona bene come uno scenario di stanza. Qui, l'utente che posiziona l'ancoraggio vuole che altri si uniscano rapidamente all'esperienza. Gli utenti non vogliono aspettare per iniziare l'esperienza fino a quando la stanza è ben scansionata. Poiché tutti gli utenti sanno esattamente dove puntare il dispositivo per individuare gli ancoraggi, questo esempio funziona meglio come scenario di destinazione.

## <a name="anchor-location"></a>Posizione di ancoraggio

I sistemi di tracciamento visivo si basano sulle funzionalità visive in un ambiente. Maggiore è il numero di caratteristiche visive incluse in una scansione, maggiore è la probabilità di trovare un'ancoraggio.

Seguire le linee guida generali in questa sezione per creare un'esperienza utente che incoraggi un'analisi utile dell'ambiente.

In primo luogo, se l'utente non individua un ancoraggio entro pochi secondi, l'app deve incoraggiare gli utenti a spostare il dispositivo per acquisire più prospettive. L'applicazione può anche incoraggiare gli utenti a muoversi intorno all'ambiente per eseguire la scansione per l'ancora da più prospettive. Più caratteristiche sono le prospettive che il dispositivo vede, meglio è.

Per gli scenari di destinazione, chiedere all'utente di spostarsi all'altro della destinazione per visualizzarla da prospettive diverse. In altre parole, chiedi all'utente di catturare la destinazione da nuove prospettive fino a quando non viene individuato l'ancoraggio.

Per gli scenari di sala, chiedere all'utente di eseguire lentamente la scansione della stanza. Ad esempio, chiedi all'utente di rivolgersi per catturare 180 gradi o anche 360 gradi della stanza. Oppure chiedi all'utente di visualizzare la stanza da una nuova prospettiva.

Il metodo più significativo è quello di eseguire la scansione attraverso la stanza. Una scansione nella stanza cattura più caratteristiche visive dell'ambiente rispetto ad esempio a una scansione di una parete vicina. Una scansione di una parete vicina non cattura tutte le caratteristiche visive utili dell'ambiente.

Non è utile spostare ripetutamente il dispositivo da un lato all'altro quando si cerca un'ancora. Questo cattura semplicemente gli stessi punti dalla stessa prospettiva.

## <a name="experience-tests"></a>Test dell'esperienza

In questo articolo sono state illustrate le linee guida generali. Con le ancore spaziali, stai scrivendo app che interagiscono con il mondo reale. Per questo, è necessario dedicare tempo al test degli scenari di ancoraggio dell'app in ambienti reali. Ciò è particolarmente vero per gli ambienti che rappresentano in cui si prevede che gli utenti di utilizzare l'app.
