<properties 
	pageTitle="Distribuzione di Live Streaming con Servizi multimediali di Azure" 
	description="Questo argomento fornisce una panoramica dei componenti principali interessati dallo streaming live." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="juliako"/>


#Distribuzione di eventi Live Streaming con Servizi multimediali di Azure

##Panoramica

Quando si usa Live Streaming sono generalmente necessari i seguenti componenti:

- Una fotocamera da usare per trasmettere un evento.
- Un codificatore video attivo in grado di convertire i segnali provenienti dalla fotocamera in flussi inviati a un servizio di streaming live. 
  
	Più codificatori attivi (facoltativi). In caso di eventi live critici per i quali sono richiesti livelli molto elevati di disponibilità e qualità di esperienza, è consigliabile usare codificatori ridondanti di tipo attivo-attivo in modo da conseguire un failover efficiente senza perdita di dati.
- Un servizio di streaming live che consenta di effettuare le seguenti operazioni: 
	- inserire contenuti live usando vari protocolli di streaming live (ad esempio RTMP o Smooth Streaming), 
	- codificare il flusso in flusso a velocità in bit adattiva,
	- visualizzare in anteprima il flusso live,
	- archiviare il contenuto inserito in modo che possa essere riprodotto in streaming in un secondo tempo (video on demand)
	- usare protocolli di streaming comuni (ad esempio, MPEG DASH, Smooth, HLS, HDS) per trasmettere i contenuti direttamente ai clienti o a una rete CDN (Content Delivery Network, rete per la distribuzione di contenuti) per una successiva ridistribuzione. 
	
		
**Servizi multimediali di Microsoft Azure** (AMS) offre la possibilità di inserire, visualizzare in anteprima, archiviare e fornire il contenuto in streaming live.

Quando si distribuiscono contenuti ai clienti, l'obiettivo è riuscire a trasmettere video di alta qualità a vari tipi di dispositivi in diverse condizioni di rete. Per garantire la qualità e le condizioni di rete, usare codificatori live per codificare il flusso video a più velocità in bit (velocità in bit adattiva). Per garantire la trasmissione a diversi tipi di dispositivi, usare la funzione di [creazione dinamica dei pacchetti](media-services-dynamic-packaging-overview.md) di Servizi multimediali per riorganizzare dinamicamente il flusso in nuovi pacchetti creati con protocolli diversi. Servizi multimediali supporta operazioni di trasmissione nelle seguenti tecnologie di streaming a velocità in bit adattiva: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (solo per i titolari di licenza Adobe PrimeTime/Access).

In Servizi multimediali di Azure, la gestione di tutte le funzionalità di live streaming è affidata a entità **Channel**, **Program** e **StreamingEndpoint**, compresi inserimento, formattazione, DVR, sicurezza, scalabilità e ridondanza.

Un **canale** rappresenta una pipeline per l'elaborazione di contenuto in streaming live. Attualmente, un canale può ricevere flussi di input live nel modo seguente:


- Un codificatore live locale invia un flusso a velocità in bit singola al canale abilitato per l'esecuzione della codifica live con Servizi multimediali in uno dei seguenti formati: RTP (MPEG-TS), RTMP o Smooth Streaming (MP4 frammentato). Il canale esegue quindi la codifica live del flusso in ingresso a velocità in bit singola in un flusso video a più velocità in bit (adattivo). Quando richiesto, Servizi multimediali invia il flusso ai clienti.

	La codifica di un flusso live con Servizi multimediali è in **anteprima**.
- Un codificatore live locale invia al canale un flusso **RTMP** o **Smooth Streaming** (MP4 frammentato) a più velocità in bit. È possibile usare i codificatori live seguenti che generano output in formato Smooth Streaming a più velocità in bit: Elemental, Envivio, Cisco. I codificatori live seguenti generano output in formato RTMP: Adobe Flash Live, Telestream Wirecast e transcodificatori Tricaster. I flussi inseriti passano attraverso il **canale** senza altre elaborazioni. Il codificatore live può inoltre inviare un flusso a velocità in bit singola a un canale non abilitato per la codifica live, ma questa operazione è sconsigliata. Quando richiesto, Servizi multimediali invia il flusso ai clienti.


##Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure


Il seguente diagramma mostra i componenti principali della piattaforma AMS interessati dal flusso di lavoro di streaming live dove un canale è abilitato a eseguire la codifica live con Servizi multimediali.

![Flusso di lavoro live][live-overview1]

Per altre informazioni, vedere [Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure](media-services-manage-live-encoder-enabled-channels.md).


##Uso di canali che ricevono il flusso live a più velocità in bit da codificatori locali


Il seguente diagramma mostra i componenti principali della piattaforma AMS interessati dal flusso di lavoro di streaming live.

![Flusso di lavoro live][live-overview2]

Per altre informazioni, vedere [Uso di canali che ricevono il flusso live a più velocità in bit da codificatori locali](media-services-manage-channels-overview.md).


##Argomenti correlati

[Concetti su Servizi multimediali di Azure](media-services-concepts.md)

[Specifica per l'inserimento live di un flusso MP4 frammentato con Servizi multimediali di Azure](media-services-fmp4-live-ingest-overview.md)





[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png

[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png
 

<!---HONumber=July15_HO4-->