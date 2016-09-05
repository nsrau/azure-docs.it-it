<properties
	pageTitle="Uso di più file di input e proprietà del componente con il codificatore Premium | Microsoft Azure"
	description="Questo argomento illustra come usare setRuntimeProperties per usare più file di input e passare dati personalizzati al processore di contenuti multimediali del flusso di lavoro Premium del codificatore multimediale."
	services="media-services"
	documentationCenter=""
	authors="xpouyat"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"  
	ms.author="xpouyat;anilmur;juliako"/>

# Uso di più file di input e proprietà del componente con il codificatore Premium

## Panoramica

In alcuni scenari potrebbe essere necessario personalizzare le proprietà del componente, specificare contenuto Clip List XML oppure inviare più file di input durante l'invio di un'attività con il processore di contenuti multimediali del **flusso di lavoro Premium del codificatore multimediale**. Di seguito sono riportati alcuni esempi:

- Sovrapporre testo sul video e impostare il valore del testo, ad esempio la data corrente, in fase di esecuzione per ogni video di input.
- Personalizzare il contenuto Clip List XML per specificare uno o più file di origine, con o senza trimming e così via.
- Sovrapporre un logo al video di input durante la codifica del video.

Per indicare al **flusso di lavoro Premium del codificatore multimediale** che verranno modificate alcune proprietà nel flusso di lavoro quando si crea l'attività o si inviano più file di input, è necessario usare una stringa di configurazione contenente **setRuntimeProperties** e/o **transcodeSource**. Questo argomento ne illustra l'uso.


## Sintassi della stringa di configurazione

La stringa di configurazione da impostare nell'attività di codifica usa un documento XML simile al seguente:

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


Di seguito è riportato il codice C# che legge la configurazione XML da un file e la passa all'attività di un processo:

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## Personalizzazione delle proprietà del componente  

### Proprietà con un valore semplice
In alcuni casi è utile personalizzare una proprietà del componente insieme al file del flusso di lavoro che verrà eseguito dal flusso di lavoro Premium del codificatore multimediale.

Si supponga che sia stato progettato un flusso di lavoro che sovrappone testo ai video e che il testo, ad esempio la data corrente, debba essere impostato in fase di esecuzione. Questa operazione può essere eseguita inviando il testo da impostare come nuovo valore della proprietà text del componente di sovrapposizione dell'attività di codifica. Questo meccanismo consente di modificare altre proprietà di un componente nel flusso di lavoro, ad esempio la posizione o il colore della sovrapposizione, la velocità in bit del codificatore AVC e così via.

**setRuntimeProperties** viene usato per sostituire una proprietà nei componenti del flusso di lavoro.

Esempio:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### Proprietà con un valore XML

Per impostare una proprietà che prevede un valore XML, incapsulare usando `<![CDATA[ and ]]>`.

Esempio:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Non inserire un ritorno a capo subito dopo `<![CDATA[`.


### Valore di propertyPath

Negli esempi precedenti, il valore di propertyPath era "/Media File Input/filename", o "/inactiveTimeout" oppure "clipListXml". Si tratta in genere del nome del componente seguito dal nome della proprietà. Il percorso può avere più o meno livelli, ad esempio "/primarySourceFile" perché la proprietà è alla radice del flusso di lavoro o "/Video Processing/Graphic Overlay/Opacity" perché la sovrimpressione è in un gruppo.

Per verificare il percorso e il nome della proprietà, usare il pulsante di azione immediatamente accanto a ogni proprietà. È possibile fare clic su questo pulsante di azione e selezionare **Modifica**. Verrà visualizzato il nome effettivo della proprietà e lo spazio dei nomi immediatamente sopra.

![Azione/modifica](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Proprietà](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## Più file di input

Ogni attività inviata al **flusso di lavoro Premium del codificatore multimediale** richiede due asset:

- il primo è un *asset di flusso di lavoro* che contiene un file di flusso di lavoro. È possibile progettare file di flusso di lavoro usando [Progettazione flussi di lavoro](media-services-workflow-designer.md).
- il secondo è un *asset multimediale* che contiene i file multimediali da codificare.

Quando si inviano più file multimediali al **flusso di lavoro Premium del codificatore multimediale**, si applicano i vincoli seguenti:

- Tutti i file multimediali devono essere nello stesso *asset multimediale*. L'uso di più asset multimediali non è supportato.
- È necessario impostare il file primario nell'asset multimediale. Si tratta idealmente del file video principale che il codificatore dovrà elaborare.
- È necessario passare al processore dati di configurazione che includono l'elemento **setRuntimeProperties** e/o **transcodeSource**.
  - **setRuntimeProperties** viene usato per sostituire la proprietà Filename o un'altra proprietà nei componenti del flusso di lavoro.
  - **transcodeSource** viene usato per specificare il contenuto Clip List XML.

Connessioni nel flusso di lavoro:

 - se si usano uno o più componenti Media File Input e si prevede di usare **setRuntimeProperties** per specificare il nome del file, non collegare il pin del componente file primario ai componenti Media File Input. Assicurarsi che non esistano collegamenti tra l'oggetto file primario e i componenti Media File Input.
 - Se si preferisce usare Clip List XML e un componente Media Source è possibile collegare entrambi.

![Nessun collegamento dal file di origine primario a Media File Input](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Non sono presenti collegamenti dal file primario ai componenti di Media File Input se si usa setRuntimeProperties per impostare la proprietà Filename.*

![Collegamento da Clip List XML a Clip List Source](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*È possibile collegare Clip List XML a Media Source e usare transcodeSource.*


### Personalizzazione di Clip List XML
È possibile specificare contenuto Clip List XML nel flusso di lavoro, in fase di esecuzione, usando **transcodeSource** nel codice XML della stringa di configurazione. Questa operazione richiede che il pin di Clip List XML sia collegato al componente Media Source nel flusso di lavoro.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Per specificare /primarySourceFile per usare questa proprietà per l'assegnazione di un nome ai file di output con espressioni, è consigliabile passare Clip List XML come proprietà, *dopo* la proprietà /primarySourceFile, per evitare che Clip List venga sostituito dall'impostazione di /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Con altro trimming preciso al fotogramma:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## Esempio

Si consideri un esempio in cui si vuole sovrapporre un logo al video di input durante la codifica del video. In questo esempio, il video di input è denominato "MyInputVideo.mp4" e il logo è denominato "MyLogo.png". Eseguire la procedura seguente:

- Creare un asset del flusso di lavoro con il file del flusso di lavoro. L'esempio è riportato di seguito.
- Creare un asset multimediale contenente due file: MyInputVideo.mp4 come file primario e MyLogo.png.
- Inviare un'attività al processore di contenuti multimediali del flusso di lavoro Premium del codificatore multimediale con gli asset di input precedenti e specificare la stringa di configurazione seguente.

Configurazione:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


Nell'esempio precedente, il nome del file video viene invitato al componente Media File Input e alla proprietà primarySourceFile. Il nome del file di logo viene inviato a un altro componente Media File Input, connesso al componente di sovrimpressione grafica.

>[AZURE.NOTE]Il nome del file video viene inviato alla proprietà primarySourceFile per usare questa proprietà nel flusso di lavoro, ad esempio per compilare il nome file di output con espressioni.


### Procedura dettagliata di creazione del flusso di lavoro per la sovrapposizione di un logo sul video     

Di seguito sono descritti i passaggi per creare un flusso di lavoro che ha due file come input: un video e un'immagine. L'immagine verrà sovrapposta sul video.

Aprire **Progettazione flussi di lavoro** e selezionare **File** > **Nuova area di lavoro** > **Transcode Blueprint**.

Il nuovo flusso di lavoro visualizzerà 3 elementi:

- Primary Source File
- Clip List XML
- Output File/Asset

![Nuovo flusso di lavoro della codifica](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nuovo flusso di lavoro della codifica*


Per accettare il file multimediale di input, iniziare aggiungendo un componente Media File Input. Per aggiungere un componente al flusso di lavoro, cercarlo nella casella di ricerca del repository e trascinare la voce desiderata sul riquadro della finestra di progettazione.

Aggiungere quindi il file video da usare per la progettazione del flusso di lavoro. A questo scopo, fare clic sul riquadro dello sfondo in Progettazione flussi di lavoro e cercare la proprietà Primary Source File (File di origine primario) nel riquadro delle proprietà a destra. Fare clic sull'icona della cartella e selezionare il file video appropriato.

![File di origine primario](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*File di origine primario*


Specificare quindi il file video nel componente Media File Input.

![Origine Media File Input](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Origine Media File Input*


Al termine dell'operazione, il componente Media File Input esaminerà il file e popolerà i pin di output per riflettere il file esaminato.

Il passaggio successivo consiste nell'aggiungere un componente "Video Data Type Updater" (Strumento di aggiornamento tipo dati video) per specificare lo spazio colore Rec.709. Aggiungere un set "Convertitore formato video" impostato per Layout dati/Tipo layout = Planare configurabile. Il flusso video verrà così convertito in un formato che può essere usato come origine del componente di sovrapposizione.

![Video Data Type Updater (Strumento di aggiornamento tipo dati video) e Format Converter (Convertitore formato video)](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Strumento di aggiornamento tipo dati video e Convertitore formato video*

![Layout type = Configurable Planar (Tipo layout = Planare configurabile)](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Il tipo di layout è Planare configurabile*

Aggiungere quindi un componente di sovrapposizione video e collegare il pin del video non compresso al pin del video non compresso del componente Media File Input.

Aggiungere un altro Media File Input per caricare il file del logo, fare clic sul componente e rinominarlo in "Media File Input Logo", selezionare un'immagine, ad esempio un file con estensione png, nella proprietà del file. Collegare il pin dell'immagine non compressa al pin dell'immagine non compressa della sovrimpressione.

![Componente della sovrimpressione e origine del file di immagine](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Componente della sovrimpressione e origine del file di immagine*


Per modificare la posizione del logo sul video, ad esempio per posizionare il logo al 10% di distanza dall'angolo superiore sinistro del video, deselezionare la casella di controllo Input manuale. È possibile deselezionare questa opzione perché si usa Media File Input per fornire il file del logo al componente della sovrimpressione.

![Posizione della sovrimpressione](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posizione della sovrimpressione*


Per codificare il flusso video in H.264, aggiungere i componenti AVC Video Encoder e AAC Encoder all'area di progettazione. Collegare i pin. Configurare il codificatore AAC e selezionare Conversione formato audio/Set di impostazioni: 2.0 - S, D.

![Codificatori audio e video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificatori audio e video*


Aggiungere ora i componenti **ISO Mpeg-4 Multiplexer** e **File Output** e collegare i pin come illustrato.

![Multiplexer MP4 e file di output](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Multiplexer MP4 e file di output*


È necessario definire il nome del file di output. Fare clic sul componente **File Output** e modificare l'espressione per il file:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome file di output](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome file di output*

È possibile eseguire il flusso di lavoro in locale per verificare che venga eseguito correttamente.

Al termine, è possibile eseguire il flusso di lavoro in Servizi multimediali di Azure.

Preparare prima un asset in Servizi multimediali di Azure con due file: il file video e il logo. Usare a tal fine .NET o l'API REST. È anche possibile eseguire l'operazione con il portale di Azure o nello [strumento di Esplorazione di Servizi multimediali di Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Questa esercitazione illustra come gestire gli asset con AMSE. Esistono due modi per aggiungere file a un asset:

- Creare una cartella locale, copiare i due file al suo interno e trascinare la cartella nella scheda **Asset**.
- Caricare il file video come asset, quindi visualizzare le informazioni sull'asset, passare alla scheda File e caricare un altro file (logo).

>[AZURE.NOTE]Impostare un file primario nell'asset, ovvero il file video principale.

![File di asset nello strumento di esplorazione di Servizi multimediali di Azure](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*File di asset nello strumento di esplorazione di Servizi multimediali di Azure*


Selezionare l'asset e scegliere di codificarlo con il codificatore Premium. Caricare il flusso di lavoro e selezionarlo.

Fare clic sul pulsante per passare i dati al processore, quindi aggiungere il codice XML seguente per impostare le proprietà di runtime:

![Codificatore Premium nello strumento di esplorazione di Servizi multimediali di Azure](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificatore Premium nello strumento di esplorazione di Servizi multimediali di Azure*


Incollare quindi i dati XML seguenti. È necessario specificare il nome del file video per Media File Input e primarySourceFile. Specificare anche il nome del file del logo.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Se si usa .NET SDK per creare ed eseguire l'attività, questi dati XML devono essere passati come stringa di configurazione.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Al termine del processo, il file MP4 nell'asset di output visualizzerà la sovrimpressione.

![Sovrimpressione sul video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sovrimpressione sul video*


È possibile scaricare il flusso di lavoro di esempio da [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## Vedere anche

- [Introduzione alla codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Come usare la codifica Premium in Servizi multimediali di Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Codifica di contenuti su richiesta con Servizi multimediali di Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Codec e formati del flusso di lavoro Premium del codificatore multimediale](media-services-premium-workflow-encoder-formats.md)

- [File del flusso di lavoro di esempio](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Strumento di esplorazione di Servizi multimediali di Azure](http://aka.ms/amse)

## Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0824_2016-->