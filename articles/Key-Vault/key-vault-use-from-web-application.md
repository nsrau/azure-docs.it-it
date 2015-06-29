\<properties pageTitle="Usare l'insieme di credenziali chiave di Azure da un'applicazione Web \| Panoramica" description="In questa esercitazione si apprenderà a usare l'insieme di credenziali chiave di Azure da un'applicazione Web." services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//\>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# Usare l'insieme di credenziali chiave di Azure da un'applicazione Web #

## Introduzione  
In questa esercitazione si apprenderà a usare l'insieme di credenziali chiave di Azure da un'applicazione Web in Azure. Viene illustrato il processo di accesso a un segreto da un insieme di credenziali chiave di Azure, in modo da poterlo usare nell'applicazione Web.

**Tempo previsto per il completamento:** 15 minuti


Per informazioni generali sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali chiave di Azure?](key-vault-whatis.md)

## Prerequisiti 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

- Un URI di un segreto in un insieme di credenziali chiave di Azure.
- Un ID client e un segreto client per un'applicazione Web registrata con Azure Active Directory che abbia accesso all'insieme di credenziali chiave.
- Un'applicazione Web. Verranno mostrati i passaggi per un'applicazione ASP.NET MVC distribuita in Azure come app Web. 

> [AZURE.NOTE]Per questa esercitazione è essenziale aver completato i passaggi elencati in [Introduzione all'insieme di credenziali chiave di Azure](key-vault-get-started.md) in modo da disporre dell'URI di un segreto e dell'ID client e del segreto client per un'applicazione Web.

L'applicazione Web che accederà all'insieme di credenziali chiave è quella registrata in Azure Active Directory e a cui è stato concesso l'accesso all'insieme di credenziali chiave. Se non è così, tornare a Registrare un'applicazione nell'esercitazione introduttiva e ripetere i passaggi elencati.

Questa esercitazione è destinata agli sviluppatori Web che conoscono le nozioni di base della creazione di applicazioni Web in Azure. Per altre informazioni sulle app Web di Azure, vedere [Panoramica delle app Web](../app-service-web-overview.md).



## <a id="packages"></a>Aggiungere i pacchetti NuGet ##
Sono tre i pacchetti che devono essere installati nell'applicazione Web.

- Active Directory Authentication Library: contiene i metodi per interagire con Azure Active Directory e gestire l'identità utente
- Azure Key Vault Library: contiene i metodi per interagire con l'insieme di credenziali chiave di Azure


È possibile installare tutti e tre questi pacchetti con la Console di Gestione pacchetti usando il comando Install-Package.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>Modificare Web.Config ##
Sono tre le impostazioni che devono essere aggiunte al file web.config, come indicato di seguito.

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Se non si ospiterà l'applicazione come app Web di Azure, è consigliabile aggiungere i valori effettivi di ID client, segreto client e URI segreto a web.config. In caso contrario, lasciare questi valori fittizi perché i valori effettivi verranno aggiunti nel portale di Azure per un ulteriore livello di sicurezza.


## <a id="gettoken"></a>Aggiungere il metodo per ottenere un token di accesso ##
Per usare l'API dell'insieme di credenziali chiave, è necessario un token di accesso. Il client dell'insieme di credenziali chiave gestisce le chiamate all'API dell'insieme di credenziali chiave, ma è necessario fornirgli una funzione che ottenga il token di accesso.

Di seguito è riportato il codice per ottenere un accesso token da Azure Active Directory. Questo codice può far accedere ovunque nell'applicazione. È possibile aggiungere una classe Utils o EncryptionHelper.

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

## <a id="appstart"></a>Recuperare il segreto all'avvio dell'applicazione ##
A questo punto è necessario il codice per chiamare l'API dell'insieme di credenziali chiave e recuperare il segreto. Purché venga chiamato prima che sia necessario usarlo, è possibile inserire il codice seguente in qualsiasi punto. In questo caso, viene inserito nell'evento Application Start in Global.asax in modo che venga eseguito una volta all'avvio e renda il segreto disponibile per l'applicazione.

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Aggiungere le impostazioni dell'app nel portale di Azure \(facoltativo\) ##
Se si dispone di un'app Web di Azure, è ora possibile aggiungere i valori effettivi per AppSettings nel portale di Azure. In questo modo, i valori effettivi non saranno in web.config, ma saranno protetti tramite il portale in cui si dispone di funzionalità di controllo di accesso separate. Questi valori verranno sostituiti per i valori immessi in web.config. Assicurarsi che i nomi siano uguali.

![Impostazioni dell'applicazione visualizzate nel portale di Azure][1]



## <a id="next"></a>Passaggi successivi ##


Per i riferimenti alla programmazione, vedere [Informazioni di riferimento sull'API client C\# dell'insieme di credenziali chiave di Azure](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=58_postMigration-->