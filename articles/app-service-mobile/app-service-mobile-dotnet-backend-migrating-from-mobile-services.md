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
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="mahender"/>

# Eseguire la migrazione del servizio mobile di Azure esistente al servizio app 

Questo argomento descrive come eseguire la migrazione di un'applicazione esistente da Servizi mobili di Azure a una nuova app per dispositivi mobili del servizio app. È possibile eseguire la migrazione di tutte le app di Servizi mobili esistenti a una nuova app per dispositivi mobili del servizio app. Durante una migrazione, l'applicazione Servizi mobili esistente può continuano a funzionare.

>[AZURE.NOTE]Attualmente, i Servizi mobili del back-end .NET possono essere migrati al servizio app tramite una migrazione manuale. Una esperienza di migrazione chiavi in mano per Node.js e .NET sarà presto disponibile. Tuttavia, se si esegue una migrazione manuale, *non* è possibile preservare l’URL **service.azure-mobile.net** esistente.

Quando si esegue la migrazione al Servizio app di Azure, essa ha accesso a tutte le funzionalità del servizio app e vengono fatturate in base ai [prezzi del servizio app], non ai prezzi di Servizi mobili.

### <a name="why-host"></a>Vantaggi dell'hosting nel servizio app

Il servizio app fornisce un ambiente più ricco di funzionalità per l'applicazione. L'hosting nel servizio app offre al servizio l'accesso a slot di staging, domini personalizzati, supporto per Gestione traffico e altro ancora. Anche se è possibile [eseguire la migrazione di un servizio mobile in un'app per dispositivi mobili nel servizio app], è possibile che alcuni clienti vogliano sfruttare immediatamente i vantaggi offerti da queste funzionalità, senza eseguire ancora l'aggiornamento SDK.

La limitazione principale dell'hosting nel servizio app consiste nel fatto che i processi pianificati di Servizi mobili non sono integrati ed è quindi necessario configurare l'Utilità di pianificazione di Azure in modo che raggiunga le API personalizzate oppure abilitare le funzionalità dei processi Web.

Per altre informazioni sui vantaggi del servizio app, vedere [Confronto tra Servizi mobili e il servizio app].

##Eseguire la migrazione o aggiornare

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

  - Per i progetti server basati su Node.js, il nuovo [SDK Node.js di app per dispositivi mobili](https://github.com/Azure/azure-mobile-apps-node) fornisce numerose nuove funzionalità. Ad esempio, è possibile ora eseguire sviluppi e debug locali, utilizzare qualsiasi versione Node.js successiva alla 0.10 e personalizzare con qualsiasi middleware Express.js.

  - Per progetti server basati su .NET, il nuovo [pacchetto NuGet dell’SDK di app per dispositivi mobili](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)dispone di più flessibilità sulle dipendenze NuGet, supporta le nuove funzionalità di autenticazione di servizio app e compone con qualsiasi progetto ASP.NET, incluso MVC. Per altre informazioni sugli aggiornamenti, vedere [Aggiornare i Servizi mobili .NET esistenti in servizio app](app-service-mobile-net-upgrading-from-mobile-services.md).


##<a name="understand"></a>Informazioni sui dispositivi mobili del servizio app 

I dispositivi mobili del servizio app offrono un nuovo modo per creare applicazioni mobili usando Microsoft Azure. Altre informazioni sulle app per dispositivi mobili sono disponibili nell'argomento [Informazioni sulle app per dispositivi mobili].

In una migrazione ad app per dispositivi mobili, tutte le funzionalità, e il codice, delle app esistenti possono essere conservate. Inoltre, sono disponibili nuove funzionalità per l'applicazione. Nel modello di app per dispositivi mobili il codice viene effettivamente eseguito in un'app Web (la nuova versione di Siti Web di Azure). L'utente ha il controllo completo sull'app Web e sul suo funzionamento. Inoltre, ora possono essere usate funzionalità delle app Web che in precedenza non erano disponibili ai clienti di Servizi mobili, ad esempio Gestione traffico e Slot di sviluppo.


##Migrazione manuale per il back-end di Servizi mobili .NET

Questo argomento illustra come ospitare un progetto di Servizi mobili .NET nel servizio app di Azure. Un'app ospitata in questo modo può usare tutte le esercitazioni di runtime per *Servizi mobili* .NET, anche se è necessario sostituire eventuali URL che usano il dominio azure-mobile.net con il dominio dell'istanza del servizio app.

Un progetto di Servizi mobili può essere ospitato anche in un [ambiente del servizio app]. Tutti i contenuti di questo argomento sono comunque applicabili, ma il sito avrà formato contoso.contosoase.p.azurewebsites.net invece di contoso.azurewebsites.net.

>[AZURE.NOTE]Se si esegue una migrazione manuale, *non* è possibile preservare l’URL **service.azure-mobile.net** esistente. Se si dispone di client di dispositivi mobili che si stanno connettendo a questo URL, si dovrebbe utilizzare l’opzione di migrazione automatica o mantenere in esecuzione il proprio servizio per dispositivi mobili fino a che tutti i client non abbiano eseguito l’aggiornamento al nuovo URL.


### <a name="app-settings"></a>Impostazioni dell'applicazione
Servizi mobili richiede la disponibilità di alcune impostazioni dell'applicazione nell'ambiente, descritte in questa sezione.

Per configurare le impostazioni dell'applicazione nell'app Web, è prima di tutto necessario accedere al [portale di gestione di Azure (anteprima)]. Passare all'app Web, per dispositivi mobili o per le API da usare, quindi selezionare "Impostazioni" e infine "Impostazioni applicazione". Scorrere verso il basso fino alla sezione intitolata "Impostazioni app", in cui è possibile configurare le coppie chiave-valore necessarie.
 
**MS\_MobileServiceName** deve corrispondere al nome dell'app. Ad esempio, se si prevede di eseguire un'app con URL contoso.azurewebsites.net, il valore corretto sarà "contoso".
 
**MS\_MobileServiceDomainSuffix** deve corrispondere al nome dell'app Web. Ad esempio, se si prevede di eseguire un'app con URL contoso.azurewebsites.net, il valore corretto sarà "azurewebsites.net".
 
**MS\_ApplicationKey** può corrispondere a qualsiasi valore, ma deve essere uguale al valore usato dall'SDK del client. È consigliabile usare un GUID.
 
**MS\_MasterKey** può corrispondere a qualsiasi valore, ma deve essere uguale al valore usato da qualsiasi API/client di amministrazione. È consigliabile usare un GUID.
 
Se si passa a un'applicazione esistente di Servizi mobili, sarà possibile ottenere sia la chiave master che la chiave applicazione dalla scheda "Configura" della sezione Servizi mobili del[portale di gestione di Azure]. Selezionare l'azione "Gestisci chiavi" in basso e copiare le chiavi.


### <a name="client-sdk"></a>Procedura: Modificare l'SDK del client

Nel progetto dell'app client modificare il costruttore dell'oggetto client di Servizi mobili in modo che accetti la nuova URL dell'app (ad esempio, `https://contoso.azurewebsites.net`), e la chiave applicazione configurata in precedenza. La versione dell'SDK del client deve essere una versione di **Servizi mobili** e **non** deve essere aggiornata. Per client iOS e Android usare le versioni 2.x e per Windows/Xamarin usare la versione 1.3.2. I client Javascript devono usare la versione 1.2.7.

### <a name="data"></a>Procedura: Abilitare le funzionalità dei dati

Per usare le classi predefinite di Entity Framework in Servizi mobili, sono necessarie altre due impostazioni delle app.
 
Le stringhe di connessione vengono archiviate nella sezione "Stringhe di connessione" del pannello Impostazioni applicazione, immediatamente sotto la sezione **Impostazioni app**. La stringa di connessione per il database deve essere configurata nella chiave **MS\_TableConnectionString**. Per spostare un'applicazione esistente di Servizi mobili, passare alla sezione "Stringhe di connessione" della scheda Configura del portale di Servizi mobili. Fare clic su "Mostra stringhe di connessione" e copiare il valore.
 
Per impostazione predefinita, lo schema da usare è **MS\_MobileServiceName**, ma può essere sovrascritto con l'impostazione **MS\_TableSchema**. In **Impostazioni app** impostare **MS\_TableSchema** come nome dello schema da usare. Se si sposta un'applicazione esistente di Servizi mobili, è già stato creato uno schema mediante Entity Framework. Si tratta del nome del servizio mobile, non dell'istanza del servizio app che ospiterà il codice.

### <a name="push"></a>Procedura: Abilitare le funzionalità push

Per consentire il funzionamento delle notifiche push, l'app Web deve avere anche informazioni sull'hub di notifica.
 
In "Stringhe di connessione" impostare **MS\_NotificationHubConnectionString** sulla stringa di connessione DefaultFullSharedAccessSignature per l'hub di notifica. Per spostare un'applicazione esistente di Servizi mobili, passare alla sezione "Stringhe di connessione" della scheda Configura del portale di Servizi mobili. Fare clic su "Mostra stringhe di connessione" e copiare il valore.

Per l'impostazione dell'app **MS\_NotificationHubName** specificare il nome dell'hub. Quando si sposta un'app esistente di Servizi mobili, è possibile ottenere questo valore dalla scheda relativa alle notifiche push nel portale di Servizi mobili. Gli altri campi di questa scheda sono collegati all'hub stesso e non devono essere copiati altrove.
 
### <a name="auth"></a>Procedura: Abilitare le funzionalità relative alle autorizzazioni

Anche le funzionalità relative all'identità hanno requisiti per le impostazioni delle app per i singoli provider. Se si esegue lo spostamento da un'app esistente di Servizi mobili, ogni campo della scheda Identità del portale di Servizi mobili include un'impostazione dell'app corrispondente.
 
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

* **MS\_AadClientID** - Nota: **MS\_AadTenants** viene archiviata come elenco delimitato da virgole dei domini del tenant (corrispondente al campo "Tenant consentiti" del portale di Servizi mobili).

### <a name="publish"></a>Procedura: Pubblicare il progetto di Servizi mobili

1. Nel portale di anteprima passare all'app e selezionare "Recupera profilo" dalla barra dei comandi. Salvare il profilo scaricato nel computer locale.
2. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server di Servizi mobili, quindi scegliere "Pubblica". Nella scheda del profilo scegliere "Importa" e passare al profilo scaricato.
3. Fare clic su Pubblica per distribuire il codice nel servizio app.

I log vengono gestiti tramite le funzionalità di registrazione standard del servizio app. Per altre informazioni sulla registrazione, vedere [Abilitare la registrazione diagnostica nel servizio app di Azure].



<!-- URLs. -->

[portale di gestione di Azure (anteprima)]: https://portal.azure.com/
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni sulle app per dispositivi mobili]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /it-IT/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /it-IT/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Abilitare la registrazione diagnostica nel servizio app di Azure]: web-sites-enable-diagnostic-log.md
[prezzi del servizio app]: https://azure.microsoft.com/it-IT/pricing/details/app-service/
[ambiente del servizio app]: app-service-app-service-environment-intro.md
[Confronto tra Servizi mobili e il servizio app]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[eseguire la migrazione di un servizio mobile in un'app per dispositivi mobili nel servizio app]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO3-->