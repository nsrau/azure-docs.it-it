<properties 
	pageTitle="Esercitazione sull'invio di ultime notizie localizzate mediante Hub di notifica" 
	description="Informazioni su come usare Hub di notifica del bus di servizio di Azure per inviare notifiche relative alle ultime notizie localizzate." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="ricksal"/>
# Uso di Hub di notifica per inviare le ultime notizie localizzate

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

Questo argomento descrive come usare la funzionalità relativa ai **modelli** di Hub di notifica di Azure per trasmettere notifiche relative alle ultime notizie localizzate in base alla lingua e al dispositivo. In questa esercitazione verrà usata l'app di Windows Store creata in [Uso di Hub di notifica per inviare le ultime notizie]. Al termine, sarà possibile effettuare la registrazione per le categorie di proprio interesse, specificare la lingua in cui ricevere le notifiche e ricevere solo notifiche push per le categorie selezionate nella lingua scelta.

In questa esercitazione vengono descritte le operazioni di base per abilitare questo scenario:

1. [Concetti relativi ai modelli] 
2. [Interfaccia utente dell'app]
3. [Compilazione dell'app client di Windows Store]
4. [Inviare notifiche dal back-end]


Lo scenario è composto da due parti:

- l'app di Windows Store consente ai dispositivi client di specificare una lingua e di sottoscrivere categorie diverse di ultime notizie; 

- il back-end trasmette le notifiche usando le funzionalità relative ai **tag** e ai **modelli** di Hub di notifica di Azure.



##Prerequisiti ##

È necessario aver completato l'esercitazione [Uso di Hub di notifica per inviare le ultime notizie] e disporre del relativo codice, in quanto questa esercitazione è basata direttamente su tale codice. 

È inoltre necessario disporre di Visual Studio 2012.


<h2><a name="concepts"></a>Concetti relativi ai modelli</h2>

In [Uso di Hub di notifica per inviare le ultime notizie] è stata creata un'app che usa i **tag** per sottoscrivere le notifiche per diverse categorie di notizie.
Molte app, tuttavia, sono destinate a più mercati ed è necessario localizzarle. Questo significa che il contenuto delle notifiche deve essere localizzato e inviato al set di dispositivi corretto.
In questo argomento verrà illustrato come usare la funzionalità relativa ai **modelli** di Hub di notifica per inviare facilmente notifiche relative alle ultime notizie localizzate.

Nota: un possibile modo per inviare notifiche localizzate consiste nel creare più versioni di ogni tag. Per supportare l'inglese, il francese e il mandarino, ad esempio, sono necessari tre tag diversi per le ultime notizie internazionali: "world_en", "world_fr" e "world_ch". È quindi necessario inviare una versione localizzata delle ultime notizie internazionali a ogni tag. In questo argomento verranno usati i modelli per evitare la proliferazione di tag e la necessità di inviare più messaggi.

A livello generale, i modelli consentono di specificare in che modo un particolare dispositivo deve ricevere una notifica. Il modello definisce lo specifico formato di payload da usare, facendo riferimento alle proprietà del messaggio inviato dal back-end dell'app. In questo caso verrà inviato un messaggio indipendente dalle impostazioni locali, che contiene tutte le lingue supportate:

	{
		"News_English": "...",
		"News_French": "...",
		"News_Mandarin": "..."
	}

Si procederà quindi a verificare che i dispositivi effettuino la registrazione con un modello che faccia riferimento alla proprietà corretta. Ad esempio, un'app di Windows Store che desidera ricevere un semplice avviso popup effettuerà la registrazione per il seguente modello:

	<toast>
	  <visual>
	    <binding template="ToastText01">
	      <text id="1">$(News_English)</text>
	    </binding>
	  </visual>
	</toast>



I modelli rappresentano uno strumento particolarmente efficace. Per altre informazioni, vedere l'articolo relativo alle [linee guida su Hub di notifica]. In [Procedure di Hub di notifica per Windows Store] è disponibile un riferimento al linguaggio di espressione dei modelli.


<h2><a name="ui"></a>Interfaccia utente dell'app</h2>

L'app Breaking News creata nell'argomento [Uso di Hub di notifica per inviare le ultime notizie] verrà ora modificata in modo da inviare notizie localizzate usando modelli.


Per adattare le app client alla ricezione di messaggi localizzati è necessario sostituire le registrazioni * native*, ovvero prive di modello, con registrazioni modello.


Nell'app di Windows Store:

Modificare il file MainPage.xaml in modo da includere una casella combinata per le impostazioni locali:

	<Grid Margin="120, 58, 120, 80"  
			Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

<h2><a name="building-client"></a><span class="building app">Interfaccia utente dell'app</span>Compilazione dell'app client di Windows Store</h2>

1. Nella classe Notifications, aggiungere un parametro relativo alle impostazioni locali ai metodi  *StoreCategoriesAndSubscribe* e  *SubscribeToCateories*.

		public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            await SubscribeToCategories(locale, categories);
        }

        public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

            await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
        }

	Tenere presente che anziché chiamare il metodo  *RegisterNativeAsync*, verrà chiamato  *RegisterTemplateAsync*: si registra un formato di notifica specifico, in cui il modello dipende dalle impostazioni locali. Verrà inoltre fornito un nome per il modello ("newsTemplate") in quanto può essere necessario registrare più modelli, ad esempio uno per le notifiche di tipo avviso popup e uno per le notifiche di tipo riquadro. In questo caso, per avere la possibilità di aggiornare o eliminare i modelli è necessario assegnare loro un nome.

	Si noti che, se un dispositivo registra più modelli con lo stesso tag, un messaggio in arrivo destinato a tale tag ha come esito il recapito al dispositivo di più notifiche, una per ogni modello. Questo comportamento risulta utile quando lo stesso messaggio logico deve avere produrre più notifiche visive, ad esempio visualizzare sia una notifica badge che una notifica di tipo avviso popup in un'app di Windows Store.

2. Aggiungere il seguente metodo per recuperare le impostazioni locali archiviate:

		public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }

3. In MainPage.xaml.cs aggiornare il gestore degli eventi clic del pulsante recuperando il valore corrente della casella combinata Locale e fornendolo alla chiamata alla classe Notifications, come illustrato di seguito:

		 var locale = (string)Locale.SelectedItem;
            
         var categories = new HashSet<string>();
         if (WorldToggle.IsOn) categories.Add("World");
         if (PoliticsToggle.IsOn) categories.Add("Politics");
         if (BusinessToggle.IsOn) categories.Add("Business");
         if (TechnologyToggle.IsOn) categories.Add("Technology");
         if (ScienceToggle.IsOn) categories.Add("Science");
         if (SportsToggle.IsOn) categories.Add("Sports");

         await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

         var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
         dialog.Commands.Add(new UICommand("OK"));
         await dialog.ShowAsync();

4. Infine, nel file App.xaml.cs aggiornare la chiamata al 
singleton Notifications nel metodo  *OnLaunched*:

		Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());


<h2><a name="send"></a>Inviare notifiche localizzate dal back-end</h2>

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]





## Passaggi successivi

Per altre informazioni sull'uso dei modelli, vedere [Uso di Hub di notifica per inviare notifiche agli utenti: ASP.NET], [Uso di Hub di notifica per inviare notifiche agli utenti: Servizi mobili], nonché l'articolo sulle [linee guida su Hub di notifica]. In [Procedure di Hub di notifica per Windows Store] è disponibile un riferimento al linguaggio di espressione dei modelli.

<!-- Anchors. -->
[Concetti relativi ai modelli]: #concepts
[Interfaccia utente dell'app]: #ui
[Compilazione dell'app client di Windows Store]: #building-client
[Inviare notifiche dal back-end]: #send
[Passaggi successivi]:#next-steps

<!-- Images. -->





















<!-- URLs. -->
[Servizio mobile]: /develop/mobile/tutorials/get-started
[Uso di Hub di notifica per inviare notifiche agli utenti: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Uso di Hub di notifica per inviare notifiche agli utenti: Servizi mobili]: /manage/services/notification-hubs/notify-users
[Uso di Hub di notifica per inviare le ultime notizie]: /manage/services/notification-hubs/breaking-news-dotnet 

[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK per Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started/#create-new-service
[Introduzione ai dati]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Introduzione all'autenticazione]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Introduzione alle notifiche push]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Inviare notifiche push agli utenti di app]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Autorizzare gli utenti con gli script]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript e HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[oggetto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Informazioni aggiuntive su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedure di Hub di notifica per iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Procedure di Hub di notifica per Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx

<!--HONumber=49--> 