---
title: Specifica per l'inserimento live di un flusso MP4 frammentato con Servizi multimediali di Azure | Microsoft Docs
description: "Questa specifica descrive il protocollo e il formato per l'inserimento di streaming live basato sul formato MP4 frammentato per Servizi multimediali di Azure. È possibile usare Servizi multimediali di Azure per lo streaming di eventi live e la trasmissione di contenuti in tempo reale usando Azure come piattaforma cloud. Questo documento contiene anche le procedure consigliate per creare meccanismi di inserimento live solidi e altamente ridondanti."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 6250b73504bec765b8299060a29e84e771791cc9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Specifica per l'inserimento live di un flusso MP4 frammentato con Servizi multimediali di Azure
Questa specifica descrive il protocollo e il formato per l'inserimento di streaming live basato sul formato MP4 frammentato per Servizi multimediali di Azure. Servizi multimediali fornisce un servizio di streaming live che può essere usato dai clienti per lo streaming di eventi live e la trasmissione di contenuti in tempo reale usando Azure come piattaforma cloud. Questo documento contiene anche le procedure consigliate per creare meccanismi di inserimento live solidi e altamente ridondanti.

## <a name="1-conformance-notation"></a>1. Nota di conformità
Le parole chiave "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" e "OPTIONAL" usate in questo documento devono essere interpretate come descritto nella specifica RFC 2119.

## <a name="2-service-diagram"></a>2. Diagramma del servizio
Il diagramma seguente mostra l'architettura di alto livello del servizio di streaming live in Servizi multimediali:

1. Un codificatore live invia feed live a canali che sono stati creati e di cui è stato effettuato il provisioning tramite Azure Media Services SDK.
2. In Servizi multimediali la gestione di tutte le funzionalità di live streaming, tra cui inserimento, formattazione, DVR sul cloud, sicurezza, scalabilità e ridondanza, è affidata a canali, programmi ed endpoint di streaming.
3. Facoltativamente, i clienti possono scegliere di distribuire un livello di rete CDN di Azure tra l'endpoint di streaming e gli endpoint client.
4. Gli endpoint client trasmettono dall'endpoint di streaming tramite protocolli di flusso adattivo HTTP. Alcuni esempi includono Microsoft Smooth Streaming, flusso adattivo dinamico su HTTP (DASH o MPEG-DASH) e Apple HTTP Live Streaming (HLS).

![Flusso di inserimento][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Formato del flusso di bit: MP4 frammentato ISO 14496-12
Il formato di trasmissione per l'inserimento di streaming live descritto in questo documento si basa sullo standard [ISO 14496-12]. Per una spiegazione dettagliata del formato MP4 frammentato e delle estensioni disponibili per i file video on demand e l'inserimento di streaming live, vedere [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definizioni del formato di inserimento live
L'elenco seguente descrive le definizioni di formato speciali applicabili all'inserimento live in Servizi multimediali di Azure:

1. Le caselle **ftyp**, **Live Server Manifest Box** e **moov** devono (MUST) essere inviate con ogni richiesta (HTTP POST). Le caselle devono (MUST) essere inviate all'inizio del flusso e ogni volta che il codificatore deve riconnettersi per riprendere l'inserimento del flusso. Per altre informazioni, vedere la sezione 6 di [1].
2. La sezione 3.3.2 di [1] definisce una casella facoltativa chiamata **StreamManifestBox** per l'inserimento live. A causa della logica di routing del bilanciamento del carico di Azure, l'uso di questa casella è deprecato. La casella non deve (SHOULD NOT) essere presente durante l'inserimento in Servizi multimediali. Se la casella è presente, Servizi multimediali la ignora automaticamente.
3. La casella **TrackFragmentExtendedHeaderBox** definita nella sezione 3.2.3.2 di [1] deve (MUST) essere presente per ogni frammento.
4. La versione 2 della casella **TrackFragmentExtendedHeaderBox** dovrebbe (SHOULD) essere usata per generare segmenti multimediali con URL identici in più data center. Il campo di indice del frammento è obbligatorio (REQUIRED) per il failover tra data center di formati di streaming basati su indice, come Apple HTTP Live Streaming (HLS) e MPEG-DASH basato su indice. Per abilitare il failover tra data center, l'indice del frammento deve (MUST) essere sincronizzato tra più codificatori e aumentare di un'unità per ogni frammento multimediale successivo, anche in caso di riavvii o errori del codificatore.
5. La sezione 3.3.6 di [1] definisce una casella chiamata **MovieFragmentRandomAccessBox** (**mfra**), che può (MAY) essere inviata al termine dell'inserimento live per indicare al canale la fine del flusso. A causa della logica di inserimento di Servizi multimediali, la fine del flusso è deprecata e la casella **mfra** per l'inserimento live non deve (SHOULD NOT) essere inviata. Se viene inviata, Servizi multimediali la ignora automaticamente. Per reimpostare lo stato del punto di inserimento, è consigliabile usare la [reimpostazione del canale](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). È anche consigliabile usare l'[interruzione del programma](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) per terminare una presentazione e un flusso.
6. La durata del frammento MP4 dovrebbe (SHOULD) essere costante, in modo da ridurre le dimensioni dei manifesti client. Una durata costante dei frammenti MP4 migliora anche l'approccio euristico di download del client tramite l'uso di tag ripetuti. La durata può (MAY) variare per compensare frequenze di fotogrammi costituite da valori non interi.
7. La durata del frammento MP4 dovrebbe (SHOULD) essere compresa tra 2 e 6 secondi circa.
8. I timestamp e gli indici (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` e `fragment_index`) del frammento MP4 dovrebbero (SHOULD) arrivare in ordine crescente. Benché Servizi multimediali sia resiliente alla duplicazione di frammenti, ha una capacità limitata di riordinare i frammenti in base alla sequenza temporale dei contenuti multimediali.

## <a name="4-protocol-format--http"></a>4. Formato del protocollo: HTTP
L'inserimento live basato sul formato ISO MP4 frammentato per Servizi multimediali usa una richiesta HTTP POST standard a esecuzione prolungata per trasmettere al servizio dati multimediali codificati in formato MP4 frammentato. Ogni richiesta HTTP POST invia un flusso di bit MP4 frammentato completo ("flusso"), iniziando con le caselle di intestazione (caselle **ftyp**, **Live Server Manifest Box** e **moov**) e continuando con una sequenza di frammenti (caselle **moof** e **mdat**). Per la sintassi dell'URL per la richiesta HTTP POST, vedere la sezione 9.2 di [1]. Un esempio di URL POST è: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Requisiti
Di seguito sono elencati i requisiti dettagliati:

1. Il codificatore dovrebbe (SHOULD) avviare la trasmissione inviando una richiesta HTTP POST con un "corpo" vuoto (lunghezza del contenuto pari a zero) mediante lo stesso URL di inserimento. In questo modo, il codificatore dovrebbe essere in grado di rilevare rapidamente se l'endpoint di inserimento live è valido e se sono necessarie l'autenticazione o altre condizioni. Per il protocollo HTTP, il server non è in grado di restituire una risposta HTTP finché non viene ricevuta l'intera richiesta, incluso il corpo della richiesta POST. A causa dell'esecuzione prolungata di un evento live, senza questo passaggio il codificatore potrebbe non essere in grado di rilevare alcun errore finché non viene completato l'invio di tutti i dati.
2. Il codificatore deve (MUST) gestire eventuali errori o problemi di autenticazione a causa di quanto riportato in [1]. Se in (1) è stata restituita una risposta 200, continua.
3. Il codificatore deve (MUST) avviare una nuova richiesta HTTP POST con il flusso MP4 frammentato. Il payload deve (MUST) iniziare con le caselle di intestazione, seguite dai frammenti. Tenere presente che con ogni richiesta devono (MUST) essere inviate le caselle **ftyp**, **Live Server Manifest Box** e **moov** (in questo ordine), anche nel caso in cui il codificatore debba riconnettersi perché la richiesta precedente è stata terminata prima della fine del flusso. 
4. Il codificatore deve (MUST) usare la codifica di trasferimento in blocchi per il caricamento, perché non è possibile prevedere la lunghezza dell'intero contenuto dell'evento live.
5. Al termine dell'evento, dopo aver inviato l'ultimo frammento, il codificatore deve (MUST) terminare normalmente la sequenza di messaggi della codifica di trasferimento in blocchi. La maggior parte degli stack client HTTP gestisce questo aspetto in modo automatico. Il codificatore deve (MUST) attendere che il servizio restituisca il codice di risposta finale e quindi terminare la connessione. 
6. Il codificatore non deve (MUST NOT) usare il nome`Events()` come descritto nella sezione 9.2 di [1] per l'inserimento live in Servizi multimediali.
7. Se la richiesta HTTP POST viene terminata o causa un timeout con errore TCP prima della fine del flusso, il codificatore deve (MUST) inviare un'altra richiesta POST usando una nuova connessione, seguendo i requisiti precedenti. Inoltre, il codificatore deve (MUST) inviare di nuovo i due frammenti MP4 precedenti per ogni traccia nel flusso e riprendere senza introdurre discontinuità nella sequenza temporale dei contenuti multimediali. L'invio degli ultimi due frammenti MP4 per ogni traccia impedisce eventuali perdite di dati. In altre parole, se un flusso contiene una traccia audio e una tracia video e la richiesta POST corrente non riesce, il codificatore deve riconnettersi e inviare gli ultimi due frammenti per la traccia audio, già correttamente inviati in precedenza, e gli ultimi due frammenti per la traccia video, anch'essi già inviati correttamente, in modo da garantire che non si siano verificate perdite di dati. Il codificatore deve (MUST) mantenere un buffer "anticipato" di frammenti di contenuti, che invia di nuovo quando si riconnette.

## <a name="5-timescale"></a>5. Scala cronologica
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) descrive l'utilizzo della scala cronologica per **SmoothStreamingMedia** (sezione 2.2.2.1), **StreamElement** (sezione 2.2.2.3), **StreamFragmentElement** (sezione 2.2.2.6) e **LiveSMIL** (sezione 2.2.7.3.1). Se non è presente un valore di scala cronologica, viene usato il valore predefinito 10.000.000 (10 MHz). Benché le specifiche del formato Smooth Streaming non impediscano l'utilizzo di altri valori di scala cronologica, la maggior parte delle implementazioni dei codificatori usa questo valore predefinito (10 MHz) per generare dati di inserimento Smooth Streaming. A causa della funzionalità di [creazione dinamica dei pacchetti di Servizi multimediali di Azure](media-services-dynamic-packaging-overview.md) , è consigliabile usare un valore di scala cronologica pari a 90 kHz per i flussi video e a 44,1 o 48,1 kHz per i flussi audio. Se vengono usati valori di scala cronologica differenti per flussi diversi, è necessario (MUST) inviare il valore di scala cronologica del livello di flusso. Per altre informazioni, vedere [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definizione di "flusso"
Il flusso è l'unità operativa di base nel processo di inserimento live per la composizione di presentazioni live, la gestione del failover di streaming e gli scenari di ridondanza. Il flusso è definito come un flusso di bit MP4 frammentato univoco che può contenere una singola traccia o più tracce. Una presentazione live completa può contenere uno o più flussi, a seconda della configurazione dei codificatori live. Gli esempi seguenti mostrano varie modalità d'uso dei flussi per comporre una presentazione live completa.

**Esempio:** 

Un cliente vuole creare una presentazione in streaming live che includa le velocità in bit audio/video seguenti:

Video: 3000 kbps, 1500 kbps, 750 kbps

Audio: 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Opzione 1: tutte le tracce in un flusso
In questo caso, un unico codificatore genera tutte le tracce audio/video e le aggrega in un flusso di bit MP4 frammentato. Il flusso di bit MP4 frammentato viene quindi inviato tramite una singola connessione HTTP POST. In questo esempio è presente un solo flusso per la presentazione live.

![Flussi - Una traccia][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Opzione 2: ogni traccia in un flusso separato
In questo caso, il codificatore inserisce una traccia in ogni flusso di bit MP4 frammentato e quindi registra tutti i flussi tramite più connessioni HTTP separate. Questa operazione può essere eseguita con uno o più codificatori. Dal punto di vista dell'inserimento live, questa presentazione live è composta da quattro flussi.

![Flussi - Tracce separate][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opzione 3: creare un bundle con una traccia audio e la traccia video a velocità in bit più bassa in un flusso
In questo caso, il cliente sceglie di aggregare la traccia audio alla traccia video con la velocità in bit minore in un flusso di bit MP4 frammentato, lasciando le altre due tracce video come flussi separati. 

![Flussi - Tracce audio e video][image4]

### <a name="summary"></a>Riepilogo
Questo non è un elenco completo di tutte le possibili opzioni di inserimento applicabili all'esempio. In realtà, qualsiasi forma di raggruppamento di tracce in flussi è supportata dal processo di inserimento live. Clienti e fornitori di codificatori possono scegliere le proprie implementazioni in base alla complessità di progettazione, alla capacità del codificatore e a considerazioni sui livelli di ridondanza e failover. Tuttavia, nella maggior parte dei casi è presente solo una traccia audio per l'intera presentazione live. Di conseguenza, è importante garantire l'integrità del flusso di inserimento che contiene la traccia audio. Questa considerazione determina spesso la scelta di inserire la traccia audio in un flusso indipendente (come nell'opzione 2) o di aggregarla alla traccia video con velocità in bit minore (come nell'opzione 3). Inoltre, per ottenere ridondanza e tolleranza di errore migliori, l'invio della stessa traccia audio in due flussi diversi (opzione 2 con tracce audio ridondanti) o l'aggregazione della traccia audio con almeno due delle tracce video con velocità in bit minore (opzione 3 con audio aggregato in almeno due flussi video) rappresenta la scelta consigliata per l'inserimento live in Servizi multimediali.

## <a name="7-service-failover"></a>7. Failover del servizio
Data la particolare natura del live streaming, un buon livello di supporto per il failover è essenziale per garantire la disponibilità del servizio. Servizi multimediali è progettato per gestire errori di diversi tipi, tra cui errori di rete, errori del server e problemi di archiviazione. Se usato insieme a una logica di failover adeguata dal lato del codificatore live, i clienti possono ottenere dal cloud un servizio di streaming live estremamente affidabile.

Questa sezione descrive gli scenari di failover del servizio. In questo caso, l'errore si verifica in un punto qualsiasi all'interno del servizio e si manifesta come errore di rete. Di seguito sono riportati alcuni consigli per implementare il codificatore in modo che sia in grado di gestire il failover del servizio:

1. Usare un timeout di 10 secondi per stabilire la connessione TCP. Se un tentativo di stabilire la connessione richiede più di 10 secondi, interrompere l'operazione e riprovare. 
2. Usare un timeout breve per inviare i blocchi del messaggio di richiesta HTTP. Se la durata del frammento MP4 di destinazione è N secondi, usare un timeout di invio compreso tra N e 2N secondi. Se, ad esempio, la durata del frammento MP4 è 6 secondi, usare un timeout compreso tra 6 e 12 secondi. Se si verifica un timeout, reimpostare la connessione, aprire una nuova connessione e riprendere l'inserimento del flusso con la nuova connessione. 
3. Gestire un buffer in sequenza contenente, per ogni traccia, gli ultimi due frammenti completamente inviati al servizio.  Se la richiesta HTTP POST per un flusso viene terminata o scade prima della fine del flusso, aprire una nuova connessione e iniziare un'altra richiesta HTTP POST, quindi inviare nuovamente le intestazioni del flusso e gli ultimi due frammenti per ogni traccia e riprendere il flusso senza introdurre una discontinuità nella sequenza temporale dei contenuti. In questo modo, si riduce il rischio di perdita di dati.
4. È bene che il codificatore NON limiti il numero di tentativi di riconnessione o di ripresa del flusso dopo un errore TCP.
5. Dopo un errore TCP:
  
    a. È necessario (MUST) chiudere la connessione corrente e creare una nuova connessione per una nuova richiesta HTTP POST.

    b. Il nuovo URL per HTTP POST deve (MUST) corrispondere all'URL relativo al POST iniziale.
  
    c. La nuove richiesta HTTP POST deve (MUST) includere intestazioni di flusso (caselle **ftyp**, **Live Server Manifest Box** e **moov**) identiche alle intestazioni di flusso della richiesta POST iniziale.
  
    d. Gli ultimi due frammenti inviati per ogni traccia devono essere inviati di nuovo e lo streaming deve riprendere senza introdurre discontinuità nella sequenza temporale dei contenuti multimediali. I timestamp del frammento MP4 devono aumentare costantemente, anche con richieste HTTP POST diverse.
6. Se i dati non vengono inviati a una velocità adeguata alla durata del frammento MP4, il codificatore dovrebbe (SHOULD) terminare la richiesta HTTP POST.  Una richiesta HTTP POST che non invia dati può impedire a Servizi multimediali di disconnettersi rapidamente dal codificatore in caso di aggiornamento del servizio. Per questo motivo, la richiesta HTTP POST per tracce di tipo sparse (segnale dell'annuncio) dovrebbe (SHOULD) avere breve durata e terminare non appena viene inviato il frammento di tipo sparse.

## <a name="8-encoder-failover"></a>8. Failover del codificatore
Il failover del codificatore è il secondo tipo di scenario di failover che deve essere affrontato per la distribuzione di un live streaming end-to-end. In questo scenario la condizione di errore avviene sul lato del codificatore. 

![Failover del codificatore][image5]

Di seguito è descritto il comportamento previsto in corrispondenza dell'endpoint di inserimento live quando si verifica il failover del codificatore:

1. Deve (MUST) essere creata una nuova istanza del codificatore per poter proseguire lo streaming, come mostrato nel diagramma (flusso per video a 3000 K con linea tratteggiata).
2. Il nuovo codificatore deve (MUST) usare per le richieste HTTP POST lo stesso URL dell'istanza non riuscita.
3. La richiesta POST del nuovo codificatore deve (MUST) includere le stesse caselle di intestazione del flusso MP4 frammentato presenti nell'istanza non riuscita.
4. Il nuovo codificatore deve (MUST) essere sincronizzato correttamente con tutti gli altri codificatori in esecuzione per la stessa presentazione live, in modo da generare campioni audio/video sincronizzati con i limiti di frammento allineati.
5. Il nuovo flusso deve (MUST) essere semanticamente equivalente al flusso precedente e intercambiabile a livello di intestazione e di frammento.
6. Il nuovo codificatore deve (MUST) tentare di ridurre al minimo la perdita di dati. I valori `fragment_absolute_time` e `fragment_index` dei frammenti multimediali dovrebbero (SHOULD) aumentare dall'ultimo punto in cui si è interrotto il codificatore. I valori `fragment_absolute_time` e `fragment_index` dovrebbero (SHOULD) aumentare in modo costante ma, se necessario, è possibile introdurre una discontinuità. Poiché Servizi multimediali ignora i frammenti già ricevuti ed elaborati, è preferibile sbagliare inviando di nuovo frammenti già ricevuti piuttosto che introdurre discontinuità nella sequenza temporale dei contenuti. 

## <a name="9-encoder-redundancy"></a>9. Ridondanza del codificatore
In caso di eventi live critici che richiedono livelli superiori di disponibilità e qualità dell'esperienza, è consigliabile usare codificatori ridondanti di tipo attivo-attivo per ottenere un failover efficiente senza perdita di dati.

![Ridondanza del codificatore][image6]

Come mostrato in questo diagramma, due gruppi di codificatori inviano contemporaneamente due copie di ogni flusso al servizio live. Questa configurazione è supportata perché Servizi multimediali è in grado di escludere i frammenti duplicati in base all'ID flusso e al timestamp del frammento. L'archivio e il flusso live risultanti saranno costituiti da un'unica copia di tutti i flussi, ovvero dalla migliore aggregazione possibile a partire dalle due origini. In un ipotetico caso estremo, ad esempio, purché per ogni flusso sia presente un codificatore in esecuzione (non necessariamente lo stesso) in ogni momento, il flusso live risultante dal servizio sarà continuo, senza perdita di dati. 

I requisiti per questo scenario sono molto simili a quelli descritti per il failover del codificatore, ad eccezione del fatto che il secondo set di codificatori viene eseguito contemporaneamente ai codificatori primari.

## <a name="10-service-redundancy"></a>10. Ridondanza del servizio
Per una distribuzione globale altamente ridondante, è necessario eseguire un backup tra più aree per poter gestire situazioni di emergenza nelle aree interessate. Estendendo la topologia descritta in "Ridondanza del codificatore", i clienti possono scegliere di distribuire un servizio ridondante in un'area diversa associata al secondo set di codificatori. I clienti possono anche usare un provider di rete CDN per distribuire uno strumento di gestione del traffico globale per le due distribuzioni del servizio, in modo da indirizzare correttamente il traffico client. I requisiti per i codificatori sono gli stessi di quelli per il caso di ridondanza del codificatore. L'unica eccezione è che il secondo set di codificatori deve puntare a un endpoint di inserimento live diverso. Il diagramma seguente mostra questa configurazione:

![Ridondanza del servizio][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Tipi speciali di formati di inserimento
Questa sezione descrive alcuni tipi speciali di formati di inserimento live, progettati per gestire scenari specifici.

### <a name="sparse-track"></a>Traccia di tipo sparse
Quando si trasmette una presentazione in streaming live con un'esperienza cliente avanzata, spesso è necessario trasmettere eventi sincronizzati o segnali in banda con i principali dati multimediali. Un esempio di questo scenario è l'inserimento dinamico di annunci live. La segnalazione per questo tipo di eventi è diversa dal normale streaming di flussi audio/video a causa della particolare natura di tipo sparse. In altre parole, i dati di segnalazione non hanno in genere un andamento costante e può essere molto difficile determinare l'intervallo. Il concetto di traccia di tipo sparse è stato sviluppato per consentire l'inserimento e la trasmissione di dati di segnalazione in banda.

I passaggi seguenti costituiscono un'implementazione consigliata per l'inserimento di una traccia di tipo sparse:

1. Creare un flusso di bit MP4 frammentato separato contenente solo le tracce di tipo sparse, senza tracce audio/video.
2. In **Live Server Manifest Box**, come definito nella sezione 6 di [1], usare il parametro *parentTrackName* per specificare il nome della traccia padre. Per altre informazioni, vedere la sezione 4.2.1.2.1.2 di [1].
3. In **Live Server Manifest Box** **manifestOutput** deve (MUST) essere impostato su **true**.
4. A causa della natura sparse dell'evento di segnalazione, è consigliabile quanto segue:
   
    a. All'inizio dell'evento live, il codificatore invia le caselle di intestazione iniziali al servizio, in modo che questo possa registrare la traccia di tipo sparse nel manifesto client.
   
    b. Il codificatore dovrebbe (SHOULD) terminare la richiesta HTTP POST quando i dati non sono ancora stati inviati. Una richiesta HTTP POST a esecuzione prolungata che non invia dati può impedire a Servizi multimediali di disconnettersi rapidamente dal codificatore in caso di aggiornamento del servizio o riavvio del server. In questi casi, il server dei contenuti multimediali è temporaneamente bloccato in un'operazione di ricezione nel socket.
   
    c. Durante il periodo in cui i dati di segnalazione non sono disponibili, il codificatore dovrebbe (SHOULD) chiudere la richiesta HTTP POST e inviare i dati mentre la richiesta POST è ancora attiva.

    d. Per l'invio di frammenti di tipo sparse, il codificatore può impostare esplicitamente l'intestazione Content-Length, se disponibile.

    e. Per l'invio di frammenti di tipo sparse con una nuova connessione, il codificatore dovrebbe (SHOULD) inviare prima le caselle di intestazione e quindi i nuovi frammenti. Questo consente di gestire casi in cui il failover avviene tra una connessione e l'altra e la nuova connessione di tipo sparse viene stabilita con un nuovo server che non ha mai visto prima la traccia di tipo sparse.

    f. Il frammento della traccia di tipo sparse è disponibile al client nel momento in cui il frammento della traccia padre corrispondente, con valore di timestamp uguale o maggiore, viene reso disponibile al client. Ad esempio, se il frammento di tipo sparse ha un timestamp t = 1000, dopo che il client rileva il timestamp del frammento video (presupponendo che il nome della traccia padre sia "video") maggiore o uguale a 1000, può scaricare il frammento di tipo sparse t = 1000. Tenere presente che il segnale effettivo può essere usato anche per una posizione diversa nella sequenza temporale della presentazione rispetto a quella designata. In questo esempio è possibile che il frammento di tipo sparse con t = 1000 includa un payload XML che consente di inserire un annuncio in una posizione di pochi secondi successiva.

    g. Il payload dei frammenti della traccia di tipo sparse può avere formati diversi, ad esempio, XML, testo o binario, a seconda dello scenario.

### <a name="redundant-audio-track"></a>Traccia audio ridondante
In un tipico scenario di streaming adattivo HTTP, ad esempio Smooth Streaming o DASH, spesso l'intera presentazione contiene una sola traccia audio. Diversamente dalle tracce video, in cui il client può scegliere tra più livelli di qualità in condizioni di errore, la traccia audio può costituire un singolo punto di errore se il processo di inserimento del flusso contenente la traccia audio viene interrotto. 

Per risolvere questo problema, Servizi multimediali supporta l'inserimento live di tracce audio ridondanti. L'idea è quella di poter inviare più volte la stessa traccia audio in flussi diversi. Benché il servizio registri la traccia audio una sola volta nel manifesto client, può usare tracce audio ridondanti come backup, in modo da recuperare i frammenti audio in caso di problemi nella traccia audio principale. Per poter inserire tracce audio ridondanti, il codificatore deve:

1. Creare la stessa traccia audio in più flussi di bit MP4 frammentati. Le tracce audio ridondanti devono (MUST) essere semanticamente equivalenti ai timestamp degli stessi frammenti e intercambiabili a livello di intestazione e di frammento.
2. Assicurarsi che la voce "audio" in Live Server Manifest (sezione 6 di [1]) sia uguale per tutte le tracce audio ridondanti.

Di seguito è riportata l'implementazione consigliata per le tracce audio ridondanti:

1. Inviare ogni singola traccia audio in un flusso separato. Inviare inoltre un flusso ridondante per ognuno di questi flussi di traccia audio, in cui il secondo flusso differisca dal primo solo per l'identificatore nell'URL HTTP POST: {protocollo}://{indirizzo server}/{percorso punto di pubblicazione}/Streams({identificatore}).
2. Usare flussi distinti per inviare le due velocità in bit video più basse. Ciascuno di questi flussi dovrebbe (SHOULD) inoltre contenere una copia di ogni singola traccia audio. Se, ad esempio, sono supportate più lingue, i flussi dovrebbero (SHOULD) contenere tracce audio per ogni lingua.
3. Usare istanze del server (codificatore) distinte per codificare e inviare i flussi ridondanti citati in (1) e (2). 

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
