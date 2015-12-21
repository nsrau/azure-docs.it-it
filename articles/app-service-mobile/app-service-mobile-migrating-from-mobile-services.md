<properties
	pageTitle="Eseguire la migrazione da Servizi mobili a un'app per dispositivi mobili del servizio app"
	description="Informazioni su come eseguire facilmente la migrazione dell'applicazione Servizi mobili a un'app per dispositivi mobili del servizio app"
	services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="mahender"/>

# Eseguire la migrazione del servizio mobile di Azure esistente al servizio app

Questo argomento descrive come eseguire la migrazione di un'applicazione esistente da Servizi mobili di Azure a una nuova app per dispositivi mobili del servizio app. È possibile eseguire facilmente la migrazione di qualsiasi servizio mobile esistente a un nuovo back-end di App per dispositivi mobili. Durante la migrazione, il servizio mobile esistente continuerà a funzionare.

Quando si esegue la migrazione al servizio app, il servizio mobile ha accesso a tutte le funzionalità del servizio app e la fatturazione viene eseguita in base ai [prezzi del servizio app], non ai prezzi di Servizi mobili.

Un altro aspetto da tenere presente è che tutti i processi pianificati verranno spostati da un piano dell'Utilità di pianificazione di Azure gestito da Microsoft a un piano dell'Utilità di pianificazione di Azure controllato e gestito dalla sottoscrizione dello sviluppatore. Analogamente ai vantaggi della migrazione al servizio app, questo consente di sfruttare appieno la potenza dell'Utilità di pianificazione di Azure.

### <a name="why-host"></a>Vantaggi dell'hosting nel servizio app

Il servizio app fornisce un ambiente più ricco di funzionalità per l'applicazione. L'hosting nel servizio app offre al servizio l'accesso a slot di staging, domini personalizzati, supporto per Gestione traffico e altro ancora. Anche se è possibile aggiornare un servizio mobile a un backend di App per dispositivi mobili dopo avere eseguito la migrazione al servizio app, è possibile che alcuni clienti vogliano sfruttare immediatamente i vantaggi offerti da queste funzionalità, senza eseguire ancora l'aggiornamento dell'SDK.

Per altre informazioni sui vantaggi del servizio app, vedere [Confronto tra Servizi mobili e servizio app].

## Eseguire la migrazione o aggiornare

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

- Per i progetti server basati su Node.js, il nuovo [SDK Node.js di App per dispositivi mobili](https://github.com/Azure/azure-mobile-apps-node) fornisce numerose nuove funzionalità. Ad esempio, è possibile ora eseguire sviluppi e debug locali, utilizzare qualsiasi versione Node.js successiva alla 0.10 e personalizzare con qualsiasi middleware Express.js.

- Per i progetti server basati su .NET, il nuovo [pacchetto NuGet dell'SDK di App per dispositivi mobili](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) dispone di più flessibilità sulle dipendenze NuGet, supporta le nuove funzionalità di autenticazione del servizio app e compone con qualsiasi progetto ASP.NET, incluso MVC. Per altre informazioni sugli aggiornamenti, vedere [Aggiornare i Servizi mobili .NET esistenti al servizio app](app-service-mobile-net-upgrading-from-mobile-services.md).


## <a name="understand"></a>Informazioni sulle app per dispositivi mobili del servizio app

App per dispositivi mobili del servizio app offre un nuovo modo per creare applicazioni mobili usando Azure. Altre informazioni su App per dispositivi mobili sono disponibili in [Informazioni sulle app per dispositivi mobili].

Durante la migrazione ad App per dispositivi mobili, tutte le funzionalità delle app esistenti, inclusa la logica di business, possono essere mantenute. Inoltre, sono disponibili nuove funzionalità per l'applicazione. Nel modello di App per dispositivi mobili il codice viene effettivamente eseguito in un'app Web in Servizio app, che è la nuova versione di Siti Web di Azure. L'utente ha il controllo completo sull'app Web e sul suo funzionamento. Inoltre, ora possono essere usate funzionalità delle app Web che in precedenza non erano disponibili ai clienti di Servizi mobili, ad esempio Gestione traffico e Slot di sviluppo.

La limitazione principale dell'aggiornamento consiste nel fatto che i processi pianificati di Servizi mobili non sono integrati ed è quindi necessario configurare l'Utilità di pianificazione di Azure in modo da chiamare le API personalizzate oppure abilitare le funzionalità dei processi Web.

## Migrazione del servizio mobile con la procedura guidata Esegui la migrazione al servizio app

Il modo più semplice e consigliato per eseguire la migrazione del servizio mobile al servizio app è l'uso della procedura guidata Esegui la migrazione al servizio app, disponibile nel portale di Azure classico. Passare al [portale di Azure classico], individuare i servizi mobili e selezionare un servizio mobile di cui eseguire la migrazione. Nella parte inferiore dello schermo verrà visualizzato un pulsante **Esegui la migrazione al servizio app**, che avvia il processo di migrazione del servizio mobile.

### <a name="what-gets-migrated"></a>Di quali elementi viene eseguita la migrazione?

La proceduta guidata di migrazione modifica le server farm che ospitano il servizio mobile dalla gestione tramite Servizi mobili alla gestione tramite Servizio app. Sposta inoltre tutti i piani dell'Utilità di pianificazione che erano gestiti da Servizi mobili da una sottoscrizione gestita da Microsoft alla sottoscrizione dello sviluppatore. Quando le server farm vengono trasferite al controllo di gestione del servizio app, tutti i Servizi mobili che erano associati a tale server farm vengono spostati nello stesso momento.

Servizi mobili organizza i servizi mobili in server farm in base all'area geografica e allo SKU (gratuito, di base, standard). Tutti i servizi gratuiti in un'area geografica sono nella stessa server farm e vengono migrati insieme. Un certo numero di servizi di base in un'area geografica è ospitato insieme nella stessa farm, ma se si dispone di numerosi servizi di base, questi saranno in piani separati. Ogni servizio standard è in una propria server farm. Se si desidera eseguire la migrazione di un singolo servizio o evitare lo spostamento di un servizio con gli altri siti, è possibile aggiornarlo al livello di base e avrà un proprio piano indipendente.

Anche i piani dell'Utilità di pianificazione di Azure verranno aggiunti alla sottoscrizione, se venivano usati processi pianificati. Per ogni sottoscrizione è consentito un numero limitato di processi pianificati gratuiti, quindi questa operazione potrebbe comportare alcuni costi, che possono essere verificati dopo la sottoscrizione.

###  Uso della migrazione guidata

1. Passare al [portale di Azure classico] e fare clic su Servizi mobili. 

2. Selezionare il servizio mobile di cui eseguire la migrazione, quindi fare clic su "Esegui la migrazione al servizio app" nella barra dei menu inferiore. Verrà visualizzata una finestra popup con l'elenco dei servizi mobili di cui sarà eseguita la migrazione. Vedere la [sezione precedente](#what-gets-migrated) per informazioni dettagliate su quali servizi verranno visualizzati e perché.

3. Digitare il nome del servizio mobile per confermare la migrazione, quindi fare clic sul segno di spunta per continuare. Quando più servizi mobili sono inclusi nella migrazione, è comunque necessario digitare il nome del servizio originale che è stato selezionato.

4. Dopo avere avviato la migrazione, è possibile visualizzare lo stato corrente dall'elenco Servizi mobili nel [portale di Azure classico]. Verrà indicato "migrazione in corso" per tutti i servizi attualmente in fase di migrazione. Una volta completate tutte le migrazioni, è possibile visualizzarle nel [portale di Azure] in App per dispositivi mobili. Durante e dopo la migrazione, i servizi mobili continueranno a funzionare e l'URL non verrà modificato.

## Migrazione manuale per il back-end di Servizi mobili .NET

>[AZURE.NOTE]Tenere presente che il metodo consigliato per la migrazione di un servizio mobile è tramite la procedura guidata Esegui la migrazione al servizio app, descritta nella sezione precedente. La migrazione manuale deve essere usata solo nelle situazioni in cui non è possibile usare la procedura guidata.

Questa sezione illustra come ospitare un progetto di Servizi mobili .NET nel servizio app di Azure. Un'app ospitata in questo modo può usare tutte le esercitazioni di runtime per *Servizi mobili* .NET, anche se è necessario sostituire eventuali URL che usano il dominio azure-mobile.net con il dominio dell'istanza del servizio app.

Un progetto di Servizi mobili può essere ospitato anche in un [ambiente del servizio app]. Tutto il contenuto in questo argomento è ancora valido, ma il sito avrà il formato `contoso.contosoase.p.azurewebsites.net` anziché `contoso.azurewebsites.net`.

>[AZURE.NOTE]Se si esegue una migrazione manuale, *non* è possibile preservare l'URL **service.azure-mobile.net** esistente. Quando si dispone di client di dispositivi mobili che si stanno connettendo a questo URL, si dovrebbe usare l'opzione di migrazione automatica o mantenere in esecuzione il proprio servizio per dispositivi mobili fino a che tutti i client non abbiano eseguito l'aggiornamento al nuovo URL.


### <a name="app-settings"></a>Impostazioni dell'applicazione
Servizi mobili richiede la disponibilità di alcune impostazioni dell'applicazione nell'ambiente, descritte in questa sezione.

Per configurare le impostazioni dell'applicazione nel back-end App per dispositivi mobili, è prima di tutto necessario accedere al [portale di Azure]. Passare all'app del servizio app che si desidera usare come back-end di App per dispositivi mobili, fare clic su **Impostazioni** > **Impostazioni applicazione**, quindi scorrere verso il basso fino a **Impostazioni app**, che consente di impostare le seguenti coppie chiave-valore obbligatorie:

+ **MS\_MobileServiceName** deve corrispondere al nome dell'app. Ad esempio, se l'URL del back-end è `contoso.azurewebsites.net`, *contoso* è il valore corretto.

+ **MS\_MobileServiceDomainSuffix** deve corrispondere al nome dell'app Web. Ad esempio, se l'URL del back-end è `contoso.azurewebsites.net`, *azurewebsites.net* è il valore corretto.

+ **MS\_ApplicationKey** può corrispondere a qualsiasi valore, ma deve essere uguale al valore usato dall'SDK del client. È consigliabile usare un GUID.

+ **MS\_MasterKey** può corrispondere a qualsiasi valore, ma deve essere uguale al valore usato da qualsiasi API/client di amministrazione. È consigliabile usare un GUID.

Durante la migrazione di un servizio mobile, sarà possibile ottenere sia la chiave master che la chiave applicazione dalla scheda **Configura** della sezione Servizi mobili del [portale di Azure classico]. Fare clic su **Gestisci chiavi** nella parte inferiore e copiare le chiavi.


### <a name="client-sdk"></a>Procedura: Modificare l'SDK del client

Nel progetto dell'app client modificare il costruttore dell'oggetto client di Servizi mobili in modo che accetti il nuovo URL dell'app (ad esempio, `https://contoso.azurewebsites.net`) e la chiave applicazione configurata in precedenza. La versione dell'SDK del client deve essere una versione di **Servizi mobili** e **non** deve essere aggiornata. Per client iOS e Android usare le versioni 2.x e per Windows/Xamarin usare la versione 1.3.2. I client Javascript devono usare la versione 1.2.7.

### <a name="data"></a>Procedura: Abilitare le funzionalità dei dati

Per usare le classi predefinite di Entity Framework in Servizi mobili, sono necessarie due impostazioni aggiuntive delle app.

Le stringhe di connessione vengono archiviate nella sezione **Stringhe di connessione** del pannello Impostazioni applicazione, immediatamente sotto la sezione **Impostazioni app**. La stringa di connessione per il database deve essere configurata nella chiave **MS\_TableConnectionString**. Durante la migrazione di un servizio mobile esistente al servizio app, passare alla sezione **Stringhe di connessione** della scheda **Configura** del servizio mobile nel [portale di Azure classico](https://manage.windowsazure.com/). Fare clic su **Mostra stringhe di connessione** e copiare il valore.

Per impostazione predefinita, lo schema da usare è **MS\_MobileServiceName**, ma può essere sovrascritto con l'impostazione **MS\_TableSchema**. In **Impostazioni app** impostare **MS\_TableSchema** come nome dello schema da usare. Se si sposta un'applicazione esistente di Servizi mobili, è già stato creato uno schema mediante Entity Framework. Si tratta del nome del servizio mobile, non dell'istanza del servizio app che ospiterà il codice.

### <a name="push"></a>Procedura: Abilitare le funzionalità push

Per consentire il funzionamento delle notifiche push, l'app Web deve avere anche informazioni sull'hub di notifica.

In **Stringhe di connessione** impostare **MS\_NotificationHubConnectionString** sulla stringa di connessione DefaultFullSharedAccessSignature dell'hub di notifica. Per eseguire la migrazione di un servizio mobile esistente, passare alla sezione **Stringhe di connessione** della scheda **Configura** del servizio mobile nel [portale di Azure classico](https://manage.windowsazure.com/). Fare clic su **Mostra stringhe di connessione** e copiare il valore.

Per l'impostazione dell'app **MS\_NotificationHubName** specificare il nome dell'hub. Quando si sposta un servizio mobile esistente, è possibile ottenere questo valore dalla scheda **Push** nel [portale di Azure classico](https://manage.windowsazure.com/). Gli altri campi di questa scheda sono collegati all'hub stesso e non devono essere copiati altrove.

### <a name="auth"></a>Procedura: Abilitare le funzionalità relative alle autorizzazioni

Anche le funzionalità relative all'identità hanno requisiti per le impostazioni delle app per i singoli provider. Se si esegue lo spostamento da un servizio mobile esistente, ogni campo della scheda **Identità** del portale di Azure classico include un'impostazione dell'app corrispondente.

Account Microsoft

* **MS\_MicrosoftClientID**

* **MS\_MicrosoftClientSecret**

* **MS\_MicrosoftPackageSID**

Facebook

* **MS\_FacebookAppID**

* **MS\_FacebookAppSecret**

Twitter

* **MS\_TwitterConsumerKey**

* **MS\_TwitterConsumerSecret**

Google

* **MS\_GoogleClientID**

* **MS\_GoogleClientSecret**

AAD

* **MS\_AadClientID**

* **MS\_AadTenants** - Nota: **MS\_AadTenants** viene archiviata come elenco delimitato da virgole dei domini del tenant (corrispondente al campo **Tenant consentiti** del portale di Azure classico).

### <a name="publish"></a>Procedura: Pubblicare il progetto di servizio mobile

1. Nel [portale di Azure] passare all'app, fare clic su **Recupera profilo** nella barra dei comandi, quindi salvare il profilo scaricato nel computer locale.
2. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server di Servizi mobili, quindi scegliere **Pubblica**. 
3. Nella scheda del profilo scegliere **Importa** e passare al profilo scaricato.
3. Fare clic su **Pubblica** per distribuire il codice nel servizio app.

I log vengono gestiti tramite le funzionalità di registrazione standard del servizio app. Per altre informazioni sulla registrazione, vedere [Abilitare la registrazione diagnostica nel servizio app di Azure].



<!-- URLs. -->

[portale di Azure]: https://portal.azure.com/
[portale di Azure classico]: https://manage.windowsazure.com/
[Informazioni sulle app per dispositivi mobili]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /it-IT/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /it-IT/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Abilitare la registrazione diagnostica nel servizio app di Azure]: web-sites-enable-diagnostic-log.md
[prezzi del servizio app]: https://azure.microsoft.com/it-IT/pricing/details/app-service/
[ambiente del servizio app]: app-service-app-service-environment-intro.md
[Confronto tra Servizi mobili e servizio app]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1210_2015-->