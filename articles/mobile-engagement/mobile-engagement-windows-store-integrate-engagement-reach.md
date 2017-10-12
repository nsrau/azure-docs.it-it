---
title: Integrazione di Reach SDK per app universali di Windows
description: Come integrare il servizio di copertura di Azure Mobile Engagement con le app universali di Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9311e998e67d8d0d56da68fc9460df32ce7ce5a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Integrazione di Reach SDK per app universali di Windows
Prima di usare questa guida, è necessario eseguire la procedura di integrazione descritta nel documento [Integrazione di Mobile Engagement SDK per app di Windows universali](mobile-engagement-windows-store-integrate-engagement.md) .

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Incorporare Engagement Reach SDK nel progetto di app universali di Windows
Nessun elemento da aggiungere. `EngagementReach` sono già presenti nel progetto.

> [!TIP]
> È possibile personalizzare le immagini incluse nella cartella `Resources` del progetto, soprattutto l'icona del marchio, che per impostazione predefinita è l'icona di Engagement. Nelle app universali è inoltre possibile spostare la cartella `Resources` del progetto condiviso per condividere il contenuto tra app, ma è necessario mantenere il file `Resources\EngagementConfiguration.xml` nel percorso predefinito poiché è dipendente dalla piattaforma.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Abilitare Servizi notifica Push Windows
### <a name="windows-8x-and-windows-phone-81-only"></a>Solo Windows 8.x e Windows Phone 8.1
Per usare **Servizi notifica Push Windows** (indicati come WNS) nel file `Package.appxmanifest` su `Application UI` fare clic su `All Image Assets` nella casella a sinistra. A destra della casella in `Notifications`, modificare il valore di `toast capable` da `(not set)` a `(Yes)`.

### <a name="all-platforms"></a>Tutte le piattaforme
È necessario sincronizzare l'app con il proprio account Microsoft e con la piattaforma di Engagement. A questo scopo, è necessario creare un account o accedere al [Windows Dev Center](https://dev.windows.com). Creare quindi una nuova applicazione e individuare il SID e la chiave privata. Nel front-end di Engagement passare all'impostazione dell'app in `native push` e incollare le credenziali. Dopo questa operazione, fare clic sul progetto, selezionare `store` e `Associate App with the Store...`. Per sincronizzare l'applicazione creata in precedenza, è sufficiente selezionarla.

## <a name="initialize-the-engagement-reach-sdk"></a>Inizializzare Engagement Reach SDK
Modificare il file `App.xaml.cs`:

* Inserire `EngagementReach.Instance.Init` subito dopo `EngagementAgent.Instance.Init` nel metodo `InitEngagement`:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` viene eseguito in un thread dedicato. Non è necessario eseguirlo manualmente.

> [!NOTE]
> Se si usano notifiche push in altre sezioni dell'applicazione, è necessario [condividere il canale di push](#push-channel-sharing) con Engagement Reach.
> 
> 

## <a name="integration"></a>Integrazione
Engagement offre due modi per aggiungere i banner in-app, le visualizzazioni intermedie di annunci e i sondaggi Reach all'applicazione: l'integrazione di una sovrimpressione e l'integrazione manuale di visualizzazioni Web. È consigliabile non combinare entrambe le soluzioni nella stessa pagina.

La scelta tra uno dei due tipi d'integrazione può essere riassunta nel modo seguente:

* Se le pagine ereditano già dall'agente `EngagementPage`, è consigliabile scegliere l'integrazione di una sovrimpressione. È sufficiente sostituire `EngagementPage` con `EngagementPageOverlay` e `xmlns:engagement="using:Microsoft.Azure.Engagement"` con `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` nelle pagine.
* È possibile scegliere l'integrazione manuale di visualizzazioni Web se si vuole inserire l'interfaccia utente di Reach in un punto preciso nelle pagine oppure se non si vuole aggiungere un altro livello di ereditarietà alle pagine. 

### <a name="overlay-integration"></a>Integrazione di una sovrimpressione
La sovrimpressione di Engagement aggiunge in modo dinamico gli elementi dell'interfaccia utente utilizzati per visualizzare le campagne Reach nella pagina. Se la sovrimpressione non è adatta al layout, considerare invece l'integrazione manuale delle visualizzazioni Web.

Nel file con estensione xaml modificare il riferimento `EngagementPage` in `EngagementPageOverlay`

* Aggiungere le dichiarazioni di spazi dei nomi:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Sostituire `engagement:EngagementPage` con `engagement:EngagementPageOverlay`:

**Con EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**Con EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Quindi, nel file con estensione cs contrassegnare la pagina in `EngagementPageOverlay` anziché `EngagementPage` e importare `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Sostituire `EngagementPage` con `EngagementPageOverlay`:

**Con EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Con EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


La sovrimpressione di Engagement aggiunge un elemento `Grid` in alto alla pagina che è costituita dal layout e dai due elementi `WebView`, uno per il banner e l'altro per la visualizzazione intermedia.

È possibile personalizzare gli elementi della sovrimpressione direttamente nel file `EngagementPageOverlay.cs`.

### <a name="web-views-manual-integration"></a>Integrazione manuale delle visualizzazioni Web
Reach cerca nelle pagine i due elementi `WebView` che determinano la visualizzazione del banner e della visualizzazione intermedia. L'unica cosa necessaria da fare è aggiungere quei due elementi `WebView` in un punto qualsiasi delle pagine. Ecco un esempio:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


In questo esempio gli elementi `WebView` vengono allungati per adattarsi al contenitore che automaticamente li ridimensiona nel caso in cui lo schermo viene ruotato o la dimensione della pagina cambia.

> [!WARNING]
> È importante che gli elementi `WebView` mantengano la stessa denominazione `engagement_notification_content` e `engagement_announcement_content`, poiché Reach li identifica dal nome. 
> 
> 

## <a name="handle-datapush-optional"></a>Gestire il push di dati (facoltativo)
Se si desidera che l'applicazione sia in grado di ricevere push di dati Reach, è necessario implementare due eventi della classe EngagementReach:

In App.xaml.cs nel costruttore App () aggiungere:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

È possibile notare che il callback di ogni metodo restituisce un valore booleano. Engagement invia un feedback per il back-end dopo l'invio del push di dati. Se il callback restituisce false, verrà inviato il feedback `exit` . In caso contrario, il feedback sarà `action`. Se non è impostato alcun callback per gli eventi, il feedback `drop` verrà restituito a Engagement.

> [!WARNING]
> Engagement non è in grado di ricevere più feedback per un push di dati. Se si prevede di impostare diversi gestori su un evento, tenere presente che il feedback corrisponderà all'ultimo inviato. In questo caso, è consigliabile restituire sempre lo stesso valore per evitare confusione di feedback sul front-end.
> 
> 

## <a name="customize-ui-optional"></a>Personalizzare l'interfaccia utente (facoltativo)
### <a name="first-step"></a>Primo passaggio
È possibile personalizzare l'interfaccia utente di Reach.

A tale scopo, è necessario creare una sottoclasse della classe `EngagementReachHandler` .

**Codice di esempio:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Impostare quindi il contenuto del campo `EngagementReach.Instance.Handler` con l'oggetto personalizzato nella classe `App.xaml.cs` all'interno del metodo `App()`.

**Codice di esempio:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> Per impostazione predefinita, Engagement usa una specifica implementazione di `EngagementReachHandler`.
> Non è necessario crearne di proprie e, se ne viene creata una, non è necessario eseguire l'override di ogni metodo. Il comportamento predefinito consiste nel selezionare l'oggetto di base di Engagement.
> 
> 

### <a name="web-view"></a>Visualizzazione Web
Per impostazione predefinita, Reach userà le risorse incorporate della DLL per visualizzare le pagine e le notifiche.

Per fornire possibilità di personalizzazione completa è utilizzata solo la visualizzazione Web. Se si vuole personalizzare i layout, eseguire direttamente l'override dei file di risorse `EngagementAnnouncement.html` e `EngagementNotification.html`. Engagement richiede tutto il codice in `<body></body>` per un'esecuzione corretta, ma è possibile aggiungere tag esterni a `engagement_webview_area`.

È tuttavia possibile usare le proprie risorse.

È possibile eseguire l'override dei metodi `EngagementReachHandler` in una sottoclasse per indicare a Engagement di usare i layout, ma fare attenzione al meccanismo di Engagement incorporato:

**Codice di esempio:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Per impostazione predefinita, AnnouncementHTML è `ms-appx-web:///Resources/EngagementAnnouncement.html`. Rappresenta il file html per il progetto del contenuto di un messaggio push (annuncio di testo, annuncio Web e annuncio di sondaggio). AnnouncementName è `engagement_announcement_content`. È il nome del progetto webview nella pagina xaml.

NotificationHTML è `ms-appx-web:///Resources/EngagementNotification.html`. Rappresenta il file html per il progetto la notifica di un messaggio di push. NotificationName è `engagement_notification_content`. È il nome del progetto webview nella pagina xaml.

### <a name="customization"></a>Personalizzazione
È possibile personalizzare la visualizzazione Web di notifiche e annunci nel modo desiderato se si mantiene l'oggetto Engagement. Tenere presente che l'oggetto webview è descritto tre volte. La prima volta in xaml, la seconda nel file cs nel metodo "setwebview()" e la terza nel file html.

* Nel file xaml si descrive il componente di webview del layout grafico corrente.
* Nel file cs è possibile definire "setwebview()" in cui è possibile impostare la dimensione delle due webview (notifica, annuncio). Questa operazione è molto efficace per il ridimensionamento dell'applicazione.
* Nel file html di Engagement viene descritto il contenuto della visualizzazione Web, il progetto e le posizioni degli elementi.

### <a name="launch-message"></a>Messaggio di avvio
Quando un utente fa clic su una notifica del sistema (avviso popup), Engagement avvia l'applicazione, carica il contenuto dei messaggi di push e visualizza la pagina per la campagna corrispondente.

Tra l'avvio dell'applicazione e la visualizzazione della pagina si verifica un ritardo (a seconda della velocità della rete).

Per indicare all'utente che è in corso un caricamento, è necessario fornire un'indicazione visiva, ad esempio una barra o un indicatore di avanzamento. Engagement non è in grado di gestire questa situazione, ma fornisce alcuni gestori.

Per implementare il callback, in App.xaml.cs in "Public App(){}" aggiungere:

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

È possibile impostare il callback nel metodo "Public App(){}" del file `App.xaml.cs`, preferibilmente prima della chiamata `EngagementReach.Instance.Init()`.

> [!TIP]
> Ogni gestore viene chiamato dal thread dell'interfaccia utente. Non è necessario preoccuparsi quando si utilizza MessageBox o un elemento correlato all'interfaccia utente.
> 
> 

## <a id="push-channel-sharing"></a> Condivisione del canale push
Se si usano notifiche push per altri scopi all'interno dell'applicazione, è necessario usare la funzione di condivisone del canale push di Engagement SDK, in modo da evitare la perdita di notifiche push.

* È possibile specificare il canale push desiderato durante la procedura di inizializzazione di Engagement Reach. In questo modo, l'SDK userà il canale specificato anziché richiederne uno nuovo.

Aggiornare l'inizializzazione di Engagement Reach con il canale push nel metodo `InitEngagement` ottenuto dal file `App.xaml.cs`:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* In alternativa, se si decide di usare il canale push dopo l'inizializzazione di Reach, è possibile impostare un callback su Engagement Reach per ottenere il canale push creato dall'SDK.

Impostare il callback in un momento qualsiasi **dopo** l'inizializzazione di Reach:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Suggerimento per lo schema
È possibile utilizzare uno schema personalizzato. È possibile inviare un tipo diverso di URI dal front-end di Engagement da utilizzare nell'applicazione Engagement. Lo schema predefinito come `http, ftp, ...` è gestito da Windows. Una finestra chiederà se nel dispositivo sono installate applicazioni predefinite. È possibile anche creare uno schema personalizzato per l'applicazione.

Il metodo semplice per impostare uno schema personalizzato nell'applicazione consiste nell'aprire `Package.appxmanifest` e andare nel pannello `Declarations`. Selezionare `Protocol` nella casella di scorrimento Dichiarazioni disponibili e aggiungerlo. Modificare il campo `Name` con il nuovo nome desiderato per il protocollo.

Per usare questo protocollo modificare `App.xaml.cs` con il metodo `OnActivated` e non dimenticare di inizializzare Engagement anche qui:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

