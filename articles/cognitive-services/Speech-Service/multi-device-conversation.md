---
title: Conversazione per più dispositivi (anteprima)-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 15b69e664ae9a01635afb44d7b6b7c24d7551ce9
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87921419"
---
# <a name="what-is-multi-device-conversation-preview"></a>Che cos'è la conversazione per più dispositivi (anteprima)?

La **conversazione multi-dispositivo** semplifica la creazione di una conversazione vocale o di testo tra più client e coordina i messaggi inviati tra di essi.

Con la conversazione per più **dispositivi**, è possibile:

- Connettere più client alla stessa conversazione e gestire l'invio e la ricezione di messaggi tra di essi.
- Puoi facilmente trascrivere l'audio da ogni client e inviare la trascrizione agli altri, con una traduzione facoltativa.
- Invio semplice di messaggi di testo tra i client, con traduzione facoltativa.

È possibile compilare una funzionalità o una soluzione che funziona in una matrice di dispositivi. Ogni dispositivo può inviare in modo indipendente messaggi (trascrizioni di audio o messaggi istantanei) a tutti gli altri dispositivi.

Mentre la [**trascrizione delle conversazioni**](conversation-transcription.md) funziona su un singolo dispositivo con un array microfonico multicanale, la **conversazione multi-dispositivo** è adatta per gli scenari con più dispositivi, ognuno con un unico microfono.

>[!IMPORTANT]
> La conversazione su più dispositivi **non supporta l'** invio di file audio tra i client: solo la trascrizione e/o la traduzione.

## <a name="key-features"></a>Funzionalità principali

- **Trascrizione in tempo reale** : tutti gli utenti riceveranno una trascrizione della conversazione, in modo da poter seguire il testo in tempo reale o salvarlo per un momento successivo.
- **Traduzione in tempo reale** : con più di 60 [lingue supportate](language-support.md#text-languages) per la traduzione di testo, gli utenti possono tradurre la conversazione nelle lingue preferite.
- **Trascrizioni leggibili** : la trascrizione e la traduzione sono facili da seguire, con punteggiatura e interruzioni di frase.
- **Input vocale o di testo** : ogni utente può parlare o digitare il proprio dispositivo, a seconda delle funzionalità di supporto del linguaggio abilitate per la lingua scelta dal partecipante. Vedere il supporto per le [lingue](language-support.md#speech-to-text).
- **Inoltro messaggi** : il servizio di conversazione per più dispositivi distribuirà i messaggi inviati da un client a tutti gli altri, nelle lingue di propria scelta.
- **Identificazione del messaggio** : ogni messaggio ricevuto dagli utenti nella conversazione verrà contrassegnato con il nome alternativo dell'utente che lo ha inviato.

## <a name="use-cases"></a>Casi d'uso

### <a name="lightweight-conversations"></a>Conversazioni leggere

Creare e partecipare a una conversazione è facile. Un utente fungerà da "host" e creerà una conversazione, che genera un codice di conversazione casuale di cinque lettere e un codice a matrice. Tutti gli altri utenti possono partecipare alla conversazione digitando il codice di conversazione o eseguendo la scansione del codice a matrice. 

Poiché gli utenti si uniscono tramite il codice di conversazione e non sono obbligati a condividere le informazioni di contatto, è facile creare conversazioni rapide e veloci.

### <a name="inclusive-meetings"></a>Riunioni Inclusive

La trascrizione e la traduzione in tempo reale consentono di rendere le conversazioni accessibili per le persone che parlano lingue diverse e/o sono sorde o difficili da sentire. Ogni persona può anche partecipare attivamente alla conversazione, pronunciando la lingua preferita o inviando messaggi istantanei.

### <a name="presentations"></a>Presentazioni

È anche possibile fornire didascalie per presentazioni e lezioni sia sullo schermo che sui dispositivi personali dei membri del pubblico. Dopo che i destinatari hanno partecipato al codice di conversazione, possono visualizzare la trascrizione nella propria lingua preferita, nel proprio dispositivo.

> [!NOTE]
> Per un esempio, vedere Translator di [presentazione](https://www.microsoft.com/translator/apps/presentation-translator/), un componente aggiuntivo di PowerPoint che usa il servizio di conversazione per più dispositivi. È possibile scaricarlo [qui](https://download.cnet.com/s/powerpoint-add-in/).

## <a name="how-it-works"></a>Funzionamento

Tutti i client utilizzeranno l'SDK vocale per creare o partecipare a una conversazione. L'SDK di riconoscimento vocale interagisce con il servizio di conversazione per più dispositivi, che gestisce la durata di una conversazione, incluso l'elenco dei partecipanti, le lingue selezionate di ogni client e i messaggi inviati.  

Ogni client può inviare messaggi audio o istantanei. Il servizio userà il riconoscimento vocale per convertire l'audio in testo e inviare messaggi istantanei così come sono. Se i client scelgono diverse lingue, il servizio tradurrà tutti i messaggi nelle lingue specificate di ogni client.

![Diagramma di panoramica della conversazione per più dispositivi](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Panoramica di conversazioni, host e partecipanti

Una **conversazione** è una sessione avviata da un utente per l'aggiunta degli altri utenti partecipanti. Tutti i client si connettono alla conversazione usando il **codice di conversazione**di cinque lettere.

Ogni conversazione crea metadati che includono:
-    Timestamp del momento in cui la conversazione è iniziata e terminata
-    Elenco di tutti i partecipanti alla conversazione, che include il nome alternativo e la lingua primaria scelti da ogni utente per l'input vocale o di testo.


Esistono due tipi di utenti in una conversazione: **host** e **partecipante**.

L' **host** è l'utente che avvia una conversazione e che funge da amministratore della conversazione.
- Ogni conversazione può avere un solo host
- L'host deve essere connesso alla conversazione per la durata della conversazione. Se l'host lascia la conversazione, la conversazione terminerà per tutti gli altri partecipanti.
- L'host dispone di alcuni controlli aggiuntivi per gestire la conversazione: 
    - Bloccare la conversazione: impedire l'aggiunta di partecipanti aggiuntivi
    - Disattiva tutti i partecipanti: impedisce ad altri partecipanti di inviare messaggi alla conversazione, indipendentemente dal fatto che vengano trascritti da messaggi vocali o istantanei
    - Disattiva singoli partecipanti
    - Disattiva tutti i partecipanti
    - Riattivazione di singoli partecipanti

Un **partecipante** è un utente che partecipa a una conversazione.
- Un partecipante può uscire e riunirsi alla stessa conversazione in qualsiasi momento, senza terminare la conversazione per altri partecipanti.
- I partecipanti non possono bloccare la conversazione o disattivare/disattivare altri utenti

> [!NOTE]
> Ogni conversazione può avere fino a 100 partecipanti, di cui 10 possono essere pronunciate contemporaneamente in un determinato momento.

## <a name="language-support"></a>Lingue supportate

Quando si crea o si aggiunge una conversazione, ogni utente deve scegliere una **lingua primaria**: la lingua in cui verranno pronunciate e invierà messaggi istantanei e anche la lingua in cui verranno visualizzati i messaggi di altri utenti.

Esistono due tipi di linguaggi: **sintesi vocale** e **testo**:
- Se l'utente sceglie una lingua **vocale** come lingua principale, sarà in grado di usare sia l'input vocale che quello di testo nella conversazione.

- Se l'utente sceglie una lingua di **solo testo** , sarà in grado di utilizzare solo l'input di testo e di inviare messaggi istantanei nella conversazione. I linguaggi di solo testo sono le lingue supportate per la traduzione del testo, ma non quelle di sintesi vocale. È possibile visualizzare le lingue disponibili nella pagina supporto per la [lingua](supported-languages.md) .

Oltre alla lingua primaria, ogni partecipante può anche specificare altre lingue per la traduzione della conversazione.

Di seguito è riportato un riepilogo delle operazioni che l'utente sarà in grado di eseguire in una conversazione a più dispositivi, in base alla lingua primaria scelta.


| Cosa può fare l'utente nella conversazione | Riconoscimento vocale | Solo testo |
|-----------------------------------|----------------|------|
| Usa input vocale | ✔️ | ❌ |
| Invia messaggi istantanei | ✔️ | ✔️ |
| Tradurre la conversazione | ✔️ | ✔️ |

> [!NOTE]
> Per gli elenchi di lingue di traduzione vocale e testo disponibili, vedere [linguaggi supportati](supported-languages.md).



## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Traduci conversazioni in tempo reale](quickstarts/multi-device-conversation.md)
