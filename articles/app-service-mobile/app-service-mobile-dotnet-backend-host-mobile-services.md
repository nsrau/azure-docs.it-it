<properties
	pageTitle="Ospitare un progetto di Servizi mobili nel servizio app | Microsoft Azure"
	description="Informazioni su come eseguire un progetto di Servizi mobili nel servizio app."
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="10/08/2015"
	ms.author="mahender"/>

# Come ospitare un progetto di Servizi mobili .NET nel servizio app

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento illustra come ospitare un progetto di Servizi mobili .NET nel servizio app di Azure. Le app eseguite in questo modo consentono l'accesso a tutte le funzionalità del servizio app e vengono fatturate in base ai [prezzi del servizio app], non ai prezzi di Servizi mobili.

Un'app ospitata in questo modo può usare tutte le esercitazioni di runtime per Servizi mobili .NET, anche se è necessario sostituire eventuali URL che usano il dominio azure-mobile.net con il dominio dell'istanza del servizio app.

Un progetto di Servizi mobili può essere ospitato anche in un [ambiente del servizio app]. Tutti i contenuti di questo argomento sono comunque applicabili, ma il sito avrà formato contoso.contosoase.p.azurewebsites.net invece di contoso.azurewebsites.net.

## <a name="app-settings"></a>Vantaggi dell'hosting nel servizio app

Il servizio app fornisce un ambiente più ricco di funzionalità per l'applicazione. L'hosting nel servizio app offre al servizio l'accesso a slot di staging, domini personalizzati, supporto per Gestione traffico e altro ancora. Anche se è possibile [eseguire la migrazione di un servizio mobile in un'app per dispositivi mobili nel servizio app], è possibile che alcuni clienti vogliano sfruttare immediatamente i vantaggi offerti da queste funzionalità, senza eseguire ancora l'aggiornamento SDK.

La limitazione principale dell'hosting nel servizio app consiste nel fatto che i processi pianificati di Servizi mobili non sono integrati ed è quindi necessario configurare l'Utilità di pianificazione di Azure in modo che raggiunga le API personalizzate oppure abilitare le funzionalità dei processi Web.

Per altre informazioni sui vantaggi del servizio app, vedere [Confronto tra Servizi mobili e il servizio app].

## <a name="app-settings"></a>Impostazioni dell'applicazione
Servizi mobili richiede la disponibilità di alcune impostazioni dell'applicazione nell'ambiente. Questa sezione illustrerà nel dettaglio l'aggiunta di ogni impostazione. Per configurare le impostazioni dell'applicazione nell'app Web, è prima di tutto necessario accedere al [portale di gestione di Azure (anteprima)]. Passare all'app Web, per dispositivi mobili o per le API da usare, quindi selezionare "Impostazioni" e infine "Impostazioni applicazione". Scorrere verso il basso fino alla sezione intitolata "Impostazioni app", in cui è possibile configurare le coppie chiave-valore necessarie.
 
**MS\_MobileServiceName** deve corrispondere al nome dell'app. Ad esempio, se si prevede di eseguire un'app con URL contoso.azurewebsites.net, il valore corretto sarà "contoso".
 
**MS\_MobileServiceDomainSuffix** deve corrispondere al nome dell'app Web. Ad esempio, se si prevede di eseguire un'app con URL contoso.azurewebsites.net, il valore corretto sarà "azurewebsites.net".
 
**MS\_ApplicationKey** può corrispondere a qualsiasi valore, ma deve essere uguale al valore usato dall'SDK del client. È consigliabile usare un GUID.
 
**MS\_MasterKey** può corrispondere a qualsiasi valore, ma deve essere uguale al valore usato da qualsiasi API/client di amministrazione. È consigliabile usare un GUID.
 
Se si passa a un'applicazione esistente di Servizi mobili, sarà possibile ottenere sia la chiave master che la chiave applicazione dalla scheda "Configura" della sezione Servizi mobili del[portale di gestione di Azure]. Selezionare l'azione "Gestisci chiavi" in basso e copiare le chiavi.


## <a name="client-sdk"></a>Procedura: Modificare l'SDK del client

Nel progetto dell'app client modificare il costruttore dell'oggetto client di Servizi mobili in modo che accetti l'URL dell'app, ad esempio contoso.azurewebsites.net, e la chiave applicazione configurata in precedenza. La versione dell'SDK del client deve essere una versione di Servizi mobili e **NON** deve essere aggiornata. Per client iOS e Android usare le versioni 2.x e per Windows/Xamarin usare la versione 1.3.2. I client Javascript devono usare la versione 1.2.7.

## <a name="data"></a>Procedura: Abilitare le funzionalità dei dati

Per usare le classi predefinite di Entity Framework in Servizi mobili, sono necessarie altre due impostazioni delle app.
 
Le stringhe di connessione vengono archiviate nella sezione "Stringhe di connessione" del pannello Impostazioni applicazione, immediatamente sotto la sezione "Impostazioni app". La stringa di connessione per il database deve essere configurata nella chiave **MS\_TableConnectionString**. Per spostare un'applicazione esistente di Servizi mobili, passare alla sezione "Stringhe di connessione" della scheda Configura del portale di Servizi mobili. Fare clic su "Mostra stringhe di connessione" e copiare il valore.
 
Per impostazione predefinita, lo schema da usare è **MS\_MobileServiceName**, ma può essere sovrascritto con l'impostazione **MS\_TableSchema**. In "Impostazioni app" impostare **MS\_TableSchema** come nome dello schema da usare. Se si sposta un'applicazione esistente di Servizi mobili, è già stato creato uno schema mediante Entity Framework. Si tratta del nome del servizio mobile, non dell'istanza del servizio app che ospiterà il codice.

## <a name="push"></a>Procedura: Abilitare le funzionalità push

Per consentire il funzionamento delle notifiche push, l'app Web deve avere anche informazioni sull'hub di notifica.
 
In "Stringhe di connessione" impostare **MS\_NotificationHubConnectionString** sulla stringa di connessione DefaultFullSharedAccessSignature per l'hub di notifica. Per spostare un'applicazione esistente di Servizi mobili, passare alla sezione "Stringhe di connessione" della scheda Configura del portale di Servizi mobili. Fare clic su "Mostra stringhe di connessione" e copiare il valore.

Per l'impostazione dell'app **MS\_NotificationHubName** specificare il nome dell'hub. Quando si sposta un'app esistente di Servizi mobili, è possibile ottenere questo valore dalla scheda relativa alle notifiche push nel portale di Servizi mobili. Gli altri campi di questa scheda sono collegati all'hub stesso e non devono essere copiati altrove.
 
## <a name="auth"></a>Procedura: Abilitare le funzionalità relative alle autorizzazioni

Anche le funzionalità relative all'identità hanno requisiti per le impostazioni delle app per i singoli provider. Se si esegue lo spostamento da un'app esistente di Servizi mobili, ogni campo della scheda Identità del portale di Servizi mobili include un'impostazione dell'app corrispondente.
 
Account Microsoft: * **MS\_MicrosoftClientID** * **MS\_MicrosoftClientSecret** * **MS\_MicrosoftPackageSID**
 
Facebook: * **MS\_FacebookAppID** * **MS\_FacebookAppSecret**
 
Twitter * **MS\_TwitterConsumerKey** * **MS\_TwitterConsumerSecret**
 
Google * **MS\_GoogleClientID** * **MS\_GoogleClientSecret**
 
AAD * **MS\_AadClientID** * **MS\_AadTenants** - Nota: l'impostazione **MS\_AadTenants** viene archiviata come elenco delimitato da virgole dei domini del tenant, corrispondente al campo "Tenant consentiti" del portale di Servizi mobili.

## <a name="publish"></a>Procedura: Pubblicare il progetto di Servizi mobili

1. Nel portale di anteprima passare all'app e selezionare "Recupera profilo" dalla barra dei comandi. Salvare il profilo scaricato nel computer locale.
2. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server di Servizi mobili, quindi scegliere "Pubblica". Nella scheda del profilo scegliere "Importa" e passare al profilo scaricato.
3. Fare clic su Pubblica per distribuire il codice nel servizio app.

I log vengono gestiti tramite le funzionalità di registrazione standard del servizio app. Per altre informazioni sulla registrazione, vedere [Abilitare la registrazione diagnostica nel servizio app di Azure].

<!-- URLs. -->

[portale di gestione di Azure (anteprima)]: https://portal.azure.com/
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Abilitare la registrazione diagnostica nel servizio app di Azure]: web-sites-enable-diagnostic-log.md
[prezzi del servizio app]: https://azure.microsoft.com/it-IT/pricing/details/app-service/
[ambiente del servizio app]: app-service-app-service-environment-intro.md
[Confronto tra Servizi mobili e il servizio app]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[eseguire la migrazione di un servizio mobile in un'app per dispositivi mobili nel servizio app]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO1-->