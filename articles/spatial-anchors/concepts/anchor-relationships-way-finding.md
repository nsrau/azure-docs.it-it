---
title: Le relazioni di ancoraggio e modo-ricerca in Azure gli ancoraggi spaziali | Microsoft Docs
description: Informazioni sul modello concettuale dietro le relazioni di ancoraggio. Informazioni per la connessione di punti di ancoraggio all'interno di uno spazio e per usare l'API nelle vicinanze per soddisfare uno scenario di modalità di ricerca.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 619cd051eccce3434469ae909f69496a254d0d9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565148"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Modo per individuare in Azure spaziali ancoraggi e le relazioni di ancoraggio

Utilizzando le relazioni di ancoraggio, è possibile creare ancoraggi connessi in uno spazio e quindi porre domande come queste:

* Sono presenti gli ancoraggi nelle vicinanze?
* La distanza sono?

## <a name="examples"></a>Esempi

In questi casi, è possibile usare gli ancoraggi connessi:

* Un ruolo di lavoro deve completare un'attività che implica che visitano diverse posizioni in un'istanza di factory industriale. La factory ha ancoraggi spaziali in ogni posizione. Un HoloLens o un'app per dispositivi mobili consente di semplificare il processo di lavoro da una posizione al successivo. L'app in primo luogo richiesto per gli ancoraggi spaziali vicini e quindi descrive il ruolo di lavoro alla posizione successiva. L'app visivamente Mostra la direzione generale e la distanza alla posizione successiva.

* Un museo crea Anchor spaziali in Visualizza pubblico. Questi punti di ancoraggio insieme, formano una presentazione di un'ora di essenziale pubblica consente di visualizzare del museo. In una visualizzazione pubblica, i visitatori possono aprire app per realtà mista del museo nel proprio dispositivo mobile. Quindi non fanno riferimento della fotocamera del telefono tutto lo spazio per visualizzare la direzione generale e la distanza e l'altra consente di visualizzare pubblici nel tour. Quando un utente scorre verso una visualizzazione pubblica, l'app Aggiorna la direzione generale e la distanza guideranno l'utente.

## <a name="set-up-way-finding"></a>Impostare le modalità di ricerca

Usa un'app che usa una linea di visuale sul direzione e la distanza tra punti di ancoraggio per fornire materiale sussidiario *modo ricerca*. Modalità di ricerca è diversa dal riquadro di spostamento da attiva-attiva. Nel riquadro di spostamento da attiva-attiva, gli utenti vengono guidati intorno pareti, tramite le porte e nei piani. Con modalità di ricerca, l'utente riceve hint per la direzione generale dell'oggetto di destinazione. Ma knowledge dello spazio o inferenza anche consente all'utente di spostarsi tra la struttura alla destinazione.

Per creare un'esperienza di ricerca di modo, è innanzitutto preparare uno spazio per l'esperienza e sviluppare un'app che gli utenti interagiranno con. Questi sono i passaggi concettuali:

1. **Pianificare lo spazio**: Decidere quali posizioni nello spazio faranno parte dell'esperienza di ricerca di modalità. Nel nostro scenari, il Supervisore factory o il coordinatore tour Cenni storici sulla potrebbe decidere di percorsi da includere nell'esperienza di ricerca di modo.
2. **Connettere gli ancoraggi**: Visita i percorsi scelti per creare ancoraggi spaziali. È possibile farlo in modalità amministratore dell'app dell'utente finale o in un'altra app completamente. Si sarà la connessione o per correlarla ogni ancoraggio agli altri. Il servizio mantiene queste relazioni.
3. **Avviare l'esperienza utente finale**: Gli utenti eseguono l'app per individuare un punto di ancoraggio, che può essere in una delle posizioni selezionate. La progettazione complessiva deve determinare i percorsi in cui gli utenti possono immettere l'esperienza.
4. **Individuare nelle vicinanze ancoraggi**: Dopo che l'utente trova un ancoraggio, l'app può richiedere nelle vicinanze ancoraggi. Questa procedura restituisce una posa tra il dispositivo e tali ancoraggi.
5. **Guida l'utente**: L'app può usare la posa per ognuno di questi punti di ancoraggio per fornire indicazioni sulla distanza e direzione generale dell'utente. Ad esempio, i feed nell'app della fotocamera potrebbe visualizzare un'icona e una freccia per rappresentare ogni potenziale destinazione, come illustrato nell'immagine seguente.
6. **Ridefinire il materiale sussidiario**: Utenti di lavorare, l'app può calcolare periodicamente una nuovo posa tra il dispositivo e il punto di ancoraggio di destinazione. L'app continua a ridefinire gli hint di indicazioni che consentono all'utente arrivano a destinazione.

    ![Un esempio di come un'app può indicare modo per individuare informazioni aggiuntive](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Connettere gli ancoraggi

Per creare un'esperienza di ricerca di modo, è necessario posizionare gli ancoraggi nelle posizioni selezionate. In questa sezione, si presupporrà che amministratore dell'app è già stato completato il lavoro.

### <a name="connect-anchors-in-a-single-session"></a>Connettere gli ancoraggi in una singola sessione

Per connettere gli ancoraggi:

1. Creare ancoraggio A utilizzando un CloudSpatialAnchorSession illustrato nel primo percorso.
2. Analisi per la seconda posizione. La piattaforma sottostante di MR/AR rileva lo spostamento.
3. Creare ancoraggio B con la stessa CloudSpatialAnchorSession. Punti di ancoraggio A e B è ora connessi. Il servizio Anchor spaziali gestisce questa relazione.
4. Continuare la procedura per gli ancoraggi rimanenti.

### <a name="connect-anchors-in-multiple-sessions"></a>Connettere gli ancoraggi in più sessioni

È possibile connettere gli ancoraggi spaziali in più sessioni. In questo modo, si può creare e connettersi alcuni punti di ancoraggio in una sola volta e quindi in un secondo momento creare e connettersi ulteriori punti di ancoraggio. 

Per connettere gli ancoraggi in più sessioni:

1. L'app crea alcuni punti di ancoraggio in uno CloudSpatialAnchorSession. 
2. In un momento diverso, l'app consente di individuare uno di questi punti di ancoraggio (ad esempio, ancoraggio A) con un nuovo CloudSpatialAnchorSession.
3. Analisi per una nuova posizione. La piattaforma sottostante di realtà mista o realtà aumentata rileva lo spostamento.
4. Creare ancoraggio C usando la stessa CloudSpatialAnchorSession. Punti di ancoraggio A, B e C sono ora collegati. Il servizio Anchor spaziali gestisce questa relazione.

Nel corso del tempo, è possibile continuare questa procedura per più punti di ancoraggio e altre sessioni.

### <a name="verify-anchor-connections"></a>Verificare le connessioni di ancoraggio

L'app può verificare che siano connessi due Anchor eseguendo una query per gli ancoraggi nelle vicinanze. Quando il risultato della query contiene il punto di ancoraggio di destinazione, viene verificata la connessione di ancoraggio. Se non sono connessi gli ancoraggi, l'app può provare a connetterle nuovamente. 

Ecco alcuni motivi per cui gli ancoraggi potrebbero non riuscire per la connessione:

* La realtà mista o realtà aumentata piattaforma sottostante perdita rilevamento durante il processo di connessione punti di ancoraggio.
* A causa di un errore di rete durante la comunicazione con il servizio Anchor spaziale, la connessione di ancoraggio non è stato possibile rendere persistenti.

### <a name="find-sample-code"></a>Trovare codice di esempio

Per trovare codice di esempio che illustra come connettere gli ancoraggi e come effettuare vicine le query, vedere [app di esempio anchor spaziali](https://github.com/Azure/azure-spatial-anchors-samples).
