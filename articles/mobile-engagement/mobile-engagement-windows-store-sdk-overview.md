<properties 
	pageTitle="Panoramica di Windows Universal SDK" 
	description="Panoramica di Windows Universal SDK per Azure Mobile Engagement" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Panoramica di Windows Universal SDK per Azure Mobile Engagement

Iniziare da qui per ottenere i dettagli su come integrare Azure Mobile Engagement in un'app universale di Windows. Se si vuole fare prima una prova, completare l'[esercitazione di 15 minuti](mobile-engagement-windows-store-dotnet-get-started.md).

Fare clic per visualizzare il [contenuto dell'SDK](mobile-engagement-windows-store-sdk-content.md).

##Procedure di integrazione

1. Iniziare da qui: [Come integrare Mobile Engagement in un'app universale di Windows](mobile-engagement-windows-store-integrate-engagement.md)

2. Per le notifiche: [Come integrare il servizio di copertura (di notifica) in un'app universale di Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Implementazione del piano di tag: [Come usare l'API per l'assegnazione di tag avanzata di Mobile Engagement in un'app universale di Windows](mobile-engagement-windows-store-use-engagement-api.md)

##Note sulla versione

###3\.2.0 (20/11/2015)

-   Aggiunta del supporto per le applicazioni di Windows 10 Universal Windows Platform.
-   Aggiunte funzionalità di condivisione del canale di push per risolvere i conflitti di canale (ora compatibili con hub di notifica di Azure).
-   Arresto anomalo fisso durante la richiesta di id del dispositivo subito dopo l'inizializzazione.
-   La console registra miglioramenti.
-   Arresto anomalo durante l'analisi di alcune eccezioni non gestite.

Per le versioni precedenti, vedere le [note sulla versione complete](mobile-engagement-windows-store-release-notes.md).

##Procedure di aggiornamento

Se nell'applicazione è già stata integrata una versione precedente dell'SDK, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Vedere quindi le [procedure di aggiornamento](mobile-engagement-windows-store-upgrade-procedure.md) complete. Se ad esempio si esegue la migrazione dalla versione 0.10.1 alla 0.11.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 0.9.0 alla 0.10.1" e quindi la procedura per la migrazione "dalla 0.10.1 alla 0.11.0".

###Dalla versione 3.1.0 alla 3.2.0

#### Risorse
Questo passaggio riguarda solo le risorse personalizzate. Se sono state personalizzate le risorse fornite dall'SDK (html, immagini, sovrimpressioni) è necessario eseguirne il backup prima dell'aggiornamento e riapplicare la personalizzazione alle risorse aggiornate.

#### l'integrazione di visualizzazione Web
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

### Eseguire l'aggiornamento da versioni precedenti

Vedere [Procedure di aggiornamento](mobile-engagement-windows-store-upgrade-procedure/).

<!---HONumber=AcomDC_1125_2015-->