<properties 
	pageTitle="Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure" 
	description="Questo argomento descrive come configurare un canale che riceve un flusso live a velocità in bit singola da un codificatore locale e quindi esegue la codifica live in un flusso a velocità in bit adattiva con Servizi multimediali. Il flusso può essere quindi distribuito alle applicazioni di riproduzione client tramite uno o più endpoint di streaming, usando uno dei seguenti protocolli di streaming adattivi: HLS, Smooth Stream, MPEG DASH, HDS." 
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
	ms.date="04/29/2015" 
	ms.author="juliako"/>

#Uso di canali abilitati per l'esecuzione della codifica live con Servizi multimediali di Azure (anteprima)

##Panoramica

In Servizi multimediali di Azure un **canale** rappresenta una pipeline per l'elaborazione di contenuto in streaming live. Un **canale** riceve i flussi di input live in uno dei due modi seguenti:

- Un codificatore live locale invia al canale un flusso **RTMP** o **Smooth Streaming** (MP4 frammentato) a più velocità in bit. È possibile usare i codificatori live seguenti che generano output in formato Smooth Streaming a più velocità in bit: Elemental, Envivio, Cisco. I codificatori live seguenti generano output in formato RTMP: Adobe Flash Live, Telestream Wirecast e transcodificatori Tricaster. I flussi inseriti passano attraverso il **canale** senza altre elaborazioni. Quando richiesto, Servizi multimediali invia il flusso ai clienti.
- Un flusso a velocità in bit singola (in uno dei seguenti formati: **RTP** (MPEG-TS)), **RTMP** o **Smooth Streaming** (MP4 frammentato)) viene inviato al **canale** abilitato per l'esecuzione della codifica live con Servizi multimediali. Il **canale** esegue quindi la codifica live del flusso in ingresso a velocità in bit singola in un flusso video a più velocità in bit (adattivo). Quando richiesto, Servizi multimediali invia il flusso ai clienti. 

	La codifica di un flusso live con Servizi multimediali è attualmente in **anteprima**.

A partire dalla versione 2.10 di Servizi multimediali, quando si crea un canale è possibile specificare in che modo il canale riceverà il flusso di input e se eseguirà o meno la codifica live del flusso. Sono disponibili due opzioni:

- **Nessuno**: specificare questo valore se si prevede di usare un codificatore live locale che genera un flusso a più velocità in bit. In questo caso, il flusso in ingresso viene passato all'output senza codifica. Questo è il comportamento di un canale prima della versione 2.10. Per informazioni più dettagliate dell'uso dei canali di questo tipo, vedere [Uso dei canali che ricevono il flusso live a più velocità in bit da codificatori locali](media-services-manage-channels-overview.md).

- **Standard** (anteprima): scegliere questo valore se si prevede di usare Servizi multimediali per codificare il flusso live a velocità in bit singola in un flusso a più velocità in bit.

	La codifica di un flusso live con Servizi multimediali è attualmente in anteprima.

>[AZURE.NOTE]Questo argomento illustra gli attributi dei canali abilitati per l'esecuzione della codifica live (tipo di codifica **Standard**). Per informazioni sull'uso dei canali non abilitati all'esecuzione della codifica live, vedere [Uso dei canali che ricevono il flusso live a più velocità in bit da codificatori locali](media-services-manage-channels-overview.md).

Il diagramma seguente rappresenta un flusso di lavoro di streaming live in cui un canale riceve un flusso a velocità in bit singola in uno dei protocolli seguenti: RTMP, Smooth Streaming o RTP (MPEG-TS), quindi ne esegue la codifica in un flusso a più velocità in bit.

![Flusso di lavoro live][live-overview]

##Contenuto dell'argomento

- Panoramica di uno [scenario comune di streaming live](media-services-manage-live-encoder-enabled-channels.md#scenario)
- [Descrizione di un canale e dei relativi componenti](media-services-manage-live-encoder-enabled-channels.md#channel)
- [Considerazioni](media-services-manage-live-encoder-enabled-channels.md#considerations)
- [Attività relative allo streaming live](media-services-manage-live-encoder-enabled-channels.md#tasks)

##<a id="scenario"></a>Scenario comune di streaming live

Di seguito sono descritti i passaggi generali relativi alla creazione di applicazioni comuni di streaming live.

1. Connettere una videocamera a un computer. Avviare e configurare un codificatore live locale che può restituire un flusso a velocità in bit **singola** in uno dei protocolli seguenti: RTMP, Smooth Streaming o RTP (MPEG-TS). Per altre informazioni, vedere l'argomento relativo a [codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).
	
	Questa operazione può essere eseguita anche dopo la creazione del canale.

1. Creare e avviare un canale.

1. Recuperare l'URL di inserimento del canale.

	L'URL di inserimento viene usato dal codificatore live per inviare il flusso al canale.
1. Recuperare l'URL di anteprima del canale. 

	Usare questo URL per verificare che il canale riceva correttamente il flusso live.

3. Creare un programma.

	Se si crea un programma tramite il portale di gestione di Azure, viene creato anche un asset.

	Se si usa .NET SDK o REST, è necessario creare un asset e specificarne l'uso quando si crea un programma.
1. Pubblicare l'asset associato al programma.   

	Accertarsi che sia presente almeno un'unità riservata di streaming nell'endpoint di streaming da cui si desidera trasmettere i contenuti in streaming.
1. Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione.
2. Facoltativamente, il codificatore live può ricevere il segnale per l'avvio di un annuncio. L'annuncio viene inserito nel flusso di output.
1. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.
1. Eliminare il programma e, facoltativamente, eliminare l'asset.   

La sezione relativa alle [attività di streaming live](media-services-manage-channels-overview.md#tasks) include collegamenti ad argomenti che illustrano come eseguire le attività descritte in precedenza.


##<a id="channel"></a>Configurazioni di input (inserimento) del canale

###<a id="Ingest_Protocols"></a>Protocollo di streaming di inserimento

Se **Tipo di codificatore** è impostato su **Standard**, le opzioni valide sono le seguenti:

- **RTP** (MPEG-TS): MPEG-2 Transport Stream su RTP.  
- **RTMP** a velocità in bit singola
- **MP4 frammentato** (Smooth Streaming) a velocità in bit singola

Per altre informazioni, vedere l'argomento relativo a [codificatori live e supporto RTMP di Servizi multimediali di Azure](http://go.microsoft.com/fwlink/?LinkId=532824).

####RTP (MPEG-TS): MPEG-2 Transport Stream su RTP.  

Caso di utilizzo tipico:

Per inviare un flusso, le emittenti professionali usano in genere codificatori live locali di fascia alta di fornitori come Elemental Technologies, Ericsson, Ateme, Imagine o Envivio. Usato spesso insieme a reti di reparti IT e private.

Considerazioni:

- È consigliabile usare l'input di un singolo flusso di trasporto del programma (SPTS). È tuttavia supportato l'uso di tracce audio in più lingue.
- Il flusso video dovrà avere una velocità in bit media inferiore a 15 Mbps.
- La velocità in bit media aggregata dei flussi video e audio dovrà essere inferiore a 1 Mbps.
- Di seguito sono elencati i codec supportati:
	- MPEG-2/H.262 Video 
		
		- Main Profile (4:2:0)
		- High Profile (4:2:0, 4:2:2)
		- 422 Profile (4:2:0, 4:2:2)

	- MPEG-4 AVC/H.264 Video  
	
		- Baseline, Main, High Profile (8-bit 4:2:0)
		- High 10 Profile (10-bit 4:2:0)
		- High 422 Profile (10-bit 4:2:2)


	- MPEG-2 AAC-LC Audio 
	
		- Mono, Stereo, Surround (5.1, 7.1)
		- MPEG-2 style ADTS packaging

	- Dolby Digital (AC-3) Audio 

		- Mono, Stereo, Surround (5.1, 7.1)

	- MPEG Audio (Layer II and III) 
			
		- Mono, Stereo

- I codificatori di trasmissione consigliati includono:
	- Ateme AM2102
	- Ericsson AVP2000
	- eVertz 3480
	- Ericsson RX8200
	- Imagine Communications Selenio ENC 1
	- Imagine Communications Selenio ENC 2
	- AdTec EN-30
	- AdTec EN-91P
	- AdTec EN-100
	- Harmonic ProStream 1000
	- Thor H-2 4HD-EM
	- eVertz 7880 SLKE
	- Cisco Spinnaker
	- Elemental Live

####<a id="single_bitrate_RTMP"></a>RTMP a velocità in bit singola

Considerazioni:

- Il flusso in ingresso non può contenere video a più velocità in bit.
- Il flusso video dovrà avere una velocità in bit media inferiore a 15 Mbps.
- Il flusso audio dovrà avere una velocità in bit media inferiore a 1 Mbps.
- Di seguito sono elencati i codec supportati:

	- MPEG-4 AVC/H.264 Video  
	
		- Baseline, Main, High Profile (8-bit 4:2:0)
		- High 10 Profile (10-bit 4:2:0)
		- High 422 Profile (10-bit 4:2:2)

	- MPEG-2 AAC-LC Audio

		- Mono, Stereo, Surround (5.1, 7.1)
		- 44.1 kHz sampling rate
		- MPEG-2 style ADTS packaging
	
- I codec consigliati includono:

	- Telestream Wirecast
	- Flash Media Live Encoder
	- Tricaster

####MP4 frammentato (Smooth Streaming) a velocità in bit singola

Caso di utilizzo tipico:

Usare codificatori live locali di fornitori come Elemental Technologies, Ericsson, Ateme o Envivio per inviare il flusso di input tramite la rete Internet aperta a un data center di Azure nelle vicinanze.

Considerazioni:

Come per [RTMP a velocità in bit singola](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

####Altre considerazioni

- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input. 
- La risoluzione massima per il flusso video in ingresso è 1920x1080 e al massimo 60 campi/seconde se interlacciato o 30 fotogrammi/secondo se progressivo.


###URL di inserimento (endpoint) 

Un canale fornisce un endpoint di input (URL di inserimento) specificato dall'utente nel codificatore live, in modo che il codificatore possa eseguire il push dei flussi nei canali.

È possibile ottenere gli URL di inserimento dopo aver creato un canale. Per ottenere questi URL, non è necessario che il canale sia nello stato **In esecuzione**. Quando si è pronti per avviare l'esecuzione del push dei dati nel canale, questo dovrà essere nello stato **In esecuzione**. Una volta avviato l'inserimento di dati nel canale, è possibile visualizzare in anteprima il flusso tramite l'URL di anteprima.

È possibile inserire un flusso live MP4 frammentato (Smooth Streaming) tramite una connessione SSL. Per l'inserimento tramite SSL, assicurarsi di aggiornare l'URL di inserimento a HTTPS.

###Indirizzi IP consentiti

È possibile definire gli indirizzi IP autorizzati a pubblicare video in questo canale. Gli indirizzi IP consentiti possono essere specificati come un singolo indirizzo IP, ad esempio '10.0.0.1', come un intervallo di indirizzi IP usando un indirizzo IP e una subnet mask CIDR, ad esempio '10.0.0.1/22' o come un intervallo di indirizzi IP usando un indirizzo IP e una subnet mask decimale puntata, ad esempio '10.0.0.1(255.255.252.0)').

Se non viene specificato alcun indirizzo IP e non è presente una definizione della regola, non sarà consentito alcun indirizzo IP. Per consentire qualsiasi indirizzo IP, creare una regola e impostare 0.0.0.0/0.


##Anteprima del canale 

###URL di anteprima

I canali forniscono anche un endpoint di anteprima (URL di anteprima) che consente di visualizzare in anteprima e convalidare il flusso prima dell'ulteriore elaborazione e del recapito.

È possibile ottenere l'URL di anteprima quando si crea il canale. Per ottenere l'URL, non è necessario che il canale sia nello stato **In esecuzione**.

Una volta che il canale inizia a inserire i dati, è possibile visualizzare in anteprima il flusso.

**Nota**: attualmente il flusso di anteprima può essere distribuito solo in formato MP4 frammentato (Smooth Streaming), indipendentemente dal tipo di input specificato. Per testare il flusso Smooth Streaming, è possibile usare il lettore [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor). Per visualizzare il flusso, è inoltre possibile usare un lettore ospitato nel portale di gestione di Azure.

###Indirizzi IP consentiti

È possibile definire gli indirizzi IP autorizzati a connettersi all'endpoint di anteprima. Se non viene specificato alcun indirizzo IP, sarà consentito qualsiasi indirizzo IP. Gli indirizzi IP consentiti possono essere specificati come un singolo indirizzo IP, ad esempio '10.0.0.1', come un intervallo di indirizzi IP usando un indirizzo IP e una subnet mask CIDR, ad esempio '10.0.0.1/22' o come un intervallo di indirizzi IP usando un indirizzo IP e una subnet mask decimale puntata, ad esempio '10.0.0.1(255.255.252.0)').

##Impostazioni per la codifica live

Questa sezione descrive come è possibile modificare le impostazioni del codificatore live all'interno del canale, quando **Tipo di codifica** di un canale è impostato su **Standard**.

###Origine del marcatore di annunci

È possibile specificare l'origine per i segnali dei marcatori di annunci. Il valore predefinito è **Api**. Indica che il codificatore live all'interno del canale deve restare in attesa di un'**API Ad Marker** asincrona.

L'altra opzione valida è **Scte35** (consentita solo se il protocollo di streaming di inserimento è impostato su RTP (MPEG-TS). Quando è specificato Scte35, il codificatore live analizza i segnali SCTE-35 dal flusso di input RTP (MPEG-TS).

###Sottotitoli codificati CEA-708

Flag facoltativo che indica al codificatore live di ignorare i dati di tutti i sottotitoli codificati CEA 708 incorporati nel video in ingresso. Quando il flag è impostato su false (impostazione predefinita), il codificatore rileva e reinserisce i dati CEA 708 nei flussi video di output.

###Flusso video

Facoltativo. Descrive il flusso video di input. Se questo campo non è specificato, viene usato il valore predefinito. Questa impostazione è consentita solo se il protocollo di streaming di input è impostato su RTP (MPEG-TS).

####Indice

Indice in base zero che specifica quale flusso video di input dovrà essere elaborato dal codificatore live all'interno del canale. Questa impostazione si applica solo se il protocollo di streaming di inserimento è RTP (MPEG-TS).

Il valore predefinito è zero. È consigliabile effettuare l'invio tramite un singolo flusso di trasporto del programma (SPTS). Se il flusso di input contiene più programmi, il codificatore live analizza la tabella di mappa dei programmi (PMT, Program Map Table) nell'input, individua quindi gli input che presentano un nome del tipo di flusso MPEG-2 Video o H.264 e li dispone in base all'ordine specificato nella tabella di mappa dei programmi. L'indice in base zero viene quindi usato per selezionare la voce n in tale disposizione.

###Flusso audio

Facoltativo. Descrive i flussi audio di input. Se questo campo non è specificato, si applica il valore predefinito specificato. Questa impostazione è consentita solo se il protocollo di streaming di input è impostato su RTP (MPEG-TS).

####Indice

È consigliabile effettuare l'invio tramite un singolo flusso di trasporto del programma (SPTS). Se il flusso di input contiene più programmi, il codificatore live all'interno del canale analizza la tabella di mappa dei programmi (PMT, Program Map Table) nell'input, individua quindi gli input che presentano un nome del tipo di flusso MPEG-2 AAC ADTS, AC-3 System-A, AC-3 System-B, MPEG-2 Private PES, MPEG-1 Audio o MPEG-2 Audio e li dispone in base all'ordine specificato nella tabella di mappa dei programmi. L'indice in base zero viene quindi usato per selezionare la voce n in tale disposizione.

####Lingua

Identificatore lingua del flusso audio, conforme alla specifica ISO 639-2, ad esempio ITA. Se non è presente, il valore predefinito è UND (undefined).

È possibile specificare un massimo di 8 set di flussi audio se l'input per il canale è MPEG-2 TS su RTP. Tuttavia, non possono essere presenti due voci con lo stesso valore di indice.

###<a id="preset"></a>Set di impostazioni del sistema

Specifica il set di impostazioni che dovrà essere usato dal codificatore live all'interno del canale. Attualmente, l'unico valore consentito è **Default720p** (impostazione predefinita).

Con **Default720p** il video sarà codificato nei 7 livelli seguenti.


####Flusso video di output

<table border="1">
<tr><th>Velocità in bit</th><th>Larghezza</th><th>Altezza</th><th>MaxFPS</th><th>Profilo</th><th>Nome del flusso di output</th></tr>
<tr><td>3500</td><td>1280</td><td>720</td><td>30</td><td>Alto</td><td>Video_1280x720_30fps_3500kbps</td></tr>
<tr><td>2200</td><td>960</td><td>540</td><td>30</td><td>Principale</td><td>Video_960x540_30fps_2200kbps</td></tr>
<tr><td>1350</td><td>704</td><td>396</td><td>30</td><td>Principale</td><td>Video_704x396_30fps_1350kbps</td></tr>
<tr><td>850</td><td>512</td><td>288</td><td>30</td><td>Principale</td><td>Video_512x288_30fps_850kbps</td></tr>
<tr><td>550</td><td>384</td><td>216</td><td>30</td><td>Principale</td><td>Video_384x216_30fps_550kbps</td></tr>
<tr><td>350</td><td>340</td><td>192</td><td>30</td><td>Di base</td><td>Video_340x192_30fps_350kbps</td></tr>
<tr><td>200</td><td>340</td><td>192</td><td>30</td><td>Di base</td><td>Video_340x192_30fps_200kbps</td></tr>
</table>

####Flusso audio di output

L'audio viene codificato nel formato stereo AAC-LC a 64 kbps, frequenza di campionamento di 44,1 kHz.

##Segnalazione di annunci

Quando nel canale è abilitata la codifica live, nella pipeline è presente un componente che elabora il flusso video ed è in grado di modificarlo. È possibile impostare il segnale in modo che il canale inserisca slate e/o annunci nel flusso in uscita a velocità in bit adattiva. Gli slate sono immagini fisse che è possibile usare per coprire feed live di input in determinati casi (ad esempio durante un'interruzione pubblicitaria). I segnali pubblicitari sono segnali con sincronizzazione temporale incorporati nel flusso in uscita, per indicare al lettore video di eseguire un'azione speciale, ad esempio passare a un annuncio al momento opportuno. Per una panoramica del meccanismo di segnalazione SCTE-35 usato a questo scopo, vedere questo [blog](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/). Di seguito è illustrato uno scenario tipico che è possibile implementare in un evento live.

1. Fare in modo che gli utenti ottengano un'immagine PRE-EVENTO prima dell'avvio dell'evento.
1. Fare in modo che gli utenti ottengano un'immagine POST-EVENTO al termine dell'evento.
1. Fare in modo che gli utenti ottengano un'immagine EVENTO-DI-ERRORE in caso di errore durante l'evento (ad esempio, interruzione dell'alimentazione allo stadio).
1. Inviare un'immagine INTERRUZIONE-PUBBLICITARIA per nascondere l'evento live durante un'interruzione pubblicitaria.

Di seguito sono elencate le proprietà che è possibile usare quando si inviano segnali pubblicitari.

###Durata

Durata dell'interruzione pubblicitaria in secondi. Per avviare l'interruzione pubblicitaria, deve essere un valore positivo diverso da zero. Quando è in corso un'interruzione pubblicitaria e la durata è impostata su zero con ID battuta corrispondente all'interruzione pubblicitaria in corso, l'interruzione viene annullata.

###ID battuta

ID univoco dell'interruzione pubblicitaria che dovrà essere usato dall'applicazione downstream per eseguire le operazioni necessarie. Deve essere un intero positivo. È possibile impostare questo valore su qualsiasi intero positivo casuale oppure usare un sistema upstream per tenere traccia degli ID battuta. Assicurarsi di normalizzare tutti gli ID in interi positivi prima dell'invio tramite l'API.

###Visualizza slate

Facoltativa. Segnala al codificatore live di passare all'immagine dello slate predefinita durante un'interruzione pubblicitaria e nascondere il feed video in ingresso. Durante la visualizzazione dello slate viene disattivato anche l'audio. Il valore predefinito è **false**.
 
L'immagine usata sarà quella specificata tramite la proprietà ID asset dello slate predefinito al momento della creazione del canale. Lo slate verrà esteso per adattarsi alle dimensioni dell'immagine visualizzata.

##Inserire immagini dello slate

È possibile inviare un segnale al codificatore live all'interno del canale per passare a un'immagine dello slate. È anche possibile inviare un segnale perché venga terminato uno slate in corso.

Il codificatore live può essere configurato per passare a un'immagine dello slate e nascondere il segnale video in ingresso in determinate situazioni, ad esempio, durante un'interruzione pubblicitaria. Se non è configurato uno slate, il video di input non viene mascherato durante l'interruzione pubblicitaria.

###Durata

Durata dello slate in secondi. Per avviare lo slate, deve essere un valore positivo diverso da zero. Se è in corso uno slate e viene specificata una durata pari a zero, lo slate in corso verrà terminato.

###Inserisci slate su marcatore di annuncio

Quando è impostata su true, questa opzione configura il codificatore live in modo che venga inserita un'immagine dello slate durante un'interruzione pubblicitaria. Il valore predefinito è true.

###ID asset dello slate predefinito

Facoltativa. Specifica l'ID asset di Servizi multimediali che contiene l'immagine dello slate. Il valore predefinito è Null.

**Nota**: prima di creare il canale, è necessario caricare l'immagine dello slate (con una risoluzione massima di 1920x1080, in formato JPEG e con dimensioni massime di 3 MB) come asset dedicato, nel quale non dovranno essere presenti altri file. L'estensione di file dovrà essere *JPG e questo AssetFile dovrà essere contrassegnato come file primario dell'asset. L'asset non può essere crittografato per l'archiviazione.

Se non si specifica l'**ID asset dello slate predefinito** e **Inserisci slate su marcatore di annuncio** è impostato su **true**, per nascondere il flusso video di input verrà usata un'immagine predefinita di Servizi multimediali di Azure. Durante la visualizzazione dello slate viene disattivato anche l'audio.



##Programmi del canale

Un canale è associato a programmi che consentono di controllare la pubblicazione e l'archiviazione di segmenti in un flusso live. I programmi sono gestiti dai canali. La relazione tra queste due entità è molto simile a quella che intercorre tra di essi nei media tradizionali, in cui un canale è costituito da un flusso costante di contenuti, mentre un programma ha come ambito una serie di eventi programmati sul canale.

È possibile specificare il numero di ore per cui si vuole mantenere il contenuto registrato per il programma impostando il valore **Intervallo di archiviazione**. Il valore impostato può essere compreso tra 5 minuti e 25 ore. La lunghezza della finestra di archiviazione determina anche il limite di tempo per cui i client possono eseguire ricerche a ritroso nel tempo dalla posizione live corrente. I programmi possono essere eseguiti per la quantità di tempo specificata, ma il contenuto che va oltre la durata prevista viene scartato in modo continuo. Il valore della proprietà determina anche il tempo per cui i manifesti client possono crescere.

Ogni programma è associato a un asset che archivia il contenuto trasmesso nel flusso. Un asset viene mappato a un contenitore BLOB nell'account di archiviazione di Azure e i file contenuti nell'asset vengono archiviati come BLOB in tale contenitore. Per pubblicare il programma in modo che possa essere visualizzato dai clienti, è necessario creare un localizzatore OnDemand per l'asset associato. Con questo localizzatore sarà possibile creare un URL di streaming da fornire ai client.

Un canale supporta fino a tre programmi in esecuzione simultanea, in modo da poter creare più archivi dello stesso flusso in ingresso. Questo consente di pubblicare e archiviare parti diverse di un evento a seconda delle necessità. Si consideri ad esempio uno scenario in cui un'azienda richiede l'archiviazione di 6 ore di un programma e la trasmissione solo degli ultimi 10 minuti. A tale scopo, è necessario creare due programmi in esecuzione contemporaneamente. Un programma è impostato per l'archiviazione di 6 ore dell'evento, ma non viene pubblicato. L'altro programma è impostato per l'archiviazione di 10 minuti e viene pubblicato.

Non riutilizzare i programmi esistenti per nuovi eventi. Creare e avviare invece un nuovo programma per ogni evento, come descritto nella sezione relativa alla programmazione di applicazioni Live Streaming.

Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.

Per eliminare il contenuto archiviato, arrestare ed eliminare il programma e quindi eliminare l'asset associato. Un asset non può essere eliminato se è usato da un programma. Per farlo, eliminare prima il programma.

Anche dopo l'arresto e l'eliminazione del programma, gli utenti possono trasmettere in streaming il contenuto archiviato sotto forma di video on demand, fintanto che non si elimina l'asset.

Se si desidera mantenere il contenuto archiviato ma non averlo disponibile per lo streaming, eliminare il localizzatore di streaming.


##Ottenere un'anteprima di un feed live

Quando è abilitata la codifica live, ora è possibile ottenere un'anteprima del feed live quando raggiunge il canale. Questo strumento può essere utile per verificare se il feed live sta effettivamente per raggiungere il canale.

##<a id="states"></a>Stati del canale e come vengono mappati alla modalità di fatturazione 

Si tratta dello stato attuale del canale. I valori possibili sono:

- **Arrestato** Lo stato iniziale del canale dopo la creazione. In questo stato le proprietà del canale possono essere aggiornate ma lo streaming non è consentito.
- **Avvio in corso**. È in corso l'avvio del canale. In questo stato non è consentito alcun aggiornamento o streaming. Se si verifica un errore, il canale torna allo stato Interrotto.
- **In esecuzione**. Il canale è in grado di elaborare flussi live.
- **Arresto in corso**. È in corso l'interruzione del canale. In questo stato non è consentito alcun aggiornamento o streaming.
- **Eliminazione in corso**. È in corso l'eliminazione del canale. In questo stato non è consentito alcun aggiornamento o streaming.

La tabella seguente illustra il mapping degli stati del canale alla modalità di fatturazione.
 
<table border="1">
<tr><th>Stato del canale</th><th>Indicatori dell'interfaccia utente del portale</th><th>Fatturato?</th></tr>
<tr><td>Avvio in corso</td><td>Avvio in corso</td><td>No (stato temporaneo)</td></tr>
<tr><td>In esecuzione</td><td>Pronto (nessun programma in esecuzione)<br/>oppure<br/>Streaming (almeno un programma in esecuzione)</td><td>Sì</td></tr>
<tr><td>Arresto in corso</td><td>Arresto in corso</td><td>No (stato temporaneo)</td></tr>
<tr><td>Arrestato</td><td>Arrestato</td><td>No</td></tr>
</table>


>[AZURE.NOTE]L'avvio del canale, attualmente in anteprima, può richiedere fino a 30 minuti. La reimpostazione del canale può richiedere fino a 5 minuti.



##<a id="Considerations"></a>Considerazioni

- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input. 
- Assicurarsi di chiamare il metodo **Reset** sul canale ogni volta che si riconfigura il codificatore live. Prima di reimpostare il canale, è necessario arrestare il programma. Dopo aver reimpostato il canale, riavviare il programma. 
- È possibile interrompere un canale solo quando si trova nello stato In esecuzione e tutti i programmi nel canale sono stati arrestati.
- Per impostazione predefinita, è possibile aggiungere solo cinque canali all'account di Servizi multimediali. Si tratta di una quota flessibile per tutti i nuovi account. Per altre informazioni, vedere [Quote e limitazioni](media-services-quotas-and-limitations.md).
- Non è possibile modificare il protocollo di input durante l'esecuzione del canale o dei relativi programmi associati. Se sono necessari protocolli diversi, è consigliabile creare canali separati per ciascun protocollo di input.
- Il costo viene addebitato solo quando il canale è nello stato **In esecuzione**. Per altre informazioni, vedere [questa](media-services-manage-live-encoder-enabled-channels.md#states) sezione.


##<a id="tasks"></a>Attività relative allo streaming live

###Creazione di un account di Servizi multimediali

[Creare un account di Servizi multimediali di Azure](media-services-create-account.md).

###Configurazione degli endpoint di streaming

Per informazioni generali sugli endpoint di streaming e su come gestirli, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md).

###Configurazione dell'ambiente di sviluppo  

Scegliere **.NET** o **API REST** per l'ambiente di sviluppo.

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

###Connessione a livello di codice  

Scegliere **.NET** o **API REST** per connettersi a livello di codice a Servizi multimediali di Azure.

[AZURE.INCLUDE [media-services-selector-connect](../../includes/media-services-selector-connect.md)]

###Creazione di canali che eseguono la codifica live da una velocità in bit singola a un flusso a velocità in bit adattiva 

Scegliere **Portale**, **.NET**, **API REST** per vedere come creare e gestire canali e programmi.

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST](https://msdn.microsoft.com/library/azure/dn783458.aspx

###Protezione degli asset

Per crittografare un asset associato a un programma tramite AES (Advanced Encryption Standard) usando chiavi di crittografia a 128 bit oppure con DRM PlayReady, è necessario creare una chiave simmetrica.

Usare **.NET** o **API REST** per creare le chiavi.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../../includes/media-services-selector-create-contentkey.md)]

Dopo aver creato la chiave simmetrica, è possibile configurare i criteri di autorizzazione chiave tramite **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

###Pubblicazione e distribuzione degli asset

**Panoramica**:

- [Panoramica della creazione dinamica dei pacchetti](../media-services-dynamic-overview.md)
- [Panoramica della distribuzione di contenuti](media-services-deliver-content-overview.md)

Configurare i criteri di distribuzione degli asset usando **.NET** o **API REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Pubblicare asset, mediante la creazione di localizzatori, usando il **portale di gestione di Azure** o **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


###Abilitazione della rete CDN di Azure

Servizi multimediali supporta l'integrazione con la rete CDN di Azure. Per informazioni su come abilitare la rete CDN di Azure, vedere [Come gestire gli endpoint di streaming in un account di Servizi multimediali](media-services-manage-origins.md#enable_cdn).

###Ridimensionamento di un account di Servizi multimediali

È possibile ridimensionare **Servizi multimediali** specificando il numero di **unità riservate di streaming** di cui eseguire il provisioning nell'account in uso.

Per informazioni sul ridimensionamento delle unità di streaming, vedere la sezione relativa al [ridimensionamento di unità di streaming](media-services-manage-origins.md#scale_streaming_endpoints.md)

##Argomenti correlati

[Distribuzione di eventi Live Streaming con Servizi multimediali di Azure](media-services-live-streaming-workflow.md)

[Concetti su Servizi multimediali di Azure](media-services-concepts.md)


[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

<!--HONumber=52-->
 