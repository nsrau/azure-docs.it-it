---
title: Streaming live con codificatori locali che creano flussi a bitrate multipli - Azure| Microsoft Docs
description: "Questo argomento descrive come configurare un canale che riceve un flusso live a bitrate multipli da un codificatore locale. Il flusso può essere quindi distribuito alle applicazioni di riproduzione client tramite uno o più endpoint di streaming, usando uno dei protocolli di flusso adattivo seguenti: HLS, Smooth Streaming o DASH."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/23/2017
ms.author: cenkd;juliako
translationtype: Human Translation
ms.sourcegitcommit: 7d980e14776cade574fc9ef4e63aea5c91fb8fdf
ms.openlocfilehash: a5867566afc80fe7ae57b5027b5578e3144f7f07


---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>Streaming live con codificatori locali che creano flussi a bitrate multipli
## <a name="overview"></a>Panoramica
In Servizi multimediali di Azure un *canale* rappresenta una pipeline per l'elaborazione di contenuto in streaming live. Un canale riceve i flussi di input live in uno dei due modi seguenti:

* Un codificatore live locale invia un flusso RTMP o Smooth Streaming (MP4 frammentato) a bitrate multipli a un canale non abilitato per eseguire la codifica live con Servizi Multimediali. I flussi inseriti passano attraverso il canale senza altre elaborazioni. Questo metodo viene chiamato *pass-through*. È possibile usare i codificatori live seguenti che generano output in formato Smooth Streaming a bitrate multipli: Media Excel, Ateme, Imagine Communications, Envivio, Cisco ed Elemental. I codificatori live seguenti generano output in formato RTMP: Adobe Flash Media Live Encoder, Telestream Wirecast, Haivision, Teradek e TriCaster. Un codificatore live può anche inviare un flusso a bitrate singolo a un canale non abilitato per la codifica live, ma questa operazione non è consigliata. Servizi multimediali invia il flusso ai clienti che lo richiedono.

  > [!NOTE]
  > L'uso del metodo pass-through è il modo più economico per eseguire uno streaming live.


* Un codificatore live locale invia un flusso a bitrate singolo al canale abilitato per l'esecuzione della codifica live con Servizi multimediali in uno dei seguenti formati: RTP (MPEG-TS), RTMP o Smooth Streaming (MP4 frammentato). Il canale esegue quindi la codifica live del flusso in ingresso a bitrate singolo in un flusso video a bitrate multipli (adattivo). Servizi multimediali invia il flusso ai clienti che lo richiedono.

A partire da Servizi multimediali versione 2.10, quando si crea un canale è possibile specificare in che modo si vuole che il canale riceva il flusso di input. È anche possibile specificare se si vuole che il canale esegue la codifica live del flusso. Sono disponibili due opzioni:

* **Nessuno**: specificare questo valore se si prevede di usare un codificatore live locale che genera un flusso a bitrate multipli (un flusso pass-through) come output. In questo caso, il flusso in ingresso viene passato all'output senza codifica. Questo è il comportamento di un canale prima della versione 2.10. Questo argomento fornisce informazioni dettagliate sull'uso dei canali di questo tipo.
* **Standard**: scegliere questo valore se si prevede di usare Servizi multimediali per codificare il flusso live a bitrate singolo in un flusso a bitrate multipli. Si noti che se si lascia un canale di codifica live nello stato **In esecuzione**, verranno applicati addebiti. Per evitare costi orari aggiuntivi, quindi, è consigliabile arrestare immediatamente i canali in esecuzione al termine dell'evento in streaming live. Servizi multimediali invia il flusso ai clienti che lo richiedono.

> [!NOTE]
> Questo argomento illustra gli attributi dei canali non abilitati per l'esecuzione della codifica live (tipo di codifica**Nessuno** ). Per informazioni sull'uso dei canali non abilitati all'esecuzione della codifica live, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).
>
>

Il diagramma seguente rappresenta un flusso di lavoro di streaming live che usa un codificatore live locale per generare flussi in formato RTMP o MP4 frammentato (Smooth Streaming) a bitrate multipli come output.

![Flusso di lavoro live][live-overview]

## <a name="a-idscenarioacommon-live-streaming-scenario"></a><a id="scenario"></a>Scenario comune di streaming live
La procedura seguente illustra le attività relative alla creazione di applicazioni comuni di streaming live.

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che genera un flusso in formato RTMP o MP4 frammentato (Smooth Streaming) a bitrate multipli come output. Per altre informazioni, vedere l'argomento relativo a [codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).

    È anche possibile eseguire questo passaggio dopo la creazione del canale.
2. Creare e avviare un canale.

3. Recuperare l'URL di inserimento del canale.

    Il codificatore live usa l'URL di inserimento per inviare il flusso al canale.
4. Recuperare l'URL di anteprima del canale.

    Usare questo URL per verificare che il canale riceva correttamente il flusso live.
5. Creare un programma.

    Se si crea un programma tramite il portale di Azure classico, viene creato anche un asset.

    Se si usa .NET SDK o REST, è necessario creare un asset e specificarne l'uso quando si crea un programma.
6. Pubblicare l'asset associato al programma.   

    >[!NOTE]
    >Quando l'account di Servizi multimediali di Azure viene creato, un endpoint di streaming **predefinito** viene aggiunto all'account con stato **Arrestato**. L'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato **In esecuzione**.

7. Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione.

8. Facoltativamente, il codificatore live può ricevere il segnale per l'avvio di un annuncio. L'annuncio viene inserito nel flusso di output.

9. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.

10. Eliminare il programma e, facoltativamente, eliminare l'asset.     

## <a name="a-idchanneladescription-of-a-channel-and-its-related-components"></a><a id="channel"></a>Descrizione di un canale e dei relativi componenti
### <a name="a-idchannelinputachannel-input-ingest-configurations"></a><a id="channel_input"></a>Configurazioni di input (inserimento) del canale
#### <a name="a-idingestprotocolsaingest-streaming-protocol"></a><a id="ingest_protocols"></a>Protocollo di streaming di inserimento
Servizi multimediali supporta l'inserimento di feed live usando MP4 frammentato a bitrate multipli e RTMP a bitrate multipli come protocolli di streaming. Quando è selezionato il protocollo di streaming di inserimento RTMP, vengono creati due endpoint di inserimento (input) per il canale:

* **URL primario**: specifica l'URL completo dell'endpoint primario di inserimento RTMP del canale.
* **URL secondario** (facoltativo): specifica l'URL completo dell'endpoint secondario di inserimento RTMP del canale.

Usare l'URL secondario se si vuole migliorare la durabilità e la tolleranza di errore del flusso di inserimento, nonché la tolleranza di errore e il failover del codificatore, soprattutto per gli scenari seguenti.

- Push doppio del codificatore singolo agli URL primario e secondario:

    Lo scopo principale di questo scenario è fornire maggiore resilienza alle instabilità e alle fluttuazioni di rete. Alcuni codificatori RTMP non gestiscono correttamente le disconnessioni dalla rete. Quando si verifica una disconnessione dalla rete, un codificatore può interrompere la codifica e, quando la connessione verrà ristabilita, non invierà i dati memorizzati nel buffer, causando discontinuità e perdita di dati. Le disconnessioni dalla rete possono verificarsi a causa della scarsa efficienza della rete o di una manutenzione sul lato Azure. Gli URL primario e secondario riducono i problemi di rete oltre a fornire un processo di aggiornamento controllato. Ogni volta che si verifica una disconnessione dalla rete programmata, Servizi multimediali gestisce la disconnessione primaria e secondaria e fornisce una disconnessione ritardata tra le due. I codificatori possono quindi continuare a inviare i dati ed eseguire nuovamente la connessione. L'ordine delle disconnessioni può essere casuale, ma ci sarà sempre un ritardo tra gli URL primario/secondario o secondario/primario. In questo scenario il codificatore è ancora il singolo punto di guasto.

- Più codificatori ognuno dei quali effettua il push al punto dedicato:

    Questo scenario fornisce sia la ridondanza di inserimento che del codificatore. In questo scenario il codificatore&1; effettua il push all'URL primario e il codificatore&2; effettua il push all'URL secondario. In caso di errore di un codificatore, l'altro codificatore può continuare a inviare dati. La ridondanza dei dati può essere mantenuta perché Servizi multimediali non disconnette simultaneamente gli URL primario e secondario. Questo scenario presume che l'ora dei codificatori sia sincronizzata e fornisce esattamente gli stessi dati.  

- Push doppio di più codificatori agli URL primario e secondario:

    In questo scenario entrambi codificatori effettuano il push dei dati agli URL primario e secondario. Questo garantisce affidabilità e tolleranza di errore ottimali, nonché la ridondanza dei dati. Questo scenario può tollerare i guasti di entrambi i codificatori e si disconnette se un solo codificatore smette di funzionare. Presume che l'ora dei codificatori sia sincronizzata e fornisce esattamente gli stessi dati.  

Per informazioni sui codificatori RTMP live, vedere [Codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>URL di inserimento (endpoint)
Un canale fornisce un endpoint di input (URL di inserimento) specificato dall'utente nel codificatore live, in modo che il codificatore possa eseguire il push dei flussi nei canali.   

È possibile ottenere gli URL di inserimento quando si crea il canale. Per ottenere questi URL, non è necessario che il canale sia nello stato **In esecuzione** . Quando si è pronti per avviare l'esecuzione del push dei dati nel canale, il canale dovrà essere nello stato **In esecuzione** . Dopo l'avvio dell'inserimento di dati nel canale, è possibile visualizzare in anteprima il flusso tramite l'URL di anteprima.

È possibile inserire un flusso live MP4 frammentato (Smooth Streaming) tramite una connessione SSL. Per l'inserimento tramite SSL, assicurarsi di aggiornare l'URL di inserimento a HTTPS. Attualmente, non è possibile inserire flussi RTMP tramite SSL.

#### <a name="a-idkeyframeintervalakeyframe-interval"></a><a id="keyframe_interval"></a>Intervallo tra fotogrammi chiave
Quando si usa un codificatore live locale per generare un flusso a bitrate multipli, l'intervallo tra fotogrammi chiave specifica la durata Group of Pictures (GOP) usata dal codificatore esterno. Dopo la ricezione del flusso in ingresso da parte del canale, è possibile distribuire il flusso live alle applicazioni di riproduzione client in uno dei formati seguenti: Smooth Streaming, DASH (Dynamic Adaptive Streaming over HTTP) e HLS (HTTP Live Streaming). Quando si esegue lo streaming live, la creazione di pacchetti in HLS avviene sempre in modo dinamico. Per impostazione predefinita, Servizi multimediali calcola automaticamente il rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment) in base all'intervallo tra fotogrammi chiave ricevuti dal codificatore live.

La seguente tabella illustra la modalità di calcolo della durata dei segmenti:

| Intervallo tra fotogrammi chiave | Rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment) | Esempio |
| --- | --- | --- |
| Minore o uguale a 3 secondi |3:1 |Se KeyFrameInterval (o GOP) è pari a 2 secondi, il rapporto per la creazione di pacchetti dei segmenti HLS predefinito è 3 a 1. Viene creato un segmento HLS di 6 secondi. |
| da 3 a 5 secondi |2:1 |Se KeyFrameInterval (o GOP) è pari a 4 secondi, il rapporto per la creazione di pacchetti dei segmenti HLS predefinito è 2 a 1. Viene creato un segmento HLS di 8 secondi. |
| Maggiore di 5 secondi |1:1 |Se KeyFrameInterval (o GOP) è pari a 6 secondi, il rapporto per la creazione di pacchetti dei segmenti HLS predefinito è 1 a 1. Viene creato un segmento HLS di 6 secondi. |

È possibile modificare il rapporto di frammenti per segmento configurando l'output del canale e impostando FragmentsPerSegment su ChannelOutputHls.

È inoltre possibile modificare il valore dell'intervallo tra fotogrammi chiave impostando la proprietà KeyFrameInterval su ChanneInput. Se si imposta in modo esplicito il valore KeyFrameInterval, il rapporto FragmentsPerSegment per la creazione di pacchetti dei segmenti HLS viene calcolato usando le regole descritte in precedenza.  

Se si impostano in modo esplicito i valori KeyFrameInterval e FragmentsPerSegment, Servizi multimediali userà i valori impostati dall'utente.

#### <a name="allowed-ip-addresses"></a>Indirizzi IP consentiti
È possibile definire gli indirizzi IP autorizzati a pubblicare video in questo canale. È possibile specificare un indirizzo IP consentito come uno dei seguenti:

* Un singolo indirizzo IP, ad esempio, 10.0.0.1
* Un intervallo di indirizzi IP che usa un indirizzo IP e una subnet mask CIDR, ad esempio, 10.0.0.1/22
* Un intervallo di indirizzi IP che usa un indirizzo IP e una subnet mask decimale puntata, ad esempio, 10.0.0.1(255.255.252.0)

Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.

### <a name="channel-preview"></a>Anteprima del canale
#### <a name="preview-urls"></a>URL di anteprima
I canali forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito.

È possibile ottenere l'URL di anteprima quando si crea il canale. Per ottenere l'URL, non è necessario che il canale sia nello stato **In esecuzione** . Dopo l'avvio dell'inserimento di dati da parte del canale, è possibile visualizzare in anteprima il flusso.

Attualmente il flusso di anteprima può essere distribuito solo in formato MP4 frammentato (Smooth Streaming), indipendentemente dal tipo di input specificato. È possibile usare il lettore [Smooth Streaming Health Monitor](http://smf.cloudapp.net/healthmonitor) per testare il flusso uniforme. Per visualizzare il flusso, è possibile anche usare un lettore ospitato nel portale di Azure classico.

#### <a name="allowed-ip-addresses"></a>Indirizzi IP consentiti
È possibile definire gli indirizzi IP autorizzati a connettersi all'endpoint di anteprima. Se non viene specificato alcun indirizzo IP, sarà consentito qualsiasi indirizzo IP. È possibile specificare un indirizzo IP consentito come uno dei seguenti:

* Un singolo indirizzo IP, ad esempio, 10.0.0.1
* Un intervallo di indirizzi IP che usa un indirizzo IP e una subnet mask CIDR, ad esempio, 10.0.0.1/22
* Un intervallo di indirizzi IP che usa un indirizzo IP e una subnet mask decimale puntata, ad esempio, 10.0.0.1(255.255.252.0)

### <a name="channel-output"></a>Output del canale
Per informazioni sull'output del canale, vedere la sezione [Intervallo tra fotogrammi chiave](#keyframe_interval).

### <a name="channel-managed-programs"></a>Programmi gestiti dal canale
Un canale è associato a programmi che consentono di controllare la pubblicazione e l'archiviazione di segmenti in un flusso live. I programmi sono gestiti dai canali. La relazione tra queste due entità è molto simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale è costituito da un flusso costante di contenuti, mentre un programma ha come ambito una serie di eventi programmati sul canale.

È possibile specificare il numero di ore per cui si vuole mantenere il contenuto registrato per il programma impostando il valore **Intervallo di archiviazione** . Il valore impostato può essere compreso tra 5 minuti e 25 ore. La lunghezza della finestra di archiviazione determina anche il limite di tempo per cui i client possono eseguire ricerche a ritroso nel tempo dalla posizione live corrente. I programmi possono essere eseguiti per la quantità di tempo specificata, ma il contenuto che va oltre la durata prevista viene scartato in modo continuo. Il valore della proprietà determina anche il tempo per cui i manifesti client possono crescere.

Ogni programma è associato a un asset che archivia il contenuto trasmesso nel flusso. Un asset viene mappato a un contenitore BLOB in blocchi nell'account di archiviazione di Azure e i file nell'asset vengono archiviati come BLOB in tale contenitore. Per pubblicare il programma in modo che possa essere visualizzato dai clienti, è necessario creare un localizzatore OnDemand per l'asset associato. È possibile usare questo localizzatore per creare un URL di streaming da fornire ai client.

Un canale supporta fino a tre programmi in esecuzione simultanea, in modo da poter creare più archivi dello stesso flusso in ingresso. È possibile pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Si consideri ad esempio uno scenario in cui un'azienda richiede l'archiviazione di 6 ore di un programma e la trasmissione solo degli ultimi 10 minuti. A tale scopo, è necessario creare due programmi in esecuzione contemporaneamente. Un programma è impostato per l'archiviazione di 6 ore dell'evento, ma non viene pubblicato. L'altro programma è impostato per l'archiviazione di 10 minuti e viene pubblicato.

Non riutilizzare i programmi esistenti per nuovi eventi. Al contrario, creare un nuovo programma per ogni evento. Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.

Per eliminare il contenuto archiviato, arrestare ed eliminare il programma e quindi eliminare l'asset associato. Un asset non può essere eliminato se viene usato da un programma. È necessario eliminare prima di tutto il programma.

Anche dopo l'arresto e l'eliminazione del programma, gli utenti possono trasmettere in streaming il contenuto archiviato sotto forma di video su richiesta, fino all'eliminazione dell'asset. Se si vuole mantenere il contenuto archiviato ma non averlo disponibile per lo streaming, eliminare il localizzatore di streaming.

## <a name="a-idstatesachannel-states-and-billing"></a><a id="states"></a>Stati del canale e fatturazione
I valori possibili per lo stato corrente di un canale includono i seguenti:

* **Arrestato**: stato iniziale del canale dopo la creazione. In questo stato le proprietà del canale possono essere aggiornate ma lo streaming non è consentito.
* **Avvio**: è in corso l'avvio del canale. In questo stato non è consentito alcun aggiornamento o streaming. Se si verifica un errore, il canale torna allo stato **Arrestato**.
* **In esecuzione**: il canale può elaborare flussi live.
* **Arresto**: è in corso l'arresto del canale. In questo stato non è consentito alcun aggiornamento o streaming.
* **Eliminazione**: è in corso l'eliminazione del canale. In questo stato non è consentito alcun aggiornamento o streaming.

La tabella seguente illustra il mapping degli stati del canale alla modalità di fatturazione.

| Stato del canale | Indicatori dell'interfaccia utente del portale | Fatturato? |
| --- | --- | --- | --- |
| **Avvio** |**Avvio** |No (stato temporaneo) |
| **Running** |**Pronto** (nessun programma in esecuzione)<p><p>oppure<p>**Streaming** (almeno un programma in esecuzione) |Sì |
| **Arresto** |**Arresto** |No (stato temporaneo) |
| **Stopped** |**Stopped** |No |

## <a name="a-idccandadsaclosed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Sottotitoli codificati e inserimento di annunci
La tabella seguente illustra gli standard supportati per i sottotitoli codificati e l'inserimento di annunci.

| Standard | Note |
| --- | --- |
| CEA-708 e EIA-608 (708/608) |CEA-708 ed EIA-608 sono standard per i sottotitoli codificati per Stati Uniti e Canada.<p><p>Attualmente, i sottotitoli sono supportati solo se eseguiti nel flusso di input codificato. È necessario usare un codificatore multimediale live che possa inserire 608 o 708 sottotitoli nel flusso codificato che viene inviato ai Servizi multimediali. Servizi multimediali fornisce agli utenti il contenuto con i sottotitoli inseriti. |
| TTML all'interno di file ismt (tracce di testo Smooth Streaming) |La creazione dinamica dei pacchetti di Servizi multimediali consente ai client di trasmettere in streaming il contenuto in uno dei formati seguenti: DASH, HLS o Smooth Streaming. Se si inserisce il formato MP4 frammentato (Smooth Streaming) con sottotitoli all'interno dei file con estensione ismt (tracce di testo Smooth Streaming), è possibile distribuire il flusso solo ai client Smooth Streaming. |
| SCTE-35 |SCTE-35 è un sistema di segnalazione digitale usato per predisporre l'inserimento di annunci pubblicitari. I ricevitori a valle usano il segnale per congiungere l'annuncio al flusso per l'intervallo assegnato. SCTE-35 deve essere inviato come una traccia di tipo sparso nel flusso di input.<p><p>Al momento l'unico formato di flusso di input supportato che trasporta i segnali degli annunci è il formato MP4 frammentato (Smooth Streaming). Smooth Streaming è anche l'unico formato di output supportato. |

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>Considerazioni
Quando si usa un codificatore live locale per l'invio di un flusso a bitrate multipli in un canale, si applicano i vincoli seguenti:

* Assicurarsi che sia disponibile connettività Internet sufficiente per inviare dati ai punti di inserimento.
* Per usare un URL di inserimento secondario, è necessaria larghezza di banda aggiuntiva.
* Il flusso a bitrate multipli in ingresso può avere un massimo di 10 livelli di qualità video e di 5 tracce audio.
* Il bitrate più elevato per ogni livello di qualità video deve essere inferiore a 10 Mbps.
* L'aggregato della velocità in bit media per tutti i flussi video e audio deve essere inferiore a 25 Mbps.
* Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input.
* È possibile inserire un bitrate singolo nel canale, ma poiché il canale non elabora il flusso, anche le applicazioni client riceveranno un flusso a bitrate singolo. Questa opzione non è consigliata.

Ecco altre considerazioni relative all'uso dei canali e dei componenti correlati:

* Assicurarsi di chiamare il metodo **Reset** sul canale ogni volta che si riconfigura il codificatore live. Prima di reimpostare il canale, è necessario arrestare il programma. Dopo aver reimpostato il canale, riavviare il programma.
* È possibile interrompere un canale solo quando si trova nello stato **In esecuzione** e tutti i programmi nel canale sono stati arrestati.
* Per impostazione predefinita, è possibile aggiungere solo cinque canali all'account di Servizi multimediali. Per altre informazioni, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).
* Il costo viene addebitato solo quando il canale è nello stato **In esecuzione** . Per altre informazioni, vedere la sezione [Stati del canale e fatturazione](media-services-live-streaming-with-onprem-encoders.md#states).

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Argomenti correlati
[Specifica per l'inserimento live di un flusso MP4 frammentato con Servizi multimediali di Azure](media-services-fmp4-live-ingest-overview.md)

[Panoramica e scenari comuni di Servizi multimediali di Azure](media-services-overview.md)

[Concetti relativi ai Servizi multimediali](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png



<!--HONumber=Feb17_HO3-->


