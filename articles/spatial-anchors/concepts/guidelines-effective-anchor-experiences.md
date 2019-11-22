---
title: Linee guida per esperienze di ancoraggio valide
description: Linee guida e considerazioni per creare e individuare in modo efficace gli ancoraggi usando gli ancoraggi spaziali di Azure.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270512"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Creare un'esperienza di ancoraggio efficace usando gli ancoraggi spaziali di Azure

Questo articolo fornisce linee guida e considerazioni per semplificare la creazione e l'individuazione di ancoraggi usando ancoraggi spaziali.

## <a name="good-anchors"></a>Ancoraggi validi

Gli ancoraggi spaziali consentono di creare ancoraggi validi. È importante investire tempo nell'istruzione o nel guidare gli utenti nell'esperienza utente (UX) per creare ancoraggi efficaci. Investendo in anticipo la creazione di ancoraggi efficaci, è possibile aiutare gli utenti finali a trovare in modo affidabile gli ancoraggi:

- In dispositivi diversi.
- In diversi momenti.
- In condizioni di illuminazione diverse.
- Dalle prospettive desiderate all'interno dello spazio.

## <a name="static-and-dynamic-locations"></a>Posizioni statiche e dinamiche

Parte della progettazione dell'esperienza di ancoraggio è la scelta dei percorsi. Le posizioni saranno statiche e definite da un amministratore dello spazio? O saranno dinamici e definiti dall'utente?

Un responsabile del negozio per la vendita al dettaglio potrebbe voler indurre gli utenti a visitare un'esperienza statica in archivio. Tuttavia, lo sviluppatore di un gioco di lavagna in realtà mista potrebbe voler consentire agli utenti di scegliere la posizione di riproduzione.

Per le posizioni statiche, è possibile insegnare agli amministratori di dedicare tempo alla cura dello spazio con ancoraggi corretti.

Per i percorsi dinamici, è necessario considerare come insegnare o guidare gli utenti nell'esperienza utente per creare ancoraggi efficaci.

## <a name="stable-visual-features"></a>Funzionalità visive stabili

I sistemi di rilevamento visivi utilizzati nei dispositivi a realtà mista e in realtà aumentata si basano sulle funzionalità visive dell'ambiente. Per ottenere un'esperienza più affidabile:

- *Creare ancoraggi* in posizioni con funzionalità visive stabili, ovvero funzionalità che non cambiano spesso.

- *Non* creare ancoraggi su superfici vuote di grandi dimensioni che non hanno caratteristiche distintive.

- *Non* creare ancoraggi su materiali altamente riflettenti.

- *Non* creare ancoraggi sulle superfici in cui il pattern si ripete, ad esempio Carpet o wallpaper.

![Esempi di un ambiente efficace per gli ancoraggi e un ambiente non valido per gli ancoraggi](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Diverse prospettive di visualizzazione

Quando si crea un ancoraggio, si pensi alle persone che in un secondo momento tenteranno di individuare l'ancoraggio.

Si consideri, ad esempio, un ancoraggio nel mezzo di una stanza con due porte. È probabile che si voglia consentire agli utenti di accedere alla stanza da uno sportello. Quando si crea l'ancoraggio, è necessario analizzarne la posizione da entrambi i portali. Si modificano le prospettive per acquisire i dati dell'ambiente intorno all'ancoraggio, in modo che gli utenti possano individuare l'ancoraggio da una delle porte.

In generale, quando si crea un ancoraggio, analizzarlo dalle prospettive degli utenti che proveranno a individuarlo. Quindi, se si sta inserendo contenuto virtuale in una scultura esterna, è opportuno esaminare la scultura, durante l'analisi, durante la creazione dell'ancoraggio. Se l'ancoraggio si trova nell'angolo di una stanza, esiste solo una direzione per l'approccio. Quando si crea questo ancoraggio, è possibile analizzarlo solo da questa prospettiva.

## <a name="multiple-anchors"></a>Più ancoraggi

L'illuminazione può avere una differenza nelle funzionalità visive rilevate da un'app. Gli ancoraggi creati in una forte luce naturale potrebbero essere difficili da individuare in luce artificiale e viceversa.

In caso di problemi, può essere utile creare due ancoraggi. Nella stessa posizione, creare un ancoraggio nell'ora legale e un altro in luce artificiale. L'app può quindi eseguire una query per entrambi gli ancoraggi. Quando uno dei due ancoraggi viene individuato, l'app avrà una posizione per l'ancoraggio.

Analogamente, negli ambienti in cui le funzionalità visive cambiano perché la maggior parte degli oggetti si sposta, possono essere utili più ancoraggi. Quando un ancoraggio diventa troppo difficile da trovare a causa di modifiche significative nell'ambiente, è possibile sostituire l'ancoraggio con uno nuovo. Questa operazione può essere eseguita, ad esempio, in un negozio al dettaglio, in cui il layout viene aggiornato a intervalli di pochi mesi.

## <a name="targets-and-rooms"></a>Destinazioni e chat room

In molti casi, un ancoraggio è un punto di ingresso per l'esperienza dell'app. È consigliabile eseguire questo passaggio in modo rapido e affidabile in modo che gli utenti possano accedere alla propria esperienza. Il tempo di dedicare al modo in cui gli utenti troveranno gli ancoraggi è un passaggio di progettazione importante. È utile considerare la possibilità di trovare ancoraggi in termini di due scenari generali: *destinazioni* e *chat*.

### <a name="targets"></a>Destinazioni

Nello scenario di destinazione, la posizione di un ancoraggio è nota. Ad esempio, in un'app di disegno di realtà mista fittizia, un utente inserisce un'area di disegno virtuale sulla parete. Indica agli altri utenti della stanza di puntare i dispositivi nella stessa posizione sulla parete per individuare l'ancoraggio e iniziare l'esperienza.

Un altro esempio di scenario di destinazione potrebbe essere un accesso a un bar che legge, "Cerca le offerte". Il bar ha inserito un ancoraggio qui. Man mano che gli utenti analizzano il segno, individuano l'ancoraggio e immettono l'esperienza di realtà aumentata per trovare le offerte di caffè.

Nello scenario di destinazione le foto possono essere utili. Se si mostra agli utenti una foto della destinazione prevista sul dispositivo, è possibile identificare rapidamente gli elementi da analizzare nel mondo reale. Ad esempio, si potrebbe aiutare gli utenti a raggiungere l'area generale di una destinazione prevista usando il GPS. Quando l'utente arriva, l'app mostra una foto della destinazione. L'utente cerca lo spazio, trova la destinazione ed esegue l'analisi dell'ancoraggio.

![Illustrazione di un ancoraggio, che mostra una foto della destinazione sul dispositivo mobile di un utente](./media/start-here-edit.png)

### <a name="rooms"></a>Camere

Nello scenario della stanza, gli utenti entrano in uno spazio semplicemente sapendo che è presente un ancoraggio in un punto qualsiasi. Gli utenti analizzano lo spazio con il dispositivo e individuano rapidamente l'ancoraggio.

Questa esperienza richiede in genere la creazione di ancoraggi ben curati, come descritto in varie prospettive di visualizzazione. Se durante la creazione dell'ancoraggio è stata analizzata la chat room da molte prospettive, gli utenti possono eseguire un'analisi quasi ovunque quando tentano di individuarla.

![Illustrazione del modo in cui un utente può analizzare una stanza per trovare un ancoraggio](./media/scan-room.png)

In pratica, si dedica più tempo alla scansione dello spazio quando si crea l'ancoraggio, in modo che gli utenti successivi possano analizzare e individuare rapidamente l'ancoraggio. Quando si crea la propria esperienza, è necessario prendere in considerazione questo importante compromesso.

L'esempio dell'app di disegno di realtà mista descritta in precedenza non funziona bene come uno scenario di chat room. In questo caso, l'utente che inserisce l'ancoraggio desidera che altri utenti possano partecipare rapidamente all'esperienza. Gli utenti non vogliono attendere per iniziare l'esperienza fino a quando non viene analizzata correttamente la stanza. Poiché tutti gli utenti conoscono esattamente dove puntare il dispositivo per individuare gli ancoraggi, questo esempio funziona meglio come uno scenario di destinazione.

## <a name="anchor-location"></a>Posizione di ancoraggio

I sistemi di rilevamento visivi si basano sulle funzionalità visive di un ambiente. Più sono le funzionalità visive incluse in un'analisi, maggiore è la probabilità di trovare un ancoraggio.

Seguire le linee guida generali in questa sezione per creare un'esperienza utente che incoraggi un'analisi utile dell'ambiente.

Prima di tutto, se l'utente non individua un ancoraggio entro pochi secondi, l'app deve incoraggiare gli utenti a spostare il dispositivo per acquisire altre prospettive. L'app può anche incoraggiare gli utenti a spostarsi all'interno dell'ambiente per analizzare l'ancoraggio da più prospettive. Maggiore è il numero di prospettive della funzionalità visualizzate dal dispositivo.

Per gli scenari di destinazione, chiedere all'utente di spostarsi sulla destinazione per visualizzarla da prospettive diverse. In altre parole, richiedere all'utente di acquisire la destinazione dalle nuove prospettive fino a quando non si trova l'ancoraggio.

Per gli scenari locali, chiedere all'utente di analizzare lentamente la chat. Ad esempio, chiedere all'utente di attivare l'acquisizione di 180 gradi o addirittura di 360 gradi della stanza. In alternativa, è possibile richiedere all'utente di visualizzare la stanza da una nuova prospettiva.

Il metodo più significativo è quello di eseguire l'analisi in tutta la stanza. Un'analisi in tutta la stanza acquisisce più funzionalità visive dell'ambiente rispetto a un'analisi di una parete vicina, ad esempio. Un'analisi di una parete vicina non acquisisce tutte le funzioni visive utili dell'ambiente.

Non è utile spostare ripetutamente il dispositivo da un lato all'altro durante la ricerca di un ancoraggio. Questa operazione acquisisce semplicemente gli stessi punti dalla stessa prospettiva.

## <a name="experience-tests"></a>Test esperienza

In questo articolo sono state illustrate le linee guida generali. Con gli ancoraggi spaziali, stai scrivendo app che interagiscono con il mondo reale. Per questo motivo, è consigliabile dedicare tempo a testare gli scenari di ancoraggio dell'app in ambienti reali. Questo vale soprattutto per gli ambienti che rappresentano il punto in cui si prevede che gli utenti usino l'app.
