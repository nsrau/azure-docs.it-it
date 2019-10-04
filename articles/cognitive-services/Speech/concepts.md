---
title: Concetti relativi a Riconoscimento vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Concetti di base usati nel servizio di riconoscimento vocale Microsoft.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fba1bbdeaf68bdd45524b336011627a27cd024da
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965705"
---
# <a name="basic-concepts"></a>Concetti di base

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Questa pagina descrive alcuni concetti di base del servizio di riconoscimento vocale Microsoft. È consigliabile leggere la pagina prima di usare l'API di riconoscimento vocale Microsoft nell'applicazione.

## <a name="understanding-speech-recognition"></a>Informazioni sul riconoscimento vocale

Se è la prima volta che si crea un'applicazione abilitata per il riconoscimento vocale o se è la prima volta che si aggiungono funzionalità di riconoscimento vocale a un'applicazione esistente, questa sezione offre informazioni di base per iniziare. Se si ha già familiarità con le applicazioni abilitate per il riconoscimento vocale, è possibile leggere brevemente questa sezione oppure è possibile saltarla completamente qualora si sia già esperti e si intende solo ottenere i dettagli del protocollo.

### <a name="audio-streams"></a>Flussi audio

Il *flusso audio* è uno dei concetti di base principali del riconoscimento vocale. A differenza di una pressione di tasti, che si verifica in un unico momento nel tempo e contiene un unico dato, una richiesta parlata è distribuita su centinaia di millisecondi e contiene molti kilobyte di informazioni. La durata delle espressioni parlate presenta alcune difficoltà per gli sviluppatori che desiderano offrire un'esperienza vocale semplice ed elegante per la propria applicazione. I computer e gli algoritmi di oggi eseguono la trascrizione vocale in circa metà della durata dell'espressione, pertanto un'espressione di 2 secondi può essere trascritta approssimativamente in 1 secondo, ma qualsiasi applicazione in cui si verifichi un ritardo di 1 secondo nell'elaborazione utente non è né semplice né elegante.

Esistono tuttavia modi di "nascondere" il tempo di trascrizione eseguendo la trascrizione stessa di una parte dell'espressione mentre l'utente ne pronuncia un'altra. Suddividendo, ad esempio, un enunciato di 1 secondo in 10 blocchi di 100 millisecondi ed eseguendo la trascrizione su ogni blocco, oltre 450 del totale di 500 millisecondi richiesti per la trascrizione può essere "nascosto", in modo che l'utente non sia a conoscenza della trascrizione viene eseguito mentre parlano. In relazione a questo esempio, tenere presente che il servizio esegue la trascrizione dei precedenti 100 millisecondi di audio mentre l'utente parla nei 100 successivi. Di conseguenza, quando l'utente smette di parlare, il servizio dovrà trascrivere circa solo 100 millisecondi di audio per generare un risultato.

Per ottenere questa esperienza utente, le informazioni audio parlate vengono raccolte in blocchi e trascritte mentre l'utente parla. La raccolta di tali blocchi dal *flusso audio* e il relativo processo di invio al servizio sono denominati, complessivamente, *streaming audio.* Lo streaming audio è una parte importante di ogni applicazione abilitata al riconoscimento vocale. La messa a punto delle dimensioni dei blocchi e l'ottimizzazione dell'implementazione del flusso sono alcuni modi per migliorare l'esperienza utente che comportano un maggiore impatto.

### <a name="microphones"></a>Microfoni

Le persone elaborano l'output parlato tramite l'udito, mentre l'hardware inanimato usa i microfoni. Per abilitare il riconoscimento vocale in un'applicazione, è necessario eseguire l'integrazione con il microfono specificando il flusso audio per l'applicazione stessa.

Le API per il microfono devono consentire l'avvio e l'interruzione della ricezione di byte audio dal microfono. È necessario decidere quali azioni utente attivano l'ascolto dell'audio da parte del microfono. È possibile scegliere, ad esempio, di avviare l'ascolto premendo un pulsante o di disporre di un rilevatore di *parole chiave* o di *parole di riattivazione* sempre in ascolto sul microfono e di usare l'output di tale modulo per attivare l'invio di audio al servizio di riconoscimento vocale Microsoft.

### <a name="end-of-speech"></a>Fine del riconoscimento vocale

Il rilevamento del *momento* in cui un parlante *ha smesso* di parlare sembra abbastanza semplice per gli umani, ma è un problema piuttosto difficile al di fuori delle condizioni di laboratorio. Non è sufficiente cercare semplicemente il solo silenzio dopo l'emissione di un suono, poiché spesso le condizioni vengono complicate dalla presenza di molto rumore ambientale. Il servizio di riconoscimento vocale Microsoft consente di raggiungere risultati ottimali in tale ambito, in quanto è in grado di rilevare rapidamente il momento in cui un utente ha smesso di parlare e invia tale informazione all'applicazione, che viene comunque a conoscenza per ultima della situazione. Questa condizione è del tutto diversa da altre forme di input in cui l'applicazione è la *prima* a sapere quando l'input dell'utente inizia *e* termina.

### <a name="asynchronous-service-responses"></a>Risposte del servizio asincrone

Il fatto che l'applicazione debba essere informata del momento in cui l'input dell'utente è stato completato non comporta alcuna riduzione delle prestazioni né alcuna difficoltà di programmazione, ma richiede che l'approccio alle richieste di riconoscimento vocale sia diverso rispetto ai modelli familiari di richiesta/risposta. Poiché l'applicazione non sarà a conoscenza del momento in cui l'utente smette di parlare, deve continuare a inviare flusso audio al servizio ponendosi contemporaneamente in attesa di una risposta del servizio in modo asincrono. Questo modello differisce da altri protocolli Web di richiesta/risposta, ad esempio HTTP. In tali protocolli è necessario completare una richiesta prima di ricevere una risposta, mentre nel protocollo del servizio di riconoscimento vocale Microsoft si ricevono risposte *mentre è ancora in atto la trasmissione audio per la richiesta*.

> [!NOTE]
> Questa funzionalità non è supportata quando si usa l'API REST HTTP per il riconoscimento vocale.

### <a name="turns"></a>Turni

Le parole sono un veicolo per le informazioni. Quando si parla, si tenta di comunicare le informazioni in proprio possesso a qualcuno che è in attesa di ascoltarle. Questa interazione avviene sulla base di un modello che prevede che l'utente alterni momenti in cui parla e momenti in cui ascolta. In modo analogo, l'applicazione abilitata al riconoscimento vocale interagisce con gli utenti ascoltando e rispondendo alternativamente, anche se in genere ascolta per la maggior parte del tempo. L'input parlato dell'utente e la risposta del servizio a tale input vengono denominati *turno*. Un *turno* inizia quando l'utente parla e termina quando l'applicazione ha completato la gestione della risposta del servizio di riconoscimento vocale.

### <a name="telemetry"></a>Telemetria

La creazione di un'applicazione o di un dispositivo abilitato per il riconoscimento vocale può essere difficile, anche per gli sviluppatori esperti. I protocolli basati sul flusso a prima vista possono sembrare spesso un ostacolo e informazioni importanti, ad esempio il rilevamento del silenzio, possono essere completamente nuove. Con un numero così elevato di messaggi che devono essere inviati e ricevuti correttamente per completare una singola fase di richiesta/risposta, la raccolta di dati completi e accurati su tali messaggi risulta essere *molto* importante. Il protocollo del servizio di riconoscimento vocale Microsoft consente di raccogliere tali dati. È necessario inviare tali dati nel modo più accurato possibile. Dati completi e precisi rappresentano infatti una garanzia qualora sia necessario, ad esempio, rivolgersi al team del servizio di riconoscimento vocale Microsoft per risolvere i problemi di implementazione del client, operazione in cui la qualità dei dati di telemetria raccolti è un fattore critico per l'analisi del problema.

> [!NOTE]
> Questa funzionalità non è supportata quando si usa l'API REST del riconoscimento vocale.

### <a name="speech-application-states"></a>Stati dell'applicazione di riconoscimento vocale

I passaggi eseguiti per abilitare l'input parlato nell'applicazione sono leggermente diversi da quelli per altre forme di input, ad esempio clic del mouse o tocchi delle dita. È necessario tenere traccia del momento in cui l'applicazione è in ascolto del microfono e di quello in cui invia dati al servizio di riconoscimento vocale nonché del momento in cui è in attesa di una risposta dal servizio e di quello in cui si trova in uno stato di inattività. La relazione tra questi stati viene illustrata nel diagramma seguente.

![Diagramma degli stati dell'applicazione di riconoscimento vocale](Images/speech-application-state-diagram.png)

Il servizio di riconoscimento vocale Microsoft partecipa ad alcuni stati e di conseguenza il protocollo del servizio definisce messaggi che consentono la transizione dell'applicazione tra i diversi stati. L'applicazione deve interpretare tali messaggi di protocollo e agire sulla base di questi per monitorare e gestire gli stati dell'applicazione di riconoscimento vocale.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Uso del servizio di riconoscimento vocale dalle app

Il servizio di riconoscimento vocale Microsoft consente agli sviluppatori di aggiungere le funzioni di riconoscimento vocale in due modi diversi.

- [API REST](GetStarted/GetStartedREST.md): gli sviluppatori possono usare chiamate HTTP dalle proprie app al servizio per il riconoscimento vocale.
- [Librerie client](GetStarted/GetStartedClientLibraries.md): per le funzionalità avanzate, gli sviluppatori possono scaricare le librerie client del riconoscimento vocale Microsoft e collegarle alle proprie app.  Le librerie client sono disponibili su diverse piattaforme (Windows, Android, iOS) con linguaggi diversi (C#, Java, JavaScript, ObjectiveC).

| Casi d'uso | [API REST](GetStarted/GetStartedREST.md) | [Librerie client](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Convertire breve contenuto vocale, ad esempio comandi (lunghezza audio < 15 secondi) senza risultati temporanei | Sì | Yes |
| Convertire contenuto audio lungo (> 15 secondi) | No | Sì |
| Trasmettere un flusso audio con risultati temporanei desiderati | No | Sì |
| Comprendere il testo convertito dall'audio tramite LUIS | No | Sì |

 Se la lingua o la piattaforma non dispone ancora di un componente SDK, è possibile creare la propria implementazione in base alla [documentazione del protocollo](API-Reference-REST/websocketprotocol.md).

## <a name="recognition-modes"></a>Modalità di riconoscimento

Sono disponibili tre modalità di riconoscimento: `interactive`, `conversation` e `dictation`. La modalità regola il riconoscimento vocale in base all'approccio seguito in genere dagli utenti per parlare. Scegliere la modalità di riconoscimento appropriata per l'applicazione.

> [!NOTE]
> Le modalità di riconoscimento potrebbero presentare comportamenti diversi nel protocollo REST rispetto al protocollo WebSocket. L'API REST, ad esempio, non supporta il riconoscimento continuo, anche in modalità conversazione o dettatura.
> [!NOTE]
> Tali modalità sono applicabili quando si usa direttamente il protocollo REST o WebSocket. Le [librerie client](GetStarted/GetStartedClientLibraries.md) usano parametri diversi per specificare la modalità di riconoscimento. Per altre informazioni, vedere la libreria client scelta.

Il servizio di riconoscimento vocale Microsoft restituisce solo il risultato di un'espressione di riconoscimento per tutte le modalità. È previsto un limite di 15 secondi per ogni singola espressione.

### <a name="interactive-mode"></a>Modalità interattiva

In modalità `interactive` un utente invia richieste brevi e prevede che l'applicazione esegua un'azione in risposta.

Le caratteristiche seguenti sono tipiche di applicazioni in modalità interattiva:

- Gli utenti sanno di comunicare con una macchina e non con un altro essere umano.
- Gli utenti dell'applicazione sanno in anticipo cosa dire in base alle operazioni che desiderano vengano effettuate dall'applicazione.
- Le espressioni durano in genere 2 e 3 secondi.

### <a name="conversation-mode"></a>Modalità conversazione

In modalità `conversation` gli utenti sono coinvolti in una conversazione tra umani.

Le caratteristiche seguenti sono tipiche di applicazioni in modalità conversazione:

- Gli utenti sanno di comunicare con un'altra persona.
- Il riconoscimento vocale migliora le conversazioni umane consentendo a uno oppure a entrambi i partecipanti di visualizzare il testo parlato.
- Gli utenti non pianificano sempre ciò che desiderano dire.
- Gli utenti usano spesso gergo e un linguaggio informale.

### <a name="dictation-mode"></a>Modalità dettatura

In modalità `dictation` gli utenti espongono espressioni più lunghe all'applicazione per un'ulteriore elaborazione.

Le caratteristiche seguenti sono tipiche di applicazioni in modalità dettatura:

- Gli utenti sanno di comunicare con un computer.
- Agli utenti vengono visualizzati i risultati del riconoscimento vocale in formato testo.
- Gli utenti spesso pianificano ciò che devono pronunciare e usano un linguaggio più formale.
- Gli utenti usano frasi complete che durano 5-8 secondi.

> [!NOTE]
> In modalità dettatura e conversazione il servizio di riconoscimento vocale Microsoft non restituisce risultati parziali. Il servizio restituisce invece i risultati di un'espressione stabile dopo i limiti della fase di silenzio nel flusso audio. Microsoft può ottimizzare il protocollo di riconoscimento vocale per migliorare l'esperienza utente in tali modalità.

## <a name="recognition-languages"></a>Lingue di riconoscimento

La *lingua di riconoscimento* specifica la lingua parlata dall'utente dell'applicazione. Specificare la *lingua di riconoscimento* con il parametro di query dell'URL *language* nella connessione. Il valore del parametro di query *language* usa il tag di lingua IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag) e **deve** essere una delle lingue supportate dall'API Riconoscimento vocale. L'elenco completo delle lingue supportate dal servizio di riconoscimento vocale è disponibile nella pagina [Lingue supportate](API-Reference-REST/supportedlanguages.md).

Il servizio di riconoscimento vocale Microsoft rifiuta le richieste di connessione non valide tramite una risposta `HTTP 400 Bad Request`. Una richiesta non valida ha le caratteristiche seguenti:

- Non include un valore del parametro di query *language*.
- Include un parametro di query *language* in formato non corretto.
- Include un parametro di query *language* il cui valore non appartiene alle lingue supportate.

È possibile scegliere di compilare un'applicazione che supporta una o tutte le lingue previste dal servizio.

### <a name="example"></a>Esempio

Nell'esempio seguente un'applicazione usa la modalità di riconoscimento vocale *conversation* per un utente che parla l'inglese-Stati Uniti.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Risposte di trascrizione

Le risposte di trascrizione restituiscono il testo convertito dall'audio ai client. Una risposta di trascrizione contiene i campi seguenti:

- `RecognitionStatus` specifica lo stato del riconoscimento. I valori possibili sono indicati nella tabella seguente.

| Stato | DESCRIZIONE |
| ------------- | ---------------- |
| Riuscito | Il riconoscimento ha avuto esito positivo e il campo DisplayText è presente |
| NoMatch | La parte parlata è stata rilevata nel flusso audio, ma non sono state trovate corrispondenze per alcuna parola nella lingua di destinazione. Per altri dettagli, vedere [NoMatch Recognition Status(#nomatch-recognition-status)  |
| InitialSilenceTimeout | La parte iniziale del flusso audio conteneva solo silenzio e il servizio ha raggiunto il timeout in attesa della parte parlata |
| BabbleTimeout | La parte iniziale del flusso audio conteneva solo rumore e il servizio ha raggiunto il timeout in attesa della parte parlata |
| Errore | Il servizio di riconoscimento ha rilevato un errore interno e non è in grado di continuare |

- `DisplayText` rappresenta l'espressione riconosciuta dopo l'applicazione di maiuscole/minuscole, punteggiatura e normalizzazione del testo inverso e dopo che il contenuto volgare è stato mascherato con asterischi. Il campo DisplayText è presente *solo* se nel campo `RecognitionStatus` è presente il valore `Success`.

- `Offset` specifica l'offset (in unità di 100 nanosecondi) in corrispondenza del quale l'espressione è stata riconosciuta, calcolato in relazione all'inizio del flusso audio.

- `Duration` specifica la durata (in unità di 100 nanosecondi) dell'espressione parlata.

Se si desidera, una risposta di trascrizione restituisce altre informazioni. Per la restituzione di output più dettagliato, vedere la sezione [formato di output](#output-format).

Il servizio di riconoscimento vocale Microsoft supporta un processo di trascrizione aggiuntivo che comprende l'aggiunta di maiuscole/minuscole e della punteggiatura e che consente di mascherare il contenuto volgare e di normalizzare il testo in forme comuni. Se ad esempio un utente legge un'espressione rappresentata dalle parole "ricordami di acquistare sei iPhone", il servizio di riconoscimento vocale Microsoft restituisce il testo trascritto "Ricordami di acquistare 6 iPhone". Il processo che converte la parola "sei" nel numero "6" è denominato *Normalizzazione testo inverso* (*ITN* in breve).

### <a name="nomatch-recognition-status"></a>Stato di riconoscimento NoMatch

La risposta di trascrizione restituisce `NoMatch` in `RecognitionStatus` quando il servizio di riconoscimento vocale Microsoft rileva una parte vocale nel flusso audio, ma non è in grado di metterla in corrispondenza con la grammatica della lingua usata per la richiesta. Una condizione *NoMatch* può verificarsi ad esempio se un utente dice qualcosa in tedesco quando lo strumento di riconoscimento si aspetta l'inglese come lingua parlata. Lo schema a onde dell'espressione indica la presenza di voce umana, ma nessuna delle parole corrisponde al lessico inglese usato dallo strumento di riconoscimento.

Un'altra condizione *NoMatch* si verifica quando l'algoritmo di riconoscimento non è in grado di trovare una corrispondenza accurata per i suoni contenuti nel flusso audio. Quando si verifica questa condizione, il servizio di riconoscimento vocale Microsoft può generare il messaggio *speech.hypothesis* che contiene *hypothesized text*, ma genera un messaggio *speech.phrase* in cui *RecognitionStatus* è *NoMatch*. Questa condizione è normale ed è necessario non fare alcuna assunzione relativa all'accuratezza o alla fedeltà del testo nel messaggio *speech.hypothesis*. Poiché il servizio di riconoscimento vocale Microsoft genera i messaggi *speech.hypothesis*, è necessario anche non presupporre che sia in grado di generare un messaggio *speech.phrase* con *RecognitionStatus* *Success*.

## <a name="output-format"></a>Formato di output

Il servizio di riconoscimento vocale Microsoft può restituire una vasta gamma di formati di payload nelle risposte di trascrizione. Tutti i payload sono strutture JSON.

È possibile controllare il formato di un'espressione restituita specificando il parametro di query dell'URL `format`. Per impostazione predefinita, il servizio restituisce `simple` risultati.

| Formato | Descrizione |
|-----|-----|
| `simple` | Risultato di un'espressione semplificata contenente lo stato di riconoscimento e il testo riconosciuto nella forma di visualizzazione. |
| `detailed` | Stato di riconoscimento ed elenco degli N migliori risultati in cui ogni espressione restituita contiene tutte le quattro forme di riconoscimento e un punteggio di attendibilità. |

Il formato `detailed` contiene gli [N valori migliori](#n-best-values), oltre a `RecognitionStatus`, `Offset` e `duration`, nella risposta.

### <a name="n-best-values"></a>N valori migliori

Gli ascoltatori, sia umani che macchine, non possono mai essere certi di aver udito *esattamente* ciò che è stato detto. Un ascoltatore può assegnare una *probabilità* solo a un'interpretazione particolare di un'espressione.

In condizioni normali, quando le persone parlano ad altri con cui interagiscono frequentemente, hanno un'elevata probabilità di riconoscere le parole che sono state dette. Gli ascoltatori basati su dispositivi di riconoscimento vocale cercano di raggiungere livelli di accuratezza analoghi e, in base a determinate condizioni, [raggiungono la parità con gli umani](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Gli algoritmi usati nel riconoscimento vocale esplorano interpretazioni alternativi di un'espressione come parte della normale elaborazione. Tali alternative vengono in genere eliminate quando l'evidenza a favore di una sola interpretazione diventa predominante. In condizioni ottimali, tuttavia, lo strumento di riconoscimento vocale termina con un elenco possibili interpretazioni alternative. Le prime *N* alternative presenti nell'elenco sono denominate *elenco degli N migliori risultati*. A ogni alternativa viene assegnato un [punteggio di attendibilità](#confidence). I punteggi di attendibilità sono compresi tra 0 e 1. Un punteggio pari a 1 rappresenta il livello di attendibilità più elevato. Un punteggio pari a 0 rappresenta il livello di attendibilità meno elevato.

> [!NOTE]
> Il numero di voci nell'elenco degli N migliori risultati varia tra più espressioni e può variare tra più riconoscimenti della *stessa* espressione. Questa variazione è un risultato naturale e previsto della natura probabilistica dell'algoritmo di riconoscimento vocale.

Ogni voce restituita nell'elenco degli N migliori risultati contiene gli elementi seguenti:

- `Confidence`, che rappresenta i [punteggi di attendibilità](#confidence) di questa voce.
- `Lexical`, ovvero la [forma lessicale](#lexical-form) del testo riconosciuto.
- `ITN`, ovvero la [forma ITN](#itn-form) del testo riconosciuto.
- `MaskedITN`, ovvero la [forma mascherata ITN](#masked-itn-form) del testo riconosciuto.
- `Display`, ovvero la [forma di visualizzazione](#display-form) del testo riconosciuto.

### Punteggi di attendibilità <a id="confidence"></a>

I punteggi di attendibilità sono parte integrante dei sistemi di riconoscimento vocale. Il servizio di riconoscimento vocale Microsoft ottiene i punteggi di attendibilità da uno *strumento di classificazione dell'attendibilità*. Microsoft esegue il training di tale strumento su un set di funzionalità progettate per distinguere nel modo migliore il riconoscimento corretto da quello non corretto. I punteggi di attendibilità vengono valutati per singole parole ed espressioni complete.

Se si sceglie di usare i punteggi di attendibilità restituiti dal servizio, è necessario tenere presente quanto segue:

- È possibile confrontare i punteggi di attendibilità solo all'interno di una stessa modalità e di una stessa lingua di riconoscimento. Non confrontare i punteggi tra lingue o modalità di riconoscimento diverse. Un punteggio di attendibilità in modalità di riconoscimento interattiva *non ha alcuna* correlazione con un punteggio di attendibilità in modalità dettatura.
- I punteggi di attendibilità restituiscono i risultati migliori su un set limitato di espressioni. Per un ampio set di espressioni, la variabilità dei punteggi è naturalmente elevata.

Se si sceglie di usare un valore di punteggio di attendibilità come una *soglia* su cui agisce l'applicazione, usare il riconoscimento vocale per stabilire i valori di soglia.

- Eseguire il riconoscimento vocale su un campione rappresentativo di espressioni per l'applicazione.
- Raccogliere i punteggi di attendibilità per ogni riconoscimento nel set di campioni.
- Basare il valore di soglia su alcuni percentile di attendibilità per tale campione.

Nessun valore di soglia singola è appropriato per tutte le applicazioni. Un punteggio di attendibilità accettabile per un'applicazione potrebbe non esserlo per un'altra.

### <a name="lexical-form"></a>Forma lessicale

La forma lessicale è il testo riconosciuto esattamente come è stato pronunciato e senza punteggiatura o specifica di maiuscole/minuscole. La forma lessicale dell'indirizzo "Via Roma 100" sarebbe *via roma cento*, presupponendo che sia stato pronunciato in questo modo. La forma lessicale della frase "Invia un promemoria per acquistare 5 matite" è *invia un promemoria per acquistare cinque matite*.

La forma lessicale è la più appropriata per le applicazioni che devono eseguire la normalizzazione non standard del testo. La forma lessicale è adatta anche per le applicazioni che richiedono parole di riconoscimento non elaborate.

In tale forma il contenuto volgare non viene mai mascherato.

### <a name="itn-form"></a>Forma ITN

La normalizzazione del testo è il processo di conversione di testo da una forma a un'altra forma "canonica". Il numero di telefono "555-1212", ad esempio, potrebbe essere convertito nella forma canonica *cinque cinque cinque uno due uno due*. La normalizzazione *inversa* del testo (ITN) inverte il processo e converte le parole "cinque cinque cinque uno due uno due" nella forma canonica invertita *555-1212*. La forma ITN di un risultato di riconoscimento non include l'uso delle maiuscole/minuscole né della punteggiatura.

La forma ITN è la più appropriata per le applicazioni che agiscono sul testo riconosciuto. Un'applicazione che consente ad esempio di pronunciare termini di ricerca e quindi di usarli in una query Web può usare la forma ITN. Il contenuto volgare non viene mai mascherato nella forma ITN. Per mascherare il contenuto volgare, usare la *forma ITN mascherata*.

### <a name="masked-itn-form"></a>Forma ITN mascherata

Il contenuto volgare è parte naturale della lingua parlata e pertanto il servizio di riconoscimento vocale Microsoft riconosce tali parole ed espressioni quando vengono pronunciate. Tale contenuto, tuttavia, può non essere appropriato per tutte le applicazioni, soprattutto per quelle destinate a un gruppo limitato di utenti non adulti.

La forma ITN mascherata applica una maschera per il contenuto volgare nella forma di normalizzazione del testo inversa. Per mascherare il contenuto volgare, impostare il valore del parametro profanity su `masked`. Quando il contenuto volgare viene mascherato, le parole riconosciute come parte del lessico volgare della lingua vengono sostituite con asterischi. Ad esempio: *invia un promemoria per acquistare 5 **** matite*. La forma ITN mascherata di un risultato di riconoscimento non include l'uso delle maiuscole/minuscole né della punteggiatura.

> [!NOTE]
> Se il valore del parametro di query profanity è impostato su `raw`, la forma ITN mascherata alla forma ITN. Il contenuto volgare il tal caso *non* viene mascherato.

### <a name="display-form"></a>Forma visualizzata

La punteggiatura e l'uso delle maiuscole/minuscole indicano i punti in cui inserire enfasi, fare una pausa e così via, al fine di semplificare la comprensione del testo. La forma visualizzata aggiunge la punteggiatura e usa le maiuscole/minuscole per i risultati di riconoscimento, rendendola la forma più appropriata per le applicazioni che visualizzano il testo parlato.

Poiché la forma visualizzata estende la forma ITN mascherata, è possibile impostare il valore del parametro profanity su `masked` o `raw`. Se il valore è impostato su `raw`, i risultati del riconoscimento includono anche il contenuto volgare pronunciato dall'utente. Se il valore è impostato su `masked`, le parole riconosciute come parte del lessico volgare della lingua vengono sostituite con asterischi.

### <a name="sample-responses"></a>Risposte di esempio

Tutti i payload sono strutture JSON.

Il formato di payload dell'espressione `simple` restituita è il seguente:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Il formato di payload dell'espressione `detailed` restituita è il seguente:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Gestione di contenuto volgare nel riconoscimento vocale

Il servizio di riconoscimento vocale Microsoft riconosce tutte le forme del parlare umano, tra cui parole ed espressioni che molte persone classificherebbero come "contenuto volgare". È possibile controllare il modo in cui il servizio gestisce il contenuto volgare usando il parametro di query *profanity*. Per impostazione predefinita, il servizio maschera il contenuto volgare nei risultati *speech.phrase* e non restituisce messaggi *speech.hypothesis* in cui è presente contenuto volgare.

| Valore di *profanity* | Descrizione |
| - | - |
| `masked` | Maschera il contenuto volgare con asterischi. Questo è il comportamento predefinito. |
| `removed` | Rimuove il contenuto volgare da tutti i risultati. |
| `raw` | Riconosce e restituisce il contenuto volgare in tutti i risultati. |

### <a name="profanity-value-masked"></a>Valore di profanity `Masked`

Per mascherare il contenuto volgare, impostare il parametro di query *profanity* sul valore *masked*. Quando il parametro di query *profanity* assume questo valore o non è specificato per una richiesta, il servizio *maschera* il contenuto volgare. A tale scopo, il servizio sostituisce il contenuto volgare nei risultati di riconoscimento con asterischi. Quando si specifica la gestione del contenuto volgare con il mascheramento, il servizio non restituisce messaggi *speech.hypothesis* in cui è presente contenuto volgare.

### <a name="profanity-value-removed"></a>Valore di profanity `Removed`

Quando il parametro di query *profanity* assume il valore *removed*, il servizio rimuove il contenuto volgare da entrambi i messaggi *speech.phrase* e *speech.hypothesis*. I risultati sono analoghi alla condizione *in cui le parole volgari non sono state pronunciate*.

#### <a name="profanity-only-utterances"></a>Espressioni di solo contenuto volgare

Un utente potrebbe pronunciare *solo* parole volgari quando un'applicazione ha configurato il servizio in modo da rimuovere il contenuto volgare. In questo scenario, se la modalità di riconoscimento è *dictation* o *conversation*, il servizio non restituisce alcun messaggio *speech.result*. Se la modalità di riconoscimento è *interactive*, il servizio restituisce un messaggio *speech.result* con il codice di stato *NoMatch*.

### <a name="profanity-value-raw"></a>Valore di profanity `Raw`

Quando il parametro di query *profanity* assume il valore *raw*, il servizio non rimuove né maschera il contenuto volgare nei messaggi *speech.phrase* o *speech.hypothesis*.
