<properties 
	pageTitle="Panoramica su Servizi multimediali di Azure" 
	description="Questo argomento fornisce una panoramica su Servizi multimediali di Azure" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="juliako"/>

#Panoramica su Servizi multimediali di Azure

Servizi multimediali di Microsoft Azure costituisce una piattaforma estensibile basata sul cloud che consente agli sviluppatori di creare applicazioni di distribuzione e gestione di contenuti multimediali altamente scalabili. Servizi multimediali si basa su API REST che consentono di caricare, archiviare e codificare in sicurezza contenuti video o audio, nonché creare pacchetti di tali contenuti per la distribuzione su richiesta e in modalità streaming live a vari tipi di client (ad esempio, TV, PC e dispositivi mobili).

È possibile creare flussi di lavoro end-to-end usando unicamente Servizi multimediali. È anche possibile usare componenti di terze parti per alcune parti del flusso di lavoro, ad esempio, la codifica con un codificatore di terze parti. Inoltre, sono possibili operazioni di caricamento, protezione, creazione di pacchetti e invio tramite Servizi multimediali.

Per creare soluzioni di Servizi multimediali, è possibile usare:

- [API REST di Servizi multimediali](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Uno dei client SDK disponibili: [Azure Media Services SDK per.NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure SDK per Java](https://github.com/Azure/azure-sdk-for-java), [Servizi multimediali di Azure per Node.js](https://github.com/fritzy/node-azure-media), [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
- Strumenti esistenti: [Portale di gestione di Azure](http://manage.windowsazure.com/) o [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).

**Contratto di servizio**: Servizi multimediali garantisce una disponibilità al 99,9% delle transazioni delle API REST per il servizio di codifica di Servizi multimediali. Con l'acquisto di almeno un'unità riservata di trasmissione in flusso, il servizio di streaming su richiesta garantisce una disponibilità al 99,9% per i contenuti multimediali esistenti. La disponibilità viene calcolata in base a un periodo di fatturazione mensile. Per altre informazioni, scaricare il [documento relativo al contratto di servizio](https://www.microsoft.com/download/details.aspx?id=39302).

Il poster seguente illustra i flussi di lavoro di Servizi multimediali di Azure, dalla creazione dei contenuti multimediali fino alla loro fruizione. È possibile scaricare il poster qui: [Poster di Servizi multimediali di Azure](http://www.microsoft.com/download/details.aspx?id=38195).

![Overview][overview]

##Concetti

Per altre informazioni, vedere l'articolo relativo ai [Concetti di Servizi Multimediali](../media-services-concepts).

##Scelta dell'esperienza multimediale

Uno dei primi passaggi per condividere contenuti video consiste nel decidere il tipo di esperienza che si vuole offrire ai clienti. In che modo verrà visualizzato il contenuto dai clienti? Sono connessi a Internet? Il contenuto verrà visualizzato in un computer o in un dispositivo mobile? I clienti si aspettano di ricevere il video in HD? Le risposte a domande come queste saranno utili per poter fornire ai clienti la miglior esperienza possibile.

###Accesso ai video
 
Esistono quattro modalità principali di accesso ai contenuti video che i clienti possono usare:

- Visualizzazione offline 
- Download progressivo
- Streaming / Streaming a velocità in bit adattiva

####Visualizzazione offline

Per visualizzare un video offline un cliente scaricherà l'intero video nel proprio computer o dispositivo. I video tendono a essere di dimensioni alquanto grandi, pertanto il completamento del download può richiedere del tempo. Il vantaggio della visualizzazione offline è dato dal fatto che non è necessario disporre di una connessione di rete per visualizzarlo, dopo che questo è stato scaricato nel dispositivo. 

####Download progressivo

Per il download progressivo è necessario che il cliente sia connesso a Internet. Questa modalità consente di iniziare a visualizzare il video prima che ne sia stato completato il download. Entrambi gli approcci, offline e download progressivo, richiedono un dispositivo con sufficiente spazio di archiviazione da poter contenere l'intero video.

####Streaming

Le tecnologie di streaming richiedono anch'esse una connessione a Internet, ma consentono di scaricare una piccola porzione del video alla volta, cancellandola dopo la visualizzazione. Questo metodo richiede solo una piccola quantità di spazio di archiviazione nel dispositivo di visualizzazione. La velocità effettiva di una connessione di rete può variare, ma i clienti si aspetteranno di poter visualizzare i video indipendentemente dalla larghezza di banda. Le tecnologie a velocità in bit adattiva consentono al lettore video di determinare le condizioni della rete e di effettuare una selezione tra più velocità in bit. Se le prestazioni della comunicazione di rete diminuiscono, il client può selezionare una velocità in bit inferiore consentendo al lettore di continuare la riproduzione del video con una qualità leggermente ridotta. Se invece le prestazioni migliorano, il client può passare a una velocità in bit maggiore e fornire quindi una migliore qualità video. Servizi multimediali di Azure supporta le seguenti tecnologie a velocità in bit adattive: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS.

###Su quali dispositivi

Un'altra decisione da prendere è il tipo di dispositivi che i clienti useranno per visualizzare i video. Servizi multimediali fornisce supporto per Web browser, smartphone, tablet, XBOX, set-top box e TV connessi.

####Web browser

I Web browser possono essere eseguiti su PC Windows, PC Macintosh e Smartphone. Durante l'esecuzione su PC o Macintosh è possibile usufruire di schermi e capacità di archiviazione di grandi dimensioni. Ciò consente di trasmettere video di qualità superiore. Nei PC Windows o Macintosh è possibile visualizzare video distribuiti da Servizi multimediali usando un'applicazione nativa o un Web browser compatibile con HTML. Le applicazioni native possono supportare Smooth Streaming, Apple HLS, download progressivo o visualizzazione offline. Le pagine Web HTML5 supportano il download progressivo.


####Smartphone

Gli smartphone dispongono di schermi di dimensioni ridotte e di minori capacità di archiviazione. Lo streaming è la scelta migliore per questi dispositivi. Sono supportati i dispositivi iPhone, Windows Phone e Android. iPhone e Android supportano Smooth Streaming e HLS. I dispositivi Windows Phone supportano Smooth Streaming.

###Tablet

I tablet hanno schermi più grandi rispetto agli smartphone, tuttavia presentano in genere minore capacità di archiviazione. Anche per i tablet, la scelta migliore è lo streaming. I tablet con maggiori capacità di archiviazione possono sfruttare i vantaggi della visualizzazione offline e del download progressivo.

####XBox

Le console XBox offrono il vantaggio di avere schermi di grandi dimensioni e maggiore capacità di archiviazione, il che rende sia la visualizzazione offline che il download progressivo, ma anche lo streaming, scelte ugualmente idonee.
Set-top box e TV connessi
Questi dispositivi presentano anch'essi schermi di grandi dimensioni ma capacità di archiviazione minime, ragion per cui la soluzione migliore è rappresentata dalla visualizzazione in streaming.

###Tecnologie supportate per dispositivo

La tabella seguente illustra ogni tipo di dispositivo e le tecnologie client supportate da Servizi multimediali:
 
<table border="1">
<tr><th>Dispositivo</th><th>Tecnologie</th></tr>
<tr><td>iOS</td><td>Smooth Streaming, Apple HLS, download progressivo</td></tr>
<tr><td>Windows Phone 8</td><td>Smooth Streaming</td></tr>
<tr><td>Windows RT</td><td>Smooth Streaming</td></tr>
<tr><td>Windows</td><td>Smooth Streaming, download progressivo</td></tr>
<tr><td>Smartphone Android</td><td>Smooth Streaming e Apple HLS</td></tr>
<tr><td>XBox</td><td>Smooth Streaming</td></tr>
<tr><td>Macintosh</td><td>Apple HLS, download progressivo</td></tr>
<tr><td>Set-top Box, TV connessi</td><td>Smooth Streaming, Apple HLS, download progressivo</td></tr>
</table>


##Flussi di lavoro di sviluppo comuni: Video-on-Demand e Live Streaming

###Video-on-Demand: Distribuire contenuti in streaming 

1. Caricare un file in formato intermedio di qualità elevata in un asset.
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva.
1. Configurare il criterio di invio degli asset (usato per la creazione dinamica dei pacchetti).
1. (Facoltativo) Configurare la protezione del contenuto e il criterio di autorizzazione chiave (usato dalla crittografia dinamica).
1. Pubblicare l'asset.
1. Inviare il contenuto pubblicato in streaming. 

###Video-on-Demand: Indicizzare il contenuto

1. Caricare un file in formato intermedio di qualità elevata in un asset.
1. Indicizzare il contenuto.
1. Usare il contenuto indicizzato.

###Video-on-Demand: Fornire il download progressivo 

1. Caricare un file in formato intermedio di qualità elevata in un asset.
1. Eseguire la codifica in Set MP4 a velocità in bit adattiva on un singolo MP4.
1. Pubblicare l'asset.
1. Eseguire il download progressivo.

[Video-on-Demand di Servizi multimediali](../media-services-video-on-demand-workflow) fornisce collegamenti ad argomenti che illustrano come eseguire le attività menzionate sopra.

###Streaming live

1. Creare e avviare un canale.
1. Recuperare l'URL di inserimento del canale.
1. Avviare e configurare il transcodificatore live di propria scelta.
1. Recuperare l'endpoint di anteprima del canale e verificare che il canale riceva correttamente il flusso live.
1. Creare un programma e un asset. 
2. Configurare il criterio di recapito per l'asset (usato dalla creazione dinamica dei pacchetti).
3. (Facoltativo) Configurare la protezione del contenuto e il criterio di autorizzazione chiave (usato dalla crittografia dinamica).
1. Per rendere il flusso disponibile agli utenti, creare un localizzatore di streaming, necessario per accedere all'asset associato al programma.
1. Avviare il programma quando si è pronti a iniziare lo streaming e l'archiviazione.
1. Arrestare il programma ogni volta che si vuole interrompere lo streaming e l'archiviazione dell'evento.
1. Eliminare il programma (e, facoltativamente, eliminare l'asset).

[Streaming live di Servizi multimediali](../media-services-live-streaming-wrokflow) fornisce collegamenti ad argomenti che illustrano come eseguire le attività menzionate sopra.

##Client

Servizi multimediali di Azure fornisce gli strumenti necessari per creare applicazioni lettore client avanzate e dinamiche per la maggior parte delle piattaforme, inclusi dispositivi iOS, dispositivi Android, Windows, Windows Phone, Xbox e set-top box.

- [Smooth Streaming Client SDK](http://www.iis.net/downloads/microsoft/smooth-streaming) 
- [Microsoft Media Platform: Player Framework](http://playerframework.codeplex.com/) 
- [Documentazione di HTML5 Player Framework](http://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
- [Plug-in Microsoft Smooth Streaming per OSMF](https://www.microsoft.com/download/details.aspx?id=36057) 
- [Media Player Framework per iOS](https://github.com/Azure/azure-media-player-framework) 
- [Licenza per Microsoft(r) Smooth Streaming Client Porting Kit](https://www.microsoft.com/mediaplatform/sspk.aspx) 
- Compilazione di applicazioni video in Windows 8 
- [Sviluppo di applicazioni video per XBox](http://xbox.create.msdn.com/) 

Per altre informazioni, vedere l'articolo relativo allo [sviluppo di applicazioni di lettore video](../media-services-develop-video-players)

##Indicazioni su modelli e procedure

[Indicazioni su modelli e procedure](https://wamsg.codeplex.com/)
[Documentazione online](https://msdn.microsoft.com/library/dn735912.aspx)
[EBook scaricabile](https://www.microsoft.com/download/details.aspx?id=42629)

##Supporto

[Il supporto tecnico di Azure](http://azure.microsoft.com/support/options/) fornisce opzioni di supporto per Azure, compreso Servizi multimediali.



<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png

<!--HONumber=47-->
