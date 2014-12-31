<properties urlDisplayName="How to manage media content" pageTitle="Come gestire i contenuti multimediali - Servizi multimediali di Azure" metaKeywords="" description="Learn how to manage your media content in Azure Media Services." metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





# Come gestire il contenuto in Servizi multimediali #
La visualizzazione del contenuto di Servizi multimediali di Azure consente di gestire il contenuto multimediale per l'account di Servizi multimediali.

Attualmente è possibile eseguire le operazioni sul contenuto seguenti direttamente dal portale:

- Visualizzare informazioni sul contenuto, ad esempio stato di pubblicazione, URL di pubblicazione, dimensione e data e ora dell'ultimo aggiornamento
- Caricare nuovo contenuto
- Codificare il contenuto
- Riprodurre video del contenuto
- Pubblicare/annullare la pubblicazione di contenuto
- Eliminare contenuto


## Procedura: Caricare contenuto ##


1. Nel [portale di gestione](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) fare clic su **Servizi multimediali** e quindi sul nome dell'account di Servizi multimediali.
2. Selezionare la pagina CONTENT. 
3. Fare clic sul pulsante **Carica** nella pagina o nella parte inferiore del portale. 
4. Nella finestra di dialogo **Carica contenuto 
** selezionare il file di asset desiderato. Fare clic sul file e quindi su **Apri** oppure premere **INVIO**.

	![UploadContentDialog][uploadcontent]

5. Nella finestra di dialogo Upload content fare clic sul pulsante con il segno di spunta per accettare il file e il nome del contenuto.
6. Verrà avviato il caricamento e sarà possibile visualizzarne lo stato nella parte inferiore del portale.  

	![JobStatus][status]

Al termine del caricamento, il nuovo asset verrà visualizzato nell'elenco Content. Per convenzione, al nome verrà aggiunto il suffisso "**-Source**" per consentire di tenere traccia del nuovo contenuto come contenuto di origine per le attività di codifica.

Se il valore delle dimensioni del file non viene aggiornato al termine del processo di caricamento, premere il pulsante **Sincronizza metadati**. Le dimensioni del file di asset verranno sincronizzate con le dimensioni effettive del file nell'archivio e verrà aggiornato il valore nella pagina Content.	


## Procedura: Codificare il contenuto

1. Nel [portale di gestione](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) fare clic su **Servizi multimediali** e quindi sul nome dell'account di Servizi multimediali. 
2. Fare clic su CONTENT nella parte superiore della pagina.
3. Fare clic sul video di origine desiderato per il processo di codifica e quindi su **Codifica** nella parte inferiore della pagina.
4. Nella finestra di dialogo Azure Media Encoder scegliere uno dei set di impostazioni di codifica comuni o avanzati.

	**Set di impostazioni comuni**

	+ **Riproduzione su PC/Mac (tramite Flash/Silverlight)**. Questo set di impostazioni genera un asset Smooth Streaming con le caratteristiche seguenti: audio stereo a 16 bit e frequenza di campionamento di 44,1 kHz con codifica CBR a 96 kbps (compressione AAC) e video 720p con codifica CBR a 6 velocità in bit da 3400 kbps a 400 kbps tramite compressione H.264 Main Profile e GOP di 2 secondi.
	+ **Riproduzione tramite HTML5 (tramite IE/Chrome/Safari)**. Questo set di impostazioni genera un singolo file MP4 con le caratteristiche seguenti: audio stereo a 16 bit e frequenza di campionamento di 44,1 kHz con codifica CBR a 128 kbps (compressione AAC) e video 720p con codifica CBR a 4500 kbps tramite compressione H.264 Main Profile.
	+ **Riproduzione su dispositivi iOS e PC/Mac**. Questo set di impostazioni genera un asset con le stesse caratteristiche dell'asset Smooth Streaming (descritte in precedenza), ma in un formato utilizzabile per trasmettere flussi Apple HLS ai dispositivi iOS. 
	+ **Codifica con protezione dei contenuti (PlayReady)**. Questo set di impostazioni produce un asset codificato con la protezione dei contenuti PlayReady.  Affinché funzioni, è necessario abilitare il servizio di distribuzione di licenze per PlayReady. A tale scopo, accedere alla scheda **PROTEZIONE CONTENUTO** e aggiungere una riga alla tabella Creazione report personalizzazione. Il servizio di licenza PlayReady di Servizi multimediali verrà abilitato pochi minuti dopo aver premuto il pulsante SALVA.   
	
	
		Per impostazione predefinita, viene usato il servizio di licenza PlayReady di Servizi multimediali. Per specificare un altro servizio dal quale i client possono ottenere una licenza per riprodurre i contenuti crittografati con PlayReady, usare le API REST o Media Services .NET SDK. Per altre informazioni, vedere [Uso della crittografia statica per proteggere i contenuti]() e impostare la proprietà **licenseAcquisitionUrl** nel set di impostazioni di Media Encryptor. In alternativa, è possibile usare la crittografia dinamica e impostare la proprietà **PlayReadyLicenseAcquisitionUrl** come descritto in [Uso della crittografia dinamica PlayReady e del server di distribuzione di licenze PlayReady](http://go.microsoft.com/fwlink/?LinkId=507720 ). 
		
		Notare che questa opzione viene visualizzata solo se si è registrati alla funzionalità di anteprima di protezione dei contenuti PlayReady. Per ottenere un'anteprima delle funzionalità, seguire la procedura descritta nella seguente pagina: [Microsoft Azure: Anteprima funzionalità](http://azure.microsoft.com/it-it/services/preview/).  
	
		
	**Set di impostazioni avanzati**
	
	+ Nell'argomento [Stringhe di set di impostazioni di attività per Azure Media Encoder](http://go.microsoft.com/fwlink/?LinkId=270865) viene fornita una descrizione di ogni set di impostazioni disponibile nell'elenco dei set di impostazioni avanzati. 


	![EncoderDialog][encoder]

	Attualmente il portale non supporta tutti i formati di codifica supportati da Media Encoder. Non supporta inoltre la crittografia/decrittografia degli asset multimediali. È possibile effettuare queste attività a livello di codice. Per altre informazioni, vedere [Creazione di applicazioni con Media Services SDK per .NET](http://go.microsoft.com/fwlink/?LinkId=270866) e [Stringhe di set di impostazioni di attività per Azure Media Encoder](http://go.microsoft.com/fwlink/?LinkId=270865).


5. Nella finestra di dialogo Azure Media Encoder immettere il nome del contenuto di output descrittivo desiderato oppure accettare il valore predefinito. Fare quindi clic sul pulsante con il segno di spunta per avviare l'operazione di codifica. Nella parte inferiore del portale sarà possibile monitorare l'avanzamento dell'operazione.

	Al termine dell'operazione di codifica, la visualizzazione avrà un aspetto simile a quello riportato di seguito. 

	![PortalViewUploadCompleted][portaloverview]


	Se il valore delle dimensioni del file non viene aggiornato al termine del processo di codifica, premere il pulsante **Sincronizza metadati**. Le dimensioni del file di asset di output verranno sincronizzate con le dimensioni effettive del file nell'archivio e verrà aggiornato il valore nella pagina Content.	

## Procedura: Pubblicare contenuto

1. Nel [portale di gestione](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) fare clic su **Servizi multimediali** e quindi sul nome dell'account di Servizi multimediali.
2. Fare clic su CONTENT nella parte superiore della pagina.
3. Fare clic su un asset non pubblicato. Fare clic sul pulsante Publish per pubblicarlo su un URL pubblico. Dopo avere pubblicato il contenuto su un URL, sarà possibile aprirlo con un lettore client in grado di eseguire il rendering del contenuto codificato.

 ![PublishedContent][publishedcontent]

## Procedura: Riprodurre contenuto dal portale


1. Nel [portale di gestione](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) fare clic su **Servizi multimediali** e quindi sul nome dell'account di Servizi multimediali.
2. Fare clic su CONTENT nella parte superiore della pagina.
3. Fare clic sul contenuto video desiderato e quindi sul pulsante **Riproduci** nella parte inferiore del portale. È possibile riprodurre dal portale solo il contenuto che è stato pubblicato e la codifica deve essere supportata dal browser in uso.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png

<!--HONumber=35_1-->
