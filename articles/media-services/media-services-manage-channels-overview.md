<properties 
	pageTitle="Uso di canali che ricevono il flusso live a più velocità in bit da codificatori locali" 
	description="Questo argomento descrive come configurare un canale che riceve un flusso live a più velocità in bit da un codificatore locale. Il flusso può essere quindi distribuito alle applicazioni di riproduzione client tramite uno o più endpoint di streaming, usando uno dei seguenti protocolli di streaming adattivi: HLS, Smooth Stream, MPEG DASH, HDS." 
	services="media-services" 
	documentationCenter="" 
	authors="cenkdin,Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="12/15/2015"  
	ms.author="juliako"/>

#Uso di canali che ricevono il flusso live a più velocità in bit da codificatori locali

##Panoramica

In Servizi multimediali di Azure un **canale** rappresenta una pipeline per l'elaborazione di contenuto in streaming live. Un **canale** riceve i flussi di input live nel modo seguente:

- Un codificatore live locale invia al canale un flusso **RTMP** o **Smooth Streaming** (MP4 frammentato) a più velocità in bit. È possibile usare i codificatori live seguenti che generano output in formato Smooth Streaming a più velocità in bit: Elemental, Envivio, Cisco. I codificatori live seguenti generano output in formato RTMP: Adobe Flash Live, Telestream Wirecast e transcodificatori Tricaster. I flussi inseriti passano attraverso il **canale** senza altre elaborazioni. Il codificatore live può anche inviare un flusso a velocità in bit singola, ma questa operazione è sconsigliata. Quando richiesto, Servizi multimediali invia il flusso ai clienti.


Il seguente diagramma rappresenta un flusso di lavoro di streaming live che usa un codificatore live locale per generare flussi in formato RTMP o MP4 frammentato (Smooth Streaming) a più velocità in bit.

![Flusso di lavoro live][live-overview]

Questo argomento illustra quanto segue:

- [Scenario comune di streaming live](media-services-manage-channels-overview.md#scenario)
- [Descrizione di un canale e dei relativi componenti](media-services-manage-channels-overview.md#channel)
- [Considerazioni](media-services-manage-channels-overview.md#considerations)
- [Attività relative allo streaming live](media-services-manage-channels-overview.md#tasks)

##<a id="scenario"></a>Scenario comune di streaming live
I seguenti passaggi descrivono le attività relative alla creazione di applicazioni comuni di streaming live.

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che genera un flusso in formato RTMP o MP4 frammentato (Smooth Streaming) a più velocità in bit. Per altre informazioni, vedere l'argomento relativo a [codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Questa operazione può essere eseguita anche dopo la creazione del canale.

1. Creare e avviare un canale.
1. Recuperare l'URL di inserimento del canale. 

	L'URL di inserimento viene usato dal codificatore live per inviare il flusso al canale.
1. Recuperare l'URL di anteprima del canale. 

	Usare questo URL per verificare che il canale riceva correttamente il flusso live.

3. Creare un programma.

	Se si crea un programma tramite il portale di Azure classico, viene creato anche un asset.

	Se si usa .NET SDK o REST, è necessario creare un asset e specificarne l'uso quando si crea un programma. 
1. Pubblicare l'asset associato al programma.   

	Accertarsi che sia presente almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere i contenuti in streaming.
1. Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione.
2. Facoltativamente, il codificatore live può ricevere il segnale per l'avvio di un annuncio. L'annuncio viene inserito nel flusso di output.
1. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.
1. Eliminare il programma e, facoltativamente, eliminare l'asset.     

La sezione relativa alle [attività di streaming live](media-services-manage-channels-overview.md#tasks) include collegamenti ad argomenti che illustrano come eseguire le attività descritte in precedenza.

##<a id="channel"></a>Descrizione di un canale e dei relativi componenti

###<a id="channel_input"></a>Configurazioni di input (inserimento) del canale

####<a id="ingest_protocols"></a>Protocollo di streaming di inserimento

Servizi multimediali supporta l'inserimento di feed live usando il seguente protocollo di streaming:

- **MP4 frammentato a più velocità in bit**
 
- **RTMP a più velocità in bit**

	Quando è selezionato il protocollo di streaming di inserimento **RTMP**, vengono creati due endpoint di inserimento (input) per il canale:
	
	**URL primario**: specifica l'URL completo dell'endpoint primario di inserimento RTMP del canale.

	**URL secondario** (facoltativo): specifica l'URL completo dell'endpoint secondario di inserimento RTMP del canale.


	Usare l'URL secondario se si vuole migliorare la durabilità e la tolleranza di errore del flusso di inserimento, nonché la tolleranza di errore e il failover del codificatore, soprattutto per gli scenari seguenti.

	- Push doppio del codificatore singolo agli URL primario e secondario:
	
		Lo scopo principale è fornire maggiore resilienza alle instabilità e alle fluttuazioni di rete. Alcuni codificatori RTMP non gestiscono correttamente le disconnessioni dalla rete. Quando si verifica una disconnessione dalla rete, un codificatore può interrompere la codifica e, quando la connessione verrà ristabilita, non invierà i dati memorizzati nel buffer, causando discontinuità e perdita di dati. Le disconnessioni dalla rete possono verificarsi a causa della scarsa efficienza della rete o di una manutenzione sul lato Azure. Gli URL primario e secondario riducono i problemi di rete oltre a fornire un processo di aggiornamento controllato. Ogni volta che si verifica una disconnessione dalla rete programmata, Servizi multimediali gestisce la disconnessione primaria e secondaria e fornisce una disconnessione ritardata tra le due, che dà ai codificatori il tempo necessario per continuare a inviare i dati e ristabilire la connessione. L'ordine delle disconnessioni può essere casuale, ma ci sarà sempre un ritardo tra la primaria/secondaria o la secondaria/primaria. In questo scenario il codificatore è ancora il singolo punto di guasto.
	 
	- Più codificatori ognuno dei quali effettua il push al punto dedicato:
		
		Questo scenario fornisce sia la ridondanza di inserimento che del codificatore. In questo scenario il codificatore 1 effettua il push all'URL primario e il codificatore 2 effettua il push all'URL secondario. Quando si verifica un guasto di un codificatore, l'altro codificatore può continuare a inviare i dati. La ridondanza dei dati continua a essere gestita perché Servizi multimediali non disconnette simultaneamente i codificatori primario e secondario. Questo scenario presume che l'ora dei codificatori sia sincronizzata e fornisce esattamente gli dati stessi.
 
	- Push doppio di più codificatori agli URL primario e secondario:
	
		In questo scenario entrambi codificatori effettuano il push dei dati agli URL primario e secondario. Questo garantisce la migliore affidabilità e tolleranza di errore, nonché la ridondanza dei dati. Può tollerare i guasti di entrambi i codificatori e si disconnette anche se un solo codificatore smette di funzionare. Questo scenario presume che l'ora dei codificatori sia sincronizzata e fornisce esattamente gli dati stessi.

Per informazioni sui codificatori RTMP live, vedere [Codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).

Si applicano le considerazioni seguenti:

- Assicurarsi che sia disponibile connettività Internet sufficiente per inviare dati ai punti di inserimento. 
- Per usare l'URL di inserimento secondario, è necessaria larghezza di banda aggiuntiva. 
- Il flusso a più velocità in bit in ingresso può avere un massimo di 10 livelli di qualità video e di 5 tracce audio.
- La velocità in bit media più elevata per ciascuno dei livelli di qualità video deve essere inferiore a 10 Mbps.
- L'aggregato della velocità in bit media per tutti i flussi video e audio deve essere inferiore a 25 Mbps.
- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input. 
- È possibile inserire una velocità in bit singola nel canale, ma poiché il flusso non viene elaborato dal canale, anche le applicazioni client riceveranno un flusso a velocità in bit singola (scelta non consigliata).

####URL di inserimento (endpoint) 

Un canale fornisce un endpoint di input (URL di inserimento) specificato dall'utente nel codificatore live, in modo che il codificatore possa eseguire il push dei flussi nei canali.

È possibile ottenere gli URL di inserimento quando si crea il canale. Per ottenere questi URL, non è necessario che il canale sia nello stato **In esecuzione**. Quando si è pronti per avviare l'esecuzione del push dei dati nel canale, il canale dovrà essere nello stato **In esecuzione**. Una volta avviato l'inserimento di dati nel canale, è possibile visualizzare in anteprima il flusso tramite l'URL di anteprima.

È possibile inserire un flusso live MP4 frammentato (Smooth Streaming) tramite una connessione SSL. Per l'inserimento tramite SSL, assicurarsi di aggiornare l'URL di inserimento a HTTPS. Attualmente, non è possibile inserire flussi RTMP tramite SSL.

####<a id="keyframe_interval"></a>Intervallo tra fotogrammi chiave

Quando si usa un codificatore live locale per generare un flusso a più velocità in bit, l'intervallo tra fotogrammi chiave specifica la durata Group of Pictures (GOP) usata dal codificatore esterno. Una volta ricevuto il flusso in ingresso dal canale, è possibile distribuire il flusso live nelle applicazioni di riproduzione client in uno dei seguenti formati: Smooth Streaming, DASH and HLS. Quando si esegue lo streaming live, la creazione di pacchetti in HLS avviene sempre in modo dinamico. Per impostazione predefinita, Servizi multimediali calcola automaticamente il rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment) in base all'intervallo tra fotogrammi chiave, noto anche come Group of Pictures (GOP), ricevuti dal codificatore live.

La seguente tabella illustra la modalità di calcolo della durata dei segmenti:

Intervallo tra fotogrammi chiave|Rapporto per la creazione di pacchetti dei segmenti HLS (FragmentsPerSegment)|Esempio
---|---|---
minore o uguale a 3 secondi|3:1|Se il valore KeyFrameInterval (o GOP) è pari a 2 secondi, il rapporto predefinito per la creazione di pacchetti dei segmenti HLS sarà di 3 a 1, che creerà un segmento HLS di 6 secondi.
da 3 a 5 secondi|2:1|Se il valore KeyFrameInterval (o GOP) è pari a 4 secondi, il rapporto predefinito per la creazione di pacchetti dei segmenti HLS sarà di 2 a 1, che creerà un segmento HLS di 8 secondi.
maggiore di 5 secondi|1:1|Se il valore KeyFrameInterval (o GOP) è pari a 6 secondi, il rapporto predefinito per la creazione di pacchetti dei segmenti HLS sarà di 1 a 1, che creerà un segmento HLS di 6 secondi.


È possibile modificare il rapporto di frammenti per segmento configurando l'output del canale e impostando FragmentsPerSegment su ChannelOutputHls.

È inoltre possibile modificare il valore dell'intervallo tra fotogrammi chiave impostando la proprietà KeyFrameInterval su ChanneInput.

Se si imposta in modo esplicito il valore KeyFrameInterval, il rapporto FragmentsPerSegment per la creazione di pacchetti dei segmenti HLS viene calcolato usando le regole descritte in precedenza.

Se si impostano in modo esplicito i valori KeyFrameInterval e FragmentsPerSegment, Servizi multimediali userà i valori impostati dall'utente.


####Indirizzi IP consentiti

È possibile definire gli indirizzi IP autorizzati a pubblicare video in questo canale. Gli indirizzi IP consentiti possono essere specificati come un singolo indirizzo IP, ad esempio '10.0.0.1', come un intervallo di indirizzi IP usando un indirizzo IP e una subnet mask CIDR, ad esempio '10.0.0.1/22' o come un intervallo di indirizzi IP usando un indirizzo IP e una subnet mask decimale puntata, ad esempio '10.0.0.1(255.255.252.0)').

Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.

###Anteprima del canale 

####URL di anteprima

I canali forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito.

È possibile ottenere l'URL di anteprima quando si crea il canale. Per ottenere l'URL, non è necessario che il canale sia nello stato **In esecuzione**.

Una volta che il canale inizia a inserire i dati, è possibile visualizzare in anteprima il flusso.

Si noti che attualmente il flusso di anteprima può essere distribuito solo in formato MP4 frammentato (Smooth Streaming), indipendentemente dal tipo di input specificato. Per testare il flusso Smooth Streaming, è possibile usare il lettore [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor). Per visualizzare il flusso, è inoltre possibile usare un lettore ospitato nel portale di Azure classico.


####Indirizzi IP consentiti

È possibile definire gli indirizzi IP autorizzati a connettersi all'endpoint di anteprima. Se non viene specificato alcun indirizzo IP, sarà consentito qualsiasi indirizzo IP. Gli indirizzi IP consentiti possono essere specificati come un singolo indirizzo IP, ad esempio '10.0.0.1', come un intervallo di indirizzi IP usando un indirizzo IP e una subnet mask CIDR, ad esempio '10.0.0.1/22' o come un intervallo di indirizzi IP usando un indirizzo IP e una subnet mask decimale puntata, ad esempio '10.0.0.1(255.255.252.0)').

###Output del canale

Per altre informazioni, vedere la sezione relativa all'[impostazione di un intervallo tra fotogrammi chiave](#keyframe_interval).


###Programmi del canale

Un canale è associato a programmi che consentono di controllare la pubblicazione e l'archiviazione di segmenti in un flusso live. I programmi sono gestiti dai canali. La relazione tra queste due entità è molto simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale è costituito da un flusso costante di contenuti, mentre un programma ha come ambito una serie di eventi programmati sul canale.

È possibile specificare il numero di ore per cui si vuole mantenere il contenuto registrato per il programma impostando il valore **Intervallo di archiviazione**. Il valore impostato può essere compreso tra 5 minuti e 25 ore. La lunghezza della finestra di archiviazione determina anche il limite di tempo per cui i client possono eseguire ricerche a ritroso nel tempo dalla posizione live corrente. I programmi possono essere eseguiti per la quantità di tempo specificata, ma il contenuto che va oltre la durata prevista viene scartato in modo continuo. Il valore della proprietà determina anche il tempo per cui i manifesti client possono crescere.

Ogni programma è associato a un asset che archivia il contenuto trasmesso nel flusso. Un asset viene mappato a un contenitore BLOB nell'account di archiviazione di Azure e i file contenuti nell'asset vengono archiviati come BLOB in tale contenitore. Per pubblicare il programma in modo che possa essere visualizzato dai clienti, è necessario creare un localizzatore OnDemand per l'asset associato. Con questo localizzatore sarà possibile creare un URL di streaming da fornire ai client.

Un canale supporta fino a tre programmi in esecuzione simultanea, in modo da poter creare più archivi dello stesso flusso in ingresso. Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Si consideri ad esempio uno scenario in cui un'azienda richiede l'archiviazione di 6 ore di un programma e la trasmissione solo degli ultimi 10 minuti. A tale scopo, è necessario creare due programmi in esecuzione contemporaneamente. Un programma è impostato per l'archiviazione di 6 ore dell'evento, ma non viene pubblicato. L'altro programma è impostato per l'archiviazione di 10 minuti e viene pubblicato.

Non riutilizzare i programmi esistenti per nuovi eventi. Creare e avviare invece un nuovo programma per ogni evento, come descritto nella sezione relativa alla programmazione di applicazioni Live Streaming.

Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.

Per eliminare il contenuto archiviato, arrestare ed eliminare il programma e quindi eliminare l'asset associato. Un asset non può essere eliminato se è usato da un programma. Per farlo, eliminare prima il programma.

Anche dopo l'arresto e l'eliminazione del programma, gli utenti possono trasmettere in streaming il contenuto archiviato sotto forma di video on demand, fintanto che non si elimina l'asset.

Se si desidera mantenere il contenuto archiviato ma non averlo disponibile per lo streaming, eliminare il localizzatore di streaming.

##<a id="states"></a>Stati del canale e come vengono mappati alla modalità di fatturazione 

Si tratta dello stato attuale del canale. I valori possibili sono:

- **Arrestato** Lo stato iniziale del canale dopo la creazione. In questo stato le proprietà del canale possono essere aggiornate ma lo streaming non è consentito.
- **Avvio in corso**. È in corso l'avvio del canale. In questo stato non è consentito alcun aggiornamento o streaming. Se si verifica un errore, il canale torna allo stato Interrotto.
- **In esecuzione**. Il canale è in grado di elaborare flussi live.
- **Arresto in corso**. È in corso l'interruzione del canale. In questo stato non è consentito alcun aggiornamento o streaming.
- **Eliminazione in corso**. È in corso l'eliminazione del canale. In questo stato non è consentito alcun aggiornamento o streaming.

La tabella seguente illustra il mapping degli stati del canale alla modalità di fatturazione.
 
Stato del canale|Indicatori dell'interfaccia utente del portale|Fatturato?
---|---|---|---
Avvio in corso|Avvio in corso|No (stato temporaneo)
In esecuzione|Pronto (nessun programma in esecuzione)<p>o<p>Streaming (almeno un programma in esecuzione)|Sì
Arresto in corso|Arresto in corso|No (stato temporaneo)
Arrestato|Arrestato|No

##<a id="cc_and_ads"></a>Sottotitoli codificati e inserimento di annunci 

La seguente tabella illustra i sottotitoli codificati supportati e gli standard per l'inserimento di annunci.

Standard|Note
---|---
CEA-708 e EIA-608 (708/608)|CEA-708 ed EIA 608 sono standard per i sottotitoli codificati per Stati Uniti e Canada.<p><p>Attualmente, i sottotitoli sono supportati solo se eseguiti nel flusso di input codificato. È necessario usare un codificatore multimediale live che possa inserire 608 o 708 sottotitoli nel flusso codificato che viene inviato ai Servizi multimediali. Servizi multimediali fornirà agli utenti il contenuto con i sottotitoli inseriti.
TTML all'interno di file ismt (tracce di testo Smooth Streaming)|La creazione dinamica di pacchetti di Servizi multimediali consente ai client di trasmettere in streaming il contenuto in uno dei seguenti formati: MPEG DASH, HLS or Smooth Streaming. Se si inserisce il formato MP4 frammentato (Smooth Streaming) con sottotitoli all'interno dei file con estensione ismt (tracce di testo Smooth Streaming), sarà possibile distribuire il flusso solo ai client Smooth Streaming.
SCTE-35|Sistema di segnalazione digitale usato per predisporre l'inserimento di annunci pubblicitari. I ricevitori a valle usano il segnale per congiungere l'annuncio al flusso per l'intervallo assegnato. SCTE-35 deve essere inviato come una traccia di tipo sparso nel flusso di input.<p><p>Si noti che al momento l'unico formato di flusso di input supportato che trasporta i segnali degli annunci è il formato MP4 frammentato (Smooth Streaming). Smooth Streaming è anche l'unico formato di output supportato.


##<a id="Considerations"></a>Considerazioni

Quando si usa un codificatore live locale per l'invio di un flusso a più velocità in bit in un canale, si applicano i seguenti vincoli:

- Assicurarsi che sia disponibile connettività Internet sufficiente per inviare dati ai punti di inserimento. 
- Il flusso a più velocità in bit in ingresso può avere un massimo di 10 livelli di qualità video e di 5 tracce audio.
- La velocità in bit media più elevata per ciascuno dei livelli di qualità video deve essere inferiore a 10 Mbps.
- L'aggregato della velocità in bit media per tutti i flussi video e audio deve essere inferiore a 25 Mbps.
- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input. 


Altre considerazioni relative all'uso dei canali e dei componenti correlati:

- Assicurarsi di chiamare il metodo **Reset** sul canale ogni volta che si riconfigura il codificatore live. Prima di reimpostare il canale, è necessario arrestare il programma. Dopo aver reimpostato il canale, riavviare il programma. 
- È possibile interrompere un canale solo quando si trova nello stato In esecuzione e tutti i programmi nel canale sono stati arrestati.
- Per impostazione predefinita, è possibile aggiungere solo cinque canali all'account di Servizi multimediali. Per altre informazioni, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).
- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input. 
- Il costo viene addebitato solo quando il canale è nello stato **In esecuzione**. Per altre informazioni, vedere [questa](media-services-manage-channels-overview.md#states) sezione.

##Come creare canali che ricevono il flusso live a più velocità in bit da codificatori locali

Per altre informazioni sui codificatori live locali, vedere [Uso di codificatori di terze parti con Servizi multimediali di Azure](https://azure.microsoft.com/blog/azure-media-services-rtmp-support-and-live-encoders/).

Scegliere **Portale**, **.NET**, **API REST** per vedere come creare e gestire canali e programmi.

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]



##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##Argomenti correlati

[Specifica per l'inserimento live di un flusso MP4 frammentato con Servizi multimediali di Azure](media-services-fmp4-live-ingest-overview.md)

[Distribuzione di eventi Live Streaming con Servizi multimediali di Azure](media-services-live-streaming-workflow.md)

[Concetti su Servizi multimediali di Azure](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
 

<!---HONumber=AcomDC_1217_2015-->