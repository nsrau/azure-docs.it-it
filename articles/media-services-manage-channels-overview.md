<properties 
	pageTitle="Uso di codificatori locali per l'invio di un flusso live a più velocità in bit a un canale" 
	description="Questo argomento descrive come configurare un canale che riceve un flusso live a più velocità in bit da un codificatore locale. Il flusso può essere quindi distribuito alle applicazioni di riproduzione client tramite uno o più endpoint di streaming, usando uno dei seguenti protocolli di streaming adattivi: HLS, Smooth Stream, MPEG DASH e HDS." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="04/06/2015" 
	ms.author="juliako"/>

# Uso di codificatori locali per l'invio di un flusso live a più velocità in bit a un canale

## Informazioni generali

In Servizi multimediali di Azure un **canale** rappresenta una pipeline per l'elaborazione dei contenuti in streaming live. Un **programma** consente di controllare la pubblicazione e l'archiviazione di segmenti in un flusso live. I programmi sono gestiti dai canali. La relazione tra queste due entità è molto simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale è costituito da un flusso costante di contenuti, mentre un programma ha come ambito una serie di eventi programmati sul canale. 

Quando si usa Live Streaming, uno dei componenti coinvolti nel flusso di lavoro è un codificatore video live che converte i segnali dalla videocamera in flussi inviati a un servizio di streaming live. Un **canale** può ricevere flussi live di input da un codificatore live locale che genera un flusso in formato RTMP o MP4 frammentato (Smooth Streaming) a più velocità in bit. È possibile usare i seguenti codificatori live che generano flussi Smooth Streaming: Elemental, Envivio e Cisco.  I seguenti codificatori live generano flussi in formato RTP: transcodificatori Adobe Flash Live, Telestream Wirecast e Tricaster. I flussi inseriti passano attraverso il **canale** senza ulteriori elaborazioni. Il codificatore live può inoltre inserire un flusso a velocità in bit singola, ma poiché il flusso non viene elaborato, anche le applicazioni client riceveranno un flusso a velocità in bit singola (scelta non consigliata). Una volta pubblicato, il contenuto ricevuto può essere trasmesso in streaming alle applicazioni di riproduzione client mediante uno o più **endpoint di streaming**. Per riprodurre il flusso, è possibile usare i seguenti protocolli di streaming adattivi: HLS, MPEG DASH, Smooth Stream e HDS. 

Un **endpoint di streaming** rappresenta un servizio di streaming in grado di distribuire contenuti direttamente a un'applicazione di lettore client o a una rete CDN per la successiva distribuzione. Il flusso in uscita da un servizio endpoint di streaming può essere costituito da un flusso live o da un asset video on demand associato all'account di Servizi multimediali. È possibile inoltre controllare la capacità del servizio endpoint di streaming in modo da poter gestire esigenze di larghezza di banda crescenti modificando le unità riservate di streaming. È consigliabile allocare almeno un'unità riservata per le applicazioni in un ambiente di produzione. Per altre informazioni, vedere l'articolo relativo alla [scalabilità di un servizio multimediale](media-services-manage-origins.md#scale_streaming_endpoints).

Il seguente diagramma rappresenta un flusso di lavoro di streaming live che usa un codificatore live locale per generare flussi in formato RTMP o MP4 frammentato (Smooth Streaming) a più velocità in bit. 

![Flusso di lavoro live][live-overview]

Questo argomento fornisce informazioni generali su un canale che riceve un flusso live a più velocità in bit da un codificatore locale. L'argomento illustra inoltre i componenti relativi ai canali.

## <a id="scenarios"></a>Scenario comune di streaming live

I seguenti passaggi descrivono le attività relative alla creazione di applicazioni comuni di streaming live.

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che genera un flusso in formato RTMP o MP4 frammentato (Smooth Streaming) a più velocità in bit. Per altre informazioni, vedere l'argomento relativo ai [codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Questa operazione può essere eseguita anche dopo la creazione del canale.

1. Creare e avviare un canale.
1. Recuperare l'URL di inserimento del canale. 

	L'URL di inserimento viene usato dal codificatore live per inviare il flusso al canale.
1. Recuperare l'URL di anteprima del canale. 

	Usare questo URL per verificare che il canale riceva correttamente il flusso live.

2. Creare un asset.
3. Se si desidera che l'asset sia crittografato in modo dinamico durante la riproduzione, seguire questa procedura: 	
	
	1. 	Creare una chiave simmetrica. 
	1. 	Configurare i criteri di autorizzazione della chiave simmetrica.
1. Configurare i criteri di distribuzione degli asset, usati per la creazione dinamica dei pacchetti e la crittografia dinamica.
3. Creare un programma e specificare di usare l'asset creato.
1. Pubblicare l'asset associato al programma creando un localizzatore OnDemand.  

	Assicurarsi di disporre di almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere il contenuto in streaming.
1. Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione.
1. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.
1. Eliminare il programma e, facoltativamente, eliminare l'asset.   

La sezione relativa alle [attività di streaming live](media-services-manage-channels-overview.md#tasks) include collegamenti ad argomenti che illustrano come eseguire le attività descritte in precedenza.


## <a id="channel_input"></a>Configurazioni di input (inserimento) del canale

### <a id="ingest_protocols"></a>Protocollo di streaming di inserimento

Servizi multimediali supporta l'inserimento di feed live usando il seguente protocollo di streaming: 

- **MP4 frammentato a più velocità in bit**
 
- **RTMP a più velocità in bit** 

	Quando è selezionato il protocollo di streaming di inserimento **RTMP**, vengono creati due endpoint di inserimento (input) per il canale: 
	
	**URL principale**: specifica l'URL completo dell'endpoint primario di inserimento RTMP del canale.

	**URL secondario**(facoltativo): specifica l'URL completo dell'endpoint secondario di inserimento RTMP del canale. 

	Usare l'URL secondario per migliorare la durabilità e la tolleranza di errore del flusso di inserimento, nonché la tolleranza di errore e il failover del codificatore. 
	
	- Per migliorare la durabilità e la tolleranza di errore di inserimento:
		
		Usare un codificatore per inviare gli stessi dati agli URL di inserimento primario e secondario. La maggior parte dei codificatori RTMP (ad esempio, Flash Media Encoder o Wirecast) offre la possibilità di usare URL primari e secondari.

	- Per gestire la tolleranza di errore e il failover del codificatore:
		
		Usare più codificatori per generare gli stessi dati e inviarli agli URL di inserimento primario e secondario. Questo approccio migliora la durabilità di inserimento e la disponibilità elevata del codificatore. NOTA: il codificatore deve supportare scenari di disponibilità elevata e deve disporre della sincronizzazione interna di data e ora. Per altre informazioni, consultare il manuale del codificatore.
	
 	Per usare l'URL di inserimento secondario, è necessaria larghezza di banda aggiuntiva. 

Si noti che è possibile inserire una velocità in bit singola nel canale, ma poiché il flusso non viene elaborato dal canale, anche le applicazioni client riceveranno un flusso a velocità in bit singola (scelta non consigliata).

Per informazioni sui codificatori RTMP live, vedere l'argomento relativo ai [codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).

Tenere presente quanto segue:

- Assicurarsi che sia disponibile connettività Internet sufficiente per inviare dati ai punti di inserimento. 
- Il flusso a più velocità in bit in ingresso può avere un massimo di 10 livelli di qualità video e di 5 tracce audio.
- La velocità in bit media più elevata per ciascuno dei livelli di qualità video deve essere inferiore a 10 Mbps.
- L'aggregato della velocità in bit media per tutti i flussi video e audio deve essere inferiore a 25 Mbps.
- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input. 


### URL di inserimento (endpoint) 

Un canale fornisce un endpoint di input (URL di inserimento) specificato dall'utente nel codificatore live, in modo che il codificatore possa eseguire il push dei flussi nei canali.   

È possibile ottenere gli URL di inserimento quando si crea il canale. Per ottenere questi URL, non è necessario che il canale sia nello stato avviato. Quando si è pronti per avviare l'esecuzione del push dei dati nel canale, è necessario avviare il canale. Una volta avviato l'inserimento di dati nel canale, è possibile visualizzare in anteprima il flusso tramite l'URL di anteprima.

È possibile inserire un flusso live MP4 frammentato (Smooth Streaming) tramite una connessione SSL. Per l'inserimento tramite SSL, assicurarsi di aggiornare l'URL di inserimento a HTTPS. Attualmente, non è possibile inserire flussi RTMP tramite SSL. 

### <a id="keyframe_interval"></a>Intervallo tra fotogrammi chiave

Quando si usa un codificatore live locale per generare un flusso a più velocità in bit, l'intervallo tra fotogrammi chiave specifica la durata Group of Pictures (GOP) usata dal codificatore esterno. Una volta ricevuto il flusso in ingresso dal canale, è possibile distribuire il flusso live nelle applicazioni di riproduzione client in uno dei seguenti formati: Smooth Streaming, DASH e HLS. Quando si esegue lo streaming live, la creazione di pacchetti in HLS avviene sempre in modo dinamico. Per impostazione predefinita, Servizi multimediali calcola automaticamente il rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment) in base all'intervallo tra fotogrammi chiave, noto anche come Group of Pictures (GOP), ricevuti dal codificatore live. 

La seguente tabella illustra la modalità di calcolo della durata dei segmenti:

<table border="1">
<tr><th>Intervallo tra fotogrammi chiave</th><th>Rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment)</th><th>Esempio</th></tr>
<tr><td>minore o uguale a 3 secondi</td><td>3:1</td><td>Se il valore KeyFrameInterval (o GOP) è pari a 2 secondi, il rapporto predefinito per la creazione di pacchetti dei segmenti HLS sarà di 3 a 1, che creerà un segmento HLS di 6 secondi.</td></tr>
<tr><td>da 3 a 5 secondi</td><td>2:1</td><td>Se il valore KeyFrameInterval (o GOP) è pari a 4 secondi, il rapporto predefinito per la creazione di pacchetti dei segmenti HLS sarà di 2 a 1, che creerà un segmento HLS di 8 secondi.</td></tr>
<tr><td>maggiore di 5 secondi</td><td>1:1</td><td>Se il valore KeyFrameInterval (o GOP) è pari a 6 secondi, il rapporto predefinito per la creazione di pacchetti dei segmenti HLS sarà di 1 a 1, che creerà un segmento HLS di 6 secondi.</td></tr>
</table>

È possibile modificare il rapporto di frammenti per segmento configurando l'output del canale e impostando FragmentsPerSegment su ChannelOutputHls. 

È inoltre possibile modificare il valore dell'intervallo tra fotogrammi chiave impostando la proprietà KeyFrameInterval su ChanneInput. 

Se si imposta in modo esplicito il valore KeyFrameInterval, il rapporto FragmentsPerSegment per la creazione di pacchetti dei segmenti HLS viene calcolato usando le regole descritte in precedenza.  

Se si impostano in modo esplicito i valori KeyFrameInterval e FragmentsPerSegment, Servizi multimediali userà i valori impostati dall'utente. 


### Indirizzi IP consentiti

È possibile definire gli indirizzi IP autorizzati a pubblicare video in questo canale. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio '10.0.0.1(255.255.252.0)'). 

Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.

## Anteprima del canale 

### URL di anteprima

I canali forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito.

È possibile ottenere l'URL di anteprima quando si crea il canale. Per ottenere l'URL, non è necessario che il canale sia nello stato avviato. 

Una volta che il canale inizia a inserire i dati, è possibile visualizzare in anteprima il flusso.

Si noti che attualmente il flusso di anteprima può essere distribuito solo in formato MP4 frammentato (Smooth Streaming), indipendentemente dal tipo di input specificato. Per testare il flusso Smooth Streaming, è possibile usare il lettore [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor). Per visualizzare il flusso, è inoltre possibile usare un lettore ospitato nel portale di gestione di Azure.


### Indirizzi IP consentiti

È possibile definire gli indirizzi IP autorizzati a connettersi all'endpoint di anteprima. Se non viene specificato alcun indirizzo IP, sarà consentito qualsiasi indirizzo IP. È possibile specificare gli indirizzi IP consentiti come un singolo indirizzo IP (ad esempio '10.0.0.1'), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio '10.0.0.1/22') o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio '10.0.0.1(255.255.252.0)').

## Output del canale

Per altre informazioni, vedere la sezione relativa all'[impostazione di un intervallo tra fotogrammi chiave](#keyframe_interval) .


## Programmi del canale

Un canale è associato a programmi che consentono di controllare la pubblicazione e l'archiviazione di segmenti in un flusso live. I programmi sono gestiti dai canali. La relazione tra queste due entità è molto simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale è costituito da un flusso costante di contenuti, mentre un programma ha come ambito una serie di eventi programmati sul canale.

È possibile specificare il numero di ore per cui si vuole mantenere il contenuto registrato per il programma impostando la lunghezza della **finestra di archiviazione**. Il valore impostato può essere compreso tra 5 minuti e 25 ore. La lunghezza della finestra di archiviazione determina anche il limite di tempo per cui i client possono eseguire ricerche a ritroso nel tempo dalla posizione live corrente. I programmi possono essere eseguiti per la quantità di tempo specificata, ma il contenuto che va oltre la durata prevista viene scartato in modo continuo. Il valore della proprietà determina anche il tempo per cui i manifesti client possono crescere.

Ogni programma è associato a un asset. Per pubblicare il programma, è necessario creare un localizzatore OnDemand per l'asset associato. Con questo localizzatore sarà possibile creare un URL di streaming da fornire ai client.

Un canale supporta fino a tre programmi in esecuzione simultanea, in modo da poter creare più archivi dello stesso flusso in ingresso. Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Si consideri ad esempio uno scenario in cui un'azienda richiede l'archiviazione di 6 ore di un programma e la trasmissione solo degli ultimi 10 minuti. A tale scopo, è necessario creare due programmi in esecuzione contemporaneamente. Un programma è impostato per l'archiviazione di 6 ore dell'evento, ma non viene pubblicato. L'altro programma è impostato per l'archiviazione di 10 minuti e viene pubblicato.

Non riutilizzare i programmi esistenti per nuovi eventi. Creare e avviare invece un nuovo programma per ogni evento, come descritto nella sezione relativa alla programmazione di applicazioni Live Streaming.

Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento. 

Per eliminare il contenuto archiviato, arrestare ed eliminare il programma e quindi eliminare l'asset associato. Un asset non può essere eliminato se è usato da un programma. Per farlo, eliminare prima il programma. 

Anche dopo l'arresto e l'eliminazione del programma, gli utenti possono trasmettere in streaming il contenuto archiviato sotto forma di video on demand, fintanto che non si elimina l'asset.

Se si desidera mantenere il contenuto archiviato ma non averlo disponibile per lo streaming, eliminare il localizzatore di streaming.

## Stato del canale

Lo stato attuale del canale. I valori possibili sono:

- Interrotto. Lo stato iniziale del canale dopo la creazione. In questo stato le proprietà del canale possono essere aggiornate ma lo streaming non è consentito.
- Avvio. È in corso l'avvio del canale. In questo stato non è consentito alcun aggiornamento o streaming. Se si verifica un errore, il canale torna allo stato Interrotto.
- In esecuzione. Il canale è in grado di elaborare flussi live.
- Interruzione. È in corso l'interruzione del canale. In questo stato non è consentito alcun aggiornamento o streaming.
- Eliminazione. È in corso l'eliminazione del canale. In questo stato non è consentito alcun aggiornamento o streaming. 

## Sottotitoli codificati e inserimento di annunci 

La seguente tabella illustra i sottotitoli codificati supportati e gli standard per l'inserimento di annunci.

<table border="1">
<tr><th>Standard</th><th>Note</th></tr>
<tr><td>CEA-708 e EIA-608 (708/608)</td><td>CEA-708 e EIA-608 sono standard per i sottotitoli codificati per Stati Uniti e Canada.<br/>Attualmente, i sottotitoli sono supportati solo se trasmessi dal flusso di input codificato. È necessario usare un codificatore multimediale live che possa inserire 608 o 708 sottotitoli nel flusso codificato che viene inviato ai Servizi multimediali. Servizi multimediali fornirà agli utenti il contenuto con i sottotitoli inseriti.</td></tr>
<tr><td>TTML all'interno di file ismt (tracce di testo Smooth Streaming)</td><td>La creazione dinamica di pacchetti di Servizi multimediali consente ai client di trasmettere in streaming il contenuto in uno dei seguenti formati: MPEG DASH, HLS o Smooth Streaming. Se si inserisce il formato MP4 frammentato (Smooth Streaming) con sottotitoli all'interno dei file con estensione ismt (tracce di testo Smooth Streaming), sarà possibile distribuire il flusso solo ai client Smooth Streaming.</td></tr>
<tr><td>SCTE-35</td><td>Sistema di segnalazione digitale usato per predisporre l'inserimento di annunci pubblicitari. I ricevitori a valle usano il segnale per congiungere l'annuncio al flusso per l'intervallo assegnato. I segnali SCTE-35 devono essere inviati al flusso di input come tracce di tipo sparse.<br/>Si noti che al momento l'unico formato di flusso di input supportato che trasporta i segnali degli annunci è il formato MP4 frammentato (Smooth Streaming). Smooth Streaming è anche l'unico formato di output supportato.</td></tr>
</table>

## Considerazioni

Quando si usa un codificatore live locale per l'invio di un flusso a più velocità in bit in un canale, si applicano i seguenti vincoli:

- Assicurarsi che sia disponibile connettività Internet sufficiente per inviare dati ai punti di inserimento. 
- Il flusso a più velocità in bit in ingresso può avere un massimo di 10 livelli di qualità video e di 5 tracce audio.
- La velocità in bit media più elevata per ciascuno dei livelli di qualità video deve essere inferiore a 10 Mbps.
- L'aggregato della velocità in bit media per tutti i flussi video e audio deve essere inferiore a 25 Mbps.
- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input. 


Altre considerazioni relative all'uso dei canali e dei componenti correlati:


- Il costo viene addebitato solo quando il canale è in stato di esecuzione.
- Assicurarsi di chiamare il metodo **Reset** sul canale ogni volta che si riconfigura il codificatore live. Prima di reimpostare il canale, è necessario arrestare il programma. Dopo aver reimpostato il canale, riavviare il programma. 
- È possibile interrompere un canale solo quando si trova nello stato In esecuzione e tutti i programmi nel canale sono stati arrestati.
- Per impostazione predefinita, è possibile aggiungere solo cinque canali all'account di Servizi multimediali. Per altre informazioni, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).
- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input. 

## <a id="tasks"></a>Attività relative allo streaming live

### Configurare il computer

Per informazioni su come configurare il computer, vedere [Configurare il computer](media-services-set-up-computer.md).

### Configurare gli endpoint di streaming

Per informazioni generali sugli endpoint di streaming e sulla relativa modalità di gestione, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md)

### Usare codificatori live locali per generare un flusso a più velocità in bit in un canale

Per altre informazioni, vedere [Uso di codificatori di terze parti con Servizi multimediali di Azure](https://msdn.microsoft.com/library/azure/dn783464.aspx).

### Gestire canali, programmi e asset
Per una panoramica dettagliata, vedere l'argomento relativo alla [gestione di programmi e canali](media-services-manage-channels-overview.md).

Per alcuni esempi, scegliere **Portale**, **.NET**, **API REST**.

[AZURE.INCLUDE [media-services-selector-manage-channels](../includes/media-services-selector-manage-channels.md)]

### Configurare i criteri di distribuzione degli asset

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

### Creare una chiave simmetrica

Creare una chiave simmetrica con cui si desidera crittografare l'asset usando **.NET** o l'**API REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

### Configurare i criteri di autorizzazione della chiave simmetrica 

Configurare la protezione dei contenuti e i criteri di autorizzazione della chiave simmetrica usando **.NET** o l'**API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]


### Pubblicare asset

Pubblicare asset, mediante la creazione di localizzatori, usando il **portale di gestione di Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


### Abilitare la rete CDN di Azure

Servizi multimediali supporta l'integrazione con la rete CDN di Azure. Per informazioni su come abilitare la rete CDN di Azure, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md#enable_cdn).

### Scalare un account di Servizi multimediali

È possibile scalare **Servizi multimediali** specificando il numero di **unità riservate di streaming** di cui eseguire il provisioning nell'account in uso. 

Per informazioni su come scalare le unità di streaming, vedere le sezioni relative alla [scalabilità di unità di streaming](media-services-manage-origins.md#scale_streaming_endpoints.md).


[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png

<!--HONumber=52-->