---
title: Note sulla versione per il pacchetto NuGet Microsoft. ApplicationInsights. SnapshotCollector-Application Insights
description: Note sulla versione per il pacchetto NuGet Microsoft. ApplicationInsights. SnapshotCollector usato dalla Application Insights Snapshot Debugger.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: faacb78ca7f055bb8e9a469afb894bc30ee87501
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507493"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Note sulla versione per Microsoft. ApplicationInsights. SnapshotCollector

Questo articolo contiene le note sulla versione per il pacchetto NuGet Microsoft. ApplicationInsights. SnapshotCollector per le applicazioni .NET, usato dalla Application Insights Snapshot Debugger.

[Altre informazioni](./snapshot-debugger.md) sul Snapshot Debugger di Application Insights per le applicazioni .NET.

Per segnalazioni di bug e commenti e suggerimenti, aprire un problema in GitHub all'indirizzo https://github.com/microsoft/ApplicationInsights-dotnet

## <a name="release-notes"></a>Note sulla versione

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Una versione punto per risolvere un paio di problemi ad alto effetto.
### <a name="bug-fixes"></a>Correzioni di bug
- Correzione dell'individuazione PDB nella cartella wwwroot/bin, che è stata interrotta quando è stato modificato l'algoritmo di ricerca dei simboli in 1.3.6.
- Correzione di ExtractWasCalledMultipleTimesException rumorosi nei dati di telemetria.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Modifiche
La destinazione netcoreapp 2.0 di SnapshotCollector dipende da Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (anche in questo caso). In questo modo viene ripristinato il comportamento precedente a 1.3.5. Si è tentato di eseguire l'aggiornamento in 1.3.6, ma è stato interrotto alcuni scenari di servizio app Azure.
### <a name="new-features"></a>Nuove funzionalità
Snapshot Collector legge e analizza ConnectionString dalla variabile di ambiente APPLICATIONINSIGHTS_CONNECTION_STRING o da TelemetryConfiguration. Principalmente, viene usato per impostare l'endpoint per la connessione al servizio snapshot. Per ulteriori informazioni, vedere la [documentazione relativa alle stringhe di connessione](./sdk-connection-string.md).
### <a name="bug-fixes"></a>Correzioni di bug
- Si è passati a usare HttpClient per tutte le destinazioni ad eccezione di Net45 perché WebRequest non ha avuto esito negativo in alcuni ambienti a causa di un SecurityProtocol incompatibile (richiede TLS 1,2).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Modifiche
- SnapshotCollector dipende ora da Microsoft. ApplicationInsights >= 2.5.1 per tutti i Framework di destinazione. Potrebbe trattarsi di una modifica di rilievo se l'applicazione dipende da una versione precedente di Microsoft. ApplicationInsights SDK.
- Rimuovere il supporto per TLS 1,0 e 1,1 nell'Uploader di snapshot.
- Il periodo di analisi PDB ora è predefinito 24 ore anziché 15 minuti. Configurabile tramite PdbRescanInterval in SnapshotCollectorConfiguration.
- L'analisi PDB Cerca solo le cartelle di primo livello, anziché ricorsivo. Potrebbe trattarsi di una modifica di rilievo se i simboli si trovano nelle sottocartelle della cartella binaria.
### <a name="new-features"></a>Nuove funzionalità
- Rotazione del log in SnapshotUploader per evitare di riempire la cartella logs con file obsoleti.
- Supporto per la deottimizzazione (tramite ReJIT on Connetti) per le applicazioni .NET Core 3,0.
- Aggiungere simboli al pacchetto NuGet.
- Impostare metadati aggiuntivi durante il caricamento di minidump.
- Aggiunta di una proprietà inizializzata a SnapshotCollectorTelemetryProcessor. Si tratta di un oggetto CancellationToken, che verrà annullato quando il Snapshot Collector viene completamente inizializzato e connesso all'endpoint del servizio.
- È ora possibile acquisire gli snapshot per le eccezioni nei metodi generati dinamicamente. Ad esempio, gli alberi delle espressioni compilati generati da Entity Framework query.
### <a name="bug-fixes"></a>Correzioni di bug
- Caricamento AmbiguousMatchException Snapshot Collector a causa di Status Monitor.
- Il metodo di estensione GetSnapshotCollector ora Cerca tutti i TelemetrySinks.
- Non avviare l'upload dello snapshot sulle piattaforme non supportate.
- Handle InvalidOperationException per la deottimizzazione di metodi dinamici (ad esempio, Entity Framework)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Aggiungere il supporto per i cloud sovrani (le versioni precedenti non funzioneranno nei cloud sovrani)
- L'aggiunta di un agente di raccolta snapshot è stata semplificata tramite AddSnapshotCollector (). Altre informazioni sono disponibili [qui](./snapshot-debugger-appservice.md).
- Usare l'impostazione MD5 di FISMA per verificare i blocchi BLOB. In questo modo si evita l'algoritmo di crittografia MD5 .NET predefinito, che non è disponibile quando il sistema operativo è impostato sulla modalità conforme a FIPS.
- Ignorare i frame .NET Framework durante la deottimizzazione delle chiamate di funzione. Questo comportamento può essere controllato dall'impostazione di configurazione DeoptimizeIgnoredModules.
- Aggiungere `DeoptimizeMethodCount` un'impostazione di configurazione che consente la deottimizzazione di più di una chiamata di funzione. Ulteriori informazioni sono disponibili qui

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Consentire chiavi di strumentazione strutturate.
- Aumenta l'affidabilità di SnapshotUploader-continua l'avvio anche se non è possibile spostare i log di upload obsoleti.
- Riabilitata la segnalazione di dati di telemetria aggiuntivi quando SnapshotUploader.exe termina immediatamente (è stato disabilitato nella 1.3.3).
- Semplificare la telemetria interna.
- _Funzionalità sperimentale_ : piani di raccolta ancoraggio: aggiungere "snapshotOnFirstOccurence". Altre informazioni sono disponibili [qui](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Correzione di un bug che causava il blocco SnapshotUploader.exe e non il caricamento di snapshot per le app .NET Core.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Funzionalità sperimentale_ : piani di raccolta ancoraggio. Altre informazioni sono disponibili [qui](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).
- SnapshotUploader.exe verrà chiuso quando il runtime Scarica l'AppDomain dal quale viene caricato SnapshotCollector, invece di attendere la chiusura del processo. Questa operazione migliora l'affidabilità degli agenti di raccolta quando è ospitata in IIS.
- Aggiungere la configurazione per consentire a più istanze di SnapshotCollector che usano la stessa chiave di strumentazione di condividere lo stesso processo SnapshotUploader: ShareUploaderProcess (il valore predefinito è `true` ).
- Segnala i dati di telemetria aggiuntivi quando SnapshotUploader.exe termina immediatamente.
- Ridotto il numero di file di supporto SnapshotUploader.exe necessario scrivere sul disco.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Rimuovere il supporto per la raccolta di snapshot con l'API RtlCloneUserProcess e supportare solo l'API PssCaptureSnapshots.
- Aumentare il limite predefinito per il numero di snapshot che è possibile acquisire in 10 minuti da 1 a 3.
- Consenti a SnapshotUploader.exe di negoziare TLS 1,1 e 1,2
- Segnala dati di telemetria aggiuntivi quando SnapshotUploader registra un avviso o un errore
- Interrompi l'esecuzione di snapshot quando il servizio back-end segnala la quota giornaliera raggiunta (snapshot 50 al giorno)
- Aggiungere un controllo aggiuntivo in SnapshotUploader.exe per non consentire l'esecuzione di due istanze nello stesso momento.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Modifiche
- Per le applicazioni destinate a .NET Framework, Snapshot Collector ora dipende da Microsoft. ApplicationInsights versione 2.3.0 o successiva.
Usato per essere 2.2.0 o versione successiva.
Si ritiene che questo non sia un problema per la maggior parte delle applicazioni, ma è possibile segnalare se questa modifica impedisce di usare la Snapshot Collector più recente.
- Quando si ritentano i caricamenti non riusciti, usare ritardi di backup esponenziale nell'Uploader di snapshot.
- Usare ServerTelemetryChannel (se disponibile) per una segnalazione più affidabile dei dati di telemetria.
- Usare "SdkInternalOperationsMonitor" nella connessione iniziale al servizio Snapshot Debugger in modo che venga ignorato dal rilevamento delle dipendenze.
- Migliorare la telemetria intorno alla connessione iniziale al servizio Snapshot Debugger.
- Segnala la telemetria aggiuntiva per:
  - Versione del servizio app Azure.
  - Istanze di calcolo di Azure.
  - Contenitori.
  - App per le funzioni di Azure.
### <a name="bug-fixes"></a>Correzioni di bug
- Quando l'intervallo di reimpostazione del contatore dei problemi è impostato su 24 giorni, interpretare come 24 ore.
- Correzione di un bug in cui l'upload dello snapshot interrompe l'elaborazione di nuovi snapshot se si è verificata un'eccezione durante l'eliminazione di uno snapshot.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Correzione della firma con nome sicuro con i file binari dell'Uploader di snapshot.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Modifiche
- I file necessari per SnapshotUploader (64). exe sono ora incorporati come risorse nella DLL principale. Ciò significa che la cartella SnapshotCollectorFiles non viene più creata, semplificando la compilazione e la distribuzione e riducendo il disordine nei Esplora soluzioni. Prestare attenzione quando si esegue l'aggiornamento per rivedere le modifiche nel `.csproj` file. Il `Microsoft.ApplicationInsights.SnapshotCollector.targets` file non è più necessario.
- La telemetria viene registrata nella risorsa Application Insights anche se ProvideAnonymousTelemetry è impostato su false. In questo modo è possibile implementare una funzionalità di controllo integrità nell'portale di Azure. ProvideAnonymousTelemetry influiscono solo sui dati di telemetria inviati a Microsoft per il supporto e il miglioramento del prodotto.
- Quando TempFolder o ShadowCopyFolder vengono reindirizzati alle variabili di ambiente, lasciare inattivo l'agente di raccolta fino a quando non vengono impostate tali variabili di ambiente.
- Per le applicazioni che si connettono a Internet tramite un server proxy, Snapshot Collector ora rileva automaticamente le impostazioni proxy e le passa a SnapshotUploader.exe.
- Abbassare la priorità del processo SnapshotUplaoder (dove possibile). Questa priorità può essere sottoposta a override tramite l'opzione IsLowPrioirtySnapshotUploader.
- Aggiunto un metodo di estensione GetSnapshotCollector in TelemetryConfiguration per gli scenari in cui si desidera configurare la Snapshot Collector a livello di codice.
- Impostare la versione di Application Insights SDK, anziché la versione dell'applicazione, nei dati di telemetria relativi al cliente.
- Invia il primo evento heartbeat dopo due minuti.
### <a name="bug-fixes"></a>Correzioni di bug
- Correzione di NullReferenceException quando le eccezioni hanno dizionari di dati null o non modificabili.
- Nell'Uploader ritentare il PDB corrispondente alcune volte se si verifica una violazione di condivisione.
- Corregge i dati di telemetria duplicati quando più di un thread chiama la pipeline di telemetria all'avvio.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Modifiche
- I file di commento del documento XML sono ora inclusi nel pacchetto NuGet.
- È stato aggiunto un metodo di estensione ExcludeFromSnapshotting in `System.Exception` per gli scenari in cui si è certi di avere un'eccezione rumorosa e si vuole evitare la creazione di snapshot.
- Aggiunta di una proprietà di configurazione IsEnabledWhenProfiling. il valore predefinito è true. Si tratta di una modifica rispetto alle versioni precedenti in cui la creazione di snapshot è stata temporaneamente disabilitata se il Application Insights Profiler stava eseguendo una raccolta dettagliata. Il comportamento precedente può essere recuperato impostando questa proprietà su false.
### <a name="bug-fixes"></a>Correzioni di bug
- Firmare SnapshotUploader64.exe correttamente.
- Proteggersi da una doppia inizializzazione del processore di telemetria.
- Impedisci la doppia registrazione dei dati di telemetria nelle app con più pipeline.
- Correggere un bug con la data di scadenza di un piano di raccolta, che potrebbe impedire gli snapshot dopo 24 ore.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
La modifica più importante in questa versione (quindi il passaggio a un nuovo numero di versione secondario) è una riscrittura della pipeline di creazione e gestione degli snapshot. Nelle versioni precedenti, questa funzionalità è stata implementata in codice nativo (ProductionBreakpoints *. dll e SnapshotHolder*. exe). La nuova implementazione è tutto il codice gestito con P/Invoke. Per questa prima versione, l'uso della nuova pipeline non è stato più lontano dal comportamento originale. La nuova implementazione consente di ottenere una migliore segnalazione degli errori e di impostarla per miglioramenti futuri.

### <a name="other-changes-in-this-version"></a>Altre modifiche in questa versione
- MinidumpUploader.exe è stato rinominato SnapshotUploader.exe (o SnapshotUploader64.exe).
- Aggiunta della telemetria temporale per la deottimizzazione/riottimizzazione delle richieste.
- Aggiunta compressione gzip per i caricamenti di minidump.
- Correzione di un problema per cui PDB bloccava l'aggiornamento del sito.
- Registrare il nome della cartella originale (SnapshotCollectorFiles) durante la copia shadow.
- Regolare i limiti di memoria per i processi a 64 bit per impedire i riavvii del sito a causa di memoria insufficiente.
- Correzione di un problema per cui gli snapshot sono stati ancora raccolti anche dopo la disabilitazione.
- Registra gli eventi heartbeat nella risorsa AI del cliente.
- Migliorare la velocità dello snapshot rimuovendo "origine" dall'ID problema.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Modifiche
Telemetria di utilizzo aumentata
- Rilevare e segnalare la versione e il sistema operativo .NET
- Rilevare e segnalare altri ambienti Azure (servizio cloud, Service Fabric)
- Registrare e segnalare le metriche delle eccezioni (numero di eccezioni di primo rischio e numero di chiamate di Trackexception) nella telemetria heartbeat.
### <a name="bug-fixes"></a>Correzioni di bug
- Corretta gestione di SqlException in cui l'eccezione interna (Win32exception) non viene generata.
- Tagliare gli spazi finali sulle cartelle dei simboli, che hanno causato l'analisi non corretta degli argomenti della riga di comando per MinidumpUploader.
- Impedisci un tentativo infinito di connessioni non riuscite all'endpoint dell'agente Snapshot Debugger.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Modifiche
- Aggiunta della protezione della memoria dell'host. Questa funzionalità riduce l'effetto sulla memoria del computer host.
- Migliorare l'esperienza di visualizzazione degli snapshot portale di Azure.