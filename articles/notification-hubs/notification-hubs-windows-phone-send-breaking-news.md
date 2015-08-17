<properties 
	pageTitle="Usare Hub di notifica per inviare le ultime notizie (Windows Phone)" 
	description="Usare Hub di notifica di Azure con i tag nelle registrazioni per inviare le ultime notizie a un'app per Windows Phone." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="wesmc"/>

# Uso di Hub di notifica per inviare le ultime notizie

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Panoramica

Questo argomento descrive come usare Hub di notifica di Azure per trasmettere le notifiche relative alle ultime notizie a un'app per Windows Phone 8.0/8.1 Silverlight. Se si sviluppano app per Windows Store o Windows Phone 8.1, fare riferimento alla versione [Windows Universal](notification-hubs-windows-store-dotnet-send-breaking-news.md). Al termine dell'esercitazione, si sarà appreso a effettuare la registrazione alle categorie di ultime notizie desiderate e ricevere le notifiche push solo da tali categorie. Questo scenario è un modello comune per molte app nelle quali le notifiche devono essere inviate a gruppi di utenti che hanno dichiarato un interesse, ad esempio lettori di feed RSS, app per fan di musica e così via.

È possibile abilitare gli scenari di trasmissione includendo uno o più _tag_ durante la creazione di una registrazione nell'hub di notifica. Quando le notifiche vengono inviate a un tag, tutti i dispositivi che hanno effettuato la registrazione al tag riceveranno la notifica. Poiché i tag sono costituiti da stringhe, non è necessario eseguire il provisioning anticipatamente. Per ulteriori informazioni sui tag, vedere l'articolo relativo alle [linee guida per gli hub di notifica].

##Prerequisiti

Questo argomento si basa sull'app creata nell'esercitazione [Introduzione ad Hub di notifica]. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione ad Hub di notifica].

##Aggiungere la selezione delle categorie all'app

Il primo passaggio prevede l'aggiunta degli elementi dell'interfaccia utente alla pagina principale esistente per consentire all'utente di selezionare le categorie per le quali registrarsi. Le categorie selezionate da un utente sono archiviate nel dispositivo. All'avvio dell'app, viene creata una registrazione del dispositivo nell'hub di notifica con le categorie selezionate come tag.

1. Aprire il file di progetto MainPage.xaml, quindi sostituire gli elementi **Grid** denominati `TitlePanel` e `ContentPanel` con il codice seguente:
			
        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. Nel progetto creare una nuova classe denominata **Notifications**, aggiungere il modificatore **public** alla definizione della classe e quindi aggiungere le istruzioni **using** seguenti al nuovo file di codice:

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;
		using System.IO.IsolatedStorage;

3. Copiare il codice seguente nella nuova classe **Notifications**:

		private NotificationHub hub;

		public Notifications()
		{
		    hub = new NotificationHub("<hub name>", "<connection string with listen access>");
		}
		
		public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
		{
		    var categoriesAsString = string.Join(",", categories);
		    var settings = IsolatedStorageSettings.ApplicationSettings;
		    if (!settings.Contains("categories"))
		    {
		        settings.Add("categories", categoriesAsString);
		    }
		    else
		    {
		        settings["categories"] = categoriesAsString;
		    }
		    settings.Save();
		
		    await SubscribeToCategories(categories);
		}
		
		public async Task SubscribeToCategories(IEnumerable<string> categories)
		{
		    var channel = HttpNotificationChannel.Find("MyPushChannel");
		
		    if (channel == null)
		    {
		        channel = new HttpNotificationChannel("MyPushChannel");
		        channel.Open();
		        channel.BindToShellToast();
		    }
		
		    await hub.RegisterNativeAsync(channel.ChannelUri.ToString(), categories);
		}

    Questa classe utilizza l'archiviazione locale per archiviare le categorie di notizie che il dispositivo deve ricevere. Contiene inoltre i metodi per effettuare la registrazione per queste categorie.

4. Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with listen access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultListenSharedAccessSignature* ottenuta in precedenza.

	> [AZURE.NOTE]Poiché le credenziali che sono distribuite con un'app client in genere non sono sicure, distribuire solo la chiave per l'accesso Listen con l'app client. L'accesso Listen consente all'app di registrarsi per le notifiche ma le registrazioni esistenti non possono essere modificate e le notifiche non possono essere inviate. La chiave di accesso completa viene utilizzata in un servizio back-end sicuro per l'invio delle notifiche e la modifica delle registrazioni esistenti.

4. Nel file di progetto App.xaml.cs aggiungere la proprietà seguente alla classe **App**:

		public Notifications notifications = new Notifications();

	Questa proprietà viene utilizzata per creare un'istanza **Notifications** e accedervi.

5. Aggiungere quindi la riga seguente nel file MainPage.xaml.cs:

		using Windows.UI.Popups;

6. Aggiungere quindi il metodo seguente nel file di progetto MainPage.xaml.cs:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
		{
		    var categories = new HashSet<string>();
		    if (WorldCheckBox.IsChecked == true) categories.Add("World");
		    if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
		    if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
		    if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
		    if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
		    if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
		
		    await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
		
		    MessageBox.Show("Subscribed to: " + string.Join(",", categories));
		}
	
	Questo metodo crea un elenco di categorie e utilizza la classe **Notifications** per archiviare l'elenco nell'archiviazione locale e registrare i tag corrispondenti nell'hub di notifica. Se le categorie vengono modificate, la registrazione viene ricreata con le nuove categorie.

L'app può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie.

##Registrazione per le notifiche

Questa procedura consente di effettuare la registrazione con l'hub di notifica all'avvio usando le categorie archiviate nella risorsa di archiviazione locale.

> [AZURE.NOTE]Poiché l'URI di canale assegnato dal Servizio di notifica Push di Microsoft può cambiare in qualsiasi momento, è necessario ripetere la registrazione per le notifiche di frequente per evitare errori di notifica. In questo esempio viene effettuata la registrazione per le notifiche a ogni avvio dell'app. Per le app che vengono eseguite di frequente, oltre una volta al giorno, è possibile ignorare la registrazione per conservare la larghezza di banda qualora sia trascorso meno di un giorno dalla registrazione precedente.

1. Aggiungere il codice seguente alla classe **Notifications**:

		public IEnumerable<string> RetrieveCategories()
		{
		    var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
		    return categories != null ? categories.Split(',') : new string[0];
		}

	Verranno restituite le categorie definite nella classe.

1. Aprire il file App.xaml.cs file e aggiungere il modificatore **async** al metodo **Application\_Launching**.

2. Nel metodo **Application\_Launching** individuare e sostituire il codice di registrazione di Hub di notifica aggiunto in [Introduzione ad Hub di notifica] con la riga di codice seguente:

		await notifications.SubscribeToCategories(notifications.RetrieveCategories());

	In questo modo, ogni volta che l'app viene avviata vengono recuperate le categorie dall'archiviazione locale e viene richiesta una registrazione per queste categorie.

3. Nel file di progetto MainPage.xaml.cs aggiungere il codice seguente che implementa il metodo **OnNavigatedTo**:

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		    var categories = ((App)Application.Current).notifications.RetrieveCategories();
		
		    if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
		    if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
		    if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
		    if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
		    if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
		    if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
		}

	La pagina principale viene aggiornata in base allo stato delle categorie salvate in precedenza.

Ora l'app è completa e può quindi archiviare un set di categorie nell'archiviazione locale del dispositivo ed effettuare la registrazione con l'hub di notifica ogni volta che l'utente modifica la selezione di categorie. Verrà ora definito un back-end per l'invio delle notifiche delle categorie all'app.

##Inviare notifiche dal back-end

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##Eseguire l'app e generare notifiche

1. In Visual Studio premere F5 per compilare e avviare l'app.

	![][1]

	Si noti che l'interfaccia utente dell'app fornisce un set di interruttori che consentono di scegliere le categorie per le quali effettuare la sottoscrizione.

2. Abilitare uno o più interruttori di categorie e quindi fare clic su **Subscribe**.

	L'app converte le categorie selezionate in tag e richiede una nuova registrazione del dispositivo per i tag selezionati dall'hub di notifica. Le categorie registrate vengono restituite e visualizzate in una finestra di dialogo.

	![][2]

4. Inviare una nuova notifica dal back-end in uno dei modi seguenti:

	+ **App console:** avviare l'app console.

	+ **Java/PHP**: eseguire l'app o lo script.

	Le notifiche per le categorie selezionate vengono visualizzate come notifiche di tipo avviso popup.

	![][3]

L'argomento è stato completato.

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

	Learn how to expand the breaking news app to enable sending localized notifications. 

+ [Notify users with Notification Hubs]

	Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Introduzione ad Hub di notifica]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Use Notification Hubs to broadcast localized breaking news]: ../breakingnews-localized-wp8.md
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users/
[Mobile Service]: /develop/mobile/tutorials/get-started
[linee guida per gli hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone]: ??

[Azure Management Portal]: https://manage.windowsazure.com/




 

<!---HONumber=August15_HO6-->