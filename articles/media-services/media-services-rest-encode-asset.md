<properties 
	pageTitle="Come codificare un asset mediante Azure Media Encoder" 
	description="Informazioni su come usare Azure Media Encoder per codificare contenuti multimediali in Servizi multimediali. Negli esempi di codice viene usata l'API REST." 
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
	ms.date="06/18/2015" 
	ms.author="juliako"/>


#Come codificare un asset mediante Azure Media Encoder

Questo articolo fa parte della serie relativa al [flusso di lavoro Video on Demand di Servizi multimediali](media-services-video-on-demand-workflow.md).

##Panoramica
Per distribuire un video digitale tramite Internet è necessario comprimere il file multimediale. I file video digitali hanno dimensioni piuttosto elevate e possono risultare troppo grandi per la distribuzione su Internet o per la visualizzazione corretta sui dispositivi dei clienti. Mediante il processo di codifica è possibile comprimere video e audio per consentire ai clienti di visualizzare i file multimediali.

I processi di codifica sono tra le operazioni di elaborazione più frequenti in Servizi multimediali. Questi processi vengono creati per convertire i file multimediali da una codifica all'altra. Durante la codifica è possibile usare il codificatore multimediale incorporato in Servizi multimediali. È inoltre possibile usare un codificatore fornito da un partner di Servizi multimediali. I codificatori di terze parti sono disponibili tramite Azure Marketplace. È possibile specificare i dettagli relativi alle attività di codifica usando stringhe di set di impostazioni definite per il codificatore oppure file di configurazione di set di impostazioni. Per i tipi di set di impostazioni disponibili, vedere [Set di impostazioni di attività per Servizi multimediali di Azure](https://msdn.microsoft.com/library/azure/dn619392.aspx). Se si usa un codificatore di terze parti, è necessario [convalidare i file](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Ogni processo può includere una o più attività in base al tipo di elaborazione che si desidera eseguire. Tramite l'API REST è possibile creare processi e le attività correlate in due modi: le attività possono essere definite inline mediante la proprietà di navigazione Tasks in entità Job o mediante l'elaborazione batch OData. L'SDK di Servizi multimediali usa l'elaborazione batch. Tuttavia, per semplificare la leggibilità degli esempi di codice inclusi in questo argomento le attività sono state definite inline. Per informazioni sull'elaborazione batch, vedere l'articolo relativo all'[elaborazione batch OData (Open Data Protocol)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/). È anche possibile trovare un esempio di elaborazione batch nell'argomento [Processo](https://msdn.microsoft.com/library/azure/hh974289.aspx).

È consigliabile codificare sempre i file in formato intermedio con un set MP4 a velocità in bit adattiva e quindi convertire il set nel formato desiderato mediante la [creazione dinamica dei pacchetti](https://msdn.microsoft.com/library/azure/jj889436.aspx). Per avvalersi della creazione dinamica dei pacchetti, è necessario ottenere prima almeno un'unità di streaming on demand per l'endpoint di streaming da cui si intende distribuire il contenuto. Per altre informazioni, vedere la sezione relativa al [ridimensionamento di Servizi multimediali](media-services-manage-origins.md#scale_streaming_endpoints).

Se l'asset di output è protetto con crittografia di archiviazione, è necessario configurare i criteri di distribuzione degli asset. Per altre informazioni, vedere [Procedura: Configurare i criteri di distribuzione degli asset](media-services-rest-configure-asset-delivery-policy.md).



##Creare un processo con una singola attività di codifica 

>[AZURE.NOTE]Quando si usa l'API REST di Servizi multimediali, tenere presenti le seguenti considerazioni:
>
>Quando si accede alle entità in Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [Panoramica dell'API REST di Servizi multimediali](media-services-rest-how-to-use.md).

>Dopo aver stabilito la connessione a https://media.windows.net, si riceverà un reindirizzamento 301 che indica un altro URI di Servizi multimediali. Le chiamate successive dovranno essere eseguite al nuovo URI, come descritto in [Connessione a un account di Servizi multimediali mediante l'API REST](media-services-rest-connect_programmatically.md).


Il seguente esempio mostra come creare e pubblicare un processo con un'attività impostata per codificare un video con determinati valori di risoluzione e qualità. Quando si esegue la codifica con Codificatore multimediale di Azure, è possibile usare i set di impostazioni di attività specificati [qui](https://msdn.microsoft.com/library/azure/dn619389.aspx).
	
Richiesta:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Risposta:
	
	HTTP/1.1 201 Created

	. . . 

##Considerazioni

- Le proprietà TaskBody DEVONO usare codice XML letterale per definire il numero di asset di input o di output che verranno usati dall'attività. L'argomento Task contiene la definizione dello schema XML per il codice XML.
- Nella definizione TaskBody ogni valore interno per <inputAsset> e <outputAsset>deve essere impostato come JobInputAsset(value) o JobOutputAsset(value).
- Un'attività può avere più asset di output. Un oggetto JobOutputAsset(x) può essere usato solo una volta come output di un'attività in un processo.
- È possibile specificare JobInputAsset o JobOutputAsset come asset di input di un'attività.
- Le attività non devono formare un ciclo.
- Il parametro value passato a JobInputAsset o JobOutputAsset rappresenta il valore di indice di un asset. Gli asset effettivi vengono definiti nelle proprietà di navigazione InputMediaAssets e OutputMediaAssets nella definizione dell'entità Job. 
- Poiché Servizi multimediali si basa su OData versione 3, i riferimenti ai singoli asset nelle raccolte delle proprietà di navigazione InputMediaAssets e OutputMediaAssets vengono definiti mediante una coppia nome/valore "__metadata : uri".
- La proprietà InputMediaAssets è mappata a uno o più asset creati in Servizi multimediali. Le proprietà OutputMediaAssets vengono create dal sistema. Non fanno riferimento a un asset esistente.
- Per assegnare un nome a OutputMediaAssets è possibile usare l'attributo assetName. Se questo attributo non è presente, il nome della proprietà OutputMediaAssets corrisponderà al valore del testo interno dell'elemento <outputAsset> preceduto dal nome o dall'ID del processo, nel caso in cui la proprietà Name non sia definita. Se ad esempio si è impostato "Sample" come valore di assetName, la proprietà Name di OutputMediaAssets sarà impostata su "Sample". Se invece non si è impostato un valore per assetName, ma si è impostato "NewJob" come nome del processo, il nome di OutputMediaAssets sarà "JobOutputAsset(value)_NewJob". 

Il seguente esempio mostra impostare l'attributo assetName:

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}



##Creare un processo con attività concatenate

In molti scenari di applicazione, gli sviluppatori desiderano creare una serie di attività di elaborazione. In Servizi multimediali è possibile creare una serie di attività concatenate. Ogni attività esegue diversi passaggi di elaborazione e può usare processori di contenuti multimediali differenti. Le attività concatenate possono trasferire un asset da un'attività a un'altra e consentono quindi l'esecuzione delle attività dell'asset in sequenza lineare. Tuttavia, non è necessario che le attività eseguite in un processo siano in sequenza. Quando si crea un'attività concatenata, gli oggetti **ITask** concatenati vengono creati in un singolo oggetto **IJob**.

>[AZURE.NOTE]Attualmente è previsto un limite di 30 attività per processo. Se è necessario concatenare più di 30 attività, creare più processi in modo da contenerle tutte.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}

###Considerazioni

Per abilitare il concatenamento di attività:

- Un processo deve avere almeno due attività.
- Deve essere presente almeno un'attività il cui input viene usato come output di un'altra attività nel processo.


## Creare un processo tramite JobTemplate


Quando si elaborano più asset usando un set comune di attività, le entità JobTemplate sono utili per specificare le impostazioni di attività predefinite, l'ordine delle attività e così via.

L'esempio seguente mostra come creare un'entità JobTemplate con un'entità TaskTemplate definita inline. L'entità TaskTemplate usa Codificatore multimediale di Azure come entità MediaProcessor per codificare il file di asset. Tuttavia, può usare anche altre entità Mediaprocessor.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]A differenza di altre entità di Servizi multimediali, è necessario definire un nuovo identificatore GUID per ogni entità TaskTemplate e inserirlo nel corpo della richiesta in taskTemplateId e nella proprietà Id. Lo schema di identificazione del contenuto deve seguire lo schema descritto in Identificare le entità di Servizi multimediali di Azure. Inoltre, non è possibile aggiornare le entità JobTemplate. È invece necessario crearne una nuova con le modifiche aggiornate.
 

Se l'esito è positivo, viene restituita la seguente risposta:
	
	HTTP/1.1 201 Created
	
	. . .


L'esempio seguente mostra come creare un processo che fa riferimento all'ID di un'entità JobTemplate:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

Se l'esito è positivo, viene restituita la seguente risposta:
	
	HTTP/1.1 201 Created
	
	. . . 


##Controllo dei nomi file di output del codificatore di Servizi multimediali 

Per impostazione predefinita, Codificatore multimediale di Azure crea nomi di file di output combinando diversi attributi dell'asset di input e al processo di codifica. Ogni attributo viene identificato mediante una macro come descritto di seguito.

Di seguito è riportato un elenco completo delle macro disponibili per la denominazione dei file di output: Velocità in bit audio: velocità in bit usata per la codifica audio, specificata in kbps

- Codec audio: codec usato per la codifica audio; i valori consentiti sono AAC, WMA e DDP
- Conteggio canali: numero di canali audio codificati; i valori consentiti sono: 1, 2 o 6
- Estensione predefinita: estensione del file predefinita 
- Lingua: codice della lingua BCP-47 che rappresenta la lingua usata nel file audio. Il valore predefinito per questa impostazione è "und". 
- Nome file originale: nome del file caricato in Archiviazione di Azure
- StreamId – StreamId: ID flusso come definito dall'attributo streamID dell'elemento <StreamInfo> nel file del set di impostazioni 
- Codec video: codec usato per la codifica video; i valori consentiti sono H264 e VC1
- Velocità in bit video: velocità in bit usata per la codifica video, specificata in kbps

Queste macro possono essere combinate in qualsiasi permutazione per controllare il nome dei file generati dal Codificatore di Servizi multimediali. Ad esempio, la convenzione di denominazione predefinita è:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

La convenzione di denominazione dei file viene specificata mediante l'attributo DefaultMediaOutputFileName dell'elemento [Set di impostazioni](https://msdn.microsoft.com/library/azure/dn554334.aspx). Ad esempio:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

Il codificatore inserisce caratteri di sottolineatura tra una macro e l'altra, ad esempio la configurazione sopra darebbe come risultato un nome file simile al seguente: MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4.

##Passaggi successivi
Dopo avere creato un processo per la codifica di un asset, passare all'argomento [Come verificare l'avanzamento di un processo con Servizi multimediali](media-services-rest-check-job-progress.md).

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=62-->