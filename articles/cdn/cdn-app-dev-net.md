<properties
	pageTitle="Introduzione ad Azure CDN Library per .NET | Microsoft Azure"
	description="Informazioni su come scrivere applicazioni .NET per la gestione della rete CDN di Azure tramite Visual Studio."
	services="cdn"
	documentationCenter=".net"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="casoper"/>

# Introduzione alla libreria CDN di Azure per .NET

È possibile usare la [libreria CDN di Azure per .NET](https://msdn.microsoft.com/library/mt657769.aspx) per automatizzare la creazione e la gestione dei profili e degli endpoint di una rete CDN. Questa esercitazione illustra in dettaglio la creazione di una semplice applicazione console .NET che dimostra varie operazioni disponibili. Lo scopo di questa esercitazione non è descrivere dettagliatamente tutti gli aspetti della libreria CDN di Azure per .NET.

Per completare questa esercitazione è necessario disporre di Visual Studio 2015. [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) è disponibile gratuitamente per il download.

Un esempio completo di questa esercitazione è disponibile [qui](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

## Operazioni preliminari

Prima di poter scrivere il codice di gestione della rete CDN, è necessaria un po' di preparazione. La prima cosa da fare è creare un gruppo di risorse per includere il profilo di rete CDN che verrà creato in questa esercitazione. Quindi si configurerà Azure Active Directory per fornire l'autenticazione per l'applicazione. Una volta eseguita questa operazione, si applicheranno le autorizzazioni al gruppo di risorse in modo che solo gli utenti autorizzati dal tenant di Azure AD possano interagire con il profilo di rete CDN.

### Creazione del gruppo di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic sul pulsante **Nuovo** in alto a sinistra e poi su **Gestione** e **Gruppo di risorse**.
	
	![Creazione di un nuovo gruppo di risorse](./media/cdn-app-dev-net/cdn-new-rg-1.png)

3. Assegnare al gruppo di risorse il nome *CdnConsoleTutorial*. Selezionare la sottoscrizione e scegliere un percorso locale. Se si desidera, è possibile selezionare la casella di controllo **Aggiungi al dashboard** per aggiungere il gruppo di risorse al dashboard del portale. In questo modo sarà più semplice trovarlo in un secondo momento. Dopo avere eseguito le selezioni, fare clic su **Crea**.

	![Assegnazione di un nome al gruppo di risorse](./media/cdn-app-dev-net/cdn-new-rg-2.png)

4. Dopo aver creato il gruppo di risorse, se non lo si è aggiunto al dashboard, è possibile trovarlo facendo clic su **Sfoglia** e poi su **Gruppi di risorse**. Fare clic sul gruppo di risorse per aprirlo. Annotare l'**ID sottoscrizione**. Sarà necessario più avanti.

	 ![Assegnazione di un nome al gruppo di risorse](./media/cdn-app-dev-net/cdn-subscription-id.png)

### Creazione dell'applicazione Azure AD

Per l'autenticazione delle app con Azure Active Directory sono possibili due approcci: singoli utenti o un'entità servizio. Un'entità servizio è simile a un account di servizio di Windows. Anziché concedere a un determinato utente le autorizzazioni per interagire con i profili di rete CDN, si concedono le autorizzazioni per l'entità servizio. Le entità servizio in genere vengono usate per i processi automatizzati e non interattivi. Sebbene in questa esercitazione si scriva un'applicazione console interattiva, ci si concentrerà sull'approccio dell'entità servizio.

La creazione di un'entità servizio è costituita da diversi passaggi, compresa la creazione di un'applicazione Azure Active Directory. A tale scopo si [eseguirà questa esercitazione](../resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Assicurarsi di completare tutti i passaggi dell'[esercitazione collegata](../resource-group-create-service-principal-portal.md). È *estremamente importante* eseguirla esattamente come descritto. Assicurarsi di annotare l'**ID tenant**, il **nome di dominio tenant** (in genere un dominio *. onmicrosoft.com*, se non è stato specificato un dominio personalizzato), l'**ID client** e la **chiave di autenticazione client**, che serviranno più avanti. Prestare attenzione all'**ID client** e alla **chiave di autenticazione client**, in quanto queste credenziali possono essere usate da chiunque per eseguire operazioni come entità servizio.
> 	
> Quando si arriva al passaggio denominato [Configura applicazione multi-tenant](../resource-group-create-service-principal-portal.md#configure-multi-tenant-application), selezionare **No**.
> 
> Quando si arriva al passaggio [Assegnare l'applicazione al ruolo](../resource-group-create-service-principal-portal.md#assign-application-to-role), usare il gruppo di risorse creato in precedenza, *CdnConsoleTutorial*, ma anziché il ruolo di **lettore**, assegnare il ruolo di **collaboratore profilo di rete CDN**. Dopo aver assegnato all'applicazione il ruolo di **collaboratore profilo di rete CDN** nel gruppo di risorse, tornare a questa esercitazione.

Dopo aver creato l'entità servizio e assegnato il ruolo di **collaboratore profilo di rete CDN**, il pannello **Utenti** per il gruppo di risorse dovrebbe essere simile a questo.

![Pannello Utenti](./media/cdn-app-dev-net/cdn-service-principal.png)


### Autenticazione utente interattiva

Se, invece di un'entità servizio, si preferisce l'autenticazione interattiva del singolo utente, il processo è molto simile a quello per un'entità servizio. Infatti, è necessario seguire la stessa procedura, a parte alcune piccole modifiche.

>[AZURE.IMPORTANT] Seguire questa procedura solo se si sceglie di utilizzare l'autenticazione interattiva del singolo utente anziché un'entità servizio.

1. Quando si crea l'applicazione, invece di **App Web**, scegliere **Applicazione nativa**. 
	
	![Applicazione nativa](./media/cdn-app-dev-net/cdn-native-application.png)
	
2. Nella pagina successiva verrà richiesto di immettere un **URI di reindirizzamento**. L'URI non verrà convalidato, ma è necessario ricordare i valori immessi. Sarà necessario più avanti.

3. Non è necessario creare una **chiave di autenticazione client**.

4. Invece di assegnare un'entità servizio al ruolo di **collaboratore profilo di rete CDN**, assegneremo singoli utenti o gruppi. In questo esempio si può notare che è stato assegnato l'*utente CDN Demo* al ruolo di **collaboratore profilo di rete CDN**.
	
	![Accesso del singolo utente](./media/cdn-app-dev-net/cdn-aad-user.png)


## Creare il progetto e aggiungere i pacchetti Nuget

Ora che abbiamo creato un gruppo di risorse per i profili di rete CDN e assegnato all'applicazione Azure AD l'autorizzazione per gestire i profili e gli endpoint della rete CDN all'interno del gruppo, è possibile iniziare a creare l'applicazione.

Da Visual Studio 2015 fare clic su **File**, **Nuovo**, **Progetto...** per aprire la finestra di dialogo Nuovo progetto. Espandere **Visual C#** e selezionare **Windows** nel riquadro a sinistra. Fare clic su **Applicazione console** nel riquadro centrale. Assegnare un nome al progetto e fare clic su **OK**.

![Nuovo progetto](./media/cdn-app-dev-net/cdn-new-project.png)

Il progetto userà alcune librerie di Azure contenute nei pacchetti Nuget. Ora si aggiungeranno al progetto.

1. Fare clic sul menu **Strumenti**, **Gestione pacchetti NuGet ** e poi su **Console di gestione pacchetti**.

	![Gestisci pacchetti NuGet](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. Nella Console di gestione pacchetti eseguire il comando seguente per installare **Active Directory Authentication Library (ADAL)**:

	`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Eseguire il codice seguente per installare **Azure CDN Management Library**:

	`Install-Package Microsoft.Azure.Management.Cdn`

## Direttive, costanti, metodo main e metodi helper

Ora si scriverà la struttura di base del programma.

1. Nella scheda Program.cs sostituire la direttiva `using` all'inizio con il codice seguente:

	```
	using System;
	using System.Collections.Generic;
	using Microsoft.Azure.Management.Cdn;
	using Microsoft.Azure.Management.Cdn.Models;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Rest;
	```

2. È necessario definire alcune costanti che i metodi useranno. Nella classe `Program`, ma prima del metodo `Main`, aggiungere il codice seguente. Sostituire i segnaposti, inclusi **&lt;angle brackets&gt;**, con i valori desiderati.

	```
	//Tenant app constants
	private const string clientID = "<YOUR CLIENT ID>";
	private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
	private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

	//Application constants
	private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
	private const string profileName = "CdnConsoleApp";
	private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
	private const string resourceGroupName = "CdnConsoleTutorial";
	private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
	```

3. Sempre a livello della classe, definire queste due variabili. Saranno usate in un secondo momento per determinare se il profilo e l'endpoint esistono già.

	```
	static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
	```

4.  Sostituire il metodo `Main` come illustrato di seguito:

	```
	static void Main(string[] args)
	{
		//Get a token
		AuthenticationResult authResult = GetAccessToken();

		// Create CDN client
		CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
			{ SubscriptionId = subscriptionId };

		ListProfilesAndEndpoints(cdn);

		// Create CDN Profile
		CreateCdnProfile(cdn);

		// Create CDN Endpoint
		CreateCdnEndpoint(cdn);
		
		Console.WriteLine();

		// Purge CDN Endpoint
		PromptPurgeCdnEndpoint(cdn);

		// Delete CDN Endpoint
		PromptDeleteCdnEndpoint(cdn);

		// Delete CDN Profile
		PromptDeleteCdnProfile(cdn);

		Console.WriteLine("Press Enter to end program.");
		Console.ReadLine();
	}
	```

5. Altri metodi che si useranno presenteranno all'utente domande "Sì/No". Aggiungere il metodo seguente per facilitare l'operazione:

	```
	private static bool PromptUser(string Question)
	{
		Console.Write(Question + " (Y/N): ");
		var response = Console.ReadKey();
		Console.WriteLine();
		if (response.Key == ConsoleKey.Y)
		{
			return true;
		}
		else if (response.Key == ConsoleKey.N)
		{
			return false;
		}
		else
		{
			// They're not pressing Y or N.  Let's ask them again.
			return PromptUser(Question);
		}
	}
	```

Ora che la struttura di base del programma è stata scritta, è necessario creare i metodi chiamati dal metodo `Main`.

## Autenticazione

Per poter usare Azure CDN Management Library, è necessario autenticare l'entità servizio e ottenere un token di autenticazione. Questo metodo usa ADAL per recuperare il token.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority); 
	ClientCredential credential = new ClientCredential(clientID, clientSecret);
	AuthenticationResult authResult = 
		authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

	return authResult;
}
```

Se si usa l'autenticazione del singolo utente, il metodo `GetAccessToken` avrà un aspetto leggermente diverso.

>[AZURE.IMPORTANT] Usare questo esempio di codice solo se si sceglie l'autenticazione interattiva del singolo utente anziché un'entità servizio.

```
private static AuthenticationResult GetAccessToken()
{
	AuthenticationContext authContext = new AuthenticationContext(authority);
	AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
		clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

	return authResult;
}
```

Assicurarsi di sostituire `<redirect URI>` con l'URI di reindirizzamento inserito al momento della registrazione dell'applicazione in Azure AD.

## Elencare i profili e gli endpoint della rete CDN

Ora tutto è pronto per eseguire le operazioni della rete CDN. La prima cosa che il metodo fa è elencare tutti i profili e gli endpoint nel gruppo di risorse e, se trova una corrispondenza per i nomi di profilo e di endpoint specificati nelle costanti, ne tiene conto per un momento successivo in modo da evitare tentativi di creazione di duplicati.

```
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
	// List all the CDN profiles in this resource group
	var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
	foreach (Profile p in profileList)
	{
		Console.WriteLine("CDN profile {0}", p.Name);
		if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
		{
			// Hey, that's the name of the CDN profile we want to create!
			profileAlreadyExists = true;
		}

		//List all the CDN endpoints on this CDN profile
		Console.WriteLine("Endpoints:");
		var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
		foreach (Endpoint e in endpointList)
		{
			Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
			if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
			{
				// The unique endpoint name already exists.
				endpointAlreadyExists = true;
			}
		}
		Console.WriteLine();
	}
}
```

## Creare profili ed endpoint della rete CDN

Ora si creerà un profilo.

```
private static void CreateCdnProfile(CdnManagementClient cdn)
{
	if (profileAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating profile {0}.", profileName);
		ProfileCreateParameters profileParms =
			new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
		cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
	}
}
```

Dopo il profilo, verrà creato un endpoint.

```
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
	if (endpointAlreadyExists)
	{
		Console.WriteLine("Profile {0} already exists.", profileName);
	}
	else
	{
		Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
		EndpointCreateParameters endpointParms =
			new EndpointCreateParameters()
			{
				Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
				IsHttpAllowed = true,
				IsHttpsAllowed = true,
				Location = resourceLocation
			};
		cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
	}
}
```

>[AZURE.NOTE] Nell'esempio precedente viene assegnata all'endpoint un'origine denominata *Contoso* con il nome host `www.contoso.com`. È necessario modificarlo in modo che punti al nome host dell'origine personalizzata.

## Ripulire un endpoint

Supponendo che l'endpoint sia stato creato, un'attività comune che potrebbe essere necessario eseguire nel programma è ripulire il contenuto dell'endpoint.

```
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
	if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
	{
		Console.WriteLine("Purging endpoint. Please wait...");
		cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

>[AZURE.NOTE] Nell'esempio precedente la stringa `/*` indica che si desidera ripulire tutto il contenuto della radice del percorso dell'endpoint. Ciò equivale a selezionare **Ripulisci tutto** nella finestra di dialogo "Ripulisci" del portale di Azure. Nel metodo `CreateCdnProfile` è stato creato il profilo come profilo **Azure CDN di Verizon** usando il codice `Sku = new Sku(SkuName.StandardVerizon)`, perciò l'operazione riuscirà. Tuttavia, i profili di **rete CDN Azure di Akamai** non supportano **Ripulisci tutto**, perciò se si usasse un profilo Akamai per questa esercitazione, si dovrebbero includere percorsi specifici da ripulire.

## Eliminare profili ed endpoint della rete CDN

Gli ultimi metodi che saranno inclusi elimineranno l'endpoint e il profilo.

```
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
	{
		Console.WriteLine("Deleting endpoint. Please wait...");
		cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
	if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
	{
		Console.WriteLine("Deleting profile. Please wait...");
		cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
		Console.WriteLine("Done.");
		Console.WriteLine();
	}
}
```

## Esecuzione del programma

È ora possibile compilare ed eseguire il programma facendo clic sul pulsante **Avvia** in Visual Studio.

![Il programma in esecuzione](./media/cdn-app-dev-net/cdn-program-running-1.png)

Quando il programma raggiunge la richiesta precedente, sarà possibile ritornare al gruppo di risorse nel portale di Azure e verificare che il profilo è stato creato.

![Completamento della procedura](./media/cdn-app-dev-net/cdn-success.png)

È quindi possibile confermare le richieste per eseguire il resto del programma.

![Il completamento del programma](./media/cdn-app-dev-net/cdn-program-running-2.png)

## Passaggi successivi

Per vedere il progetto completato di questa procedura dettagliata [scaricare l'esempio](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Per altra documentazione su Azure CDN Management Library per .NET, [consultare MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

<!---HONumber=AcomDC_0615_2016-->