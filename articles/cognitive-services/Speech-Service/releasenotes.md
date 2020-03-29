---
title: Note sulla versione - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Registro in esecuzione delle versioni delle funzionalità del servizio di riconoscimento vocale, miglioramenti, correzioni di bug e problemi noti.
services: cognitive-services
author: brianem
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: cbe9c7cbd0f402e38d1163050d77b055f89948ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219643"
---
# <a name="release-notes"></a>Note sulla versione
## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: versione 2020-febbraio

**Nuove funzionalità**
 - Aggiunti i pacchetti Python per supportare la nuova versione 3.8 di Python.
 - Supporto di Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 (C , C , Java, Python).
   > [!NOTE] 
   > I clienti devono configurare OpenSSL in base a [queste istruzioni.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)
 - Supporto Linux ARM32 per Debian e Ubuntu.
 - DialogServiceConnector now supports an optional "bot ID" parameter on BotFrameworkConfig. Questo parametro consente l'uso di più bot di riconoscimento vocale Direct Line con una singola risorsa vocale di Azure.This parameter allows the use of multiple Direct Line Speech bots with a single Azure speech resource. Senza il parametro specificato, verrà utilizzato il bot predefinito (come determinato dalla pagina di configurazione del canale Direct Line Speech).
 - DialogServiceConnector dispone ora di una proprietà SpeechActivityTemplate.DialogServiceConnector now has a SpeechActivityTemplate property. Il contenuto di questa stringa JSON verrà usato da Direct Line Speech per precompilare un'ampia gamma di campi supportati in tutte le attività che raggiungono un bot di riconoscimento vocale diretto, incluse le attività generate automaticamente in risposta a eventi come il riconoscimento vocale.
 - TTS ora utilizza la chiave di sottoscrizione per l'autenticazione, riducendo la latenza del primo byte del primo risultato di sintesi dopo la creazione di un sintetizzatore.
 - Modelli di riconoscimento vocale aggiornati per 19 impostazioni locali per una riduzione media del tasso di errore delle parole del 18,6% (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, TR). I nuovi modelli apportano miglioramenti significativi in più domini, tra cui Dettatura, trascrizione Call-Center e Scenari di indicizzazione video.

**Correzioni**
 - Risolto un bug in cui il trascrittore di conversazione non attendeva correttamente nelle API JAVAFixed bug where Conversation Transcriber did not wait properly in JAVA APIs 
 - Correzione dell'emulatore x86 Android per il problema di Xamarin [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Aggiungi mancante (Get Set)Metodi di proprietà su AudioConfig
 - Correggere un bug di registrazione informazioni in cui non è stato possibile arrestare audioDataStream quando la connessione non riesceFix a TTS bug where the audioDataStream could not be stopped when connection fails
 - L'utilizzo di un endpoint senza un'area causerebbe errori USP per il traduttore di conversazione
 - La generazione di ID nelle applicazioni Windows universali ora utilizza un algoritmo GUID opportunamente univoco; in precedenza e involontariamente predefinito per un'implementazione stubbed che spesso prodotto collisioni su grandi set di interazioni.
 
 **Esempi**
 - Esempio Unity per l'uso di Speech SDK con [il microfono Unity e lo streaming](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone) in modalità push

**Altre modifiche**
 - [OpenSSL configuration documentation updated for Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: versione 2020-gennaio

**Nuove funzionalità**

- Conversazione multi-dispositivo: connetti più dispositivi alla stessa conversazione vocale o basata su testo e, facoltativamente, traduci i messaggi inviati tra di loro. Ulteriori informazioni in [questo articolo](multi-device-conversation.md). 
- Aggiunto il supporto per il riconoscimento delle parole chiave per il pacchetto .aar Android e il supporto per i gusti x86 e x64. 
- Objective-C: `SendMessage` `SetMessageProperty` e metodi `Connection` aggiunti all'oggetto. Vedere la documentazione [qui](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- L'API TTS C `std::wstring` è ora supporta come input di testo di sintesi, eliminando la necessità di convertire un wstring in stringa prima di passarlo all'SDK. Vedere i dettagli [qui](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C:: [l'ID lingua](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) e la [configurazione della lingua di origine](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) sono ora disponibili.
- JavaScript: aggiunta una `Connection` funzionalità all'oggetto da passare attraverso `receivedServiceMessage`messaggi personalizzati dal servizio di riconoscimento vocale come callback .
- JavaScript: aggiunto `FromHost API` il supporto per facilitare l'uso con contenitori on-prem e cloud sovrani. Vedere la documentazione [qui](speech-container-howto.md).
- JavaScript: Ora `NODE_TLS_REJECT_UNAUTHORIZED` onoriamo grazie a un contributo di [orgads](https://github.com/orgads). Vedere i dettagli [qui](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).


**Modifiche che causano un'interruzione**

- `OpenSSL`è stato aggiornato alla versione 1.1.1b ed è collegato in modo statico alla libreria principale di Speech SDK per Linux. Ciò potrebbe causare un'interruzione se la posta in arrivo `OpenSSL` non è stata installata nella `/usr/lib/ssl` directory del sistema. Consulta [la nostra documentazione](how-to-configure-openssl-linux.md) in Documenti di Speech SDK per risolvere il problema.
- È stato modificato il tipo di `WordLevelTimingResult.Offset` `int` dati `long` restituito per `WordLevelTimingResults` il linguaggio C, da a per consentire l'accesso a quando i dati vocali sono più lunghi di 2 minuti.
- `PushAudioInputStream`e `PullAudioInputStream` ora invia le informazioni dell'intestazione wav al servizio di riconoscimento vocale in `AudioStreamFormat`base a , specificate facoltativamente al momento della creazione. I clienti devono ora utilizzare il formato di [input audio supportato.](how-to-use-audio-input-streams.md) Qualsiasi altro formato otterrà risultati di riconoscimento non ottimali o potrebbe causare altri problemi. 


**Correzioni**

- Vedere `OpenSSL` l'aggiornamento in Modifiche di rilievo sopra. Abbiamo risolto sia un arresto anomalo intermittente che un problema di prestazioni (contesa di blocco sotto carico elevato) in Linux e Java. 
- Java: sono stati apportati miglioramenti alla chiusura degli oggetti in scenari con concorrenza elevata.
- Ristrutturato il nostro pacchetto NuGet. Abbiamo rimosso le tre `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` copie di e sotto le cartelle lib, rendendo il pacchetto NuGet più piccolo e più veloce da scaricare, e abbiamo aggiunto le intestazioni necessarie per compilare alcune applicazioni native di C.
- Sono disponibili esempi di avvio rapido [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). Questi erano uscire senza visualizzare l'eccezione "microfono non trovato" su Linux, MacOS, Windows.
- Corretto l'arresto anomalo dell'SDK con lunghi risultati di riconoscimento vocale in determinati percorsi di codice, ad [esempio questo esempio.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Risolto l'errore di distribuzione dell'SDK nell'ambiente Azure Web App per risolvere [questo problema del cliente.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)
- Correzione di un errore di `<voice>` totS durante l'utilizzo di più tag o `<audio>` tag per risolvere il problema del [cliente.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433) 
- Correzione di un errore di risparmio posta in scadenza 401 quando l'SDK viene ripristinato dalla sospensione.
- JavaScript: Risolto un'importazione circolare di dati audio grazie a un contributo di [euirim](https://github.com/euirim). 
- JavaScript: aggiunto il supporto per l'impostazione delle proprietà del servizio, come aggiunto nella 1.7.
- JavaScript: risolto un problema a causa del quale un errore di connessione poteva causare tentativi di riconnessione websocket continui e non riusciti.


**Esempi**

- Aggiunto l'esempio di riconoscimento di parole chiave per Android [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Aggiunto esempio di totS per lo scenario server [qui](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Aggiunte le guide introduttive per le conversazioni multi-dispositivo per C' e C, [qui.](quickstarts/multi-device-conversation.md)


**Altre modifiche**

- Dimensioni della libreria di base SDK ottimizzate su Android.
- SDK in 1.9.0 e versioni `int` `string` successive supporta entrambi i tipi nel campo della versione della firma vocale per Conversation Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: versione 2019-novembre

**Nuove funzionalità**

- Aggiunta `FromHost()` un'API per facilitare l'uso con contenitori on-prem e cloud sovrani.
- Aggiunto il rilevamento automatico della lingua di origine per il riconoscimento vocale (in Java e in C
- Aggiunta `SourceLanguageConfig` dell'oggetto per il riconoscimento vocale, utilizzata per specificare i linguaggi di origine previsti (in Java e in C)
- Aggiunto `KeywordRecognizer` il supporto su Windows (UWP), Android e iOS attraverso i pacchetti NuGet e Unity
- Aggiunta l'API Java conversazione remota per eseguire la trascrizione delle conversazioni in batch asincroni.

**Modifiche che causano un'interruzione**

- Funzionalità del trascrittore di `Microsoft.CognitiveServices.Speech.Transcription`conversazione spostate in spazio dei nomi .
- Parte dei metodi trascrittori di `Conversation` conversazione vengono spostati in una nuova classe.
- Interruzione del supporto per iOS a 32 bit (ARMv7 e x86)

**Correzioni**

- Correzione dell'arresto `KeywordRecognizer` anomalo se viene utilizzato locale senza una chiave di sottoscrizione del servizio di riconoscimento vocale validaFix for crash if local is used without a valid Speech service subscription key

**Esempi**

- Xamarin campione per`KeywordRecognizer`
- Esempio Unity per`KeywordRecognizer`
- Esempi di codice E Java per il rilevamento automatico del linguaggio di origine.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: versione 2019-settembre

**Nuove funzionalità**

- Aggiunto il supporto beta per Xamarin su UWP (Universal Windows Platform), Android e iOS
- Aggiunto il supporto iOS per Unity
- Aggiunto `Compressed` il supporto di input per ALaw, Mulaw, FLAC su Android, iOS e Linux
- Aggiunto `SendMessageAsync` `Connection` in classe per l'invio di un messaggio al servizio
- Aggiunto `SetMessageProperty` `Connection` in classe per l'impostazione della proprietà di un messaggio
- TTS ha aggiunto associazioni per Java (Jre e Android), Python, Swift e Objective-C
- TTS ha aggiunto il supporto di riproduzione per macOS, iOS e Android.
- Aggiunte informazioni "confine di parola" per la funzionalità di registrazione t.A.

**Correzioni**

- Risolto il problema di compilazione IL2CPP in Unity 2019 per AndroidFixed IL2CPP build issue on Unity 2019 for Android
- Risolto un problema con le intestazioni non formate nell'input del file wav elaborate in modo non corretto
- Risolto un problema relativo al fatto che gli UUID non sono univoci in alcune proprietà di connessione
- Correzione di alcuni avvisi relativi agli identificatori di supporto dei valori Null nelle associazioni Swift (potrebbe richiedere piccole modifiche al codice)Fixed a few warnings about nullability specifiers in the Swift bindings (might require small code changes)
- Risolto un bug che causava la chiusura in modo ingerante delle connessioni websocket sotto il carico di rete
- Risolto un problema su Android che a volte genera ID impressioni duplicati utilizzati da`DialogServiceConnector`
- Miglioramenti alla stabilità delle connessioni attraverso interazioni a più `Canceled` turni e la segnalazione di errori (tramite eventi) quando si verificano con`DialogServiceConnector`
- `DialogServiceConnector`l'avvio della sessione ora `ListenOnceAsync()` fornisce correttamente gli eventi, anche quando si chiama durante un`StartKeywordRecognitionAsync()`
- Risolto un arresto `DialogServiceConnector` anomalo associato alle attività ricevute

**Esempi**

- Guida introduttiva per Xamarin
- Guida introduttiva CPP aggiornata con informazioni su Linux ARM64
- Guida introduttiva di Unity aggiornata con le informazioni iOS

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: versione 2019-giugno

**Esempi**

- Esempi introduttivi per la sintesi vocale su UWP e Unity
- Esempio di guida introduttiva per Swift su iOS
- Esempi di unità per il riconoscimento e la traduzione delle finalità di riconoscimento e traduzione delle & vocali
- Esempi di avvio rapido aggiornati per`DialogServiceConnector`

**Miglioramenti/modifiche**

- Spazio dei nomi della finestra di dialogo:
  - `SpeechBotConnector` è stata rinominata `DialogServiceConnector`
  - `BotConfig` è stata rinominata `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()`è stato rimappato a`DialogServiceConfig::FromBotSecret()`
  - Tutti i client Direct Line Speech esistenti continuano a essere supportati dopo la ridenominazione
- Aggiornare l'adattatore REST di servizi di sicurezza per supportare la connessione proxy e permanenteUpdate TTS REST adapter to support proxy, persistent connection
- Migliorare il messaggio di errore quando viene passata un'area non validaImprove error message when an invalid region is passed
- Swift/Obiettivo-C:
  - Segnalazione errori migliorata: i metodi che possono generare un errore `NSError` sono ora presenti in due versioni: uno che espone un oggetto per la gestione degli errori e uno che genera un'eccezione. I primi sono esposti a Swift. Questa modifica richiede adattamenti al codice Swift esistente.
  - Gestione degli eventi migliorata

**Correzioni**

- Correzione per la ttS: dove `SpeakTextAsync` future è tornato senza attendere il completamento del rendering dell'audio
- Correzione del marshalling delle stringhe in C , per abilitare il supporto completo del linguaggioFix for marshaling strings in C's to enable full language support
- Risolvere il problema dell'app principale .NET per caricare la libreria principale con il framework di destinazione net461 negli esempiFix for .NET core app problem to load core library with net461 target framework in samples
- Correzione di problemi occasionali relativi alla distribuzione di librerie native nella cartella di output negli esempiFix for occasional issues to deploy native libraries to the output folder in samples
- Correzione per la chiusura affidabile delle prese web
- Correzione del possibile arresto anomalo durante l'apertura di una connessione con carico molto pesante su Linux
- Correzione dei metadati mancanti nel bundle del framework per macOSFix for missing metadata in the framework bundle for macOS
- Correzione dei `pip install --user` problemi relativi a Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Si tratta di una versione di correzione di bug e che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

**Correzioni**

- Correggere FromSubscription quando viene usato con la trascrizione conversazione.
- Correggere il bug nell'spoting delle parole chiave per gli assistenti vocali.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: versione 2019-maggio

**Nuove funzionalità**

- Lo spotting delle parole chiave (KWS) è ora disponibile per Windows e Linux. La funzionalità KWS potrebbe funzionare con qualsiasi tipo di microfono, il supporto ufficiale KWS, tuttavia, è attualmente limitato agli array di microfoni presenti nell'hardware Azure Kinect DK o nell'SDK dei dispositivi di riconoscimento vocale.
- La funzionalità di suggerimento frase è disponibile tramite l'SDK. Per altre informazioni, vedere [qui](how-to-phrase-lists.md).
- La funzionalità di trascrizione delle conversazioni è disponibile tramite l'SDK. Vedi [qui](conversation-transcription-service.md).
- Aggiungi il supporto per gli assistenti vocali utilizzando il canale Direct Line Speech.

**Esempi**

- Sono stati aggiunti esempi per nuove funzionalità o nuovi servizi supportati dall'SDK.

**Miglioramenti/modifiche**

- Aggiunte varie proprietà di riconoscimento per regolare il comportamento del servizio o i risultati del servizio (ad esempio volgarità mascheranti e altri).
- È ora possibile configurare il sistema di riconoscimento tramite `FromEndpoint`le proprietà di configurazione standard, anche se è stato creato il sistema di riconoscimento .
- Objective-C: `OutputFormat` la proprietà `SPXSpeechConfiguration`è stata aggiunta a .
- L'SDK ora supporta Debian 9 come distribuzione Linux.

**Correzioni**

- Risolto un problema a causa del quale la risorsa dell'oratore veniva distrutta troppo presto nella sintesi vocale.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Si tratta di una versione di correzione di bug e che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

- Impedire al web pack di caricare https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: versione 2019-aprile

**Nuove funzionalità**

- L'SDK supporta ora il servizio di sintesi vocale come versione beta. È supportato su Windows e Linux Desktop da C e C. Per ulteriori informazioni, consultare la [sintesi vocale](text-to-speech.md#get-started).
- L'SDK ora supporta i file audio MP3 e Opus/OGG come file di input di flusso. Questa funzionalità è disponibile solo su Linux a partire da C e c'è attualmente in versione beta (ulteriori dettagli [qui](how-to-use-codec-compressed-audio-input-streams.md)).
- L'SDK di riconoscimento vocale per Java, .NET core, C, e Objective-C ha ottenuto il supporto di macOS. Il supporto Objective-C per macOS è attualmente in versione beta.
- iOS: Speech SDK per iOS (Objective-C) è ora pubblicato anche come CocoaPod.iOS: The Speech SDK for iOS (Objective-C) is now also published as a CocoaPod.
- JavaScript: supporto per microfono non predefinito come dispositivo di input.
- JavaScript: supporto proxy per Node.js.

**Esempi**

- Sono stati aggiunti esempi per l'uso dell'SDK di riconoscimento vocale con C e con Objective-C in macOS.
- Sono stati aggiunti esempi che illustrano l'utilizzo del servizio di sintesi vocale.

**Miglioramenti/modifiche**

- Python: Le proprietà aggiuntive dei risultati `properties` del riconoscimento sono ora esposte tramite la proprietà.
- Per ulteriore supporto per lo sviluppo e il debug, è possibile reindirizzare le informazioni di diagnostica e registrazione SDK in un file di log (ulteriori dettagli [qui](how-to-use-logging.md)).
- JavaScript: migliora le prestazioni di elaborazione audio.

**Correzioni**

- Mac/iOS: un bug che ha comportato una lunga attesa quando non è stato possibile stabilire una connessione al servizio di riconoscimento vocale.
- Python: migliorare la gestione degli errori per gli argomenti nei callback Python.
- JavaScript: Fixed wrong state reporting for speech ended on RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: aggiornamento 2019-febbraio

Si tratta di una versione di correzione di bug e che interessa solo l'SDK nativo/gestito. Non influisce sulla versione JavaScript dell'SDK.

**Correzione di bug**

- Risolto il problema di una perdita di memoria quando si utilizza l'input del microfono. L'input basato sul flusso o sul file non è interessato.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: versione 2019-febbraio

**Nuove funzionalità**

- Speech SDK supporta la selezione del `AudioConfig` microfono di input tramite la classe. Ciò consente di trasmettere dati audio al servizio di riconoscimento vocale da un microfono non predefinito. Per ulteriori informazioni, consultate la documentazione che descrive la selezione del dispositivo di [input audio.](how-to-select-audio-input-devices.md) Questa funzione non è ancora disponibile da JavaScript.
- Speech SDK supporta ora Unity in versione beta. Fornire commenti e suggerimenti tramite la sezione issue nel [repository di esempio GitHub](https://aka.ms/csspeech/samples). Questa versione supporta Unity in Windows x86 e x64 (applicazioni desktop o per la piattaforma UWP) e Android (ARM32/64, x86). Altre informazioni sono disponibili nell'[Avvio rapido di Unity](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- Il `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` file (fornito nelle versioni precedenti) non è più necessario. La funzionalità è ora integrata nell'SDK principale.

**Esempi**

I nuovi contenuti seguenti sono disponibili nel [repository di esempio](https://aka.ms/csspeech/samples):

- Ulteriori campioni `AudioConfig.FromMicrophoneInput`per .
- Altri esempi di Python per il riconoscimento finalità e la traduzione.
- Esempi aggiuntivi per `Connection` l'utilizzo dell'oggetto in iOS.Additional samples for using the object in iOS.
- Esempi aggiuntivi per Java per la traduzione con output audio.
- Nuovo esempio per l'uso dell'[API REST di trascrizione batch](batch-transcription.md).

**Miglioramenti/modifiche**

- Python
  - Miglioramento della verifica dei `SpeechConfig`parametri e dei messaggi di errore in .
  - Aggiungere il `Connection` supporto per l'oggetto.
  - Supporto per Python a 32 bit (x86) in Windows.
  - Speech SDK per Python non è incluso nella beta.
- iOS
  - L'SDK è ora basato sull'SDK per iOS versione 12.1.
  - L'SDK supporta ora iOS 9.2 e versioni successive.
  - Miglioramento della documentazione di riferimento e correzione di diversi nomi di proprietà.
- JavaScript
  - Aggiungere il `Connection` supporto per l'oggetto.
  - Aggiunta di file di definizione del tipo per JavaScript in bundle.
  - Supporto iniziale e implementazione per gli hint della frase.
  - Restituzione della raccolta di proprietà con il file JSON del servizio per il riconoscimento.
- Le DLL di Windows contengono ora una risorsa di versione.
- Se si crea `FromEndpoint` un sistema di riconoscimento, è possibile aggiungere parametri direttamente all'URL dell'endpoint. Utilizzando `FromEndpoint` non è possibile configurare il sistema di riconoscimento tramite le proprietà di configurazione standard.

**Correzioni**

- Il nome utente proxy e la password proxy vuoti non erano gestiti correttamente. Con questa versione, se si imposta il nome utente proxy e la password proxy su una stringa vuota, non verranno inviati quando ci si connette al proxy.
- Gli ID sessione creati dall'SDK non erano sempre realmente casuali per alcune lingue&nbsp;/ ambienti. Aggiunta l'inizializzazione del generatore casuale per risolvere questo problema.
- Gestione migliorata del token di autorizzazione. Se si vuole usare un token `SpeechConfig` di autorizzazione, specificare e lasciare vuota la chiave di sottoscrizione. Creare quindi il sistema di riconoscimento come di consueto.
- In alcuni `Connection` casi l'oggetto non è stato rilasciato correttamente. Il problema è stato risolto.
- L'esempio di JavaScript è stato corretto per supportare l'output audio per la sintesi della traduzione anche in Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

- Attivazione della fine del flusso in corrispondenza di turn.end e non di speech.end.
- Correzione di un bug nel data pump audio che non pianificava l'invio successivo in caso di errore dell'invio corrente.
- Correzione del riconoscimento continuo con il token di autenticazione.
- Correzione di bug per sistemi di riconoscimento/endpoint diversi.
- Miglioramenti alla documentazione.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: versione 2018-dicembre

**Nuove funzionalità**

- Python
  - La versione beta del supporto di Python (versione 3.5 e successive) è disponibile con questa versione. Per ulteriori informazioni, vedere qui](python.md di avvio rapido).
- JavaScript
  - Speech SDK per JavaScript è ora open source. Il codice sorgente è disponibile in [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - È ora supportato Node.js. Altre informazioni sono reperibili [qui](quickstart-js-node.md).
  - È stata rimossa la restrizione di lunghezza per le sessioni audio. La riconnessione avverrà automaticamente.
- Oggetto `Connection`
  - Da `Recognizer`, è possibile `Connection` accedere a un oggetto. Questo oggetto consente di avviare in modo esplicito la connessione al servizio e di aggiungere una sottoscrizione per connettere e disconnettere gli eventi.
    (Questa funzione non è ancora disponibile da JavaScript e Python.)
- Supporto per Ubuntu 18.04.
- Android
  - Supporto di ProGuard abilitato durante la generazione di APK.

**Miglioramenti**

- Miglioramenti nell'uso dei thread interni, con riduzione del numero di thread, blocchi e mutex.
- Miglioramento della segnalazione errori e delle informazioni sugli errori. In diversi casi, i messaggi di errore non sono stati propagati fino in fondo.
- Aggiornamento delle dipendenze di sviluppo in JavaScript per usare i moduli aggiornati.

**Correzioni**

- Correzione delle perdite di memoria dovute a una mancata corrispondenza del tipo in `RecognizeAsync`.
- In alcuni casi perdita delle eccezioni.
- Correzione della perdita di memoria negli argomenti degli eventi di conversione.
- Correzione di un problema di blocco per la riconnessione in sessioni a esecuzione prolungata.
- Risolto un problema che poteva causare la mancanza del risultato finale per le traduzioni non riuscite.
- C'è: `async` se un'operazione non era attesa nel thread principale, era possibile che il sistema di riconoscimento potesse essere eliminato prima del completamento dell'attività asincrona.
- Java: risolto un problema che causava un arresto anomalo della java VM.
- Objective-C: mapping di enumerazione fissa; RecognizedIntent è stato `RecognizingIntent`restituito anziché .
- JavaScript: impostare il formato di `SpeechConfig`output predefinito su 'semplice' in .
- JavaScript: rimozione dell'incoerenza tra le proprietà nell'oggetto config in JavaScript e in altri linguaggi.

**Esempi**

- Aggiornati e corretti diversi esempi (ad esempio voci di output per la traduzione, ecc.).
- Aggiunta di esempi di Node.js nel [repository degli esempi](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nuove funzionalità**

- Supporto per Android x86/x64.
- Supporto proxy: `SpeechConfig` nell'oggetto è ora possibile chiamare una funzione per impostare le informazioni sul proxy (nome host, porta, nome utente e password). Questa funzionalità non è ancora disponibile in iOS.
- Messaggi e codice di errore migliorati. Se un riconoscimento ha restituito un errore, l'elemento `Reason` (nell'evento annullato) o `CancellationDetails` (nel risultato del riconoscimento) è già stato impostato su `Error`. L'evento annullato contiene ora due membri aggiuntivi: `ErrorCode` e `ErrorDetails`. Se il server ha restituito l'errore corredato da informazioni aggiuntive, le informazioni saranno ora disponibili nei nuovi membri.

**Miglioramenti**

- È stata aggiunta una verifica supplementare nella configurazione del sistema di riconoscimento ed è stato aggiunto un nuovo messaggio di errore.
- È stata migliorata la gestione di un silenzio prolungato nel mezzo di un file audio.
- Pacchetto NuGet: per i progetti .NET Framework, impedisce la compilazione con una configurazione AnyCPU.

**Correzioni**

- Sono state corrette varie eccezioni rilevate nei sistemi di riconoscimento. Inoltre, le eccezioni vengono `Canceled` rilevate e convertite in evento.
- È stata corretta una perdita di memoria nella gestione delle proprietà.
- È stato corretto un bug per il quale un file di input audio può determinare l'arresto anomalo del sistema di riconoscimento.
- È stato risolto un bug per il quale si continuano a ricevere eventi anche dopo un evento di arresto della sessione.
- Sono state corrette alcune race condition nel threading.
- È stato corretto un problema di compatibilità iOS che può determinare un arresto anomalo.
- Sono stati apportati miglioramenti di stabilità per il supporto di microfoni Android.
- È stato risolto un bug per il quale un sistema di riconoscimento in JavaScript ignorava la lingua di riconoscimento.
- Risolto un bug che `EndpointId` impediva di impostare (in alcuni casi) in JavaScript.
- Modificato l'ordine dei parametri in AddIntent in JavaScript e aggiunto la firma JavaScript mancante. `AddIntent`

**Esempi**

- Aggiunti esempi di C e C, per l'utilizzo del flusso pull e push nel [repository di esempio.](https://aka.ms/csspeech/samples)

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

**Modifiche che causano un'interruzione**

- Con questa versione, vengono introdotte numerose modifiche di rilievo.
  Controlla [questa pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) per i dettagli.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Speech SDK 0.6.0 di Servizi cognitivi: versione di agosto 2018

**Nuove funzionalità**

- Le app UWP compilate con Speech SDK ora possono superare il Kit di certificazione app Windows (WACK).
  Consultare la [Guida introduttiva della piattaforma UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Supporto per .NET Standard 2.0 in Linux (Ubuntu 16.04 x64).
- Sperimentale: supporto di Java 8 in Windows (64 bit) e Linux (Ubuntu 16.04 x64).
  Consultare la [Guida introduttiva di Java Runtime Environment](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Modifica funzionale**

- Informazioni aggiuntive dettagliate sull'errore in caso di errori di connessione.

**Modifiche che causano un'interruzione**

- In Java (Android), la funzione `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` non richiede più un parametro di percorso. Il percorso viene ora rilevato automaticamente in tutte le piattaforme supportate.
- La funzione di accesso get della proprietà `EndpointUrl` in Java e C# è stata rimossa.

**Correzioni**

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

**Modifiche che causano un'interruzione**

- Eventi di `NoMatch` riconoscimento: il `Error` tipo di evento è stato unito all'evento.
- È stato rinominato SpeechOutputFormat `OutputFormat` in C' rinominato per rimanere allineato con C .
- Il tipo restituito di alcuni metodi dell'interfaccia `AudioInputStream` è stato leggermente modificato:
  - In Java, il metodo `read` restituisce ora `long` invece di `int`.
  - In C#, il metodo `Read` restituisce ora `uint` invece di `int`.
  - In C++, i metodi `Read` e `GetFormat` restituiscono ora `size_t` invece di `int`.
- C++: le istanze di flussi di input audio possono ora essere passate solo come `shared_ptr`.

**Correzioni**

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

**Cambiamento di rottura**

- Modifica da `SpeechRecognitionResult.RecognizedText` a `SpeechRecognitionResult.Text` in linguaggio C#.

**Correzioni**

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
