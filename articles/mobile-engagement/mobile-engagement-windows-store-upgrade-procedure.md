---
title: Procedure di aggiornamento di Windows Universal Apps SDK
description: Procedure di aggiornamento di Windows Universal Apps SDK per Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo

---
# Procedure di aggiornamento di Windows Universal Apps SDK
Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Se ad esempio si esegue la migrazione dalla versione 0.10.1 alla 0.11.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 0.9.0 alla 0.10.1" e quindi la procedura per la migrazione "dalla 0.10.1 alla 0.11.0".

## Dalla versione 3.3.0 alla 3.4.0
### Log di test
I log della console generati da SDK possono essere abilitati/disattivati/filtrati. Per eseguire una personalizzazione, aggiornare la proprietà `EngagementAgent.Instance.TestLogEnabled` scegliendo uno dei valori disponibili nell'enumerazione `EngagementTestLogLevel`, ad esempio:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### Risorse
La sovrimpressione Reach è stata migliorata. Fa parte delle risorse del pacchetto NuGet di SDK.

Durante l'aggiornamento alla nuova versione di SDK, è possibile scegliere se mantenere i file esistenti contenuti nella cartella della sovrimpressione delle risorse o meno:

* Se la sovrimpressione precedente è in funzione o si stanno integrando manualmente gli elementi `WebView`, è possibile decidere di mantenere i file esistenti per poter proseguire.
* Se invece si vuole passare alla sovrimpressione nuova, è sufficiente sostituire l'intera cartella `overlay` delle risorse con quella nuova disponibile nel pacchetto SDK. Dopo aver completo l'aggiornamento, nelle app UWP è possibile ottenere la cartella della nuova sovrimpressione da %USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\\3.4.0\\content\\win81\\Resources.

> [!WARNING]
> Se si usa la sovrimpressione nuova, le personalizzazioni eseguite con la versione precedente saranno sovrascritte.
> 
> 

## Dalla versione 3.2.0 alla 3.3.0
### Risorse
Questo passaggio riguarda solo le risorse personalizzate. Se sono state personalizzate le risorse fornite dall'SDK (html, immagini, sovrimpressioni) è necessario eseguirne il backup prima dell'aggiornamento e riapplicare la personalizzazione alle risorse aggiornate.

## Dalla versione 3.1.0 alla 3.2.0
### Risorse
Questo passaggio riguarda solo le risorse personalizzate. Se sono state personalizzate le risorse fornite dall'SDK (html, immagini, sovrimpressioni) è necessario eseguirne il backup prima dell'aggiornamento e riapplicare la personalizzazione alle risorse aggiornate.

### l'integrazione di visualizzazione Web
In questa versione sono stati introdotti alcuni miglioramenti per la corrispondenza dei fattori di forma del dispositivo diversi. Assicurarsi che l'integrazione delle webview corrisponda a quanto segue:

Nella pagina XAML ():

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

E nel file con estensione cs associato:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## Dalla versione 2.0.0 alla 3.0.0
### Risorse
Questo passaggio riguarda solo le risorse personalizzate. Se sono state personalizzate le risorse fornite dall'SDK (html, immagini, sovrimpressioni) è necessario eseguirne il backup prima dell'aggiornamento e riapplicare la personalizzazione alle risorse aggiornate.

## Dalla versione 1.1.1 alla 2.0.0
La sezione seguente illustra come eseguire la migrazione di un'integrazione dell'SDK dal servizio Capptain offerto da Capptain SAS a un'app basata su Azure Mobile Engagement.

> [!IMPORTANT]
> Capptain e Mobile Engagement sono servizi diversi e la procedura seguente illustra solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON comporta la migrazione dei dati dai server di Capptain ai server di Mobile Engagement
> 
> 

Se si esegue la migrazione da una versione precedente, consultare il sito web Capptain per eseguire prima la migrazione a 1.1.1, quindi applicare la procedura seguente

### Pacchetto NuGet
Sostituire **Capptain.WindowsPhone** con il pacchetto NuGet **MicrosoftAzure.MobileEngagement**.

### Applicazione di Mobile Engagement
L'SDK usa il termine `Engagement`. È necessario aggiornare il progetto per tenere conto di questa modifica.

È necessario disinstallare il pacchetto nuget corrente di Capptain. Si consideri che verranno rimosse tutte le modifiche nella cartella Risorse di Capptain. Se si desidera mantenere tali file, eseguirne una copia.

Successivamente, installare il nuovo pacchetto NuGet di Microsoft Azure Engagement nel progetto. È possibile trovarlo direttamente sul [sito Web di NuGet] o qui nell'indice. Questa operazione sostituisce tutti i file di risorse usati da Engagement e aggiunge la nuova DLL di Engagement ai riferimenti del progetto.

È necessario eliminare i riferimenti del progetto rimuovendo i riferimenti DLL di Capptain. Se non si effettua questa operazione, la versione di Capptain creerà un conflitto e si verificheranno errori.

Se sono state personalizzate risorse Capptain, copiare il contenuto dei file precedenti e incollarlo in nuovi file di progetto. Si noti che è necessario aggiornare sia i file xaml che i file cs.

Al termine di queste operazioni, è necessario sostituire i riferimenti di Capptain precedenti con i nuovi riferimenti di Engagement.

1. Tutti gli spazi dei nomi Capptain devono essere aggiornati.
   
    Prima della migrazione:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Dopo la migrazione:
   
        using Microsoft.Azure.Engagement;
2. Tutte le classi Capptain che contengono "Capptain" devono contenere "Engagement".
   
    Prima della migrazione:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Dopo la migrazione:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Per i file xaml cambiano anche attributi e spazio dei nomi di Capptain.
   
    Prima della migrazione:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Dopo la migrazione:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Modifiche alle pagine di sovrimpressione
   
   > [!IMPORTANT]
   > Anche la sovrimpressione cambia. Il nuovo spazio dei nomi è `Microsoft.Azure.Engagement.Overlay`. Deve essere usato sia nei file xaml sia nei file cs. Inoltre, `CapptainGrid` deve essere denominato `EngagementGrid` e `capptain_notification_content` e `capptain_announcement_content` sono denominati `engagement_notification_content` e `engagement_announcement_content`.
   > 
   > 
   
    Per la sovrimpressione:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Diventa:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Per altre risorse come le immagini di Capptain e i file HTML, tenere presente che sono state rinominate per l'utilizzo di "Engagement".

### Dichiarazione di progetto
In Package.appxmanifest `File Type Associations` è stato aggiornato da:

* capptain\_reach\_content a engagement\_reach\_content
* capptain\_log\_file a engagement\_log\_file

### ID applicazione / chiave SDK
Engagement utilizza una stringa di connessione. Non è necessario specificare un ID applicazione e una chiave SDK con Mobile Engagement, è sufficiente specificare una stringa di connessione. È possibile configurarla nel file EngagementConfiguration.

La configurazione di Engagement può essere impostata nel file `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare:

* La stringa di connessione dell'applicazione tra i tag `<connectionString>` e `<\connectionString>`.

Se si desidera specificarla in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure classico.

### Modifica del nome di elementi
Tutti gli elementi contenenti *capptain* sono stati rinominati con *engagement*. Lo stesso vale per *Capptain*, che è stato sostituito con *Engagement*.

Esempi di elementi di Capptain di uso comune:

* CapptainConfiguration è diventato EngagementConfiguration
* CapptainAgent è diventato EngagementAgent
* CapptainReach è diventato EngagementReach
* CapptainHttpConfig è diventato EngagementHttpConfig
* GetCapptainPageName è diventato GetEngagementPageName

Si noti la ridenominazione influisce anche sui metodi sottoposti a override.

<!---HONumber=AcomDC_0824_2016-->