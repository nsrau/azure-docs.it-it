---
title: Note sulla versione - Servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Log in continuo aggiornamento relativo a rilasci di funzionalità, miglioramenti, correzioni di bug e problemi noti per i servizi di riconoscimento vocale di Azure.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 13d43a3810bc07cede2a49760f122157de86c44d
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010159"
---
# <a name="release-notes"></a>Note sulla versione

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: Versione di aprile 2019

**Nuove funzionalità** 

* il SDK supporta ora il servizio di sintesi vocale come una versione beta. È supportata in Windows e Linux Desktop da C++, C#e Java. Per altre informazioni, vedere la [Panoramica di sintesi vocale](text-to-speech.md#get-started-with-text-to-speech).
* il SDK supporta ora i file audio MP3 e sono state/Ogg come file di input di flusso. Questa funzionalità è disponibile solo in Linux da C++ e C# ed è attualmente in versione beta (ulteriori dettagli [qui](how-to-use-compressed-audio-input-streams.md)).
* Speech SDK per Java, .NET core, Objective-C e C++ abbia avuto il supporto di macOS. Il supporto di Objective-C per macOS è attualmente in versione beta.
* iOS: Speech SDK per iOS (Objective-C) è ora stata pubblicata anche un' CocoaPod.
* JavaScript: Supporto per non predefinito microfono come dispositivo di input.
* JavaScript: Supporto di proxy per Node. js.

**Esempi**

* Sono stati aggiunti esempi per l'uso di Speech SDK con C++ e Objective-C in macOS.
* Sono stati aggiunti esempi che illustrano l'utilizzo del servizio di sintesi vocale.

**Miglioramenti o modifiche**

* Python: Proprietà aggiuntive dei risultati del riconoscimento vengono ora esposti tramite il `properties` proprietà.
* Per ulteriore supporto di sviluppo e debug è possibile reindirizzare le informazioni di registrazione e diagnostica SDK in un file di log (altre informazioni [qui](how-to-use-logging.md)).
* JavaScript: Migliorare le prestazioni di elaborazione audio.

**Correzioni di bug**

* Mac/iOS: È stato risolto un bug che ha portato a un tempo di attesa quando non è stato possibile stabilire una connessione al servizio riconoscimento vocale.
* Python: migliorare la gestione degli errori per gli argomenti nei callback Python.
* JavaScript: Stato errato fisso reporting per il riconoscimento vocale è terminato RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: Aggiornamento: febbraio 2019

Si tratta di una versione di correzione di bug e influisce solo sulle SDK nativo/gestito. Che non impedisca la versione JavaScript del SDK.

**Correzione di bug**

* Risolto un problema di memoria quando si utilizza l'input del microfono. Stream di base o un file di input non è interessato.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: versione di febbraio 2019

**Nuove funzionalità**

* Speech SDK supporta la selezione del microfono per l'input attraverso la classe AudioConfig. Ciò consente ai dati audio stream per i servizi di riconoscimento vocale dal microfono non predefinito. Per altre informazioni, vedere la documentazione che descrive [selezione del dispositivo di input audio](how-to-select-audio-input-devices.md). Questa funzionalità non è ancora disponibile in JavaScript.
* Speech SDK supporta ora Unity in versione beta. Inviare commenti e suggerimenti tramite la sezione relativa ai problemi nel [repository GitHub di esempio](https://aka.ms/csspeech/samples). Questa versione supporta Unity in Windows x86 e x64 (applicazioni desktop o per la piattaforma UWP) e Android (ARM32/64, x86). Altre informazioni sono disponibili nell'[Avvio rapido di Unity](quickstart-csharp-unity.md).
* Il file `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (forniti nelle versioni precedenti) non è più necessaria. La funzionalità è ora integrata nel core SDK.


**Esempi**

I nuovi contenuti seguenti sono disponibili nel [repository di esempio](https://aka.ms/csspeech/samples):

* Esempi aggiuntivi per AudioConfig.FromMicrophoneInput.
* Altri esempi di Python per il riconoscimento finalità e la traduzione.
* Altri esempi per l'uso dell'oggetto Connection in iOS.
* Esempi aggiuntivi per Java per la traduzione con output audio.
* Nuovo esempio per l'uso dell'[API REST di trascrizione batch](batch-transcription.md).

**Miglioramenti o modifiche**

* Python
  * Messaggi di errore e verifica dei parametri migliorati in SpeechConfig.
  * Aggiunta del supporto per l'oggetto Connection.
  * Supporto per Python a 32 bit (x86) in Windows.
  * Speech SDK per Python non è incluso nella beta.
* iOS
  * L'SDK è ora basato sull'SDK per iOS versione 12.1.
  * L'SDK supporta ora iOS 9.2 e versioni successive.
  * Miglioramento della documentazione di riferimento e correzione di diversi nomi di proprietà.
* JavaScript
  * Aggiunta del supporto per l'oggetto Connection.
  * Aggiunta di file di definizione del tipo per JavaScript in bundle.
  * Supporto iniziale e implementazione per gli hint della frase.
  * Restituzione della raccolta di proprietà con il file JSON del servizio per il riconoscimento.
* Le DLL di Windows contengono ora una risorsa di versione.
* Se si crea un sistema di riconoscimento `FromEndpoint` è possibile aggiungere parametri direttamente all'URL dell'endpoint. Usando `FromEndpoint` non è possibile configurare il riconoscimento tramite le proprietà di configurazione standard.

**Correzioni di bug**

* Il nome utente proxy e la password proxy vuoti non erano gestiti correttamente. Con questa versione, se si imposta il nome utente proxy e la password proxy su una stringa vuota, non verranno inviati quando ci si connette al proxy.
* Gli ID sessione creati dall'SDK non erano sempre realmente casuali per alcune lingue&nbsp;/ ambienti. È stata aggiunta l'inizializzazione del generatore casuale per risolvere questo problema.
* Gestione migliorata del token di autorizzazione. Se si vuole usare un token di autorizzazione, specificarlo in SpeechConfig e lasciare vuota la chiave di sottoscrizione. Creare quindi il sistema di riconoscimento come di consueto.
* In alcuni casi l'oggetto Connection non veniva rilasciato correttamente. Questo problema è stato risolto.
* L'esempio di JavaScript è stato corretto per supportare l'output audio per la sintesi della traduzione anche in Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Questa è una versione solo per JavaScript. Non sono state aggiunte funzionalità. Sono state apportate le correzioni seguenti:

* Attivazione della fine del flusso in corrispondenza di turn.end e non di speech.end.
* Correzione di un bug nel data pump audio che non pianificava l'invio successivo in caso di errore dell'invio corrente.
* Correzione del riconoscimento continuo con il token di autenticazione.
* Correzione di bug per sistemi di riconoscimento/endpoint diversi.
* Miglioramenti alla documentazione.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: versione di dicembre 2018

**Nuove funzionalità**

* Python
  * La versione beta del supporto di Python (versione 3.5 e successive) è disponibile con questa versione. Per altre informazioni, vedere here](quickstart-python.md).
* JavaScript
  * Speech SDK per JavaScript è ora open source. Il codice sorgente è disponibile in [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * È ora supportato Node.js. Altre informazioni sono reperibili [qui](quickstart-js-node.md).
  * È stata rimossa la restrizione di lunghezza per le sessioni audio. La riconnessione avverrà automaticamente.
* Oggetto Connection
  * Dal riconoscimento, è possibile accedere a un oggetto di connessione. Questo oggetto consente di avviare in modo esplicito la connessione al servizio e di aggiungere una sottoscrizione per connettere e disconnettere gli eventi.
    (Ciò non è ancora disponibile da JavaScript e Python.)
* Supporto per Ubuntu 18.04.
* Android
  * Supporto di ProGuard abilitato durante la generazione di APK.

**Miglioramenti**

* Miglioramenti nell'uso dei thread interni, con riduzione del numero di thread, blocchi e mutex.
* Miglioramento della segnalazione errori e delle informazioni sugli errori. In molti casi i messaggi di errore non sono stati propagati completamente.
* Aggiornamento delle dipendenze di sviluppo in JavaScript per usare i moduli aggiornati.

**Correzioni di bug**

* Correzione delle perdite di memoria causate da un tipo non corrispondente in RecognizeAsync.
* In alcuni casi perdita delle eccezioni.
* Correzione della perdita di memoria negli argomenti degli eventi di conversione.
* Correzione di un problema di blocco per la riconnessione in sessioni a esecuzione prolungata.
* Risolto un problema che potrebbe causare risultati finali per le conversioni non riuscite mancante.
* C#: Se non è prevista l'attesa di un'operazione asincrona nel thread principale, lo strumento di riconoscimento potrebbe essere eliminato prima del completamento dell'attività asincrona.
* Java: Risoluzione di un problema che causava l'arresto anomalo della macchina virtuale Java.
* Objective-C: Correzione del mapping delle enumerazioni: restituzione di RecognizedIntent anziché RecognizingIntent.
* JavaScript: Impostazione del formato di output predefinito su 'simple' in SpeechConfig.
* JavaScript: Rimozione di un'incoerenza tra le proprietà dell'oggetto config in JavaScript e altri linguaggi.

**Esempi**

* Aggiornato e risolto diversi esempi (ad esempio output voci per la conversione e così via.).
* Aggiunta di esempi di Node.js nel [repository degli esempi](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nuove funzionalità**

* Supporto per Android x86/x64.
* Supporto proxy: Nell'oggetto SpeechConfig, è ora possibile chiamare una funzione per impostare le informazioni proxy (nome host, porta, nome utente e password). Questa funzionalità non è ancora disponibile in iOS.
* Messaggi e codice di errore migliorati. Se un riconoscimento ha restituito un errore, l'elemento `Reason` (nell'evento annullato) o `CancellationDetails` (nel risultato del riconoscimento) è già stato impostato su `Error`. L'evento annullato contiene ora due membri aggiuntivi: `ErrorCode` e `ErrorDetails`. Se il server ha restituito l'errore corredato da informazioni aggiuntive, le informazioni saranno ora disponibili nei nuovi membri.

**Miglioramenti**

* È stata aggiunta una verifica supplementare nella configurazione del sistema di riconoscimento ed è stato aggiunto un nuovo messaggio di errore.
* È stata migliorata la gestione di un silenzio prolungato nel mezzo di un file audio.
* Pacchetto NuGet: per i progetti .NET Framework, impedisce la compilazione con una configurazione AnyCPU.

**Correzioni di bug**

* Sono state corrette varie eccezioni rilevate nei sistemi di riconoscimento. Inoltre, le eccezioni vengono rilevate e convertite in eventi annullato.
* È stata corretta una perdita di memoria nella gestione delle proprietà.
* È stato corretto un bug per il quale un file di input audio può determinare l'arresto anomalo del sistema di riconoscimento.
* È stato risolto un bug per il quale si continuano a ricevere eventi anche dopo un evento di arresto della sessione.
* Sono state corrette alcune race condition nel threading.
* È stato corretto un problema di compatibilità iOS che può determinare un arresto anomalo.
* Sono stati apportati miglioramenti di stabilità per il supporto di microfoni Android.
* È stato risolto un bug per il quale un sistema di riconoscimento in JavaScript ignorava la lingua di riconoscimento.
* È stato risolto un bug che impediva l'impostazione di EndpointId (in alcuni casi) in JavaScript.
* È stato modificato l'ordine dei parametri in AddIntent in JavaScript ed è stata aggiunta una firma AddIntent JavaScript mancante.

**Esempi**

* Sono stati aggiunti esempi in C++ e C# dell'uso di flussi pull e push nel [repository degli esempi](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Miglioramenti apportati alla stabilità e correzioni di bug:

* Risoluzione di un potenziale errore irreversibile dovuto a una race condition nell'eliminazione dello strumento di riconoscimento.
* Risoluzione di un potenziale errore irreversibile in caso di proprietà non impostate.
* Aggiunta di un ulteriore controllo di errori e parametri.
* Objective-C: risoluzione di un potenziale errore irreversibile causato dall'override del nome in NSString.
* Objective-C: modifica della visibilità dell'API
* JavaScript: correzioni relative a eventi e payload correlati.
* Miglioramenti alla documentazione.

Al [repository di esempi](https://aka.ms/csspeech/samples) è stato aggiunto un nuovo esempio per JavaScript.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Speech SDK di Servizi cognitivi 1.0.0: versione di settembre 2018

**Nuove funzionalità**

* Supporto per Objective-C in iOS. Vedere la [Guida introduttiva su Objective-C per iOS](quickstart-objectivec-ios.md).
* Supporto per JavaScript nel browser. Vedere la [Guida introduttiva su JavaScript](quickstart-js-browser.md).

**Modifiche di rilievo**

* Con questa versione, viene introdotte numerose modifiche di rilievo.
  Per informazioni dettagliate, vedere [questa pagina](https://aka.ms/csspeech/breakingchanges_1_0_0).

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Speech SDK di Servizi cognitivi 0.6.0: versione di agosto 2018

**Nuove funzionalità**

* Le app UWP compilate con Speech SDK ora possono superare il Kit di certificazione app Windows (WACK).
  Consultare la [Guida introduttiva della piattaforma UWP](quickstart-csharp-uwp.md).
* Supporto per .NET Standard 2.0 in Linux (Ubuntu 16.04 x64).
* Sperimentale: supporto di Java 8 in Windows (64 bit) e Linux (Ubuntu 16.04 x64).
  Consultare la [Guida introduttiva di Java Runtime Environment](quickstart-java-jre.md).

**Modifiche funzionali**

* Informazioni aggiuntive dettagliate sull'errore in caso di errori di connessione.

**Modifiche di rilievo**

* In Java (Android), la funzione `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` non richiede più un parametro di percorso. Il percorso viene ora rilevato automaticamente in tutte le piattaforme supportate.
* La funzione di accesso get della proprietà `EndpointUrl` in Java e C# è stata rimossa.

**Correzioni di bug**

* In Java, il risultato di sintesi audio sul sistema di riconoscimento di traduzione è ora implementato.
* È stato risolto un bug che potrebbe causare un maggior numero di socket aperti e inutilizzati e thread inattivi.
* È stato risolto un problema in cui un riconoscimento con esecuzione prolungata terminava la trasmissione a metà.
* Correzione di una race condition nel sistema di riconoscimento di arresto.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Speech SDK di Servizi cognitivi 0.5.0: versione di luglio 2018

**Nuove funzionalità**

* Supporto piattaforma Android (API 23: Android 6.0 Marshmallow o versione successiva). Consultare la [Guida introduttiva di Android](quickstart-java-android.md).
* Supporto di .NET Standard 2.0 in Windows. Consultare la [Guida introduttiva di .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Sperimentale: supporto di UWP in Windows (versione 1709 o successiva).
  * Consultare la [Guida introduttiva della piattaforma UWP](quickstart-csharp-uwp.md).
  * Note: le app UWP compilate con Speech SDK non superano ancora il Kit di certificazione app Windows (WACK).
* Supporto del riconoscimento a esecuzione prolungata con riconnessione automatica.

**Modifiche funzionali**

* `StartContinuousRecognitionAsync()` supporta il riconoscimento di lunga durata.
* Il risultato del riconoscimento contiene più campi. Scostamento da inizio audio e durata (entrambi in tick) del testo riconosciuto, valori aggiuntivi che rappresentano lo stato di riconoscimento, ad esempio `InitialSilenceTimeout` e `InitialBabbleTimeout`.
* Supporto del token di autorizzazione per la creazione di istanze di factory.

**Modifiche di rilievo**

* Eventi di riconoscimento: il tipo di evento NoMatch è stato unito all'evento Error.
* SpeechOutputFormat in C# è stato rinominato in OutputFormat per coerenza con C++.
* Il tipo restituito di alcuni metodi dell'interfaccia `AudioInputStream` è stato leggermente modificato:
   * In Java, il metodo `read` restituisce ora `long` invece di `int`.
   * In C#, il metodo `Read` restituisce ora `uint` invece di `int`.
   * In C++, i metodi `Read` e `GetFormat` restituiscono ora `size_t` invece di `int`.
* C++: le istanze di flussi di input audio possono ora essere passate solo come `shared_ptr`.

**Correzioni di bug**

* Sono stati corretti i valori restituiti errati nel risultato alla scadenza di `RecognizeAsync()`.
* È stata rimossa la dipendenza dalle librerie di Media Foundation in Windows. L'SDK usa ora le API Audio Core.
* Correzione della documentazione: è stata aggiunta una pagina relativa alle [aree](regions.md) per descrivere le aree supportate.

**Problema noto**

* Speech SDK per Android non segnala i risultati della sintesi vocale per la traduzione. Questo problema verrà risolto nella prossima versione.

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
