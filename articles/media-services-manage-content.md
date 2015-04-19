<properties 
	pageTitle="Come gestire i contenuti multimediali - Servizi multimediali di Azure" 
	description="Informazioni su come gestire i contenuti multimediali in Servizi multimediali di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





# Come gestire il contenuto in Servizi multimediali #

Questo articolo fa parte della serie [Flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md). 

Questo argomento descrive come usare il portale di gestione di Azure per gestire i contenuti multimediali nell'account di Servizi multimediali.

Attualmente è possibile eseguire le seguenti operazioni sul contenuto direttamente dal portale:

- Visualizzare informazioni sul contenuto, ad esempio stato di pubblicazione, URL di pubblicazione, dimensione, data e ora dell'ultimo aggiornamento e se l'asset è crittografato.
- Caricare nuovo contenuto
- Indicizzare il contenuto
- Codificare il contenuto
- Riprodurre il contenuto
- Crittografare il contenuto
- Pubblicare/annullare la pubblicazione di contenuto



## <a id="upload"></a>Procedura: Caricare contenuto 


1. Nel [portale di gestione](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) scegliere **Servizi multimediali**, quindi fare clic sul nome dell'account di Servizi multimediali.
2. Selezionare la pagina CONTENT. 
3. Fare clic sul pulsante **Carica** nella pagina o nella parte inferiore del portale. 
4. Nella finestra di dialogo **Carica contenuto 
** selezionare il file di asset desiderato. Fare clic sul file e quindi su **Apri** oppure premere **INVIO**.

	![UploadContentDialog][uploadcontent]

5. Nella finestra di dialogo Upload content fare clic sul pulsante con il segno di spunta per accettare il file e il nome del contenuto.
6. Verrà avviato il caricamento e sarà possibile visualizzarne lo stato nella parte inferiore del portale.  

	![JobStatus][status]

Al termine del caricamento, il nuovo asset verrà visualizzato nell'elenco Content. Per convenzione, al nome verrà aggiunto il suffisso "**-Source**" per consentire di tenere traccia del nuovo contenuto come contenuto di origine per le attività di codifica.

![ContentPage][contentpage]

Se il valore delle dimensioni del file non viene aggiornato al termine del processo di caricamento, premere il pulsante **Sincronizza metadati**. Le dimensioni del file di asset verranno sincronizzate con le dimensioni effettive del file nell'archivio e verrà aggiornato il valore nella pagina Content.	

## <a id="index"></a>Procedura: Indicizzare il contenuto

Azure Media Indexer consente di rendere disponibile per la ricerca il contenuto dei file multimediali e di generare una trascrizione full-text per i sottotitoli codificati e le parole chiave. L'indicizzazione del contenuto può essere eseguita dal portale di gestione con la procedura riportata di seguito. Tuttavia, per avere più controllo sui file e sulle modalità del processo di indicizzazione, è possibile usare l'SDK di Servizi multimediali per .NET o le API REST. Per altre informazioni vedere [Indicizzazione di file multimediali con Azure Media Indexer](https://msdn.microsoft.com/it-it/library/azure/dn783455.aspx).

I seguenti passaggi mostrano come usare il portale di gestione per indicizzare il contenuto.

1. Selezionare il file da indicizzare.
	Se l'indicizzazione è supportata per questo tipo di file, il pulsante ELABORA viene abilitato nella parte inferiore della pagina CONTENUTO.
1. Premere il pulsante ELABORA.
2. Nella finestra di dialogo **Elabora** scegliere il processore **Azure Media Indexer**.
3. Quindi, inserire nella finestra di dialogo Elabora le informazioni dettagliate su **titolo** e **descrizione** relative al file multimediale di input.
	
	![Process][process]

## <a id="encode"></a>Procedura: Codificare il contenuto

Per distribuire un video digitale tramite Internet è necessario comprimere il file multimediale. Servizi multimediali fornisce un codificatore multimediale che consente di specificare la modalità di codifica del contenuto (ad esempio, i codec da usare, il formato di file, la risoluzione e la velocità in bit). 

Quando si usa Servizi multimediali di Azure, uno degli scenari più frequenti è l'invio di streaming a velocità in bit adattiva ai client. Con lo streaming a velocità in bit adattiva, il client può passare a un flusso con velocità in bit maggiore o minore durante la visualizzazione del video, in base alla larghezza di banda attuale della rete, all'utilizzo della CPU e ad altri fattori. Servizi multimediali supporta le seguenti tecnologie di streaming a velocità in bit adattiva: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH e HDS (solo per i titolari di una licenza Adobe PrimeTime/Access). 

Servizi multimediali fornisce il servizio di creazione dinamica dei pacchetti che consente di inviare il contenuto codificato in formato MP4 o Smooth Streaming con velocità in bit adattiva in formati di streaming supportati da Servizi multimediali (MPEG DASH, HLS, Smooth Streaming, HDS) senza dover ricreare i pacchetti con questi formati di streaming. 

Per sfruttare i vantaggi del servizio di creazione dinamica dei pacchetti, è necessario seguire questa procedura:

- Codificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a velocità in bit adattiva (i passaggi per la codifica sono descritti più avanti in questa esercitazione).
- Ottenere almeno un'unità di streaming su richiesta per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti. Per altre informazioni, vedere [Come scalare un servizio multimediale](media-services-manage-origins#scale_streaming_endpoints.md).

Con la creazione dinamica dei pacchetti si archiviano e si pagano solo i file in un unico formato di archiviazione e Servizi multimediali crea e fornisce la risposta appropriata in base alle richieste di un client. 

Oltre alle funzionalità di creazione dinamica dei pacchetti, le unità riservate di streaming su richiesta offrono capacità in uscita dedicate, acquistabili in incrementi di 200 Mbps. Per impostazione predefinita, lo streaming on demand è configurato in un modello di istanza condivisa in base al quale le risorse del server (ad esempio, calcolo, capacità in uscita e così via) vengono condivise con tutti gli altri utenti. Per migliorare la velocità effettiva dello streaming on demand, si consiglia di acquistare unità riservate di streaming on demand.

Questa sezione descrive la procedura per la codifica del contenuto con Azure Media Encoder tramite il portale di gestione.

1.  Selezionare il file da codificare.
	Se la codifica è supportata per questo tipo di file, il pulsante ELABORA viene abilitato nella parte inferiore della pagina CONTENUTO.
4. Nella finestra di dialogo **Elabora** scegliere il processore **Azure Media Encoder**.
5. Scegliere una delle **configurazioni di codifica**.

	![Process2][process2]

		
	L'argomento [Stringhe di set di impostazioni di attività per Azure Media Encoder](https://msdn.microsoft.com/it-it/library/azure/dn619392.aspx) spiega il significato di ciascun set di impostazioni nelle categorie **Set di impostazioni per streaming adattivo (creazione dinamica dei pacchetti)**, **Set di impostazioni il download progressivo** e **Set di impostazioni legacy per streaming adattivo**  .  


	Le **altre** configurazioni sono descritte di seguito:

	+ **Codifica con protezione dei contenuti (PlayReady)**. Questo set di impostazioni produce un asset codificato con la protezione dei contenuti PlayReady.  
	
	
		Per impostazione predefinita, viene usato il servizio di licenza PlayReady di Servizi multimediali. Per specificare un altro servizio dal quale i client possono ottenere una licenza per riprodurre i contenuti crittografati con PlayReady, usare le API REST o Media Services .NET SDK. Per altre informazioni, vedere [Uso della crittografia statica per proteggere i contenuti]() e impostare la proprietà **licenseAcquisitionUrl** nel set di impostazioni di Media Encryptor. In alternativa, è possibile usare la crittografia dinamica e impostare la proprietà **PlayReadyLicenseAcquisitionUrl** come descritto in [Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze PlayReady](http://go.microsoft.com/fwlink/?LinkId=507720 ). 
	+ **Riproduzione su PC/Mac (tramite Flash/Silverlight)**. Questo set di impostazioni genera un asset Smooth Streaming con le seguenti caratteristiche: audio stereo a 16 bit e frequenza di campionamento di 44,1 kHz con codifica CBR a 96 kbps (compressione AAC) e video 720p con codifica CBR a 6 velocità in bit da 3400 kbps a 400 kbps tramite compressione H.264 Main Profile e GOP di 2 secondi.
	+ **Riproduzione tramite HTML5 (tramite IE/Chrome/Safari)**. Questo set di impostazioni genera un singolo file MP4 con le seguenti caratteristiche: audio stereo a 16 bit e frequenza di campionamento di 44,1 kHz con codifica CBR a 128 kbps (compressione AAC) e video 720p con codifica CBR a 4500 kbps tramite compressione H.264 Main Profile.
	+ **Riproduzione su dispositivi iOS e PC/Mac**. Questo set di impostazioni genera un asset con le stesse caratteristiche dell'asset Smooth Streaming (descritte in precedenza), ma in un formato utilizzabile per trasmettere flussi Apple HLS ai dispositivi iOS. 

5. Quindi, immettere il nome del contenuto di output descrittivo desiderato oppure accettare il valore predefinito. Fare quindi clic sul pulsante con il segno di spunta per avviare l'operazione di codifica. Nella parte inferiore del portale sarà possibile monitorare l'avanzamento dell'operazione.
6. Premere OK.

	Al termine della codifica, la pagina CONTENUTO includerà il file codificato. 

	Per visualizzare l'avanzamento del processo di codifica, passare alla pagina **PROCESSI**.  


	Se il valore delle dimensioni del file non viene aggiornato al termine del processo di codifica, premere il pulsante **Sincronizza metadati**. Le dimensioni del file di asset di output verranno sincronizzate con le dimensioni effettive del file nell'archivio e verrà aggiornato il valore nella pagina Content.	

## <a id="encrypt"></a>Procedura: Crittografare il contenuto

Se si desidera crittografare in modo dinamico l'asset in Servizi multimediali con una chiave AES o PlayReady DRM, assicurarsi di seguire questa procedura:

- Codificare il file in formato intermedio (di origine) in un set di file MP4 o Smooth Streaming a velocità in bit adattiva (i passaggi per la codifica sono descritti nella sezione [Codificare il contenuto](#encode) di questo argomento).
- Ottenere almeno un'unità di streaming su richiesta per l'endpoint di streaming da cui si pianifica la distribuzione dei contenuti. Per altre informazioni, vedere [Come scalare un servizio multimediale](media-services-manage-origins#scale_streaming_endpoints.md).
- Configurare "i criteri di servizio predefiniti relativi alla chiave AES" o "i criteri di servizio predefiniti relativi alla licenza". Per altre informazioni, vedere l'argomento relativo alla [configurazione dei criteri di autorizzazione della chiave simmetrica](media-services-portal-configure-content-key-auth-policy.md).  


	Quando si è pronti ad abilitare la crittografia, premere il pulsante **CRITTOGRAFIA** nella parte inferiore della pagina **CONTENUTO**.

	![Encrypt][encrypt] 

	Dopo aver abilitato la crittografia, ogni volta che un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante AES o PlayReady. Per decrittografare il flusso, il lettore richiederà la chiave dal servizio di distribuzione delle chiavi. Per decidere se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati.

## <a id="publish"></a>Procedura: Pubblicare contenuto

Quando si pubblica il contenuto, viene fornito un URL per il download in streaming o progressivo. Con questo URL, il client sarà in grado di riprodurre i video.

1. Fare clic sull'asset da pubblicare. 
2. Fare quindi clic sul pulsante Pubblica. 
	
	Dopo avere pubblicato il contenuto su un URL, sarà possibile aprirlo con un lettore client in grado di eseguire il rendering del contenuto codificato.

 ![PublishedContent][publishedcontent]

## Procedura: Riprodurre contenuto dal portale

Il portale di gestione fornisce un Lettore di contenuti di Servizi multimediali che è possibile usare per testare il video.

Fare clic sul contenuto video desiderato e quindi sul pulsante **Riproduci** nella parte inferiore del portale. 
 
È possibile riprodurre dal portale solo il contenuto che è stato pubblicato e la codifica deve essere supportata dal browser in uso.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
<!--HONumber=45--> 
