---
title: Note sulla versione-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Un log in esecuzione di versioni della funzionalità del servizio vocale, miglioramenti, correzioni di bug e problemi noti.
services: cognitive-services
author: oscholz
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 15a118dc72ae9ffc4b6c99b0e0b527a252d3ded8
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293590"
---
# <a name="release-notes"></a>Note sulla versione

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020-versione di gennaio

**Nuove funzionalità**

- Conversazione a più dispositivi: connettere più dispositivi alla stessa conversazione vocale o basata su testo e, facoltativamente, convertire i messaggi inviati tra di essi. Altre informazioni sono disponibili in [questo articolo](multi-device-conversation.md). 
- Aggiunta del supporto per il riconoscimento delle parole chiave per il pacchetto Android. Aar e aggiunta del supporto per le versioni x86 e x64. 
- Objective-C: `SendMessage` e `SetMessageProperty` metodi aggiunti all'oggetto `Connection`. Vedere la documentazione [qui](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- L' C++ API TTS ora supporta `std::wstring` come input di testo di sintesi, eliminando la necessità di convertire una wstring in una stringa prima di passarla all'SDK. Vedere i dettagli [qui](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: L' [ID lingua](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) e la [configurazione della lingua di origine](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) sono ora disponibili.
- JavaScript: è stata aggiunta una funzionalità per `Connection` oggetto per passare i messaggi personalizzati dal servizio di riconoscimento vocale come `receivedServiceMessage`di callback.
- JavaScript: è stato aggiunto il supporto per `FromHost API` per semplificare l'uso con i contenitori locali e i cloud sovrani. Vedere la documentazione [qui](speech-container-howto.md).
- JavaScript: ora è possibile rispettare `NODE_TLS_REJECT_UNAUTHORIZED` grazie a un contributo di [orgads](https://github.com/orgads). Vedere i dettagli [qui](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).


**Modifiche di rilievo**

- `OpenSSL` è stato aggiornato alla versione 1.1.1 b ed è collegato in modo statico alla libreria principale di Speech SDK per Linux. Questa operazione può causare un'interrotta se la posta in arrivo `OpenSSL` non è stata installata nella directory `/usr/lib/ssl` del sistema. Per risolvere il problema, vedere la [documentazione relativa](how-to-configure-openssl-linux.md) ai documenti dell'SDK per la sintesi vocale.
- È stato modificato il tipo di dati restituito C# per `WordLevelTimingResult.Offset` da `int` a `long` per consentire l'accesso a `WordLevelTimingResults` quando i dati vocali sono più lunghi di 2 minuti.
- `PushAudioInputStream` e `PullAudioInputStream` ora inviano informazioni di intestazione WAV al servizio di riconoscimento vocale basato su `AudioStreamFormat`, facoltativamente specificate al momento della creazione. I clienti devono ora usare il [formato di input audio supportato](how-to-use-audio-input-streams.md). Eventuali altri formati otterranno risultati di riconoscimento non ottimali o potrebbero causare altri problemi. 


**Correzioni di bug**

- Vedere il `OpenSSL` aggiornamento in modifiche di rilievo precedenti. Sono stati corretti un arresto intermittente e un problema di prestazioni (conflitti di blocco sotto carico elevato) in Linux e Java. 
- Java: miglioramenti apportati alla chiusura degli oggetti in scenari di concorrenza elevata.
- Il pacchetto NuGet è stato ristrutturato. Sono state rimosse le tre copie di `Microsoft.CognitiveServices.Speech.core.dll` e `Microsoft.CognitiveServices.Speech.extension.kws.dll` in cartelle lib, rendendo il pacchetto NuGet più piccolo e veloce da scaricare e abbiamo aggiunto le intestazioni necessarie C++ per compilare alcune app native.
- Correzione degli esempi di avvio rapido [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Si è verificata l'uscita senza visualizzare l'eccezione "microfono non trovato" in Linux, MacOS e Windows.
- Correzione di un arresto anomalo dell'SDK con i risultati del riconoscimento vocale lungo su determinati percorsi di codice come [questo esempio](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Correzione dell'errore di distribuzione dell'SDK nell'ambiente app Web di Azure per risolvere [questo problema del cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Correzione di un errore TTS durante l'utilizzo del tag multi`<voice>` o del tag `<audio>` per risolvere [questo problema del cliente](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Correzione di un errore TTS 401 quando l'SDK viene recuperato dalla sospensione.
- JavaScript: è stata corretta un'importazione circolare di dati audio grazie a un contributo di [euirim](https://github.com/euirim). 
- JavaScript: è stato aggiunto il supporto per l'impostazione delle proprietà del servizio, come aggiunto in 1,7.
- JavaScript: è stato risolto un problema per cui un errore di connessione poteva causare tentativi di riconnessione WebSocket continui e non riusciti.


**Esempi**

- Aggiunta dell'esempio di riconoscimento delle parole chiave [per Android.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)
- Aggiunto esempio TTS per lo scenario server [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Sono state aggiunte guide introduttive per la C# conversazione C++ per più dispositivi per e [qui](quickstarts/multi-device-conversation.md).


**Altre modifiche**

- Dimensioni della libreria principale ottimizzate SDK in Android.
- SDK in 1.9.0 e versioni successive supporta sia `int` che i tipi di `string` nel campo della versione della firma vocale per il trascrittore delle conversazioni.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0:2019-versione di novembre

**Nuove funzionalità**

- È stata aggiunta un'API di `FromHost()` per semplificare l'uso con i contenitori locali e i cloud sovrani.
- Aggiunta Rilevamento lingua di origine automatica per il riconoscimento vocale (in C++Java e)
- Aggiunta `SourceLanguageConfig` oggetto per il riconoscimento vocale, utilizzato per specificare le lingue di origine previste ( C++in Java e)
- Aggiunta del supporto `KeywordRecognizer` in Windows (UWP), Android e iOS tramite i pacchetti NuGet e Unity
- Aggiunta dell'API Java per la conversazione remota per eseguire la trascrizione in batch asincroni.

**Modifiche di rilievo**

- Funzionalità del trascrittore di conversazione spostate sotto lo spazio dei nomi `Microsoft.CognitiveServices.Speech.Transcription`.
- Parte dei metodi del trascrittore di conversazione viene spostata in una nuova classe `Conversation`.
- Supporto eliminato per iOS a 32 bit (ARMv7 e x86)

**Correzioni di bug**

- Correzione dell'arresto anomalo se viene utilizzata la `KeywordRecognizer` locale senza una chiave di sottoscrizione del servizio vocale valida

**Esempi**

- Esempio di Novell per `KeywordRecognizer`
- Esempio di Unity per `KeywordRecognizer`
- C++e esempi Java per l'origine automatica Rilevamento lingua.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0:2019-versione di settembre

**Nuove funzionalità**

- Aggiunta del supporto beta per Novell in piattaforma UWP (Universal Windows Platform) (UWP), Android e iOS
- Aggiunta del supporto iOS per Unity
- Aggiunta del supporto di `Compressed` input per alegge, mulaw, FLAC in Android, iOS e Linux
- Aggiunta `SendMessageAsync` nella classe `Connection` per l'invio di un messaggio al servizio
- Aggiunta `SetMessageProperty` nella classe `Connection` per l'impostazione della proprietà di un messaggio
- Binding aggiunto TTS per Java (JRE e Android), Python, Swift e Objective-C
- TTS aggiunto supporto per la riproduzione per macOS, iOS e Android.
- Sono state aggiunte informazioni "confine di parola" per TTS.

**Correzioni di bug**

- Correzione del problema di compilazione di IL2CPP in Unity 2019 per Android
- Correzione del problema relativo all'elaborazione non corretta delle intestazioni in formato non corretto nell'input del file WAV
- Correzione di un problema con UUID non univoco in alcune proprietà di connessione
- Correzione di alcuni avvisi sugli identificatori di supporto di valori null nelle associazioni Swift (potrebbe richiedere modifiche minime al codice)
- Correzione di un bug che causava la chiusura non corretta delle connessioni WebSocket in un carico di rete
- È stato risolto un problema in Android che a volte produce ID di impressione duplicati usati da `DialogServiceConnector`
- Miglioramenti alla stabilità delle connessioni tra interazioni a più turni e segnalazione di errori (tramite `Canceled` eventi) quando si verificano con `DialogServiceConnector`
- `DialogServiceConnector` sessione inizia ora fornisce correttamente gli eventi, ad esempio quando viene chiamata `ListenOnceAsync()` durante un `StartKeywordRecognitionAsync()` attivo
- Risolto un arresto anomalo associato alle attività `DialogServiceConnector` ricevute

**Esempi**

- Guida introduttiva per Novell
- Guida introduttiva di CPP aggiornata con Linux ARM64 informazioni
- Aggiornamento rapido di Unity con informazioni iOS

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0:2019-versione di giugno

**Esempi**

- Esempi di avvio rapido per sintesi vocale in UWP e Unity
- Esempio di Guida introduttiva per SWIFT in iOS
- Esempi di Unity per la & vocale Riconoscimento finalità e traduzione
- Esempi di avvio rapido aggiornati per `DialogServiceConnector`

**Miglioramenti/modifiche**

- Spazio dei nomi finestra di dialogo:
  - `SpeechBotConnector` è stata rinominata `DialogServiceConnector`
  - `BotConfig` è stata rinominata `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` è stato nuovamente mappato a `DialogServiceConfig::FromBotSecret()`
  - Tutti i client di sintesi vocale diretta esistenti continuano a essere supportati dopo la ridenominazione
- Aggiornare l'adapter REST TTS per supportare il proxy, la connessione permanente
- Miglioramento del messaggio di errore quando viene passata un'area non valida
- Swift/Objective-C:
  - Segnalazione errori migliorata: i metodi che possono generare un errore sono ora presenti in due versioni: una che espone un oggetto `NSError` per la gestione degli errori e una che genera un'eccezione. Il primo è esposto a Swift. Questa modifica richiede adattamenti al codice Swift esistente.
  - Gestione degli eventi migliorata

**Correzioni di bug**

- Correzione per TTS: dove `SpeakTextAsync` future restituite senza attendere il completamento del rendering dell'audio
- Correzione per il marshalling delle C# stringhe in per abilitare il supporto completo del linguaggio
- Correzione del problema dell'app .NET Core per caricare la libreria principale con il Framework di destinazione net461 negli esempi
- Correzione di problemi occasionali per la distribuzione di librerie native nella cartella di output in esempi
- Correzione per la chiusura di Web socket in modo affidabile
- Correzione di un possibile arresto anomalo durante l'apertura di una connessione con carico molto elevato in Linux
- Correzione per i metadati mancanti nel bundle del Framework per macOS
- Correzione di problemi con `pip install --user` in Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

**Correzioni di bug**

- Correggere FromSubscription consente quando viene usato con la trascrizione delle conversazioni.
- Correzione di un bug nell'individuazione di parole chiave per gli assistenti vocali.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019-versione di maggio

**Nuove funzionalità**

- L'individuazione delle parole chiave (KWS) è ora disponibile per Windows e Linux. La funzionalità KWS potrebbe funzionare con qualsiasi tipo di microfono, il supporto ufficiale di KWS, tuttavia, è attualmente limitato agli array di microfoni presenti nell'hardware Kinect DK di Azure o nell'SDK per i dispositivi vocali.
- La funzionalità hint per la frase è disponibile tramite l'SDK. Per altre informazioni, vedere [qui](how-to-phrase-lists.md).
- La funzionalità di trascrizione delle conversazioni è disponibile tramite l'SDK. Vedere [qui](conversation-transcription-service.md).
- Aggiunta del supporto per gli assistenti vocali tramite il canale di riconoscimento vocale diretto.

**Esempi**

- Sono stati aggiunti esempi per le nuove funzionalità o i nuovi servizi supportati dall'SDK.

**Miglioramenti/modifiche**

- Sono state aggiunte diverse proprietà di riconoscimento per modificare il comportamento del servizio o i risultati del servizio (ad esempio la maschera di volgarità e altri).
- È ora possibile configurare il riconoscimento tramite le proprietà di configurazione standard, anche se è stato creato il riconoscitore `FromEndpoint`.
- Objective-C: `OutputFormat` proprietà è stata aggiunta al `SPXSpeechConfiguration`.
- SDK supporta ora Debian 9 come distribuzione Linux.

**Correzioni di bug**

- Correzione di un problema a causa del quale la risorsa Speaker è stata distrutta troppo presto in sintesi vocale.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

- Impedisci al Web Pack di caricare HTTPS-proxy-Agent.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0:2019-versione di aprile

**Nuove funzionalità**

- L'SDK supporta ora il servizio di sintesi vocale come versione beta. È supportato nel desktop Windows e Linux da C++ e. C# Per altre informazioni, vedere [Cenni preliminari](text-to-speech.md#get-started)sulla sintesi vocale.
- L'SDK supporta ora i file audio MP3 e Opus/OGG come file di input di flusso. Questa funzionalità è disponibile solo in Linux da C++ e C# ed è attualmente in versione beta (altri dettagli [qui](how-to-use-codec-compressed-audio-input-streams.md)).
- L'SDK di riconoscimento vocale per Java, .NET C++ core e Objective-C ha acquisito supporto MacOS. Il supporto Objective-C per macOS è attualmente in versione beta.
- iOS: l'SDK di riconoscimento vocale per iOS (Objective-C) è ora pubblicato anche come CocoaPod.
- JavaScript: supporto per microfoni non predefiniti come dispositivo di input.
- JavaScript: supporto del proxy per node. js.

**Esempi**

- Sono stati aggiunti esempi per l'uso C++ dell'SDK di riconoscimento vocale con e con Objective-C in MacOS.
- Sono stati aggiunti esempi che dimostrano l'uso del servizio di sintesi vocale.

**Miglioramenti/modifiche**

- Python: le proprietà aggiuntive dei risultati del riconoscimento sono ora esposte tramite la proprietà `properties`.
- Per un ulteriore supporto per lo sviluppo e il debug, è possibile reindirizzare le informazioni di diagnostica e registrazione SDK in un file di log. per ulteriori informazioni, vedere [qui](how-to-use-logging.md).
- JavaScript: migliorare le prestazioni di elaborazione audio.

**Correzioni di bug**

- Mac/iOS: un bug che ha provocato un lungo periodo di attesa quando non è stato possibile stabilire una connessione al servizio di riconoscimento vocale è stato risolto.
- Python: migliorare la gestione degli errori per gli argomenti nei callback Python.
- JavaScript: correzione della creazione di report sullo stato errato per la voce terminata in RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019-aggiornamento di febbraio

Si tratta di una versione di correzione di bug che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

**Correzione di bug**

- Correzione di una perdita di memoria quando si utilizza l'input del microfono. L'input basato sul flusso o sul file non è interessato.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0:2019-versione di febbraio

**Nuove funzionalità**

- Speech SDK supporta la selezione del microfono di input tramite la classe `AudioConfig`. In questo modo è possibile trasmettere i dati audio al servizio riconoscimento vocale da un microfono non predefinito. Per ulteriori informazioni, vedere la documentazione che descrive la [selezione del dispositivo di input audio](how-to-select-audio-input-devices.md). Questa funzionalità non è ancora disponibile in JavaScript.
- Speech SDK supporta ora Unity in versione beta. Inviare commenti e suggerimenti tramite la sezione problema nel [repository di esempio GitHub](https://aka.ms/csspeech/samples). Questa versione supporta Unity in Windows x86 e x64 (applicazioni desktop o per la piattaforma UWP) e Android (ARM32/64, x86). Altre informazioni sono disponibili nell'[Avvio rapido di Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- Il file `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (fornito nelle versioni precedenti) non è più necessario. La funzionalità è ora integrata nell'SDK di base.

**Esempi**

I nuovi contenuti seguenti sono disponibili nel [repository di esempio](https://aka.ms/csspeech/samples):

- Esempi aggiuntivi per `AudioConfig.FromMicrophoneInput`.
- Altri esempi di Python per il riconoscimento finalità e la traduzione.
- Esempi aggiuntivi per l'uso dell'oggetto `Connection` in iOS.
- Esempi aggiuntivi per Java per la traduzione con output audio.
- Nuovo esempio per l'uso dell'[API REST di trascrizione batch](batch-transcription.md).

**Miglioramenti/modifiche**

- Python
  - Miglioramento della verifica dei parametri e dei messaggi di errore in `SpeechConfig`.
  - Aggiunta del supporto per l'oggetto `Connection`.
  - Supporto per Python a 32 bit (x86) in Windows.
  - Speech SDK per Python non è incluso nella beta.
- iOS
  - L'SDK è ora basato sull'SDK per iOS versione 12.1.
  - L'SDK supporta ora iOS 9.2 e versioni successive.
  - Miglioramento della documentazione di riferimento e correzione di diversi nomi di proprietà.
- JavaScript
  - Aggiunta del supporto per l'oggetto `Connection`.
  - Aggiunta di file di definizione del tipo per JavaScript in bundle.
  - Supporto iniziale e implementazione per gli hint della frase.
  - Restituzione della raccolta di proprietà con il file JSON del servizio per il riconoscimento.
- Le DLL di Windows contengono ora una risorsa di versione.
- Se si crea un riconoscitore `FromEndpoint` è possibile aggiungere parametri direttamente all'URL dell'endpoint. Utilizzando `FromEndpoint` non è possibile configurare il riconoscimento tramite le proprietà di configurazione standard.

**Correzioni di bug**

- Il nome utente proxy e la password proxy vuoti non erano gestiti correttamente. Con questa versione, se si imposta il nome utente proxy e la password proxy su una stringa vuota, non verranno inviati quando ci si connette al proxy.
- Gli ID sessione creati dall'SDK non erano sempre realmente casuali per alcune lingue&nbsp;/ ambienti. Aggiunta dell'inizializzazione del generatore casuale per risolvere il problema.
- Gestione migliorata del token di autorizzazione. Se si desidera utilizzare un token di autorizzazione, specificare nella `SpeechConfig` e lasciare vuota la chiave della sottoscrizione. Creare quindi il sistema di riconoscimento come di consueto.
- In alcuni casi l'oggetto `Connection` non è stato rilasciato correttamente. Il problema è stato risolto.
- L'esempio di JavaScript è stato corretto per supportare l'output audio per la sintesi della traduzione anche in Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

- Attivazione della fine del flusso in corrispondenza di turn.end e non di speech.end.
- Correzione di un bug nel data pump audio che non pianificava l'invio successivo in caso di errore dell'invio corrente.
- Correzione del riconoscimento continuo con il token di autenticazione.
- Correzione di bug per sistemi di riconoscimento/endpoint diversi.
- Miglioramenti alla documentazione.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018-versione di dicembre

**Nuove funzionalità**

- Python
  - La versione beta del supporto di Python (versione 3.5 e successive) è disponibile con questa versione. Per ulteriori informazioni, vedere qui] (avvio rapido-python.md).
- JavaScript
  - Speech SDK per JavaScript è ora open source. Il codice sorgente è disponibile in [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - È ora supportato Node.js. Altre informazioni sono reperibili [qui](quickstart-js-node.md).
  - È stata rimossa la restrizione di lunghezza per le sessioni audio. La riconnessione avverrà automaticamente.
- Oggetto `Connection`
  - Dal `Recognizer`è possibile accedere a un oggetto `Connection`. Questo oggetto consente di avviare in modo esplicito la connessione al servizio e di aggiungere una sottoscrizione per connettere e disconnettere gli eventi.
    Questa funzionalità non è ancora disponibile da JavaScript e Python.
- Supporto per Ubuntu 18.04.
- Android
  - Supporto di ProGuard abilitato durante la generazione di APK.

**Miglioramenti**

- Miglioramenti nell'uso dei thread interni, con riduzione del numero di thread, blocchi e mutex.
- Miglioramento della segnalazione errori e delle informazioni sugli errori. In molti casi, i messaggi di errore non sono stati propagati fino a questo punto.
- Aggiornamento delle dipendenze di sviluppo in JavaScript per usare i moduli aggiornati.

**Correzioni di bug**

- Perdite di memoria fisse dovute a un tipo non corrispondente in `RecognizeAsync`.
- In alcuni casi perdita delle eccezioni.
- Correzione della perdita di memoria negli argomenti degli eventi di conversione.
- Correzione di un problema di blocco per la riconnessione in sessioni a esecuzione prolungata.
- Correzione di un problema che potrebbe causare la mancata riuscita delle traduzioni non riuscite.
- C#: Se un'operazione di `async` non è stata attesa nel thread principale, è possibile che il riconoscimento possa essere eliminato prima del completamento dell'attività asincrona.
- Java: è stato risolto un problema che causava un arresto anomalo della macchina virtuale Java.
- Objective-C: mapping di enumerazione fisso; RecognizedIntent è stato restituito anziché `RecognizingIntent`.
- JavaScript: impostare il formato di output predefinito su' Simple ' in `SpeechConfig`.
- JavaScript: rimozione dell'incoerenza tra le proprietà nell'oggetto config in JavaScript e in altri linguaggi.

**Esempi**

- Sono stati aggiornati e corretti diversi esempi, ad esempio le voci di output per la traduzione e così via.
- Aggiunta di esempi di Node.js nel [repository degli esempi](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nuove funzionalità**

- Supporto per Android x86/x64.
- Supporto del proxy: nell'oggetto `SpeechConfig` è ora possibile chiamare una funzione per impostare le informazioni sul proxy (nome host, porta, nome utente e password). Questa funzionalità non è ancora disponibile in iOS.
- Messaggi e codice di errore migliorati. Se un riconoscimento ha restituito un errore, l'elemento `Reason` (nell'evento annullato) o `CancellationDetails` (nel risultato del riconoscimento) è già stato impostato su `Error`. L'evento annullato contiene ora due membri aggiuntivi: `ErrorCode` e `ErrorDetails`. Se il server ha restituito l'errore corredato da informazioni aggiuntive, le informazioni saranno ora disponibili nei nuovi membri.

**Miglioramenti**

- È stata aggiunta una verifica supplementare nella configurazione del sistema di riconoscimento ed è stato aggiunto un nuovo messaggio di errore.
- È stata migliorata la gestione di un silenzio prolungato nel mezzo di un file audio.
- Pacchetto NuGet: per i progetti .NET Framework, impedisce la compilazione con una configurazione AnyCPU.

**Correzioni di bug**

- Sono state corrette varie eccezioni rilevate nei sistemi di riconoscimento. Inoltre, le eccezioni vengono rilevate e convertite in `Canceled` evento.
- È stata corretta una perdita di memoria nella gestione delle proprietà.
- È stato corretto un bug per il quale un file di input audio può determinare l'arresto anomalo del sistema di riconoscimento.
- È stato risolto un bug per il quale si continuano a ricevere eventi anche dopo un evento di arresto della sessione.
- Sono state corrette alcune race condition nel threading.
- È stato corretto un problema di compatibilità iOS che può determinare un arresto anomalo.
- Sono stati apportati miglioramenti di stabilità per il supporto di microfoni Android.
- È stato risolto un bug per il quale un sistema di riconoscimento in JavaScript ignorava la lingua di riconoscimento.
- Correzione di un bug che impedisce l'impostazione del `EndpointId` (in alcuni casi) in JavaScript.
- Modifica dell'ordine del parametro in AddIntent in JavaScript e aggiunta della firma `AddIntent` JavaScript mancante.

**Esempi**

- Aggiunta C++ di C# ed esempi per l'utilizzo del flusso pull e push nel [repository di esempio](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Miglioramenti apportati alla stabilità e correzioni di bug:

- Risoluzione di un potenziale errore irreversibile dovuto a una race condition nell'eliminazione dello strumento di riconoscimento.
- Risoluzione di un potenziale errore irreversibile in caso di proprietà non impostate.
- Aggiunta di un ulteriore controllo di errori e parametri.
- Objective-C: risoluzione di un potenziale errore irreversibile causato dall'override del nome in NSString.
- Objective-C: modifica della visibilità dell'API
- JavaScript: correzioni relative a eventi e payload correlati.
- Miglioramenti alla documentazione.

Al [repository di esempi](https://aka.ms/csspeech/samples) è stato aggiunto un nuovo esempio per JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Speech SDK 1.0.0 di Servizi cognitivi: versione di settembre 2018

**Nuove funzionalità**

- Supporto per Objective-C in iOS. Vedere la [Guida introduttiva su Objective-C per iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Supporto per JavaScript nel browser. Vedere la [Guida introduttiva su JavaScript](quickstart-js-browser.md).

**Modifiche di rilievo**

- In questa versione sono state introdotte alcune modifiche di rilievo.
  Per informazioni dettagliate, vedere [Questa pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Speech SDK 0.6.0 di Servizi cognitivi: versione di agosto 2018

**Nuove funzionalità**

- Le app UWP compilate con Speech SDK ora possono superare il Kit di certificazione app Windows (WACK).
  Consultare la [Guida introduttiva della piattaforma UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Supporto per .NET Standard 2.0 in Linux (Ubuntu 16.04 x64).
- Sperimentale: supporto di Java 8 in Windows (64 bit) e Linux (Ubuntu 16.04 x64).
  Consultare la [Guida introduttiva di Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Modifica funzionale**

- Informazioni aggiuntive dettagliate sull'errore in caso di errori di connessione.

**Modifiche di rilievo**

- In Java (Android), la funzione `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` non richiede più un parametro di percorso. Il percorso viene ora rilevato automaticamente in tutte le piattaforme supportate.
- La funzione di accesso get della proprietà `EndpointUrl` in Java e C# è stata rimossa.

**Correzioni di bug**

- In Java, il risultato di sintesi audio sul sistema di riconoscimento di traduzione è ora implementato.
- È stato risolto un bug che potrebbe causare un maggior numero di socket aperti e inutilizzati e thread inattivi.
- È stato risolto un problema in cui un riconoscimento con esecuzione prolungata terminava la trasmissione a metà.
- Correzione di una race condition nel sistema di riconoscimento di arresto.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Speech SDK 0.5.0 di Servizi cognitivi: versione di luglio 2018

**Nuove funzionalità**

- Supporto della piattaforma Android (API 23: Android Marshmallow 6.0 o versione successiva). Consultare la [Guida introduttiva di Android](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android).
- Supporto di .NET Standard 2.0 in Windows. Consultare la [Guida introduttiva di .NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).
- Sperimentale: supporto di UWP in Windows (versione 1709 o successiva).
  - Consultare la [Guida introduttiva della piattaforma UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Nota: le app UWP compilate con Speech SDK non superano ancora il Kit di certificazione app Windows (WACK).
- Supporto del riconoscimento a esecuzione prolungata con riconnessione automatica.

**Modifiche funzionali**

- `StartContinuousRecognitionAsync()` supporta il riconoscimento a esecuzione prolungata.
- Il risultato del riconoscimento contiene più campi. Scostamento da inizio audio e durata (entrambi in tick) del testo riconosciuto, valori aggiuntivi che rappresentano lo stato di riconoscimento, ad esempio `InitialSilenceTimeout` e `InitialBabbleTimeout`.
- Supporto del token di autorizzazione per la creazione di istanze di factory.

**Modifiche di rilievo**

- Eventi di riconoscimento: `NoMatch` tipo di evento è stato unito nell'evento `Error`.
- SpeechOutputFormat in C# è stato rinominato in `OutputFormat` per restare allineato con C++.
- Il tipo restituito di alcuni metodi dell'interfaccia `AudioInputStream` è stato leggermente modificato:
  - In Java, il metodo `read` restituisce ora `long` invece di `int`.
  - In C#, il metodo `Read` restituisce ora `uint` invece di `int`.
  - In C++, i metodi `Read` e `GetFormat` restituiscono ora `size_t` invece di `int`.
- C++: le istanze di flussi di input audio possono ora essere passate solo come `shared_ptr`.

**Correzioni di bug**

- Sono stati corretti i valori restituiti errati nel risultato alla scadenza di `RecognizeAsync()`.
- È stata rimossa la dipendenza dalle librerie di Media Foundation in Windows. L'SDK usa ora le API Audio Core.
- Correzione della documentazione: è stata aggiunta una pagina relativa alle [aree](regions.md) per descrivere le aree supportate.

**Problema noto**

- Speech SDK per Android non segnala i risultati della sintesi vocale per la traduzione. Questo problema verrà risolto nella prossima versione.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Speech SDK di Servizi cognitivi 0.4.0: versione di giugno 2018

**Modifiche funzionali**

- AudioInputStream

  Uno strumento di riconoscimento può ora usare un flusso come origine audio. Per informazioni dettagliate, vedere la [guida pratica](how-to-use-audio-input-streams.md).

- Formato dettagliato dell'output

  Durante la creazione di uno `SpeechRecognizer`, è possibile richiedere un formato di output `Detailed` o `Simple`. Il `DetailedSpeechRecognitionResult` contiene punteggio di attendibilità, testo riconosciuto, forma lessicale non elaborata, forma normalizzata e forma normalizzata con messaggi dal contenuto volgare mascherati.

**Modifica di rilievo**

- Modifica da `SpeechRecognitionResult.RecognizedText` a `SpeechRecognitionResult.Text` in linguaggio C#.

**Correzioni di bug**

- È stato corretto un possibile problema di callback nel livello USP durante l'arresto.
- Se un riconoscimento usa un file di input audio, significa che esso contiene l'handle del file più a lungo rispetto al necessario.
- Sono stati rimossi diversi deadlock tra message pump e strumento di riconoscimento.
- Attiva un risultato `NoMatch` quando la risposta dal servizio è scaduta.
- Le librerie di Media Foundation in Windows sono a caricamento ritardato. Questa libreria è richiesta solo per l'input del microfono.
- La velocità di caricamento dei dati audio è limitata a circa due volte la velocità dell'audio originale.
- In Windows, gli assembly C# .NET hanno ora un nome sicuro.
- Correzione della documentazione: `Region` è un'informazione obbligatoria per la creazione di un riconoscimento.

Sono stati aggiunti altri esempi che sono costantemente in corso l'aggiornamento. Per il set di esempi più recente, vedere il [repository GitHub degli esempi di Speech SDK](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Speech SDK di Servizi cognitivi 0.2.12733: versione di maggio 2018

Questa è la prima versione di anteprima pubblica di Speech SDK di Servizi cognitivi.
