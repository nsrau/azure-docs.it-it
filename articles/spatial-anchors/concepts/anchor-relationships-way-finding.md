---
title: Relazioni di ancoraggio e ricerca del modo
description: Informazioni sul modello concettuale alla base delle relazioni di ancoraggio. Scopri come connettere gli ancoraggi all'interno di uno spazio e a usare l'API Nelle vicinanze per soddisfare uno scenario di individuazione.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270609"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relazioni di ancoraggio e ricerca di percorso in Azure Spatial AnchorsAnchor relationships and way-finding in Azure Spatial Anchors

Utilizzando le relazioni di ancoraggio, è possibile creare ancoraggi collegati in uno spazio e quindi porre domande come queste:

* Ci sono ancoraggi nelle vicinanze?
* Quanto sono lontani?

## <a name="examples"></a>Esempi

È possibile utilizzare ancoraggi collegati in casi come questi:You could use connected anchors in cases like these:

* Un lavoratore deve completare un'attività che prevede la visita di varie sedi in una fabbrica industriale. La fabbrica dispone di ancoraggi spaziali in ogni posizione. Un'app HoloLens o mobile consente di guidare il lavoratore da una posizione all'altra. L'app richiede innanzitutto gli ancoraggi spaziali nelle vicinanze, quindi guida il lavoratore alla posizione successiva. L'app mostra visivamente la direzione generale e la distanza dalla posizione successiva.

* Un museo crea ancore spaziali in esposizione pubblica. Insieme, queste ancore formano un tour di un'ora delle esposizioni pubbliche essenziali del museo. In un'esposizione pubblica, i visitatori possono aprire l'app di realtà mista del museo sul proprio dispositivo mobile. Poi puntano la loro fotocamera del telefono intorno allo spazio per vedere la direzione generale e la distanza dagli altri display pubblici sul tour. Mentre un utente cammina verso una visualizzazione pubblica, l'app aggiorna la direzione generale e la distanza per guidare l'utente.

## <a name="set-up-way-finding"></a>Configurare la ricerca della modalità

Un'app che usa la direzione della linea di vista e la distanza tra gli ancoraggi per fornire indicazioni consiste nell'usare la *ricerca dei modi*. Way-finding è diverso dalla navigazione turn-by-turn. Nella navigazione a turno, gli utenti vengono guidati intorno alle pareti, attraverso le porte e tra i piani. Con way-finding, l'utente ottiene suggerimenti sulla direzione generale della destinazione. Ma l'inferenza o la conoscenza dello spazio aiuta anche l'utente a navigare attraverso la struttura verso la destinazione.

Per creare un'esperienza di ricerca della procedura, prepara innanzitutto uno spazio per l'esperienza e sviluppa un'app con cui gli utenti interagiranno. Questi sono i passaggi concettuali:

1. **Pianificare lo spazio**: Decidere quali posizioni all'interno dello spazio saranno parte dell'esperienza di come trovare. Nei nostri scenari, il supervisore della fabbrica o il coordinatore del tour del museo potrebbero decidere quali luoghi includere nell'esperienza di ricerca del modo.
2. **Collega ancoraggi**: Visita le posizioni scelte per creare ancoraggi spaziali. Puoi farlo in una modalità di amministrazione dell'app per l'utente finale o in un'app completamente diversa. Potrai collegare o collegare ogni ancora agli altri. Il servizio mantiene queste relazioni.
3. **Inizia l'esperienza dell'utente finale:** gli utenti eseguono l'app per individuare un ancoraggio, che può trovarsi in una delle posizioni scelte. La progettazione complessiva deve determinare le posizioni in cui gli utenti possono immettere l'esperienza.
4. **Trova ancoraggi nelle vicinanze:** dopo che l'utente ha trovato un ancoraggio, l'app può richiedere ancoraggi vicini. Questa procedura restituisce una posa tra il dispositivo e questi ancoraggi.
5. **Guida l'utente:** l'app può usare la posa per ognuno di questi ancoraggi per fornire indicazioni sulla direzione e la distanza generale dell'utente. Ad esempio, il feed della fotocamera nell'app potrebbe mostrare un'icona e una freccia per rappresentare ogni destinazione potenziale, come illustrato nell'immagine seguente.
6. **Perfezionare le linee guida**: Mentre l'utente cammina, l'app può calcolare periodicamente una nuova posa tra il dispositivo e l'ancoraggio di destinazione. L'app continua a perfezionare i suggerimenti di orientamento che aiutano l'utente ad arrivare a destinazione.

    ![Un esempio di come un'app può mostrare indicazioni sulla ricerca di modi](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Collegare gli ancoraggi

Per creare un'esperienza di ricerca della strada, devi prima posizionare le ancore nei luoghi scelti. In questa sezione si presuppone che l'amministratore dell'app abbia già completato il lavoro.

### <a name="connect-anchors-in-a-single-session"></a>Collegare gli ancoraggi in una singola sessione

Per collegare gli ancoraggi:

1. Passare alla prima posizione e creare l'ancoraggio A usando un CloudSpatialAnchorSession.Walk to the first location and create Anchor A by using a CloudSpatialAnchorSession.
2. Raggiungere la seconda posizione. La piattaforma MR/AR sottostante tiene traccia del movimento.
3. Creare ancoraggio B utilizzando la stessa CloudSpatialAnchorSession.Create Anchor B by using the same CloudSpatialAnchorSession. Le ancore A e B sono ora collegate. Il servizio Ancoraggi spaziali mantiene questa relazione.
4. Continuare la procedura per gli ancoraggi rimanenti.

### <a name="connect-anchors-in-multiple-sessions"></a>Collegare gli ancoraggi in più sessioni

È possibile collegare ancoraggi spaziali su più sessioni. Utilizzando questo metodo, è possibile creare e collegare alcuni ancoraggi contemporaneamente e quindi creare e collegare più ancoraggi.

Per collegare gli ancoraggi su più sessioni:

1. L'app crea alcuni ancoraggi in un CloudSpatialAnchorSession.The app creates some anchors in one CloudSpatialAnchorSession.
2. In un momento diverso, l'app individua uno di questi ancoraggi (ad esempio, Anchor A) utilizzando una nuova CloudSpatialAnchorSession.At a different time, the app locates one of these anchors (for example, Anchor A) by using a new CloudSpatialAnchorSession.
3. Raggiungere la nuova posizione. La piattaforma di realtà mista o realtà aumentata sottostante segue il movimento.
4. Creare Anchor C utilizzando la stessa CloudSpatialAnchorSession.Create Anchor C by using the same CloudSpatialAnchorSession. Le ancore A, B e C sono ora connesse. Il servizio Ancoraggi spaziali mantiene questa relazione.

È possibile continuare questa procedura per più ancoraggi e più sessioni nel tempo.

### <a name="verify-anchor-connections"></a>Verificare le connessioni di ancoraggio

L'app può verificare che due ancoraggi siano connessi inviando una query per gli ancoraggi vicini. Quando il risultato della query contiene l'ancoraggio di destinazione, viene verificata la connessione di ancoraggio. Se gli ancoraggi non sono connessi, l'app può provare a connetterli di nuovo.

Ecco alcuni motivi per cui gli ancoraggi potrebbero non riuscire a connettersi:

* La piattaforma di realtà mista o realtà aumentata sottostante ha perso il tracciamento durante il processo di connessione delle ancore.
* A causa di un errore di rete durante la comunicazione con il servizio di ancore spaziali, la connessione di ancoraggio non può essere mantenuta.

### <a name="find-sample-code"></a>Trovare codice di esempio

Per trovare codice di esempio che mostra come connettere ancoraggi ed eseguire query nelle vicinanze, consultate [App di esempio di ancore spaziali.](https://github.com/Azure/azure-spatial-anchors-samples)
