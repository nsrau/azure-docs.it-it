---
title: Conversazione multi-dispositivo (anteprima) - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: b3802e66b0ba5a68c898e69ec64b01edce1541c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371359"
---
# <a name="what-is-multi-device-conversation-preview"></a>Che cos'è Conversazione multidispositivo (anteprima)?

**La conversazione multidispositivo** semplifica la creazione di una conversazione vocale o di testo tra più client e il coordinamento dei messaggi inviati tra di essi.

Con **la conversazione multi-dispositivo,** è possibile:

- Connetti più client alla stessa conversazione e gestisci l'invio e la ricezione di messaggi tra di loro.
- Trascrivi facilmente l'audio da ogni cliente e invia la trascrizione agli altri, con traduzione opzionale.
- Invia facilmente messaggi di testo tra i clienti, con traduzione opzionale.

È possibile creare una funzionalità o una soluzione che funziona in una matrice di dispositivi. Ogni dispositivo può inviare messaggi in modo indipendente (trascrizioni di messaggi audio o istantanei) a tutti gli altri dispositivi.

Mentre [**la trascrizione**](conversation-transcription.md) della conversazione funziona su un singolo dispositivo con un array di microfoni multicanale, la **conversazione multidispositivo** è adatta per scenari con più dispositivi, ognuno con un singolo microfono.

>[!IMPORTANT]
> La conversazione multidispositivo **non** supporta l'invio di file audio tra client: solo la trascrizione e/o la traduzione.

## <a name="key-features"></a>Funzionalità principali

- **Trascrizione** in tempo reale: tutti riceveranno una trascrizione della conversazione, in modo da poter seguire il testo in tempo reale o salvarlo per un secondo momento.
- **Traduzione in tempo reale** – Con più di 60 [lingue supportate](language-support.md#text-languages) per la traduzione di testo, gli utenti possono tradurre la conversazione nella loro lingua preferita.
- **Trascrizioni leggibili** – La trascrizione e la traduzione sono facili da seguire, con punteggiatura e interruzioni di frase.
- **Input vocale o di testo:** ogni utente può parlare o digitare sul proprio dispositivo, a seconda delle funzionalità di supporto linguistico abilitate per la lingua scelta dal partecipante. Fare riferimento a [Supporto lingue](language-support.md#speech-to-text).
- **Inoltro dei messaggi** - Il servizio di conversazione multi-dispositivo distribuirà i messaggi inviati da un client a tutti gli altri, nelle lingue di loro scelta.
- **Identificazione del messaggio:** ogni messaggio che gli utenti ricevono nella conversazione verrà contrassegnato con il nickname dell'utente che lo ha inviato.

## <a name="use-cases"></a>Casi d'uso

### <a name="lightweight-conversations"></a>Conversazioni leggere

Creare e partecipare a una conversazione è facile. Un utente fungerà da 'host' e creerà una conversazione, che genera un codice di conversazione casuale di cinque lettere e un codice QR. Tutti gli altri utenti possono partecipare alla conversazione digitando il codice della conversazione o scansionando il codice QR. 

Poiché gli utenti si uniscono tramite il codice della conversazione e non sono tenuti a condividere le informazioni di contatto, è facile creare conversazioni rapide e in loco.

### <a name="inclusive-meetings"></a>Riunioni inclusive

La trascrizione e la traduzione in tempo reale possono aiutare a rendere le conversazioni accessibili a persone che parlano lingue diverse e/o sono sorde o con problemi di udito. Ogni persona può anche partecipare attivamente alla conversazione, parlando la lingua preferita o inviando messaggi istantanei.

### <a name="presentations"></a>Presentazioni

Puoi anche fornire didascalie per presentazioni e lezioni sia sullo schermo che sui dispositivi dei membri del pubblico. Dopo che il pubblico si unisce al codice della conversazione, può vedere la trascrizione nella lingua preferita, sul proprio dispositivo.

> [!NOTE]
> Per un esempio, vedere [Presentation Translator](https://www.microsoft.com/translator/apps/presentation-translator/), un componente aggiuntivo di PowerPoint che utilizza il servizio di conversazione multidispositivo. È possibile scaricarlo [qui](https://www.microsoft.com/download/details.aspx?id=55024).

## <a name="how-it-works"></a>Funzionamento

Tutti i client utilizzeranno Speech SDK per creare o partecipare a una conversazione. Speech SDK interagisce con il servizio di conversazione multi-dispositivo, che gestisce la durata di una conversazione, incluso l'elenco dei partecipanti, le lingue scelte da ogni client e i messaggi inviati.  

Ogni client può inviare messaggi audio o istantanei. Il servizio utilizzerà il riconoscimento vocale per convertire l'audio in testo e invierà messaggi istantanei così come sono. Se i client scelgono lingue diverse, il servizio tradurrà tutti i messaggi nelle lingue specificate di ogni client.

![Diagramma di panoramica della conversazione multidispositivo](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Panoramica di Conversazione, Ospitante e Partecipante

Una **conversazione** è una sessione a cui un utente avvia la partecipazione degli altri utenti partecipanti. Tutti i client si connettono alla conversazione utilizzando il codice di **conversazione**di cinque lettere .

Ogni conversazione crea metadati che includono:Each conversation creates metadata that includes:
-    Timestamp di quando la conversazione è iniziata e terminata
-    Elenco di tutti i partecipanti alla conversazione, che include il soprannome scelto dall'utente e la lingua principale per il riconoscimento vocale o di testo.


Ci sono due tipi di utenti in una conversazione: **host** e **partecipante**.

**L'host** è l'utente che avvia una conversazione e che funge da amministratore di tale conversazione.
- Ogni conversazione può avere un solo host
- L'host deve essere connesso alla conversazione per tutta la durata della conversazione. Se l'host lascia la conversazione, la conversazione terminerà per tutti gli altri partecipanti.
- L'host ha alcuni controlli aggiuntivi per gestire la conversazione: 
    - Bloccare la conversazione - impedire ad altri partecipanti di partecipare
    - Disattivare l'audio di tutti i partecipanti - impedire ad altri partecipanti di inviare messaggi alla conversazione, sia che siano trascritti da messaggi vocali o istantanei
    - Disattivare l'audio dei singoli partecipanti
    - Riattivare l'audio di tutti i partecipanti
    - Riattivare l'audio dei singoli partecipanti

Un **partecipante** è un utente che partecipa a una conversazione.
- Un partecipante può lasciare e partecipare nuovamente alla stessa conversazione in qualsiasi momento, senza terminare la conversazione per gli altri partecipanti.
- I partecipanti non possono bloccare la conversazione o attivare/disattivare/riattivare l'audio di altri utenti

> [!NOTE]
> Ogni conversazione può avere fino a 100 partecipanti, di cui 10 possono parlare contemporaneamente in qualsiasi momento.

## <a name="language-support"></a>Lingue supportate

Quando si crea o si unisce a una conversazione, ogni utente deve scegliere una **lingua principale:** la lingua in cui parlerà e invierà messaggi istantanei, nonché la lingua in cui vedranno i messaggi degli altri utenti.

Esistono due tipi di lingue: **sintesi vocale** e **solo testo**:
- Se l'utente sceglie una lingua **di sintesi vocale** come lingua principale, sarà in grado di utilizzare sia il riconoscimento vocale che l'input di testo nella conversazione.

- Se l'utente sceglie una lingua **di solo testo,** sarà in grado di utilizzare solo l'input di testo e inviare messaggi istantanei nella conversazione. Le lingue solo testo sono le lingue supportate per la traduzione del testo, ma non la sintesi vocale. È possibile visualizzare le lingue disponibili nella pagina [del supporto linguistico.](supported-languages.md)

Oltre alla lingua principale, ogni partecipante può anche specificare altre lingue per tradurre la conversazione.

Di seguito è riportato un riepilogo di ciò che l'utente sarà in grado di fare in una conversazione multi-dispositivo, in base alla lingua principale scelta.


| Operazioni che l'utente può eseguire nella conversazione | Riconoscimento vocale | Solo testo |
|-----------------------------------|----------------|------|
| Usare l'input vocale | ✔️ | ❌ |
| Inviare messaggi istantanei | ✔️ | ✔️ |
| Tradurre la conversazione | ✔️ | ✔️ |

> [!NOTE]
> Per un elenco delle lingue di sintesi vocale e di traduzione di testo disponibili, vedere [Lingue supportate.](supported-languages.md)



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Tradurre conversazioni in tempo reale](quickstarts/multi-device-conversation.md)
