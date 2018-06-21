---
title: Panoramica dello streaming live con Servizi multimediali di Azure | Documentazione Microsoft
description: Questo argomento offre una panoramica dello streaming live con Servizi multimediali di Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: b8c9375d8ad915200cbc8b2e1a62979fd1b7d179
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35237086"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming live con Servizi multimediali di Azure v3

Quando si distribuiscono eventi Live Streaming con Servizi multimediali di Azure, sono generalmente necessari i componenti seguenti:

* Una fotocamera da usare per trasmettere un evento.
* Un codificatore video live in grado di convertire i segnali provenienti dalla fotocamera (o da un altro dispositivo, come un portatile) in flussi inviati al servizio di streaming live di Servizi multimediali. I segnali possono includere anche segnali SCTE-35 e segnali pubblicitari. 
* Il servizio di streaming live di Servizi multimediali consente di inserire, visualizzare in anteprima, includere in un pacchetto, registrare, crittografare e trasmettere il contenuto ai clienti o a una rete CDN per un'ulteriore distribuzione.

Questo articolo offre una panoramica dettagliata e include i diagrammi dei principali componenti coinvolti nello streaming live con Servizi multimediali.

## <a name="overview-of-main-components"></a>Panoramica dei componenti principali

In Servizi multimediali i [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) sono responsabili dell'elaborazione dei contenuti in streaming live. Un LiveEvent fornisce un endpoint di input (URL di inserimento) che può essere a sua volta fornito a un codificatore live locale. Il LiveEvent riceve flussi di input live dal codificatore live in formato RTMP o Smooth Streaming e li rende disponibili per lo streaming mediante uno o più [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints). Un [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) consente di controllare la pubblicazione, la registrazione e le impostazioni della finestra DVR del flusso live. Il LiveEvent fornisce anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito. 

Servizi multimediali include la **creazione dinamica dei pacchetti**, che consente di visualizzare in anteprima e trasmettere il contenuto nei formati di streaming MPEG DASH, HLS e Smooth Streaming senza dover ricreare manualmente i pacchetti con questi formati di streaming. È possibile riprodurre con qualsiasi lettore compatibile HLS, DASH o Smooth. È anche possibile usare [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) per testare il flusso.

Servizi multimediali consente di distribuire contenuti crittografati dinamicamente (**Crittografia dinamica**) con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati.

Se si vuole, è anche possibile applicare **filtri dinamici**, che possono essere usati per controllare il numero di tracce, i formati e i bitrate che vengono inviati ai lettori. Servizi multimediali supporta anche l'inserimento di annunci.


## <a name="liveevent-types"></a>Tipi di LIveEvent

Un [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) può essere di due tipi: codifica live e pass-through. 

### <a name="live-encoding-with-media-services"></a>Codifica live con Servizi multimediali

![codifica live](./media/live-streaming/live-encoding.png)

Un codificatore live locale invia un flusso a bitrate singolo al LiveEvent abilitato per l'esecuzione della codifica live con Servizi multimediali in uno dei seguenti protocolli: RTMP o Smooth Streaming (MP4 frammentato). Il LiveEvent esegue quindi la codifica live del flusso in ingresso a bitrate singolo in un flusso video a bitrate multipli (adattivo). Quando richiesto, Servizi multimediali invia il flusso ai clienti.

Quando si crea questo tipo di LiveEvent, specificare **Basic** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.png)

Un LiveEvent pass-through è ottimizzato per i flussi live a esecuzione prolungata o la codifica live lineare 24x7 con un codificatore live locale. Il codificatore locale invia un flusso **RTMP** o **Smooth Streaming** (MP4 frammentato) a bitrate multipli al LiveEvent configurato per il recapito **pass-through**. Il recapito **pass-through** avviene quando i flussi inseriti passano attraverso i **LiveEvent** senza altre elaborazioni. 

I LiveEvent pass-through possono supportare fino a una risoluzione 4K e il pass-through HEVC se usati con il protocollo di inserimento Smooth Streaming. 

Quando si crea questo tipo di LiveEvent, specificare **None** (LiveEventEncodingType.None).

> [!NOTE]
> L'uso di un metodo pass-through è il modo più economico per realizzare lo streaming live quando si eseguono più eventi per un lungo periodo di tempo e si è già investito in codificatori locali. Vedere i dettagli sui [prezzi](https://azure.microsoft.com/pricing/details/media-services/) .
> 

## <a name="liveevent-types-comparison"></a>Confronto tra tipi di LiveEvent 

La tabella seguente mette a confronto le funzionalità dei due tipi di LiveEvent.

| Funzionalità | LiveEvent pass-through | LiveEvent Basic |
| --- | --- | --- |
| Input a bitrate singolo codificato in bitrate multipli nel cloud |No  |Sì |
| Risoluzione massima, numero di livelli |4Kp30  |720p, 6 livelli, 30 fps |
| Protocolli di input |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Prezzo |Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) e fare clic sulla scheda "Video live" |Vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/media-services/) |
| Tempo di esecuzione massimo |24 x 7 |24 x 7 |
| Supporto per l'inserimento di slate |No  |Sì |
| Supporto per annunci pubblicitari tramite API|No  |Sì |
| Supporto per annunci pubblicitari tramite SCTE35 in banda|Sì |Sì |
| Pass-through di sottotitoli CEA 608/708 |Sì |Sì |
| Possibilità di recuperare brevi fasi di stallo in feed di contributo |Sì |No (senza dati di input, il LiveEvent avvierà lo slate dopo 6 secondi)|
| Supporto per GOP di input non uniformi |Sì |No: l'input deve essere fisso (GOP di 2 secondi) |
| Supporto per input con frequenza dei fotogrammi variabile |Sì |No: l'input deve essere una frequenza di fotogrammi fissa.<br/>Sono tollerate lievi variazioni, ad esempio durante scene ad alta velocità. Il codificatore, tuttavia, non può scendere a 10 fotogrammi al secondo. |
| Arresto automatico del LiveEvent in caso di perdita del feed di input |No  |Dopo 12 ore, se nessun LiveOutput è in esecuzione |

## <a name="liveevent-states"></a>Stati del LiveEvent 

Si tratta dello stato attuale del LiveEvent. I valori possibili sono:

|Stato|DESCRIZIONE|
|---|---|
|**Stopped**| Lo stato iniziale del LiveEvent dopo la creazione (se non è stata selezionata l'opzione di avvio automatico). In questo stato non viene eseguita alcuna attività di fatturazione. In questo stato le proprietà del LiveEvent possono essere aggiornate ma lo streaming non è consentito.|
|**Avvio**| Il LiveEvent viene avviato. In questo stato non viene eseguita alcuna attività di fatturazione. In questo stato non è consentito alcun aggiornamento o streaming. Se si verifica un errore, il LiveEvent torna allo stato Interrotto.|
|**Running**| Il LiveEvent è in grado di elaborare flussi live. La fatturazione è ora attiva. È necessario interrompere il LiveEvent per sospendere la fatturazione.|
|**Arresto**| Il LiveEvent viene interrotto. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. In questo stato non è consentito alcun aggiornamento o streaming.|
|**Eliminazione in corso**| Il LiveEvent viene eliminato. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. In questo stato non è consentito alcun aggiornamento o streaming.|

## <a name="liveoutput"></a>LiveOutput

Un [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) consente di controllare la pubblicazione, la registrazione e le impostazioni della finestra DVR del flusso live. La relazione tra LiveEvent e LiveOutput è simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale (LiveEvent) è costituito da un flusso costante di contenuti, mentre un programma (LiveOutput) ha come ambito una serie di eventi programmati su tale LiveEvent.
È possibile specificare il numero di ore per cui si vuole mantenere il contenuto registrato per il LiveOutput impostando la proprietà **ArchiveWindowLength**. La proprietà **ArchiveWindowLength** indica la durata in termini di intervallo di tempo ISO 8601 della lunghezza dell'intervallo di archiviazione (videoregistratore digitale o DVR). Il valore impostato può essere compreso tra 5 minuti e 25 ore. 

La proprietà **ArchiveWindowLength** determina anche l'intervallo massimo di tempo per cui i client possono eseguire ricerche a ritroso nel tempo dalla posizione live corrente. I LiveOutput possono essere eseguiti per l'intervallo di tempo specificato, ma il contenuto che supera la durata prevista viene scartato in modo continuo. Il valore della proprietà determina anche il tempo per cui i manifesti client possono crescere.

Ogni LiveOutput è associato a un [Asset](https://docs.microsoft.com/rest/api/media/assets) e registra i dati in un contenitore nell'archiviazione di Azure collegato all'account di Servizi multimediali. Per pubblicare il LiveOutput, è necessario creare uno [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) per l'asset associato. Con questo localizzatore sarà possibile creare un URL di streaming da fornire ai client.

Un LiveEvent supporta fino a tre LiveOutput in esecuzione simultanea, quindi consente di creare più archivi dello stesso flusso in ingresso. Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Ad esempio, l'azienda ha l'esigenza di trasmettere un feed lineare live 24x7, ma si vogliono creare "registrazioni" dei programmi nel corso della giornata da offrire ai clienti come contenuto visualizzabile su richiesta.  Per questo scenario, si crea innanzitutto un LiveOutput primario, con un intervallo di archiviazione breve di 1 ora o meno per la sintonizzazione dei clienti come flusso live primario. È necessario creare uno StreamingLocator per questo LiveOutput e pubblicarlo nell'applicazione o nel sito Web come feed "Live".  Mentre il feed è in esecuzione, è possibile creare a livello di codice un secondo LiveOutput simultaneo all'inizio di una presentazione (o 5 minuti prima per fornire alcuni handle per il trimming successivo). Questo secondo LiveOutput può essere arrestato 5 minuti dopo il termine del programma o dell'evento ed è quindi possibile creare un nuovo StreamingLocator per pubblicare questo programma come asset su richiesta nel catalogo dell'applicazione.  È possibile ripetere questo processo più volte per altri limiti del programma o elementi di rilievo che si vuole condividere immediatamente come contenuto su richiesta, mentre il feed "Live" dal primo LiveOutput continua a trasmettere il feed lineare.  Inoltre, è possibile sfruttare il supporto per il filtro dinamico per tagliare la parte iniziale e la parte finale dell'archivio dal LiveOutput introdotto per motivi di "sicurezza di sovrapposizione" tra i programmi e ottenere un inizio e una fine del contenuto più accurate. Il contenuto archiviato può essere anche inviato a un'entità [Transform](https://docs.microsoft.com/rest/api/media/transforms) per la codifica o il frame di clip secondarie accurate in più formati di output da usare come diffusione ad altri servizi.

## <a name="streamingendpoint"></a>StreamingEndpoint

Dopo l'avvio del flusso nel LiveEvent, è possibile iniziare l'evento di streaming creando Asset, LiveOutput e StreamingLocator. In questo modo il flusso viene archiviato e reso disponibile agli utenti tramite [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

Quando viene creato l'account di Servizi multimediali, viene aggiunto all'account un endpoint di streaming predefinito con stato "Arrestato". Per avviare lo streaming del contenuto e sfruttare i vantaggi della creazione dinamica dei pacchetti e della crittografia dinamica, l'endpoint di streaming da cui si vuole trasmettere il contenuto deve essere nello stato In esecuzione.

## <a name="billing"></a>Fatturazione

Un LiveEvent avvia la fatturazione non appena il suo stato viene impostato su "In esecuzione". Per impedire la fatturazione da parte del LiveEvent, è necessario arrestare il LiveEvent.

> [!NOTE]
> Quando **LiveEventEncodingType** nel [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) è impostato su Basic, Servizi multimediali arresterà automaticamente qualsiasi LiveEvent ancora nello stato "In esecuzione" 12 ore dopo che il feed di input è andato perso e non ci sono più LiveOutput in esecuzione. Verrà tuttavia addebitato il tempo in cui il LiveEvent è rimasto nello stato "In esecuzione".
>

La tabella seguente illustra il mapping degli stati del LiveEvent alla modalità di fatturazione.

| Stato LiveEvent | Fatturazione? |
| --- | --- |
| Avvio in corso |No (stato temporaneo) |
| In esecuzione |SÌ |
| Stopping |No (stato temporaneo) |
| Arrestato |No  |

## <a name="next-steps"></a>Passaggi successivi

[Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
