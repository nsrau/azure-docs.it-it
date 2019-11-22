---
title: Relazioni di ancoraggio e ricerca di modo
description: Informazioni sul modello concettuale dietro le relazioni di ancoraggio. Informazioni su come connettere ancoraggi in uno spazio e usare l'API vicina per ottenere uno scenario di ricerca.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270609"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Relazioni di ancoraggio e ricerca di metodi negli ancoraggi spaziali di Azure

Usando le relazioni di ancoraggio, è possibile creare ancoraggi connessi in uno spazio, quindi porre domande simili alle seguenti:

* Sono presenti ancoraggi nelle vicinanze?
* Quanto sono lontani?

## <a name="examples"></a>esempi

È possibile usare ancoraggi connessi in casi come i seguenti:

* Un thread di lavoro deve completare un'attività che prevede la visita di diverse località in una factory industriale. La Factory dispone di ancoraggi spaziali in ogni posizione. Un HoloLens o un'app per dispositivi mobili consente di guidare il lavoro da una posizione all'altra. L'app richiede prima di tutto gli ancoraggi spaziali adiacenti e quindi Guida il thread di lavoro alla posizione successiva. L'app mostra visivamente la direzione generale e la distanza alla posizione successiva.

* Un museo crea ancoraggi spaziali in visualizzazione pubblica. Insieme, questi ancoraggi formano una panoramica di un'ora per le visualizzazioni pubbliche essenziali del Museo. In una visualizzazione pubblica, i visitatori possono aprire l'app per la realtà mista del Museo sul proprio dispositivo mobile. Quindi, puntano alla fotocamera del telefono attorno allo spazio per visualizzare la direzione generale e la distanza per le altre visualizzazioni pubbliche del tour. Man mano che un utente si avvicina a una visualizzazione pubblica, l'app Aggiorna la direzione generale e la distanza per aiutare l'utente.

## <a name="set-up-way-finding"></a>Configurare la modalità di ricerca

Un'app che usa la direzione della linea di visione e la distanza tra gli ancoraggi per fornire indicazioni sta usando la *modalità di ricerca*. La modalità di ricerca è diversa dalla navigazione turn-by-turn. Nell'esplorazione a turni, gli utenti sono guidati intorno alle pareti, attraverso le porte e tra i piani. Con il metodo di ricerca, l'utente ottiene suggerimenti sulla direzione generale della destinazione. L'inferenza o la conoscenza dello spazio consente inoltre all'utente di spostarsi tra la struttura e la destinazione.

Per creare un'esperienza di ricerca, prima di tutto preparare uno spazio per l'esperienza e sviluppare un'app con cui gli utenti interagiranno. Questi sono i passaggi concettuali:

1. **Pianificare lo spazio**: decidere quali posizioni all'interno dello spazio faranno parte dell'esperienza di ricerca. In questi scenari, il responsabile della fabbrica o il coordinatore del tour del Museo può decidere quali posizioni includere nell'esperienza di ricerca.
2. **Connetti ancoraggi**: visitare le località selezionate per creare ancoraggi spaziali. Questa operazione può essere eseguita in modalità di amministrazione dell'app per l'utente finale o in un'altra app completamente. Ogni ancoraggio verrà connesso o correlato alle altre. Il servizio gestisce queste relazioni.
3. **Avviare l'esperienza dell'utente finale**: gli utenti eseguono l'app per individuare un ancoraggio, che può trovarsi in qualsiasi posizione scelta. La progettazione complessiva dovrebbe determinare le posizioni in cui gli utenti possono accedere all'esperienza.
4. **Trova ancoraggi vicini**: dopo che l'utente ha trovato un ancoraggio, l'app può richiedere ancoraggi adiacenti. Questa procedura restituisce una posa tra il dispositivo e questi ancoraggi.
5. **Guida dell'utente**: l'app può usare la disposizione per ognuno di questi ancoraggi per fornire indicazioni sulla direzione e sulla distanza generali dell'utente. Ad esempio, il feed della fotocamera nell'app potrebbe mostrare un'icona e una freccia per rappresentare ogni potenziale destinazione, come illustrato nella figura seguente.
6. **Perfezionare le linee guida**: quando l'utente esegue l'esercitazione, l'app può calcolare periodicamente una nuova forma tra il dispositivo e l'ancoraggio di destinazione. L'app continua a perfezionare gli hint per le linee guida che consentono all'utente di arrivare alla destinazione.

    ![Un esempio di come un'app può mostrare le linee guida per trovare la strada](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Connetti ancoraggi

Per creare un'esperienza di ricerca, è prima di tutto necessario inserire gli ancoraggi nelle posizioni selezionate. In questa sezione si presuppone che l'amministratore dell'app abbia già completato il lavoro.

### <a name="connect-anchors-in-a-single-session"></a>Connetti ancoraggi in una singola sessione

Per connettere ancoraggi:

1. Passare alla prima posizione e creare Anchor A usando un CloudSpatialAnchorSession.
2. Passare alla seconda posizione. La piattaforma MR/AR sottostante tiene traccia dello spostamento.
3. Creare l'ancoraggio B utilizzando lo stesso CloudSpatialAnchorSession. Gli ancoraggi A e B sono ora connessi. Il servizio ancoraggi spaziali gestisce questa relazione.
4. Continuare la procedura per gli ancoraggi rimanenti.

### <a name="connect-anchors-in-multiple-sessions"></a>Connetti ancoraggi in più sessioni

È possibile connettere ancoraggi spaziali su più sessioni. Utilizzando questo metodo, è possibile creare e connettere alcuni ancoraggi alla volta e successivamente creare e connettere più ancoraggi.

Per connettere ancoraggi su più sessioni:

1. L'app crea alcuni ancoraggi in un CloudSpatialAnchorSession.
2. In un momento diverso, l'app individua uno di questi ancoraggi (ad esempio, ancoraggio A) usando un nuovo CloudSpatialAnchorSession.
3. Passare a una nuova posizione. La piattaforma di realtà mista o di realtà aumentata sottostante tiene traccia dello spostamento.
4. Creare Anchor C utilizzando lo stesso CloudSpatialAnchorSession. Gli ancoraggi A, B e C sono ora connessi. Il servizio ancoraggi spaziali gestisce questa relazione.

È possibile continuare questa procedura per ulteriori ancoraggi e altre sessioni nel tempo.

### <a name="verify-anchor-connections"></a>Verificare le connessioni di ancoraggio

L'app può verificare che due ancoraggi siano connessi inviando una query per gli ancoraggi adiacenti. Quando il risultato della query contiene l'ancoraggio di destinazione, viene verificata la connessione di ancoraggio. Se gli ancoraggi non sono connessi, l'app può provare a connetterli nuovamente.

Ecco alcuni motivi per cui i ancoraggi potrebbero non riuscire a connettersi:

* La realtà mista sottostante o la piattaforma di realtà aumentata ha perso il rilevamento durante il processo di connessione di ancoraggi.
* A causa di un errore di rete durante la comunicazione con il servizio di ancoraggio spaziale, non è stato possibile salvare in maniera permanente la connessione di ancoraggio.

### <a name="find-sample-code"></a>Trovare codice di esempio

Per trovare il codice di esempio che illustra come connettere ancoraggi e per eseguire query nelle vicinanze, vedere [app di esempio di ancoraggi spaziali](https://github.com/Azure/azure-spatial-anchors-samples).
