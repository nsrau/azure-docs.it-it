---
title: Integrazione dell&quot;SDK di Reach per Windows Phone Silverlight
description: Come integrare il servizio Reach di Azure Mobile Engagement con le app per Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: dwrede
editor: 
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 72f69e334172ad69bedb9c8fc4d270a607d1598f


---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Integrazione dell'SDK di Reach per Windows Phone Silverlight
Prima di usare questa guida, è necessario eseguire la procedura di integrazione descritta nel documento [Integrazione di Mobile Engagement SDK per Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) .

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Incorporare l'SDK del servizio Reach di Engagement nel progetto Windows Phone Silverlight
Nessun elemento da aggiungere. `EngagementReach` sono già presenti nel progetto.

> [!TIP]
> È possibile personalizzare le immagini incluse nella cartella `Resources` del progetto, soprattutto l'icona del marchio, che per impostazione predefinita è l'icona di Engagement.
> 
> 

## <a name="add-the-capabilities"></a>Aggiungere le funzionalità
L'SDK del servizio Reach di Engagement richiede alcune funzionalità aggiuntive.

Aprire il file `WMAppManifest.xml` e assicurarsi che le seguenti funzionalità siano dichiarate:

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

La prima viene usata dal servizio MPNS per consentire la visualizzazione di notifiche popup. La seconda viene usata per incorporare un'attività del browser nell'SDK.

Modificare il file `WMAppManifest.xml` e aggiungere il tag `<Capabilities />` all'interno:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Abilitare il Servizio notifica push Microsoft
Per usare il **Servizio notifica push Microsoft** (indicato come MPNS), il file `WMAppManifest.xml` deve avere un tag `<App />` con un attributo `Publisher` impostato sul nome del progetto.

## <a name="initialize-the-engagement-reach-sdk"></a>Inizializzare Engagement Reach SDK
### <a name="engagement-configuration"></a>Configurazione di Engagement
La configurazione di Engagement è centralizzata nel file `Resources\EngagementConfiguration.xml` del progetto.

Modificare questo file per specificare la configurazione di Reach:

* *Facoltativo*: indicare se il push nativo (MPNS) è attivato tra i tag `<enableNativePush>` e `</enableNativePush>`. L'impostazione predefinita è `true`.
* *Facoltativo*: indicare il nome del canale di push tra i tag `<channelName>` e `</channelName>`. Fornire quello che potrebbe essere in uso nell'applicazione o lasciare vuoto il campo.

Se si desidera specificarlo in fase di esecuzione, è possibile chiamare il metodo seguente prima dell'inizializzazione dell'agente di Engagement:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> È possibile specificare il nome del canale di push MPNS dell'applicazione. Per impostazione predefinita, Engagement crea un nome basato su appId. Non è necessario specificare il nome manualmente, a meno che non si preveda di utilizzare il canale di push fuori da Engagement.
> 
> 

### <a name="engagement-initialization"></a>Inizializzazione di Engagement
Modificare il file `App.xaml.cs`:

* Aggiungere quanto segue alle istruzioni `using`:
  
      using Microsoft.Azure.Engagement;
* Inserire `EngagementReach.Instance.Init` subito dopo `EngagementAgent.Instance.Init` in `Application_Launching`:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Inserire `EngagementReach.Instance.OnActivated` nel metodo `Application_Activated`:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> `EngagementReach.Instance.Init` viene eseguito in un thread dedicato. Non è necessario eseguirlo manualmente.
> 
> 

## <a name="app-store-submission-considerations"></a>Considerazioni sull'invio di notifiche di App Store
Microsoft impone alcune regole relative all'uso delle notifiche push.

Come definito nella documentazione di Microsoft sui [criteri relativi alle applicazioni] , sezione 2.9:

1) È necessario chiedere all'utente di accettare la ricezione delle notifiche push. Aggiungere quindi nelle impostazioni un modo per disattivare le notifiche push.

L'oggetto EngagementReach fornisce due metodi per gestire il consenso/rifiuto, `EnableNativePush()` e `DisableNativePush()`. È possibile, ad esempio, creare nelle impostazioni un'opzione che consente di disabilitare o abilitare MPNS.

È possibile anche decidere di disattivare MPNS tramite la configurazione di Engagement \<windows-phone-sdk-reach-configuration\>.

> 2.9.1) L'applicazione deve descrivere prima le notifiche da fornire e **ottenere l'autorizzazione esplicita dell'utente (consenso)** e **deve fornire un meccanismo attraverso il quale l'utente può rifiutare esplicitamente la ricezione di notifiche push**. Tutte le notifiche fornite tramite il Servizio notifica push Microsoft devono essere coerenti con la descrizione fornita all'utente e conformi a tutti i [criteri applicazione][Content Policies] e a tutti i [requisiti aggiuntivi per tipi di applicazione specifici] applicabili.
> 
> 

2) Non fare un uso eccessivo delle notifiche push. Engagement gestisce le notifiche in modo automatico.

> 2.9.2) L'applicazione che usa il Servizio notifica push Microsoft non deve sfruttare in modo eccessivo la capacità di rete o la larghezza di banda del Servizio notifica push Microsoft o sovraccaricare inutilmente un dispositivo Windows Phone o altro dispositivo o servizio Microsoft con un numero eccessivo di notifiche push, come stabilito da Microsoft a sua ragionevole discrezione, e non deve danneggiare o interferire con le reti o i server Microsoft o i server e le reti di terze parti connesse al Servizio notifica push Microsoft.
> 
> 

3) Non fare affidamento su MPNS per inviare informazioni critiche. Engagement usa MPNS, pertanto questa regola è valida anche per le campagne create all'interno del front-end di Engagement.

> 2.9.3) Il Servizio notifica push Microsoft non può essere usato per inviare notifiche di importanza cruciale o che possono influire su questioni di vita o di morte, incluse, senza alcuna limitazione, notifiche correlate a una condizione o a un dispositivo medico. MICROSOFT RIFIUTA ESPRESSAMENTE QUALSIASI GARANZIA CHE L'UTILIZZO DEL SERVIZIO DI NOTIFICA PUSH DI MICROSOFT O CHE IL RECAPITO DELLE NOTIFICHE DEL SERVIZIO NOTIFICA PUSH MICROSOFT SARÀ ININTERROTTO, PRIVO DI ERRORI O CHE SARÀ ESEGUITO IN TEMPO REALE.
> 
> 

**Non è possibile garantire che l'applicazione supererà il processo di convalida se non si rispettano queste indicazioni.**

## <a name="handle-data-push-optional"></a>Gestire il push di dati (facoltativo)
Se si desidera che l'applicazione sia in grado di ricevere push di dati Reach, è necessario implementare due eventi della classe EngagementReach:

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

Impostare quindi il contenuto del campo `EngagementReach.Instance.Handler` con l'oggetto personalizzato nella classe `App.xaml.cs` all'interno del metodo `Application_Launching`.

**Codice di esempio:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Per impostazione predefinita, Engagement usa una specifica implementazione di `EngagementReachHandler`. Non è necessario crearne di proprie e, se ne viene creata una, non è necessario eseguire l'override di ogni metodo. Il comportamento predefinito consiste nel selezionare l'oggetto di base di Engagement.
> 
> 

### <a name="layouts"></a>Layout
Per impostazione predefinita, Reach userà le risorse incorporate della DLL per visualizzare le pagine e le notifiche.

Tuttavia, è possibile decidere di utilizzare le proprie risorse in modo da riflettere il marchio in questi componenti.

È possibile eseguire l'override dei metodi `EngagementReachHandler` in una sottoclasse per indicare a Engagement di usare layout personalizzati:

**Codice di esempio:**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> Il metodo `CreateNotification` può restituire null. La notifica non verrà visualizzata e la campagna Reach verrà eliminata.
> 
> 

Per semplificare l'implementazione di layout, viene fornito anche un xaml che può essere utilizzato come base per il codice. Tale elemento si trova nell'archivio dell'SDK di Engagement (/src/reach/).

> [!WARNING]
> Le origini fornite da Microsoft sono le stesse. Pertanto, se si desidera modificarle direttamente, non dimenticare di modificare lo spazio dei nomi e il nome.
> 
> 

### <a name="notification-position"></a>Posizione di notifica
Per impostazione predefinita, una notifica in-app viene visualizzata nella parte inferiore sinistra dell'applicazione. È possibile modificare questo comportamento eseguendo l'override del metodo `GetNotificationPosition` dell'oggetto `EngagementReachHandler`.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

Attualmente, è possibile scegliere tra le posizioni `BOTTOM` (impostazione predefinita) e `TOP`.

### <a name="launch-message"></a>Messaggio di avvio
Quando un utente fa clic su una notifica del sistema (avviso popup), Engagement avvia l'app, carica il contenuto dei messaggi di push e visualizza la pagina per la campagna corrispondente.

Tra l'avvio dell'applicazione e la visualizzazione della pagina si verifica un ritardo (a seconda della velocità della rete).

Per indicare all'utente che è in corso un caricamento, è necessario fornire un'indicazione visiva, ad esempio una barra o un indicatore di avanzamento. Engagement non è in grado di gestire questa situazione, ma fornisce alcuni gestori.

Per implementare il callback:

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

È possibile impostare il callback nel metodo `Application_Launching` del file `App.xaml.cs`, preferibilmente prima della chiamata `EngagementReach.Instance.Init()`.

> [!TIP]
> Ogni gestore viene chiamato dal thread dell'interfaccia utente. Non è necessario preoccuparsi quando si utilizza MessageBox o un elemento correlato all'interfaccia utente.
> 
> 

[criteri relativi alle applicazioni]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[requisiti aggiuntivi per tipi di applicazione specifici]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx




<!--HONumber=Dec16_HO2-->


