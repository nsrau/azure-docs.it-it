<properties 
	pageTitle="Connessione a un account di Servizi multimediali mediante .NET" 
	description="Questo argomento illustra come connettersi a Servizi multimediali mediante .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
  ms.date="02/03/2016"
	ms.author="juliako"/>


# Connessione a un account di Servizi multimediali mediante l'SDK di Servizi multimediali per .NET

> [AZURE.SELECTOR]
- [REST](media-services-rest-connect-programmatically.md)
- [.NET](media-services-dotnet-connect_programmatically.md)


Questo argomento descrive come ottenere una connessione a Servizi multimediali di Microsoft Azure a livello di codice quando si programma con l'SDK di Servizi multimediali per .NET.


## Connessione a Servizi multimediali

Prima di connettersi a Servizi multimediali a livello di codice, è necessario impostare un account Azure, configurare Servizi multimediali su tale account e quindi definire un progetto di Visual Studio per lo sviluppo con l'SDK di Servizi multimediali per .NET. Per altre informazioni, vedere Configurazione per lo sviluppo con l'SDK di Servizi multimediali per .NET.

Al termine del processo di configurazione dell'account di Servizi multimediali, l'utente ottiene i seguenti valori di connessione obbligatori, che può usare per configurare connessioni a Servizi multimediali a livello di codice.

- Nome dell'account di Servizi multimediali

- Chiave dell'account di Servizi multimediali

Per trovare questi valori, passare al portale di gestione di Azure, selezionare l'account di Servizi multimediali e fare clic sull'icona "**GESTISCI CHIAVI**" nella parte inferiore della finestra del portale. Facendo clic sull'icona accanto a ciascuna casella di testo, il valore viene copiato negli Appunti di sistema.


## Creazione di un'istanza di CloudMediaContext

Prima di iniziare la programmazione basata su Servizi multimediali, è necessario creare un'istanza di **CloudMediaContext** che rappresenta il contesto del server. **CloudMediaContext** contiene riferimenti a raccolte importanti composte da processi, asset, file, criteri di accesso e localizzatori.

>[AZURE.NOTE] La classe **CloudMediaContext** non è di tipo thread-safe. È quindi necessario creare una nuova istanza di CloudMediaContext per ogni thread o set di operazioni.


CloudMediaContext include cinque overload del costruttore. Si consiglia di usare costruttori che accettano **MediaServicesCredentials** come parametro. Per altre informazioni, vedere la sezione **Riutilizzo dei token del Servizio di controllo di accesso** seguente.

Nel seguente esempio viene usato il costruttore pubblico CloudMediaContext(MediaServicesCredentials credentials):

	// _cachedCredentials and _context are class member variables. 
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	_context = new CloudMediaContext(_cachedCredentials);


## Riutilizzo dei token del Servizio di controllo di accesso

Questa sezione mostra come riutilizzare i token del Servizio di controllo di accesso mediante i costruttori CloudMediaContext che accettano MediaServicesCredentials come parametro.


[Azure Active Directory Access Control](https://msdn.microsoft.com/library/hh147631.aspx) (noto anche come Servizio di controllo di accesso o ACS) è un servizio basato sul cloud che offre un modo semplice per autenticare e autorizzare gli utenti, in modo che possano accedere alle applicazioni Web. Servizi multimediali di Microsoft Azure controlla l'accesso ai servizi tramite il protocollo OAuth che richiede un token ACS. Servizi multimediali riceve i token ACS da un server di autorizzazione.

Quando si sviluppa usando l'SDK di Servizi multimediali, è possibile scegliere di non gestire i token, poiché questi vengono gestiti automaticamente dal codice dell'SDK. Tuttavia, se si lascia all'SDK la gestione completa dei token ACS, possono verificarsi richieste di token non necessarie. Le richieste di token richiedono tempo e utilizzano le risorse di client e server. Inoltre, il server ACS limita le richieste in caso di velocità eccessiva. Il limite è di 30 richieste al secondo. Per informazioni dettagliate, vedere [Limitazioni del servizio ACS](https://msdn.microsoft.com/library/gg185909.aspx).

A partire dalla versione 3.0.0.0 dell'SDK di Servizi multimediali, è possibile riutilizzare i token ACS. I costruttori **CloudMediaContext** che accettano **MediaServicesCredentials** come parametro consentono di condividere i token ACS tra più contesti. La classe MediaServicesCredentials incapsula le credenziali di Servizi multimediali. Se è disponibile un token e la data di scadenza è nota, è possibile creare una nuova istanza di MediaServicesCredentials con il token e passarla al costruttore di CloudMediaContext. Si noti che l'SDK di Servizi multimediali aggiorna automaticamente i token ogni volta che scadono. Per riutilizzare i token ACS sono disponibili i due modi illustrati nei seguenti esempi.

- È possibile memorizzare l'oggetto **MediaServicesCredentials** nella cache, ad esempio in una variabile di classe statica, e quindi passare l'oggetto memorizzato nella cache al costruttore CloudMediaContext. L'oggetto MediaServicesCredentials contiene un token ACS che, se ancora valido, può essere riutilizzato. Se il token non è più valido, verrà aggiornato dall'SDK di Servizi multimediali usando le credenziali fornite al costruttore MediaServicesCredentials.

	Si noti che l'oggetto **MediaServicesCredentials** ottiene un token valido dopo la chiamata a RefreshToken. **CloudMediaContext** chiama il metodo **RefreshToken** nel costruttore. Se si prevede di salvare i valori dei token in una risorsa di archiviazione esterna, assicurarsi che il valore di TokenExpiration sia ancora valido prima di salvare i dati del token. Se non è valido, chiamare RefreshToken prima di procedere alla memorizzazione nella cache.

		// Create and cache the Media Services credentials in a static class variable.
		_cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

		
		// Use the cached credentials to create a new CloudMediaContext object.
		if(_cachedCredentials == null)
		{
		    _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
		}
		
		CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- È anche possibile memorizzare nella cache la stringa AccessToken e i valori TokenExpiration. I valori possono essere usati successivamente per creare un nuovo oggetto MediaServicesCredentials con i dati del token memorizzati nella cache. Ciò è particolarmente utile in scenari in cui il token può essere condiviso in modo sicuro tra più processi o computer.

	I seguenti frammenti di codice chiamano i metodi SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage e UpdateTokenDataInExternalStorageIfNeeded che non sono definiti in questo esempio. È possibile definire questi metodi per archiviare, recuperare e aggiornare i dati del token in una risorsa di archiviazione esterna.

		CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
		
		// Get token values from the context.
		var accessToken = context1.Credentials.AccessToken;
		var tokenExpiration = context1.Credentials.TokenExpiration;
		
		// Save token values for later use. 
		// The SaveTokenDataToExternalStorage method should check 
		// whether the TokenExpiration value is valid before saving the token data. 
		// If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
		SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
		
	Usare i valori del token per creare MediaServicesCredentials.


		var accessToken = "";
		var tokenExpiration = DateTime.UtcNow;
		
		// Retrieve saved token values.
		GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
		
		// Create a new MediaServicesCredentials object using saved token values.
		MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
		{
		    AccessToken = accessToken,
		    TokenExpiration = tokenExpiration
		};
		
		CloudMediaContext context2 = new CloudMediaContext(credentials);

	Aggiornare la copia del token, nel caso in cui il token sia stato aggiornato dall'SDK di Servizi multimediali.
	
		if(tokenExpiration != context2.Credentials.TokenExpiration)
		{
		    UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
		}
		

- Se sono presenti più account di Servizi multimediali, ad esempio per la condivisione del carico o la distribuzione geografica, è possibile memorizzare nella cache gli oggetti MediaServicesCredentials tramite la raccolta System.Collections.Concurrent.ConcurrentDictionary, una raccolta thread-safe di coppie chiave/valore a cui possono accedere simultaneamente più thread. È quindi possibile usare il metodo GetOrAdd per ottenere le credenziali memorizzate nella cache.

		// Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
		private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
		    new ConcurrentDictionary<string, MediaServicesCredentials>();
		

		// Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
		static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
		{
		    CloudMediaContext cloudMediaContext;
		    MediaServicesCredentials mediaServicesCredentials;
		
		    mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
		        accountName,
		        valueFactory => new MediaServicesCredentials(accountName, accountKey));
		
		    cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
		
		    return cloudMediaContext;
		}
		
## Connessione a un account di Servizi multimediali in Cina settentrionale

Se il proprio account si trova in Cine settentrionale, usare il seguente costruttore:

	public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Ad esempio:


	_context = new CloudMediaContext(
	    new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
	    _mediaServicesAccountName,
	    _mediaServicesAccountKey,
	    "urn:WindowsAzureMediaServices",
	    "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## Archiviazione dei valori di connessione nella configurazione

È consigliabile archiviare i valori di connessione, in particolare quelli sensibili come nome account e password, all'interno della configurazione, e anche crittografare i dati di configurazione sensibili. È possibile crittografare l'intero file di configurazione tramite il sistema EFS (Encrypting File System) di Windows. Per abilitare il sistema EFS per un file, fare clic con il pulsante destro del mouse sul file, scegliere **Proprietà** e abilitare la crittografia nella scheda delle impostazioni **Avanzate**. In alternativa, è possibile creare una soluzione personalizzata per crittografare parti selezionate di un file di configurazione tramite la configurazione protetta. Vedere l'argomento relativo alla [crittografia delle informazioni di configurazione usando la configurazione protetta](https://msdn.microsoft.com/library/53tyfkaw.aspx).

Il file App.config contiene i valori di configurazione necessari. I valori nell'elemento <appSettings> sono i valori necessari ottenuti durante il processo di configurazione dell'account di Servizi multimediali.

	<configuration>
	  <appSettings>
	    <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
	    <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
	  </appSettings>
	</configuration>


Per recuperare i valori di connessione dalla configurazione, è possibile usare la classe **ConfigurationManager** e quindi assegnare i valori ai campi nel codice:
	
	private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];



##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0413_2016-->