<properties 
	pageTitle="Esercitazione per l'app Smooth Streaming Windows Store" 
	description="Informazioni su come usare Servizi multimediali di Azure per creare un'applicazione Windows Store C# con un controllo XML MediaElement per riprodurre contenuto Smooth Streaming." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016"   
	ms.author="juliako"/>



#Come creare un'applicazione Windows Store Smooth Streaming

Smooth Streaming Client SDK per Windows 8 consente agli sviluppatori di creare app di Windows Store in grado di riprodurre contenuto Smooth Streaming in diretta e su richiesta. Oltre a fornire le funzionalità di riproduzione di base del contenuto Smooth Streaming, l'SDK offre inoltre funzionalità avanzate, ad esempio protezione Microsoft PlayReady, limitazione del livello di qualità, DVR in diretta, commutazione del flusso audio, ascolto per aggiornamenti di stato (ad esempio variazioni del livello di qualità) ed eventi di errore, e così via. Per altre informazioni sulle funzionalità supportate, vedere le [note sulla versione](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Per ulteriori informazioni, vedere [Player Framework per Windows 8](http://playerframework.codeplex.com/).

In questa esercitazione vengono presentate quattro lezioni:

1. Creare un'applicazione Windows Store Smooth Streaming di base
2. Aggiungere una barra del dispositivo scorrimento per controllare l'avanzamento del file multimediale
3. Selezionare flussi Smooth Streaming
4. Selezionare tracce Smooth Streaming

##Prerequisiti

- Windows 8 a 32 o 64 bit. È possibile scaricare la [versione di valutazione di Windows 8 Enterprise](http://msdn.microsoft.com/evalcenter/jj554510.aspx) da MSDN.
- Visual Studio 2012 o Visual Studio Express 2012 (o versione successiva). Per scaricare la versione di valutazione, fare clic [qui](http://www.microsoft.com/visualstudio/11/downloads).
- [Microsoft Smooth Streaming Client SDK per Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).


La soluzione completata per ogni lezione può essere scaricata dagli esempi di esempi di codice per sviluppatori in MSDN.

- [Lezione 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f): semplice lettore multimediale Smooth Streaming per Windows 8 
- [Lezione 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a): semplice lettore multimediale Smooth Streaming per Windows 8 con controllo per la barra del dispositivo di scorrimento 
- [Lezione 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44): lettore multimediale Smooth Streaming per Windows 8 con selezione flusso  
- [Lezione 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907): lettore multimediale Smooth Streaming per Windows 8 con selezione traccia

##Lezione 1: Creare un'applicazione Windows Store Smooth Streaming di base

In questa lezione verrà creata un'applicazione Windows Store con un controllo MediaElement per riprodurre contenuto Smooth Streaming. L'applicazione in esecuzione avrà un aspetto simile al seguente:

![Esempio di applicazione Windows Store Smooth Streaming][PlayerApplication]
 
Per ulteriori informazioni sullo sviluppo di app di Windows Store, vedere il sito relativo allo [sviluppo di app di Windows Store per Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). In questa lezione sono incluse le procedure seguenti:

1.	Creare un progetto Windows Store
2.	Progettare l'interfaccia utente (XAML)
3.	Modificare il file code-behind
4.	Compilare ed eseguire il test dell'applicazione

**Per creare un progetto Windows Store**

1.	Eseguire Visual Studio 2012 o versione successiva.
2.	Scegliere **Nuovo** dal menu **FILE**, quindi fare clic su **Progetto**.
3.	Nella finestra di dialogo Nuovo progetto digitare o selezionare i valori seguenti:

Nome|Valore
---|---
Gruppo dei modelli di progetto|Installato/Modelli/Visual C#/Windows Store
Modello|Applicazione vuota (XAML)
Nome|SSPlayer
Percorso|C:\\SSTutorials
Nome soluzione|SSPlayer
Crea directory per soluzione|(selezionata)

4.	Fare clic su **OK**.

**Per aggiungere un riferimento a Smooth Streaming Client SDK**

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **SSPlayer**, quindi scegliere **Aggiungi riferimento**.
2.	Digitare o selezionare i valori seguenti:

Nome|Valore
---|---
Gruppo di riferimenti|Windows/Estensioni
Riferimento|Selezionare Microsoft Smooth Streaming Client SDK per Windows 8 e Microsoft Visual C++ Runtime Package.
	
3.	Fare clic su **OK**. 

Dopo avere aggiunto i riferimenti, è necessario selezionare la piattaforma di destinazione (x64 o x86). L'aggiunta di riferimenti non funzionerà per la configurazione di piattaforma Qualsiasi CPU. In Esplora soluzioni verrà visualizzato un simbolo di avviso giallo per i riferimenti aggiunti.

**Per progettare l'interfaccia utente del lettore**

1.	In Esplora soluzioni fare doppio clic su **MainPage.xaml** per aprirlo in visualizzazione Progettazione.
2.	Individuare i tag **&lt;Grid&gt;** e **&lt;/Grid&gt;** nel file XAML e incollare il codice seguente tra i due tag:

		<Grid.RowDefinitions>
		    <RowDefinition Height="20"/>    <!-- spacer -->
		    <RowDefinition Height="50"/>    <!-- media controls -->
		    <RowDefinition Height="100*"/>  <!-- media element -->
		    <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
		    <RowDefinition Height="50"/>    <!-- status bar -->
		</Grid.RowDefinitions>
		
		<StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
		    <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
		    <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
		    <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
		    <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
		    <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
		    <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
		    <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
		    <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
		</StackPanel>

		<StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
		            HorizontalAlignment="Center" VerticalAlignment="Center">
		    <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
		                  HorizontalAlignment="Center" VerticalAlignment="Center" 
		                  AudioCategory="BackgroundCapableMedia" />
		    <StackPanel Orientation="Horizontal">
		        <Slider x:Name="sliderProgress" Width="924" Height="44"
		                HorizontalAlignment="Center" VerticalAlignment="Center"
		                PointerPressed="sliderProgress_PointerPressed"/>
		        <Slider x:Name="sliderVolume" 
		                HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
		                Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
		                Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
		                ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
		    </StackPanel>
		</StackPanel>

		<StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
		    <TextBlock x:Name="tbStatus" Text="Status :  " 
		       FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
		    <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
		</StackPanel>

	Il controllo MediaElement viene usato per riprodurre i file multimediali. Nella lezione successiva verrà utilizzato il controllo del dispositivo di scorrimento denominato sliderProgress per controllare l'avanzamento del file multimediale.

3.	Premere **CTRL+S** per salvare il file.

Il controllo MediaElement non supporta il contenuto Smooth Streaming per impostazione predefinita. Per abilitare il supporto Smooth Streaming, è necessario registrare il gestore del flusso di byte in base all'estensione del nome file e al tipo MIME. Per effettuare la registrazione è necessario usare il metodo MediaExtensionManager.RegisterByteStremHandler dello spazio dei nomi Windows.Media.

Nel file XAML alcuni gestori di eventi sono associati ai controlli. Sarà quindi necessario definire tali gestori di eventi.

**Per modificare il file code-behind**

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza codice**.
2.	Aggiungere l'istruzione using seguente all'inizio del file:

		using Windows.Media;

3.	All'inizio della classe **MainPage** aggiungere il membro dati seguente:

		private MediaExtensionManager extensions = new MediaExtensionManager();

4.	Alla fine del costruttore **MainPage** aggiungere le due righe seguenti:

		extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
		extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
		
5.	Alla fine della classe **MainPage** incollare il codice seguente:

		#region UI Button Click Events
		private void btnPlay_Click(object sender, RoutedEventArgs e)
		{
		    mediaElement.Play();
		    txtStatus.Text = "MediaElement is playing ...";
		}
		
		private void btnPause_Click(object sender, RoutedEventArgs e)
		{
		    mediaElement.Pause();
		    txtStatus.Text = "MediaElement is paused";
		}
		
		private void btnSetSource_Click(object sender, RoutedEventArgs e)
		{
		    sliderProgress.Value = 0;
		    mediaElement.Source = new Uri(txtMediaSource.Text);
		
		    if (chkAutoPlay.IsChecked == true)
		    {
		        txtStatus.Text = "MediaElement is playing ...";
		    }
		    else
		    {
		        txtStatus.Text = "Click the Play button to play the media source.";
		    }
		}
		
		private void btnStop_Click(object sender, RoutedEventArgs e)
		{
		    mediaElement.Stop();
		    txtStatus.Text = "MediaElement is stopped";
		}
		
		private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
		{
		    txtStatus.Text = "Seek to position " + sliderProgress.Value;
		    mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
		}
		#endregion

	Il gestore di eventi sliderProgress\_PointerPressed viene definito qui. Per renderlo funzionante, è tuttavia necessario eseguire ulteriori attività che verranno illustrate nella lezione successiva di questa esercitazione.
6.	Premere **CTRL+S** per salvare il file.

Il file code-behind finito avrà un aspetto simile al seguente:

![Visualizzazione del codice dell'applicazione Windows Store Smooth Streaming in Visual Studio][CodeViewPic]

**Per compilare e testare l'applicazione**

1.	Nel menu **COMPILA** scegliere **Gestione configurazione**.
2.	Modificare **Piattaforma soluzione attiva** in base alla piattaforma di sviluppo in uso.
3.	Premere **F6** per compilare il progetto. 
4.	Premere **F5** per eseguire l'applicazione.
5.	Nella parte superiore dell'applicazione, è possibile utilizzare l'URL di Smooth Streaming predefinito o immetterne uno nuovo. 
6.	Fare clic su **Imposta origine**. Poiché l'opzione **Riproduzione automatica** è abilitata per impostazione predefinita, il file multimediale verrà riprodotto automaticamente. È possibile controllare il contenuto multimediale usando i pulsanti di **riproduzione**, **pausa** e **arresto**. È possibile controllare il volume del contenuto multimediale tramite il dispositivo di scorrimento verticale. Il dispositivo di scorrimento orizzontale per il controllo dell'avanzamento del file multimediale non è tuttavia ancora implementato. 

La lezione 1 è stata completata. In questa lezione è stato utilizzato un controllo MediaElement per riprodurre contenuto multimediale Smooth Streaming. Nella lezione successiva verrà aggiunto un dispositivo di scorrimento per controllare l'avanzamento del contenuto multimediale Smooth Streaming.


##Lezione 2: Aggiungere una barra del dispositivo di scorrimento per controllare l'avanzamento del file multimediale
Nella lezione 1 è stata creata un'applicazione Windows Store con un controllo XAML MediaElement per riprodurre contenuto multimediale Smooth Streaming, che include alcune funzionalità multimediali di base, come l'avvio, l'arresto e la sospensione della riproduzione. In questa lezione verrà aggiunto un controllo per la barra del dispositivo di scorrimento all'applicazione.

In questa esercitazione verrà utilizzato un timer per l'aggiornamento della posizione del dispositivo di scorrimento in base alla posizione corrente del controllo MediaElement. Sarà inoltre necessario aggiornare l'ora di inizio e di fine del dispositivo di scorrimento in caso di contenuto in diretta. È possibile gestire questo scenario nell'evento di aggiornamento dell'origine adattiva.

Le origini multimediali sono oggetti che generano dati multimediali. Il resolver dell'origine accetta un URL o un flusso di byte e crea l'origine multimediale appropriata per il contenuto. Il resolver dell'origine rappresenta il modo standard per la creazione di origini multimediali da parte delle applicazioni.

In questa lezione sono incluse le procedure seguenti:

1.	Registrare il gestore Smooth Streaming 
2.	Aggiungere i gestori di eventi a livello di gestione dell'origine adattiva
3.	Aggiungere i gestori di eventi a livello di origine adattiva
4.	Aggiungere i gestori di eventi MediaElement
5.	Aggiungere il codice relativo alla barra del dispositivo di scorrimento
6.	Compilare ed eseguire il test dell'applicazione

**Per registrare il gestore del flusso di byte Smooth Streaming e passare propertySet **

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza codice**.
2.	Aggiungere l'istruzione using seguente all'inizio del file:

		using Microsoft.Media.AdaptiveStreaming;

3.	All'inizio della classe MainPage aggiungere i membri dati seguenti:

		private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
		private IAdaptiveSourceManager adaptiveSourceManager;
	
4.	Nel costruttore **MainPage** aggiungere il codice seguente dopo la riga **this.Initialize Components();** e le righe di codice per la registrazione scritte nella lezione precedente:
	
		// Gets the default instance of AdaptiveSourceManager which manages Smooth 
		//Streaming media sources.
		adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
		// Sets property key value to AdaptiveSourceManager default instance.
		// {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
		propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
	
5.	Nel costruttore **MainPage** modificare i due metodi RegisterByteStreamHandler per aggiungere i parametri seguenti:

		// Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
		// "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
		// http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
		extensions.RegisterByteStreamHandler(
		    "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
		    ".ism", 
		    "text/xml", 
		    propertySet );
		extensions.RegisterByteStreamHandler(
		    "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
		    ".ism", 
		    "application/vnd.ms-sstr+xml", 
		propertySet);

6.	Premere **CTRL+S** per salvare il file.

**Per aggiungere il gestore eventi a livello di gestione dell'origine adattiva**

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza codice**.
2.	All'interno della classe **MainPage** aggiungere il membro dati seguente:

		private AdaptiveSource adaptiveSource = null;

3.	Alla fine della classe **MainPage** aggiungere il gestore di eventi seguente:
	
		#region Adaptive Source Manager Level Events
		private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
		{
		    adaptiveSource = args.AdaptiveSource;
		}
		#endregion Adaptive Source Manager Level Events

4.	Alla fine del costruttore **MainPage** aggiungere la riga seguente per effettuare la sottoscrizione all'evento di apertura dell'origine adattiva:
	
	adaptiveSourceManager.AdaptiveSourceOpenedEvent += new AdaptiveSourceOpenedEventHandler(mediaElement\_AdaptiveSourceOpened);

5.	Premere **CTRL+S** per salvare il file.

**Per aggiungere i gestori eventi a livello di origine adattiva**

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza codice**.
2.	All'interno della classe **MainPage** aggiungere il membro dati seguente:
	
		private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
		private Manifest manifestObject;
	
3.	Alla fine della classe **MainPage** aggiungere i gestori di eventi seguenti:

		#region Adaptive Source Level Events
		private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
		{
		    adaptiveSource = args.AdaptiveSource;
		    manifestObject = args.AdaptiveSource.Manifest;
		}
		
		private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
		{
		    adaptiveSourceStatusUpdate = args;
		}
		
		private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
		{
		    txtStatus.Text = "Error: " + args.HttpResponse;
		}
		#endregion Adaptive Source Level Events

4.	Alla fine del metodo **mediaElement AdaptiveSourceOpened** aggiungere il codice seguente per eseguire la sottoscrizione agli eventi:
	
		adaptiveSource.ManifestReadyEvent +=
	                mediaElement_ManifestReady;
	    adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
		    mediaElement_AdaptiveSourceStatusUpdated;
		adaptiveSource.AdaptiveSourceFailedEvent += 
		    mediaElement_AdaptiveSourceFailed;
	
5.	Premere **CTRL+S** per salvare il file.

Gli stessi eventi sono inoltre disponibili a livello di gestione dell'origine adattiva e possono essere utilizzati per gestire le funzionalità comuni a tutti gli elementi multimediali nell'app. Ogni elemento AdaptiveSource include i relativi eventi e tutti gli eventi AdaptiveSource vengono propagati in AdaptiveSourceManager.

**Per aggiungere i gestori eventi MediaElement**

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza codice**.
2.	Alla fine della classe **MainPage** aggiungere i gestori di eventi seguenti:
	
		#region Media Element Event Handlers 
		private void MediaOpened(object sender, RoutedEventArgs e)
		{
		    txtStatus.Text = "MediaElement opened";
		}
		
		private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
		{
		    txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
		}
		
		private void MediaEnded(object sender, RoutedEventArgs e)
		{
		    txtStatus.Text ="MediaElement ended.";
		}
		#endregion Media Element Event Handlers

3.	Alla fine del costruttore **MainPage** aggiungere il codice seguente per eseguire la sottoscrizione agli eventi:
	
		mediaElement.MediaOpened += MediaOpened;
		mediaElement.MediaEnded += MediaEnded;
		mediaElement.MediaFailed += MediaFailed;

4.	Premere **CTRL+S** per salvare il file.

**Per aggiungere il codice relativo alla barra del dispositivo di scorrimento**

1.	In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza codice**.
2.	Aggiungere l'istruzione using seguente all'inizio del file:
	
		using Windows.UI.Core;

3.	All'interno della classe **MainPage** aggiungere i membri dati seguenti:
	
		public static CoreDispatcher _dispatcher;
		private DispatcherTimer sliderPositionUpdateDispatcher;
	
4.	Alla fine del costruttore **MainPage** aggiungere il codice seguente:

		_dispatcher = Window.Current.Dispatcher;
		PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
		sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
	
5.	Alla fine della classe **MainPage** aggiungere il codice seguente:
	
		#region sliderMediaPlayer
		private double SliderFrequency(TimeSpan timevalue)
		{
		    long absvalue = 0;
		    double stepfrequency = -1;
		
		    if (manifestObject != null)
		    {
		        absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
		    }
		    else
		    {
		        absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
		    }
		
		    TimeSpan totalDVRDuration = new TimeSpan(absvalue);
		
		    if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
		    {
		       stepfrequency = 10;
		    }
		    else if (totalDVRDuration.TotalMinutes >= 30 
		             && totalDVRDuration.TotalMinutes < 60)
		    {
		        stepfrequency = 30;
		    }
		    else if (totalDVRDuration.TotalHours >= 1)
		    {
		        stepfrequency = 60;
		    }
		
		    return stepfrequency;
		}
		
		void updateSliderPositionoNTicks(object sender, object e)
		{
		    sliderProgress.Value = mediaElement.Position.TotalSeconds;
		}
		
		public void setupTimer()
		{
		    sliderPositionUpdateDispatcher = new DispatcherTimer();
		    sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
		    startTimer();
		}

		public void startTimer()
		{
		    sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
		    sliderPositionUpdateDispatcher.Start();
		}
		
		// Slider start and end time must be updated in case of live content
		public async void setSliderStartTime(long startTime)
		{
		    await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
		    {
		        TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
		        double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
		        sliderProgress.Minimum = absvalue;
		    });
		}
		
		// Slider start and end time must be updated in case of live content
		public async void setSliderEndTime(long startTime)
		{
		    await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
		    {
		        TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
		        double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
		        sliderProgress.Maximum = absvalue;
		    });
		}
		#endregion sliderMediaPlayer

	**Nota:** per apportare modifiche al thread dell'interfaccia utente da un thread non di interfaccia utente viene usato CodeDispatcher. In caso di colli di bottiglia nel thread del dispatcher, gli sviluppatori possono scegliere di usare il dispatcher fornito dall'elemento dell'interfaccia utente che intendono aggiornare. Ad esempio:
	
		await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
		  timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
		double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
		  sliderProgress.Maximum = absvalue; }); 
		

6.	Alla fine del metodo **mediaElement\_AdaptiveSourceStatusUpdated** aggiungere il codice seguente:
	
		setSliderStartTime(args.StartTime);
		setSliderEndTime(args.EndTime);

7.	Alla fine del metodo **MediaOpened** aggiungere il codice seguente:
	
	sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.	Premere **CTRL+S** per salvare il file.

**Per compilare e testare l'applicazione**

1. Premere **F6** per compilare il progetto. 
2.	Premere **F5** per eseguire l'applicazione.
3.	Nella parte superiore dell'applicazione, è possibile utilizzare l'URL di Smooth Streaming predefinito o immetterne uno nuovo. 
4.	Fare clic su **Imposta origine**. 
5.	Eseguire il test della barra del dispositivo di scorrimento.

La lezione 2 è stata completata. In questa lezione è stato aggiunto un dispositivo di scorrimento all'applicazione.

##Lezione 3: Selezionare flussi Smooth Streaming
Smooth Streaming consente di trasmettere contenuto in streaming con tracce audio in più lingue selezionabili dagli utenti. In questa lezione verrà illustrato come abilitare gli utenti per la selezione dei flussi. In questa lezione sono incluse le procedure seguenti:

1. Modificare il file XAML
2. Modificare il file code-behind
3. Compilare ed eseguire il test dell'applicazione


**Per modificare il file XAML**

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza finestra di progettazione**.
2. Individuare &lt;Grid.RowDefinitions&gt; e modificare gli elementi RowDefinitions in modo che l'aspetto risulti simile al seguente:

		<Grid.RowDefinitions>            
			<RowDefinition Height="20"/>
		    <RowDefinition Height="50"/>
		    <RowDefinition Height="100"/>
		    <RowDefinition Height="80"/>
		    <RowDefinition Height="50"/>
		</Grid.RowDefinitions>

3. All'interno dei tag &lt;Grid&gt;&lt;/Grid&gt; aggiungere il codice seguente per definire un controllo listbox (casella di controllo) per consentire agli utenti di visualizzare l'elenco dei flussi disponibili e selezionare quelli desiderati:

		<Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
			<Grid.RowDefinitions>
				<RowDefinition Height="300"/>
			</Grid.RowDefinitions>
			<Grid.ColumnDefinitions>
				<ColumnDefinition Width="250*"/>
				<ColumnDefinition Width="250*"/>
			</Grid.ColumnDefinitions>
			<StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
				<StackPanel Orientation="Horizontal">
					<TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
					<Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
				</StackPanel>
			    <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
			    	ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
					<ListBox.ItemTemplate>
						<DataTemplate>
				        	<CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
						</DataTemplate>
					</ListBox.ItemTemplate>
				</ListBox>
			</StackPanel>
		</Grid>

4. Premere **CTRL+S** per salvare le modifiche.


**Per modificare il file code-behind**

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza codice**.
2. All'interno dello spazio dei nomi SSPlayer aggiungere una nuova classe: #region class Stream
	
	    public class Stream
	    {
	        private IManifestStream stream;
	        public bool isCheckedValue;
	        public string name;
	
	        public string Name
	        {
	            get { return name; }
	            set { name = value; }
	        }
	
	        public IManifestStream ManifestStream
	        {
	            get { return stream; }
	            set { stream = value; }
	        }
	
	        public bool isChecked
	        {
	            get { return isCheckedValue; }
	            set
	            {
	                // mMke the video stream always checked.
	                if (stream.Type == MediaStreamType.Video)
	                {
	                    isCheckedValue = true;
	                }
	                else
	                {
	                    isCheckedValue = value;
	                }
	            }
	        }
	
	        public Stream(IManifestStream streamIn)
	        {
	            stream = streamIn;
	            name = stream.Name;
	        }
	    }
	    #endregion class Stream

3. All'inizio della classe MainPage aggiungere le seguenti definizioni di variabili:

		private List<Stream> availableStreams;
		private List<Stream> availableAudioStreams;
		private List<Stream> availableTextStreams;
		private List<Stream> availableVideoStreams;

4. All'interno della classe MainPage aggiungere l'area seguente:

		#region stream selection
		///<summary>
		///Functionality to select streams from IManifestStream available streams
		/// </summary>
		
		// This function is called from the mediaElement_ManifestReady event handler 
		// to retrieve the streams and populate them to the local data members.
		public void getStreams(Manifest manifestObject)
		{
		    availableStreams = new List<Stream>();
		    availableVideoStreams = new List<Stream>();
		    availableAudioStreams = new List<Stream>();
		    availableTextStreams = new List<Stream>();
		
		    try
		    {
		        for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
		        {
		            Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
		            newStream.isChecked = false;
		
		            //populate the stream lists based on the types
		            availableStreams.Add(newStream);
		
		            switch (newStream.ManifestStream.Type)
		            {
		                case MediaStreamType.Video:
		                    availableVideoStreams.Add(newStream);
		                    break;
		                case MediaStreamType.Audio:
		                    availableAudioStreams.Add(newStream);
		                    break;
		                case MediaStreamType.Text:
		                    availableTextStreams.Add(newStream);
		                    break;
		            }
		
		            // Select the default selected streams from the manifest.
		            for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
		            {
		                string selectedStreamName = manifestObject.SelectedStreams[j].Name;
		                if (selectedStreamName.Equals(newStream.Name))
		                {
		                    newStream.isChecked = true;
		                    break;
		                }
		            }
		        }
		    }
		    catch (Exception e)
		    {
		        txtStatus.Text = "Error: " + e.Message;
		    }
		}
		
		// This function set the list box ItemSource
		private async void refreshAvailableStreamsListBoxItemSource()
		{
		    try
		    {
		        //update the stream check box list on the UI
		        await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
		            => { lbAvailableStreams.ItemsSource = availableStreams; });
		    }
		    catch (Exception e)
		    {
		        txtStatus.Text = "Error: " + e.Message;
		    }
		}
		
		// This function creates a selected streams list
		private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
		{
		    bool isOneVideoSelected = false;
		    bool isOneAudioSelected = false;
		
		    // Only one video stream can be selected
		    for (int j = 0; j<availableVideoStreams.Count; j++)
		    {
		        if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
		        {
		            selectedStreams.Add(availableVideoStreams[j].ManifestStream);
		            isOneVideoSelected = true;
		        }
		    }
		
		    // Select the frist video stream from the list if no video stream is selected
		    if (!isOneVideoSelected)
		    {
		        availableVideoStreams[0].isChecked = true;
		        selectedStreams.Add(availableVideoStreams[0].ManifestStream);
		    }
		
		    // Only one audio stream can be selected
		    for (int j = 0; j<availableAudioStreams.Count; j++)
		    {
		        if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
		        {
		            selectedStreams.Add(availableAudioStreams[j].ManifestStream);
		            isOneAudioSelected = true;
		            txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
		        }
		    }
		
		    // Select the frist audio stream from the list if no audio steam is selected.
		    if (!isOneAudioSelected)
		    {
		        availableAudioStreams[0].isChecked = true;
		        selectedStreams.Add(availableAudioStreams[0].ManifestStream);
		    }
		
		    // Multiple text streams are supported.
		    for (int j = 0; j < availableTextStreams.Count; j++)
		    {
		        if (availableTextStreams[j].isChecked)
		        {
		            selectedStreams.Add(availableTextStreams[j].ManifestStream);
		        }
		    }
		}
		
		// Change streams on a smooth streaming presentation with multiple video streams.
		private async void changeStreams(List<IManifestStream> selectStreams)
		{
		    try
		    {
		        IReadOnlyList<IStreamChangedResult> returnArgs =
		            await manifestObject.SelectStreamsAsync(selectStreams);
		    }
		    catch (Exception e)
		    {
		        txtStatus.Text = "Error: " + e.Message;
		    }
		}
		#endregion stream selection

5. Individuare il metodo mediaElement\_ManifestReady e aggiungere il codice seguente alla fine della funzione:
	
		getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

	Quando il manifesto di MediaElement è pronto, il codice recupera un elenco dei flussi disponibili e popola la casella di riepilogo dell'interfaccia utente con l'elenco.

6. All'interno della classe MainPage, individuare l'area degli eventi clic dei pulsanti dell'interfaccia utente e aggiungere la seguente definizione di funzione:

		private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Per compilare e testare l'applicazione**

1. Premere **F6** per compilare il progetto. 
2.	Premere **F5** per eseguire l'applicazione.
3.	Nella parte superiore dell'applicazione, è possibile utilizzare l'URL di Smooth Streaming predefinito o immetterne uno nuovo. 
4.	Fare clic su **Imposta origine**. 
5.	La lingua predefinita è audio\_eng. Provare a passare da audio\_eng ad audio\_es e viceversa. Ogni volta che si seleziona un nuovo flusso, è necessario fare clic sul pulsante Invia.

La lezione 3 è stata completata. In questa lezione è stata aggiunta la funzionalità per la selezione dei flussi.

##Lezione 4: Selezionare tracce Smooth Streaming
Una presentazione Smooth Streaming può contenere più file video codificati con livelli di qualità (velocità in bit) e risoluzioni diversi. In questa lezione verrà illustrato come abilitare gli utenti per la selezione delle tracce. In questa lezione sono incluse le procedure seguenti:

1. Modificare il file XAML
2. Modificare il file code-behind
3. Compilare ed eseguire il test dell'applicazione

**Per modificare il file XAML**

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza finestra di progettazione**.
2. Individuare il tag &lt;Grid&gt; con il nome **gridStreamAndBitrateSelection** e aggiungere il codice seguente alla fine del tag:

		<StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
		 <StackPanel Orientation="Horizontal">
		     <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
		     <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
		 </StackPanel>
		 <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
		          ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
		     <ListBox.ItemTemplate>
		         <DataTemplate>
		             <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
		         </DataTemplate>
		     </ListBox.ItemTemplate>
		 </ListBox>
		</StackPanel>

3. Premere **CTRL+S** per salvare le modifiche.


**Per modificare il file code-behind**

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **MainPage.xaml**, quindi scegliere **Visualizza codice**.
2. All'interno dello spazio dei nomi SSPlayer, aggiungere una nuova classe:
	
		#region class Track
	    public class Track
	    {
	        private IManifestTrack trackInfo;
	        public string _bitrate;
	        public bool isCheckedValue;
	
	        public IManifestTrack TrackInfo
	        {
	            get { return trackInfo; }
	            set { trackInfo = value; }
	        }
	
	        public string Bitrate
	        {
	            get { return _bitrate; }
	            set { _bitrate = value; }
	        }
	
	        public bool isChecked
	        {
	            get { return isCheckedValue; }
	            set
	            {
	                isCheckedValue = value;
	            }
	        }
	
	        public Track(IManifestTrack trackInfoIn)
	        {
	            trackInfo = trackInfoIn;
	            _bitrate = trackInfoIn.Bitrate.ToString();
	        }
	        //public Track() { }
	    }
	    #endregion class Track

3. All'inizio della classe MainPage aggiungere le definizioni di variabili seguenti:
	
		private List<Track> availableTracks;

4. All'interno della classe MainPage aggiungere l'area seguente:
	
		#region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

		// This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

		// This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

		// This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

		// This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. Individuare il metodo mediaElement\_ManifestReady e aggiungere il codice seguente alla fine della funzione:

		getTracks(manifestObject);
		refreshAvailableTracksListBoxItemSource();

6. All'interno della classe MainPage, individuare l'area degli eventi clic dei pulsanti dell'interfaccia utente e aggiungere la seguente definizione di funzione:

		private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Per compilare e testare l'applicazione**

1. Premere **F6** per compilare il progetto. 
2.	Premere **F5** per eseguire l'applicazione.
3.	Nella parte superiore dell'applicazione, è possibile utilizzare l'URL di Smooth Streaming predefinito o immetterne uno nuovo. 
4.	Fare clic su **Imposta origine**. 
5.	Per impostazione predefinita, tutte le tracce del flusso video sono selezionate. Per sperimentare le diverse velocità in bit, è possibile selezionare la velocità in bit più bassa e quella più alta tra quelle disponibili. Dopo ogni modifica, è necessario fare clic su Invia. Sarà quindi possibile visualizzare le modifiche alla qualità del video.

La lezione 4 è stata completata. In questa lezione è stata aggiunta la funzionalità per la selezione delle tracce.


##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Altre risorse:
- [Come creare un'applicazione Smooth Streaming per Windows 8 in JavaScript con funzionalità avanzate](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [Panoramica tecnica relativa a Smooth Streaming](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 

<!---HONumber=AcomDC_0420_2016-->