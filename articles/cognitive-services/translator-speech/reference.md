---
title: Informazioni di riferimento sull'API Traduzione vocale Microsoft | Microsoft Docs
titleSuffix: Cognitive Services
description: Documentazione di riferimento per l'API Traduzione vocale Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jansko
ms.openlocfilehash: be8faddf56158de3399713c41638c0b913b4627e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378404"
---
# <a name="microsoft-translator-speech-api"></a>API Traduzione vocale Microsoft

Questo servizio offre un'API di streaming per trascrivere le conversazioni vocali da una lingua in testo in un'altra lingua. L'API integra anche funzionalità di sintesi vocale per riconvertire in audio il testo tradotto. L'API Traduzione vocale Microsoft abilita alcuni scenari tra cui la traduzione in tempo reale delle conversazioni come in Skype Translator.

Con l'API Traduzione vocale Microsoft le applicazioni client trasmettono audio vocale al servizio e ricevono un flusso di risultati basati su testo, inclusi il testo riconosciuto nella lingua di origine e la sua traduzione nella lingua di destinazione. I risultati del testo vengono prodotti applicando il riconoscimento vocale automatico (ASR) alimentato da reti neurali profonde al flusso audio in ingresso. L'output ASR non elaborato viene migliorato ulteriormente con una nuova tecnica chiamata TrueText, per riflettere più da vicino le finalità dell'utente. Ad esempio, TrueText rimuove le disfluenze (gli hmmm e i colpi di tosse) e ripristina la punteggiatura e l'uso delle maiuscole corretti. Vi è anche la possibilità di nascondere o escludere i contenuti volgari. Per i motori di riconoscimento e di traduzione il training viene eseguito specificamente per gestire gli argomenti delle conversazioni. Il servizio di traduzione vocale usa il rilevamento dei silenzi per determinare la fine di un'espressione. Dopo una pausa nell'attività vocale, il servizio riprodurrà un risultato finale per l'espressione completata. Il servizio può anche restituire risultati parziali, che forniscono traduzioni e riconoscimenti intermedi per un'espressione in corso. Per i risultati finali, il servizio offre la possibilità di sintetizzare il vocale (sintesi vocale) dal testo parlato alle lingue di destinazione. L'audio della sintesi vocale viene creato nel formato specificato dal client. Sono disponibili i formati WAV e MP3.

L'API Traduzione vocale Microsoft sfrutta il protocollo WebSocket per fornire un canale di comunicazione full-duplex tra il client e il server. Per usare il servizio in un'applicazione, saranno necessari questi passaggi:

## <a name="1-getting-started"></a>1. Introduzione
Per accedere all'API Traduzione testuale Microsoft, è necessario [registrarsi per Microsoft Azure](translator-speech-how-to-signup.md).

## <a name="2-authentication"></a>2. Authentication

Usare la chiave di sottoscrizione per l'autenticazione. L'API Traduzione vocale Microsoft supporta due modalità di autenticazione:

* **Uso di un token di accesso:** nell'applicazione ottenere un token di accesso dal servizio token. Usare la chiave di sottoscrizione dell'API Traduzione vocale Microsoft per ottenere un token di accesso dal servizio di autenticazione di Servizi cognitivi. Il token di accesso è valido per 10 minuti. Ottenere un nuovo token di accesso ogni 10 minuti e continuare a usare lo stesso token di accesso per le richieste ripetute entro 10 minuti.

* **Uso diretto di una chiave di sottoscrizione:** nell'applicazione passare la chiave di sottoscrizione come valore nell'intestazione `Ocp-Apim-Subscription-Key`.

Trattare la chiave di sottoscrizione e il token di accesso come informazioni riservate da tenere nascoste e non visualizzare.

## <a name="3-query-languages"></a>3. Eseguire una query delle lingue
**Cercare nella risorsa Languages il set corrente di lingue supportate.** La [risorsa Languages](languages-reference.md) espone il set di lingue e voci disponibili per riconoscimento vocale, traduzione testuale e sintesi vocale. A ogni lingua o voce è assegnato un identificatore usato dall'API Traduzione vocale Microsoft per identificare la stessa lingua o voce.

## <a name="4-stream-audio"></a>4. Trasmettere l'audio
**Aprire una connessione e iniziare a trasmettere l'audio al servizio.** L'URL del servizio è `wss://dev.microsofttranslator.com/speech/translate`. I parametri e i formati audio previsti dal servizio sono descritti sotto, nell'operazione `/speech/translate`. Uno dei parametri viene usato per passare il token di accesso dal passaggio 2 precedente.

## <a name="5-process-the-results"></a>5. Elaborare i risultati
**Elaborare i risultati trasmessi dal servizio.** Il formato dei risultati parziali, i risultati finali e i segmenti audio di sintesi vocale sono descritti sotto nella documentazione dell'operazione `/speech/translate`.

Nel [sito GitHub di Microsoft Translator](https://github.com/MicrosoftTranslator) sono disponibili esempi di codice che illustrano l'uso dell'API Traduzione vocale.

## <a name="implementation-notes"></a>Note sull'implementazione

GET /speech/translate stabilisce una sessione per la traduzione vocale

### <a name="connecting"></a>Connessione
Prima di connettersi al servizio, esaminare l'elenco di parametri indicati più avanti in questa sezione. Un richiesta di esempio è:

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

La richiesta specifica che l'inglese parlato verrà trasmesso al servizio e tradotto in italiano. Ogni risultato di riconoscimento finale genererà una risposta audio di sintesi vocale con la voce femminile chiamata Elsa. Si noti che la richiesta include le credenziali in `Ocp-Apim-Subscription-Key header`. La richiesta segue una procedura consigliata impostando un identificatore univoco globale nell'intestazione `X-ClientTraceId`. Un'applicazione client registrerà l'ID traccia che verrà quindi usato per risolvere i problemi quando si verificano.

### <a name="sending-audio"></a>Invio di audio
Una volta stabilita la connessione, il client inizia la trasmissione dell'audio al servizio. Il client invia l'audio in blocchi. Ogni blocco viene trasmesso usando un messaggio Websocket di tipo Binary.

L'input audio è nel formato di file audio Waveform (WAVE, più comunemente noto come WAV dall'estensione di file). L'applicazione client trasmetterà audio PCM a 16 bit con segno a un solo canale, campionato a 16 kHz. Il primo set di byte trasmessi dal client includerà l'intestazione WAV. Un'intestazione a 44 byte per un flusso PCM a 16 bit con segno a un solo canale, campionato a 16 kHz è:

|Offset|Valore|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|"WAVE"|
|12 - 15|"fmt"|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|"data"|
|40 - 43|0|

Si noti che le dimensioni del file totali (byte 4-7) e le dimensioni di "data" (byte 40-43) sono impostate su zero. Questa impostazione è corretta per lo scenario di flusso in cui le dimensioni totali non sono necessariamente note all'inizio.

Dopo l'invio dell'intestazione WAV (RIFF), il client invia blocchi di dati audio. Il client trasmetterà in genere blocchi con dimensioni fisse che rappresentano una durata fissa (ad esempio, 100 ms di audio per volta).

### <a name="final-result"></a>Risultato finale
Alla fine di un'espressione viene generato un risultato di riconoscimento vocale finale. Un risultato viene trasmesso dal servizio al client con un messaggio WebSocket di tipo Text. Il contenuto del messaggio è la serializzazione JSON di un oggetto con le proprietà seguenti:

* `type`: costante di tipo stringa per identificare il tipo di risultato. Il valore è finale per i risultati finali.
* `id`: identificatore di tipo stringa assegnato al risultato del riconoscimento.
* `recognition`: testo riconosciuto nella lingua di origine. Il testo può essere una stringa vuota in caso di falso riconoscimento.
* `translation`: testo tradotto riconosciuto nella lingua di destinazione.
* `audioTimeOffset`: offset temporale dell'inizio del riconoscimento in tick (1 tick = 100 nanosecondi). L'offset è relativo all'inizio del flusso.
* `audioTimeSize`: durata in tick (100 nanosecondi) del riconoscimento.
* `audioStreamPosition`: offset di byte dell'inizio del riconoscimento. L'offset è relativo all'inizio del flusso.
* `audioSizeBytes`: dimensioni in byte del riconoscimento.

Si noti che il posizionamento del riconoscimento nel flusso audio non è incluso nei risultati per impostazione predefinita. La funzionalità `TimingInfo` deve essere selezionata dal client. Vedere il parametro `features`.

Il seguente è un risultato finale di esempio:

```
{
  type: "final"
  id: "23",
  recognition: "what was said", 
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>Risultato parziale
I risultati del riconoscimento vocale parziali o intermedi non vengono trasmessi al client per impostazione predefinita. Il client può usare il parametro di query features per richiederli.

Un risultato parziale viene trasmesso dal servizio al client con un messaggio WebSocket di tipo Text. Il contenuto del messaggio è la serializzazione JSON di un oggetto con le proprietà seguenti:

* `type`: costante di tipo stringa per identificare il tipo di risultato. Il valore è parziale per i risultati parziali.
* `id`: identificatore di tipo stringa assegnato al risultato del riconoscimento.
* `recognition`: testo riconosciuto nella lingua di origine.
* `translation`: testo tradotto riconosciuto nella lingua di destinazione.
* `audioTimeOffset`: offset temporale dell'inizio del riconoscimento in tick (1 tick = 100 nanosecondi). L'offset è relativo all'inizio del flusso.
* `audioTimeSize`: durata in tick (100 nanosecondi) del riconoscimento.
* `audioStreamPosition`: offset di byte dell'inizio del riconoscimento. L'offset è relativo all'inizio del flusso.
* `audioSizeBytes`: dimensioni in byte del riconoscimento.

Si noti che il posizionamento del riconoscimento nel flusso audio non è incluso nei risultati per impostazione predefinita. La funzionalità TimingInfo deve essere selezionata dal client. Vedere il parametro features.

Il seguente è un risultato finale di esempio:

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was", 
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>Sintesi vocale
Quando la funzionalità di sintesi vocale è abilitata (vedere sotto il parametro `features`), un risultato finale è seguito dall'audio del testo tradotto parlato. I dati audio vengono suddivisi in blocchi e inviati dal servizio al client come sequenza di messaggi Websocket di tipo Binary. Un client può rilevare la fine del flusso controllando il bit FIN di ogni messaggio. L'ultimo messaggio Binary avrà il bit FIN impostato su uno per indicare la fine del flusso. Il formato del flusso dipende dal valore del parametro `format`.

### <a name="closing-the-connection"></a>Chiusura della connessione
Quando un'applicazione client ha completato la trasmissione dell'audio e ha ricevuto l'ultimo risultato finale, chiuderà la connessione avviando l'handshake di chiusura del WebSocket. Alcune condizioni fanno sì che il server termini la connessione. I codici seguenti relativi alla chiusura del WebSocket possono essere ricevuti dal client:

* `1003 - Invalid Message Type`: il server terminerà la connessione perché non può accettare il tipo di dati ricevuto. Ciò accade in genere quando l'audio in ingresso non inizia con un'intestazione corretta.
* `1000 - Normal closure`: la connessione è stata chiusa dopo che la richiesta è stata soddisfatta. Il server chiuderà la connessione: quando non viene ricevuto audio dal client per un lungo periodo di tempo, quando viene trasmesso silenzio per un lungo periodo di tempo e quando una sessione raggiunge la durata massima consentita (circa 90 minuti).
* `1001 - Endpoint Unavailable`: indica che il server diventerà non disponibile. L'applicazione client potrebbe provare a ristabilire la connessione con un limite al numero di tentativi.
* `1011 - Internal Server Error`: la connessione verrà chiusa dal server a causa di un errore sul server.

### <a name="parameters"></a>Parametri

|Parametro|Valore|DESCRIZIONE|Tipo di parametro|Tipo di dati|
|:---|:---|:---|:---|:---|
|api-version|1.0|Versione dell'API richiesta dal client. I valori consentiti sono: `1.0`.|query   |stringa|
|from|(vuoto)   |Specifica la lingua dell'audio in ingresso. Il valore è uno degli identificatori della lingua dell'ambito `speech` nella risposta dall'API Languages.|query|stringa|
|to|(vuoto)|Specifica la lingua in cui tradurre il testo trascritto. Il valore è uno degli identificatori della lingua dell'ambito `text` nella risposta dall'API Languages.|query|stringa|
|funzionalità|(vuoto)   |Set di funzionalità separate da virgole selezionate dal client. Le funzionalità includono:<ul><li>`TextToSpeech`: specifica che il servizio deve restituire l'audio tradotto della frase tradotta finale.</li><li>`Partial`: specifica che il servizio deve restituire i risultati intermedi del riconoscimento mentre è in corso la trasmissione dell'audio al servizio.</li><li>`TimingInfo`: specifica che il servizio deve restituire le informazioni sugli intervalli associati a ogni riconoscimento.</li></ul>Un client, ad esempio, specificherà `features=partial,texttospeech` per ricevere i risultati parziali e la sintesi vocale, ma non le informazioni sugli intervalli. Si noti che i risultati finali vengono sempre trasmessi al client.|query|stringa|
|voice|(vuoto)|Identifica la voce da usare per il rendering della sintesi vocale del testo tradotto. Il valore è uno degli identificatori della voce dell'ambito tts nella risposta dall'API Languages. Se non viene specificata una voce, il sistema ne sceglierà automaticamente una quando verrà abilitata la funzionalità di sintesi vocale.|query|stringa|
|format|(vuoto)|Specifica il formato del flusso audio di sintesi vocale restituito dal servizio. Le opzioni disponibili sono:<ul><li>`audio/wav`: flusso audio Waveform. Il client userà l'intestazione WAV per interpretare correttamente il formato audio. L'audio WAV per la sintesi vocale è una modulazione PCM a canale singolo a 16 bit con una frequenza di campionamento di 24 kHz o 16 kHz.</li><li>`audio/mp3`: flusso audio MP3.</li></ul>Il valore predefinito è `audio/wav`.|query|stringa|
|ProfanityAction    |(vuoto)    |Specifica come il servizio deve gestire i contenuti volgari riconosciuti nel parlato. Le azioni valide sono:<ul><li>`NoAction`: i contenuti volgari rimangono invariati.</li><li>`Marked`: i contenuti volgari vengono sostituiti con un marcatore. Vedere il parametro `ProfanityMarker`.</li><li>`Deleted`: i contenuti volgari vengono eliminati. Se ad esempio la parola `"jackass"` viene trattata come contenuto volgare, la frase `"He is a jackass."` diventerà `"He is a .".`</li></ul>Il valore predefinito è Marked.|query|stringa|
|ProfanityMarker|(vuoto)    |Specifica come vengono gestiti i contenuti volgari quando `ProfanityAction` è impostato su `Marked`. Le opzioni valide sono:<ul><li>`Asterisk`: i contenuti volgari vengono sostituiti con la stringa `***`. Se ad esempio la parola `"jackass"` viene trattata come contenuto volgare, la frase `"He is a jackass."` diventerà `"He is a ***.".`</li><li>`Tag`: il contenuto volgare viene racchiuso in un tag XML profanity. Se ad esempio la parola `"jackass"` viene trattata come contenuto volgare, la frase `"He is a jackass."` diventerà `"He is a <profanity>jackass</profanity>."`.</li></ul>Il valore predefinito è `Asterisk`.|query|stringa|
|Authorization|(vuoto)  |Specifica il valore del token di connessione del client. Usare il prefisso `Bearer` seguito dal valore del valore `access_token` restituito dal servizio token di autenticazione.|intestazione   |stringa|
|Ocp-Apim-Subscription-Key|(vuoto)|Obbligatorio se l'intestazione `Authorization` non è specificata.|intestazione|stringa|
|access_token|(vuoto)   |Modo alternativo per passare un token di accesso OAuth valido. Il token di connessione viene in genere fornito con l'intestazione `Authorization`. Alcune librerie WebSocket non consentono al codice client di impostare le intestazioni. In tal caso, il client può usare il parametro di query `access_token` per passare un token valido. Quando si usa un token di accesso per l'autenticazione, se l'intestazione `Authorization` non è impostata, è necessario impostare `access_token`. Se vengono impostati sia l'intestazione che il parametro di query, il parametro di query viene ignorato. I client devono usare solo un metodo per passare il token.|query|stringa|
|subscription-key|(vuoto)   |Modo alternativo per passare la chiave di sottoscrizione. Alcune librerie WebSocket non consentono al codice client di impostare le intestazioni. In tal caso, il client può usare il parametro di query `subscription-key` per passare una chiave di sottoscrizione valida. Quando si usa una chiave di sottoscrizione per l'autenticazione, se l'intestazione `Ocp-Apim-Subscription-Key` non è impostata, è necessario impostare subscription-key. Se vengono impostati sia l'intestazione che il parametro di query, il parametro di query viene ignorato. I client devono usare solo un metodo per passare `subscription key`.|query|stringa|
|X-ClientTraceId    |(vuoto)    |GUID generato dal client per tenere traccia di una richiesta. Per la corretta risoluzione dei problemi, i client devono fornire un nuovo valore con ogni richiesta e registrarlo.<br/>Invece di usare un'intestazione, questo valore può essere passato con il parametro di query `X-ClientTraceId`. Se vengono impostati sia l'intestazione che il parametro di query, il parametro di query viene ignorato.|intestazione|stringa|
|X-CorrelationId|(vuoto)    |Identificatore generato dal client usato per correlare più canali in una conversazione. È possibile creare più sessioni di traduzione vocale per abilitare le conversazioni tra gli utenti. In questo scenario, tutte le sessioni di traduzione vocale usano lo stesso ID di correlazione per collegare i canali. Ciò facilita l'analisi e la diagnostica. L'identificatore deve essere conforme a: `^[a-zA-Z0-9-_.]{1,64}$`<br/>Invece di usare un'intestazione, questo valore può essere passato con il parametro di query `X-CorrelationId`. Se vengono impostati sia l'intestazione che il parametro di query, il parametro di query viene ignorato.|intestazione|stringa|
|X-ClientVersion|(vuoto)    |Identifica la versione dell'applicazione client. Esempio: "2.1.0.123".<br/>Invece di usare un'intestazione, questo valore può essere passato con il parametro di query `X-ClientVersion`. Se vengono impostati sia l'intestazione che il parametro di query, il parametro di query viene ignorato.|intestazione|stringa|
|X-OsPlatform|(vuoto)   |Identifica il nome e la versione del sistema operativo in cui l'applicazione client è in esecuzione. Esempi: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>Invece di usare un'intestazione, questo valore può essere passato con il parametro di query `X-OsPlatform`. Se vengono impostati sia l'intestazione che il parametro di query, il parametro di query viene ignorato.|intestazione|stringa|

### <a name="response-messages"></a>Messaggi di risposta

|Codice di stato HTTP|Motivo|Modello di risposta|Headers|
|:--|:--|:--|:--|
|101    |Aggiornamento WebSocket.|Valore di esempio del modello <br/> Oggetto {}|X-RequestId<br/>Valore che identifica la richiesta per la risoluzione dei problemi.<br/>stringa|
|400    |Richiesta non valida. Controllare i parametri di input per verificare che siano validi. L'oggetto della risposta include una descrizione più dettagliata dell'errore.|||
|401    |Non autorizzato. Assicurarsi che le credenziali siano impostate, che siano valide e che la sottoscrizione di Azure Data Market sia in regola con il saldo disponibile.|||
|500    |Si è verificato un errore. Se l'errore persiste, segnalarlo con l'identificatore di traccia client (X-ClientTraceId) o l'identificatore della richiesta (X-RequestId).|||
|503    |Il server è temporaneamente non disponibile. Si prega di ripetere la richiesta. Se l'errore persiste, segnalarlo con l'identificatore di traccia client (X-ClientTraceId) o l'identificatore della richiesta (X-RequestId).|||

    


    





    
    




    




    




    

            




        









