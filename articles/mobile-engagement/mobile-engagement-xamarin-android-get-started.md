<properties
    pageTitle="Introduzione ad Azure Mobile Engagement per Xamarin.Android"
    description="Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app Xamarin.Android."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager=""
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="piyushjo" />


# <a name="get-started-with-azure-mobile-engagement-for-xamarin.android-apps"></a>Introduzione ad Azure Mobile Engagement per app Xamarin.Android

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'uso dell'app e sull'invio di notifiche push a utenti segmentati di un'applicazione Xamarin.Android.
Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Verrà creata un'app Xamarin.Android vuota che raccoglie dati di base e riceve notifiche push tramite il servizio Google Cloud Messaging (GCM).

Per completare questa esercitazione, è necessario disporre di:

+ [Xamarin Studio](http://xamarin.com/studio). È anche possibile usare Visual Studio con Xamarin ma questa esercitazione usa Xamarin Studio. Per istruzioni di installazione, vedere [Configurazione e installazione per Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a name="<a-id="setup-azme"></a>setup-mobile-engagement-for-your-android-app"></a><a id="setup-azme"></a>Configurare Mobile Engagement per l'app Android

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a name="<a-id="connecting-app"></a>connect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. 

Verrà creata un'app di base con Xamarin Studio per illustrare l'integrazione.

###<a name="create-a-new-xamarin.android-project"></a>Creare un nuovo progetto Xamarin.Android

1. Avviare **Xamarin Studio** e passare a **File** -> **New** ->  (Nuovo) **Solution** (Soluzione) 

    ![][1]

2. Selezionare **Android App** (App Android), assicurarsi che il linguaggio selezionato sia **C#** e quindi fare clic su **Next** (Avanti).

    ![][2]

3. Specificare le informazioni richieste nei campi **App Name** (Nome app) e **Organization Identifier** (Identificatore organizzazione). Assicurarsi di selezionare **Google Play Services** con un segno di spunta e quindi fare clic su **Next** (Avanti). 

    ![][3]
    
4. Aggiornare i campi **Project Name** (Nome progetto), **Solution Name** (Nome soluzione) e **Location** (Posizione), se necessario, e fare clic su **Create** (Crea).

    ![][4]
 
Xamarin Studio creerà l'app in cui verrà integrato Mobile Engagement. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Connettere l'app al back-end di Mobile Engagement

1. Fare clic con il pulsante destro del mouse sulla cartella **Packages** (Pacchetti) nelle finestre della soluzione, quindi scegliere **Add Packages...** (Aggiungi pacchetti)

    ![][5]

2. Cercare **Microsoft Azure Mobile Engagement Xamarin SDK** e aggiungerlo alla soluzione.  

    ![][6]
   
3. Aprire **MainActivity.cs** e aggiungere le istruzioni using seguenti:

        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;

4. Nel metodo `OnCreate` aggiungere quanto segue per inizializzare la connessione con il back-end di Mobile Engagement. Assicurarsi di aggiungere **ConnectionString**. 

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

###<a name="add-permissions-and-a-service-declaration"></a>Aggiungere le autorizzazioni e una dichiarazione del servizio

1. Aprire il file **Manifest.xml** nella cartella Properties. Selezionare la scheda Source per aggiornare direttamente l'origine XML.
 
2. Aggiungere queste autorizzazioni al file Manifest.xml, nella cartella **Properties** del progetto, immediatamente prima o dopo il tag `<application>`:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Aggiungere quanto indicato sotto tra i tag `<application>` e `</application>` per dichiarare il servizio agente:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

4. Nel codice appena incollato sostituire `"<Your application name>"` nell'etichetta. Questo è il valore visualizzato nel menu **Impostazioni** , che mostra all'utente i servizi in esecuzione nel dispositivo. È possibile aggiungere la parola "Service" all'etichetta, ad esempio.

###<a name="send-a-screen-to-mobile-engagement"></a>Inviare una schermata a Mobile Engagement

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata al back-end di Mobile Engagement. Per eseguire questa operazione, assicurarsi che `MainActivity` erediti da `EngagementActivity` anziché da `Activity`.

    public class MainActivity : EngagementActivity
    
In alternativa, se non è possibile ereditare da `EngagementActivity` è necessario aggiungere i metodi `.StartActivity` e `.EndActivity` rispettivamente in `OnResume` e `OnPause`.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }
    
            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

##<a name="<a-id="monitor"></a>connect-app-with-real-time-monitoring"></a><a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a name="<a-id="integrate-push"></a>enable-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e COINVOLGERLI tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement.
Le sezioni seguenti consentono di configurare l'app per la ricezione.

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png



<!--HONumber=Oct16_HO2-->


