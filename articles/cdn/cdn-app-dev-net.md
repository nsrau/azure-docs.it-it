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
	ms.date="07/28/2016"
	ms.author="casoper"/>

# Introduzione allo sviluppo della rete CDN di Azure

> [AZURE.SELECTOR]
- [.NET](cdn-app-dev-net.md)
- [Node.JS](cdn-app-dev-node.md)

È possibile usare la [libreria CDN di Azure per .NET](https://msdn.microsoft.com/library/mt657769.aspx) per automatizzare la creazione e la gestione di profili ed endpoint di una rete CDN. Questa esercitazione illustra in dettaglio la creazione di una semplice applicazione console .NET che dimostra varie operazioni disponibili. Lo scopo di questa esercitazione non è descrivere dettagliatamente tutti gli aspetti della libreria CDN di Azure per .NET.

Per completare questa esercitazione, è necessario Visual Studio 2015. [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) è disponibile gratuitamente per il download.

> [AZURE.TIP] Il [progetto completato di questa esercitazione](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) è disponibile per il download in MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## Creare il progetto e aggiungere i pacchetti Nuget

Ora che abbiamo creato un gruppo di risorse per i profili di rete CDN e assegnato all'applicazione Azure AD l'autorizzazione per gestire i profili e gli endpoint della rete CDN all'interno del gruppo, è possibile iniziare a creare l'applicazione.

Da Visual Studio 2015 fare clic su **File**, **Nuovo**, **Progetto** per aprire la finestra di dialogo del nuovo progetto. Espandere **Visual C#** e selezionare **Windows** nel riquadro a sinistra. Fare clic su **Applicazione console** nel riquadro centrale. Assegnare un nome al progetto e fare clic su **OK**.

![Nuovo progetto](./media/cdn-app-dev-net/cdn-new-project.png)

Il progetto userà alcune librerie di Azure contenute nei pacchetti Nuget. Ora si aggiungeranno al progetto.

1. Fare clic sul menu **Strumenti**, **Gestione pacchetti NuGet** e poi su **Console di Gestione pacchetti**.

	![Gestisci pacchetti NuGet](./media/cdn-app-dev-net/cdn-manage-nuget.png)

2. Nella Console di Gestione pacchetti eseguire il comando seguente per installare **Active Directory Authentication Library (ADAL)**:

	`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`

3. Eseguire il codice seguente per installare **Azure CDN Management Library**:

	`Install-Package Microsoft.Azure.Management.Cdn`

## Direttive, costanti, metodo main e metodi helper

Ora si scriverà la struttura di base del programma.

1. Nella scheda Program.cs sostituire la direttiva `using` all'inizio con il codice seguente:

	```csharp
	using System;
	using System.Collections.Generic;
	using Microsoft.Azure.Management.Cdn;
	using Microsoft.Azure.Management.Cdn.Models;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Rest;
	```

2. È necessario definire alcune costanti che i metodi useranno. Nella classe `Program`, ma prima del metodo `Main`, aggiungere il codice seguente. Sostituire i segnaposto, incluse le **&lt;parentesi acute&gt;**, con i valori necessari.

	```csharp
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

	```csharp
	static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
	```

4.  Sostituire il metodo `Main` come illustrato di seguito:

	```csharp
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

	```csharp
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
			// They pressed something other than Y or N.  Let's ask them again.
			return PromptUser(Question);
		}
	}
	```

Ora che la struttura di base del programma è stata scritta, è necessario creare i metodi chiamati dal metodo `Main`.

## Autenticazione

Per poter usare Azure CDN Management Library, è necessario autenticare l'entità servizio e ottenere un token di autenticazione. Questo metodo usa ADAL per recuperare il token.

```csharp
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

```csharp
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

```csharp
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

```csharp
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

```csharp
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

>[AZURE.NOTE] L'esempio precedente assegna all'endpoint un'origine denominata *Contoso* con il nome host `www.contoso.com`. È necessario modificarlo in modo che punti al nome host dell'origine personalizzata.

## Ripulire un endpoint

Supponendo che l'endpoint sia stato creato, un'attività comune che potrebbe essere necessario eseguire nel programma è ripulire il contenuto dell'endpoint.

```csharp
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

>[AZURE.NOTE] Nell'esempio precedente la stringa `/*` indica che si vuole ripulire tutto il contenuto della radice del percorso dell'endpoint. Ciò equivale a selezionare **Elimina tutti** nella finestra di dialogo "Elimina" del portale di Azure. Nel metodo `CreateCdnProfile` è stato creato il profilo come profilo **Rete CDN di Azure da Verizon** usando il codice `Sku = new Sku(SkuName.StandardVerizon)`, perciò l'operazione riuscirà. I profili **Rete CDN Azure da Akamai** tuttavia non supportano **Elimina tutti**, perciò se si usasse un profilo Akamai per questa esercitazione, si dovrebbero includere percorsi specifici da ripulire.

## Eliminare profili ed endpoint della rete CDN

Gli ultimi metodi consentiranno di eliminare l'endpoint e il profilo.

```csharp
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

Quando il programma raggiunge la richiesta precedente, sarà possibile ritornare al gruppo di risorse nel portale di Azure e verificare che il profilo sia stato creato.

![Completamento della procedura](./media/cdn-app-dev-net/cdn-success.png)

È quindi possibile confermare le richieste per eseguire il resto del programma.

![Il completamento del programma](./media/cdn-app-dev-net/cdn-program-running-2.png)

## Passaggi successivi

Per vedere il progetto completato di questa procedura dettagliata, [scaricare l'esempio](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Per altra documentazione su Azure CDN Management Library per .NET, vedere i [riferimenti su MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Gestire le risorse della rete CDN con [PowerShell](./cdn-manage-powershell.md).

<!---HONumber=AcomDC_0817_2016-->