<properties
	pageTitle="Autenticazione dell'entità servizio per app per le API nel servizio app di Azure | Microsoft Azure"
	description="Informazioni su come proteggere un'app per le API nel servizio app di Azure per scenari da servizio a servizio."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2016" 
	ms.author="rachelap"/>

# Autenticazione dell'entità servizio per app per le API nel servizio app di Azure

## Panoramica

Questo articolo illustra come usare l'autenticazione del servizio app per l'accesso *interno* alle app per le API. In uno scenario interno un'app per le API deve essere utilizzabile solo dal codice dell'applicazione dell'utente. Il modo consigliato per implementare questo scenario nel servizio app consiste nell'usare Azure AD per proteggere l'app per le API chiamata. L'app per le API protetta viene chiamata con un token di connessione ottenuto da Azure AD fornendo le credenziali dell'identità di applicazione (entità servizio). Per alternative all'uso di Azure AD, vedere la sezione **Autenticazione da servizio a servizio** in [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md#service-to-service-authentication).

In questo articolo si apprenderà:

* Come usare Azure Active Directory (Azure AD) per proteggere un'app per le API dall'accesso non autenticato.
* Come utilizzare un'app per le API protetta da un'app per le API, un'app Web o un'app per dispositivi mobili usando le credenziali dell'entità servizio (identità di app) di Azure AD. Per informazioni sull'utilizzo da un'app per la logica, vedere [Uso dell'API personalizzata ospitata nel servizio app con App per la logica](../app-service-logic/app-service-logic-custom-hosted-api.md).
* Come verificare che l'app per le API protetta non possa essere chiamata dagli utenti connessi da un browser.
* Come verificare che l'app per le API protetta possa essere chiamata solo da un'entità servizio di Azure AD specifica.

L'articolo contiene due sezioni:

* La sezione [Come configurare l'autenticazione dell'entità servizio di Servizio app di Azure](#authconfig) illustra in generale come configurare l'autenticazione per le app per le API e come utilizzare un'app per le API protetta. Questa sezione si applica allo stesso modo a tutti i framework supportati dal servizio app, inclusi .NET, Node.js e Java.

* A partire dalla sezione [Proseguimento della serie di esercitazioni sulle app per le API .NET](#tutorialstart), l'esercitazione descrive la configurazione di uno scenario di "accesso interno" per un'applicazione .NET di esempio in esecuzione nel servizio app.

## <a id="authconfig"></a> Come configurare l'autenticazione dell'entità servizio nel servizio app di Azure

Questa sezione fornisce istruzioni generali applicabili a tutte le app per le API. Per i passaggi specifici dell'applicazione .NET di esempio, To Do List, vedere [Proseguimento della serie di esercitazioni sulle app per le API .NET](#tutorialstart).

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello **Impostazioni** dell'app per le API da proteggere, individuare la sezione **Funzionalità**, quindi fare clic su **Autenticazione/Autorizzazione**.

	![Autenticazione/Autorizzazione nel portale di Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

5. In **Provider di autenticazione** selezionare **Azure Active Directory**.

	![Pannello Autenticazione/Autorizzazione nel portale di Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Configurare il pannello **Impostazioni di Azure Active Directory** per creare una nuova applicazione Azure AD oppure, se disponibile, usare un'applicazione Azure AD esistente.

	Gli scenari interni in genere comprendono un'app per le API che chiama un'app per le API. È possibile usare applicazioni Azure AD separate per ogni app per le API o una sola applicazione Azure AD.

	Per istruzioni dettagliate su questo pannello, vedere [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

7. Dopo aver completato le operazioni nel pannello di configurazione del provider di autenticazione, fare clic su **OK**.

7. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.

	![Fare clic su Salva.](./media/app-service-api-dotnet-service-principal-auth/authsave.png)

Al termine, il servizio app consente solo le richieste dai chiamanti nel tenant di Azure AD configurato. Nell'app per le API protetta non sono necessari codici di autenticazione o di autorizzazione. Il token di connessione viene passato all'app per le API con le attestazioni di uso comune nelle intestazioni HTTP ed è possibile leggere tali informazioni nel codice per convalidare la provenienza delle richieste da un determinato chiamante, ad esempio un'entità servizio.

Questa funzionalità di autenticazione è la stessa per tutti i linguaggi supportati dal servizio app, inclusi .NET, Node.js e Java.

#### Come utilizzare l'app per le API protetta

Il chiamante deve fornire un token di connessione di Azure AD con le chiamate API. Per ottenere un token di connessione con le credenziali dell'entità servizio, il chiamante usa Active Directory Authentication Library (ADAL per [.NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory), [Node.js](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) o [Java](https://github.com/AzureAD/azure-activedirectory-library-for-java)). Per ottenere un token, il codice che chiama ADAL fornisce ad ADAL le informazioni seguenti:

* Nome del tenant di Azure AD.
* ID client e segreto client (chiave app) dell'app Azure AD associata al chiamante.
* ID client dell'applicazione Azure AD associata all'app per le API protetta. Se viene usata una sola applicazione Azure AD, è lo stesso ID client del chiamante.

Questi valori sono disponibili nelle pagine di Azure AD del [portale di Azure classico](https://manage.windowsazure.com/).

Una volta acquisito il token, il chiamante lo include con le richieste HTTP nell'intestazione dell'autorizzazione. Il servizio app convalida il token e consente alle richieste di raggiungere l'app per le API protetta.

#### Come proteggere l'app per le API dall'accesso di utenti nello stesso tenant

I token di connessione per gli utenti nello stesso tenant sono considerati validi per l'app per le API protetta. Per assicurarsi che una sola entità servizio possa chiamare l'app per le API protetta, aggiungere il codice nell'app per le API protetta per convalidare le attestazioni seguenti dal token:

* `appid` deve essere l'ID client dell'applicazione Azure AD associata al chiamante.
* `oid` (`objectidentifier`) deve essere l'ID entità servizio del chiamante.

Il servizio app fornisce anche l'attestazione `objectidentifier` nell'intestazione X-MS-CLIENT-PRINCIPAL-ID.

### Come proteggere l'app per le API dall'accesso dal browser

Se non si convalidano le attestazioni nel codice nell'app per le API protetta e si usa un'applicazione Azure AD separata per l'app per le API protetta, verificare che l'URL di risposta dell'applicazione Azure AD non sia lo stesso URL di base dell'app per le API. Se l'URL di risposta punta direttamente all'app per le API protetta, un utente nello stesso tenant di Azure AD potrebbe selezionare l'app per le API, accedere e chiamare l'API.

## <a id="tutorialstart"></a> Proseguimento della serie di esercitazioni su .NET

Se si sta seguendo la serie di esercitazioni su Node.js o Java per le app per le API, passare alla sezione [Passaggi successivi](#next-steps).

Il resto di questo articolo rappresenta la continuazione della serie di esercitazioni sulle app per le API .NET. Queste esercitazioni presuppongono che sia stata completata l'[esercitazione sull'autenticazione utente](app-service-api-dotnet-user-principal-auth.md) e che l'applicazione di esempio sia in esecuzione in Azure con l'autenticazione utente abilitata.

## Configurare l'autenticazione in Azure

In questa sezione si configura il servizio app in modo che le sole richieste HTTP a cui è consentito raggiungere l'app per le API del livello dati siano quelle con token di connessione di Azure AD validi.

Nella sezione seguente si configura l'app per le API del livello intermedio in modo che invii le credenziali dell'applicazione ad Azure AD, ottenga un token di connessione e invii il token di connessione all'app per le API del livello dati. Questo processo è illustrato nel diagramma.

![Diagramma di autenticazione del servizio](./media/app-service-api-dotnet-service-principal-auth/appdiagram.png)

Se si verificano problemi mentre si seguono le istruzioni dell'esercitazione, vedere la sezione [Risoluzione dei problemi](#troubleshooting) alla fine dell'esercitazione.

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello **Impostazioni** dell'app per le API creata per l'app per le API ToDoListDataAPI (livello dati) e quindi fare clic su **Impostazioni**.

2. Nel pannello **Impostazioni** individuare la sezione **Funzionalità** e quindi fare clic su **Autenticazione/Autorizzazione**.

	![Autenticazione/Autorizzazione nel portale di Azure](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

	Questa impostazione fa in modo che il servizio app verifichi che solo le richieste autenticate raggiungano l'app per le API. Per le richieste con token di connessione validi, il servizio app passa i token all'app per le API e popola le intestazioni HTTP con attestazioni di uso comune per rendere tali informazioni più facilmente disponibili al codice.

5. In **Provider di autenticazione** fare clic su **Azure Active Directory**.

	![Pannello Autenticazione/Autorizzazione nel portale di Azure](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Nel pannello **Impostazioni di Azure Active Directory** fare clic su **Rapide**.

	Con l'opzione **Rapida** Azure può creare automaticamente un'applicazione AAD nel [tenant](https://msdn.microsoft.com/it-IT/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) di Azure AD.

	Non è necessario creare un tenant, perché ne esiste già uno per ogni account Azure.

7. In **Modalità di gestione** fare clic su **Crea nuova app AD** se questa opzione non è già selezionata.

	Il portale inserisce un valore predefinito nella casella di input **Crea app**. Per impostazione predefinita, all'applicazione Azure AD viene assegnato lo stesso nome dell'app per le API. Se si preferisce, è possibile immettere un nome diverso.
	
	![Impostazioni di Azure AD](./media/app-service-api-dotnet-service-principal-auth/aadsettings.png)

	**Nota**: in alternativa, è possibile usare una sola applicazione Azure AD sia per l'app per le API chiamante che per l'app per le API protetta. Se si sceglie questa alternativa, l'opzione **Crea nuova app AD** non sarà necessaria perché un'applicazione Azure AD è già stata creata prima nell'esercitazione sull'autenticazione utente. Per questa esercitazione si useranno applicazioni Azure AD separate per l'app per le API chiamante e per l'app per le API protetta.

8. Prendere nota del valore nella casella di input **Crea app**. Più avanti sarà necessario cercare questa applicazione AAD nel portale di Azure classico.

7. Fare clic su **OK**.

10. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.

	![Fare clic su Salva.](./media/app-service-api-dotnet-service-principal-auth/saveauth.png)

	Il servizio app crea l'applicazione Azure Active Directory con **URL accesso** e **URL di risposta** impostati automaticamente sull'URL dell'app per le API. Il secondo valore consente agli utenti nel tenant di AAD di accedere all'app per le API.

### Verificare che l'app per le API sia protetta

1. In un browser passare all'URL dell'app per le API. A tale scopo, nel pannello **App per le API** del portale di Azure fare clic sul collegamento in **URL**.

	Si viene reindirizzati a una schermata di accesso, perché alle richieste non autenticate non è consentito raggiungere l'app per le API.

	Se il browser passa all'interfaccia utente di Swagger, potrebbe già essere connesso. In tal caso, aprire una finestra di InPrivate o in incognito e andare all'URL dell'interfaccia utente di Swagger.

18. Accedere con le credenziali di un utente che appartiene al tenant AAD.

	Dopo avere effettuato l'accesso, viene visualizzata una pagina che informa che l'operazione di creazione è riuscita.

## Configurare il progetto ToDoListAPI per acquisire e inviare il token di Azure AD

In questa sezione vengono eseguite le attività seguenti:

* Aggiunta di codice nell'app per le API del livello intermedio che usa le credenziali dell'applicazione Azure AD per acquisire un token e inviarlo con le richieste HTTP all'app per le API del livello dati.
* Acquisizione delle credenziali necessarie da Azure AD.
* Immissione delle credenziali nelle impostazioni dell'ambiente di runtime di Servizio app di Azure nell'app per le API del livello intermedio.

### Configurare il progetto ToDoListAPI per acquisire e inviare il token di Azure AD

Apportare le modifiche seguenti al progetto ToDoListAPI in Visual Studio.

1. Rimuovere il commento da tutto il codice nel file *ServicePrincipal.cs*.

	Si tratta del codice che usa ADAL per .NET per acquisire il token di connessione di Azure AD. Usa diversi valori di configurazione che si imposteranno più avanti nell'ambiente di runtime di Azure. Ecco il codice:

		public static class ServicePrincipal
		{
		    static string authority = ConfigurationManager.AppSettings["ida:Authority"];
		    static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
		    static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
		    static string resource = ConfigurationManager.AppSettings["ida:Resource"];
		
		    public static AuthenticationResult GetS2SAccessTokenForProdMSA()
		    {
		        return GetS2SAccessToken(authority, resource, clientId, clientSecret);
		    }
		
		    static AuthenticationResult GetS2SAccessToken(string authority, string resource, string clientId, string clientSecret)
		    {
		        var clientCredential = new ClientCredential(clientId, clientSecret);
		        AuthenticationContext context = new AuthenticationContext(authority, false);
		        AuthenticationResult authenticationResult = context.AcquireToken(
		            resource,
		            clientCredential);
		        return authenticationResult;
		    }
		}

	**Nota:** questo codice richiede il pacchetto NuGet ADAL per .NET (Microsoft.IdentityModel.Clients.ActiveDirectory), che è già installato nel progetto. Se si crea questo progetto da zero, sarà necessario installare il pacchetto. Questo pacchetto non viene installato automaticamente dal modello di nuovo progetto dell'app per le API.

2. In *Controllers/ToDoListController* rimuovere il commento dal codice nel metodo `NewDataAPIClient` che aggiunge il token alle richieste HTTP nell'intestazione dell'autorizzazione.

		client.HttpClient.DefaultRequestHeaders.Authorization =
		    new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);

3. Distribuire il progetto ToDoListAPI. Fare clic con il pulsante destro del mouse sul progetto, quindi selezionare **Pubblica > Pubblica**.

	Visual Studio distribuisce il progetto e apre una finestra del browser all'URL di base dell'app Web. Viene visualizzata la pagina dell'errore 403, che rappresenta la risposta normale quando si tenta di passare a un URL di base dell'API Web da un browser.

4. Chiudere il browser.

### Ottenere i valori di configurazione di Azure AD

11. Nel [portale di Azure classico](https://manage.windowsazure.com/) passare a **Azure Active Directory**.

12. Nella scheda **Directory** fare clic sul tenant AAD.

14. Fare clic su **Applicazioni > Applicazioni di proprietà dell'azienda** e quindi fare clic sul segno di spunta.

15. Nell'elenco delle applicazioni fare clic sul nome di quella creata automaticamente da Azure quando è stata abilitata l'autenticazione per l'app per le API ToDoListDataAPI (livello dati).

16. Fare clic sulla scheda **Configure**.

5. Copiare il valore **ID client** e salvarlo in una posizione da cui poterlo recuperare più tardi.

8. Nel portale di Azure classico tornare all'elenco **Applicazioni di proprietà dell'azienda** e fare clic sull'applicazione AAD creata per l'app per le API ToDoListAPI di livello intermedio. Si tratta dell'app creata nell'esercitazione precedente, non in quella corrente.

16. Fare clic sulla scheda **Configure**.

5. Copiare il valore **ID client** e salvarlo in una posizione da cui poterlo recuperare più tardi.

6. In **chiavi** selezionare **1 anno** dall'elenco a discesa **Seleziona durata**.

6. Fare clic su **Save**.

	![Generare una chiave dell'app](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copiare il valore chiave e salvarlo in una posizione da cui poterlo recuperare più tardi.

	![Copiare una nuova chiave dell'app](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

### Configurare le impostazioni di Azure AD nell'ambiente di runtime dell'app per le API del livello intermedio

1. Passare al [portale di Azure](https://portal.azure.com/) e quindi passare al pannello **App per le API** per l'app per le API che ospita il progetto TodoListAPI (livello intermedio).

2. Fare clic su **Impostazioni > Impostazioni applicazione**.

3. Nella sezione **Impostazioni app** aggiungere le chiavi e i valori seguenti:

	| **Chiave** | ida:Authority |
	|---|---|
	| **Valore** | https://login.microsoftonline.com/{your nome tenant di Azure AD} |
	| **Esempio** | https://login.microsoftonline.com/contoso.onmicrosoft.com |

	| **Chiave** | ida:ClientId |
	|---|---|
	| **Valore** | ID client dell'applicazione chiamante (livello intermedio - ToDoListAPI) |
	| **Esempio** | 960adec2-b74a-484a-960adec2-b74a-484a |

	| **Chiave** | ida:ClientSecret |
	|---|---|
	| **Valore** | Chiave app dell'applicazione chiamante (livello intermedio - ToDoListAPI) |
	| **Esempio** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

	| **Chiave** | ida:Resource |
	|---|---|
	| **Valore** | ID client dell'applicazione chiamata (livello dati - ToDoListDataAPI) |
	| **Esempio** | e65e8fc9-5f6b-48e8-e65e8fc9-5f6b-48e8 |

	**Nota**: per `ida:Resource`, assicurarsi di usare l'**ID client** dell'applicazione chiamata e non il relativo **URI ID App**.

	`ida:ClientId` e `ida:Resource` sono valori diversi per questa esercitazione perché si usano applicazioni Azure AD diverse per il livello intermedio e il livello dati. Se si usa una sola applicazione Azure AD sia per l'app per le API chiamante che per l'app per le API protetta, usare lo stesso valore sia in `ida:ClientId` che in `ida:Resource`.

	Il codice usa ConfigurationManager per ottenere questi valori, che quindi possono essere archiviati nel file Web.config del progetto o nell'ambiente di runtime di Azure. Mentre un'applicazione ASP.NET è in esecuzione nel servizio app di Azure, le impostazioni dell'ambiente eseguono automaticamente l'override delle impostazioni di Web.config. Le impostazioni dell'ambiente in genere rappresentano un [modo più sicuro di archiviare le informazioni riservate rispetto a un file Web.config](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

6. Fare clic su **Save**.

	![Fare clic su Salva.](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

### Test dell'applicazione

1. In un browser andare all'URL HTTPS dell'app Web front-end AngularJS.

2. Fare clic sulla scheda **To Do List** e accedere con le credenziali di un utente nel tenant di Azure AD.

4. Aggiungere alcune attività per verificare che l'applicazione funzioni.

	![Pagina To Do List](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

	Se l'applicazione non funziona come previsto, controllare tutte le impostazioni immesse nel portale di Azure. Se si ritiene che tutte le impostazioni siano corrette, vedere la sezione [Risoluzione dei problemi](#troubleshooting) più avanti in questa esercitazione.

## Proteggere l'app per le API dall'accesso dal browser

Per questa esercitazione è stata creata un'applicazione Azure AD separata per l'app per le API ToDoListDataAPI (livello dati). Come si è visto, quando il servizio app crea un'applicazione AAD, configura l'applicazione AAD per poter consentire all'utente di passare all'URL dell'app per le API in un browser ed eseguire l'accesso. Ciò significa che anche un utente finale nel tenant di Azure AD può accedere all'API, non solo un'entità servizio.

Se si vuole impedire l'accesso dal browser senza scrivere codice nell'app per le API protetta, è possibile modificare l'**URL di risposta** nell'applicazione AAD in modo che sia diverso dall'URL di base dell'app per le API.

### Disabilitare l'accesso dal browser

1. Nella scheda **Configura** del portale classico per l'applicazione AAD creata per TodoListService modificare il valore del campo **URL di risposta** in modo che sia un URL valido, ma non l'URL dell'app per le API.
 
2. Fare clic su **Save**.

### Verificare che non sia più possibile accedere dal browser

Prima si è verificato che sia possibile andare all'URL dell'app per le API da un browser accedendo con le credenziali di un singolo utente. In questa sezione si verifica che non sia più possibile.

1. In una nuova finestra del browser passare nuovamente all'URL dell'app per le API.

2. Eseguire l'accesso quando viene richiesto di farlo.

3. L'accesso ha esito positivo, ma viene visualizzata una pagina di errore.

	L'app AAD è stata configurata in modo che gli utenti nel tenant AAD non possano accedere all'API da un browser. È tuttavia possibile accedere all'app per le API usando un token per un'entità servizio, che è possibile verificare andando all'URL dell'app Web e aggiungendo altre attività.

## Limitare l'accesso a una particolare entità servizio  

Attualmente qualsiasi chiamante possa ottenere un token per un'utente o un'entità servizio nel tenant di Azure AD può chiamare l'app per le API TodoListDataAPI (livello dati). È possibile verificare che l'app per le API del livello dati accetti chiamate solo dall'app per le API TodoListAPI (livello intermedio) e solo da una particolare entità servizio.

Per aggiungere queste restrizioni, aggiungere il codice per convalidare le attestazioni `appid` e `objectidentifier` nelle chiamate in arrivo.

Per questa esercitazione, inserire il codice che convalida l'ID app e l'ID entità servizio direttamente nelle azioni del controller. In alternativa, usare un attributo `Authorize` personalizzato o eseguire la convalida nelle sequenze di avvio (ad esempio, il middleware OWIN). Per un esempio della seconda opzione, vedere [questa applicazione di esempio](https://github.com/mohitsriv/EasyAuthMultiTierSample/blob/master/MyDashDataAPI/Startup.cs).

Apportare le modifiche seguenti al progetto TodoListDataAPI.

2. Aprire il file *Controllers/TodoListController.cs*.

3. Rimuovere il commento dalle righe che impostano `trustedCallerClientId` e `trustedCallerServicePrincipalId`.

		private static string trustedCallerClientId = ConfigurationManager.AppSettings["todo:TrustedCallerClientId"];
		private static string trustedCallerServicePrincipalId = ConfigurationManager.AppSettings["todo:TrustedCallerServicePrincipalId"];

4. Rimuovere il commento dal codice nel metodo CheckCallerId, che viene chiamato all'avvio di ogni metodo azione nel controller.

		private static void CheckCallerId()
		{
		    string currentCallerClientId = ClaimsPrincipal.Current.FindFirst("appid").Value;
		    string currentCallerServicePrincipalId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		    if (currentCallerClientId != trustedCallerClientId || currentCallerServicePrincipalId != trustedCallerServicePrincipalId)
		    {
		        throw new HttpResponseException(new HttpResponseMessage { StatusCode = HttpStatusCode.Unauthorized, ReasonPhrase = "The appID or service principal ID is not the expected value." });
		    }
		}

5. Ridistribuire il progetto ToDoListDataAPI nel servizio app di Azure.

6. Nel browser passare all'URL HTTPS dell'app Web front-end AngularJS e nella home page fare clic sulla scheda **To Do List**.

	L'applicazione non funziona perché le chiamate al back-end hanno esito negativo. Il nuovo codice sta controllando gli effettivi appid e objectidentifier, ma non ha ancora i valori corretti con cui confrontarli. La console degli strumenti di sviluppo del browser segnala che il server restituisce un errore HTTP 401.

	![Errore nella console degli strumenti di sviluppo](./media/app-service-api-dotnet-service-principal-auth/webapperror.png)

	Nei passaggi seguenti vengono configurati i valori previsti.

8. Usando Azure AD PowerShell, ottenere il valore dell'entità servizio per l'applicazione Azure AD creata per il progetto TodoListWebApp.

	a. Per istruzioni su come installare Azure PowerShell e connettersi alla sottoscrizione, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

	b. Per ottenere un elenco di entità servizio, eseguire il comando `Login-AzureRmAccount` e quindi il comando `Get-AzureRmADServicePrincipal`.

	c. Trovare il valore di objectid per l'entità servizio dell'applicazione TodoListAPI e salvarlo in una posizione da cui poterlo copiare più tardi.

7. Nel portale di Azure passare al pannello App per le API dell'app per le API in cui è stato distribuito il progetto ToDoListDataAPI.

9. Fare clic su **Impostazioni > Impostazioni applicazione**.

3. Nella sezione **Impostazioni app** aggiungere le chiavi e i valori seguenti:

	| **Chiave** | todo:TrustedCallerServicePrincipalId |
	|---|---|
	| **Valore** | ID entità servizio dell'applicazione chiamante |
	| **Esempio** | 4f4a94a4-6f0d-4072-4f4a94a4-6f0d-4072 |

	| **Chiave** | todo:TrustedCallerClientId |
	|---|---|
	| **Valore** | ID client dell'applicazione chiamante, copiato dall'applicazione Azure AD TodoListAPI |
	| **Esempio** | 960adec2-b74a-484a-960adec2-b74a-484a |

6. Fare clic su **Save**.

	![Fare clic su Salva.](./media/app-service-api-dotnet-service-principal-auth/trustedcaller.png)

6. Nel browser tornare all'URL dell'app Web e nella home page fare clic sulla scheda **To Do List**.

	Ora l'applicazione funziona come previsto perché l'ID app chiamante attendibile e l'ID entità servizio hanno i valori previsti.

	![Pagina To Do List](./media/app-service-api-dotnet-service-principal-auth/mvchome.png)

## Compilazione dei progetti da zero

I due progetti API Web sono stati creati usando il modello di progetto **App per le API di Azure** e sostituendo il controller predefinito Values con un controller ToDoList. Per acquisire i token dell'entità servizio di Azure AD nel progetto ToDoListAPI, è stato installato il pacchetto NuGet [Active Directory Authentication Library (ADAL) per .NET](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).
 
Per informazioni su come creare un'applicazione a pagina singola AngularJS con un back-end API Web, ad esempio ToDoListAngular, vedere [Hands On Lab: Build a Single Page Application (SPA) with ASP.NET Web API and Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs) (Laboratorio pratico: creare un'applicazione a pagina singola con un'API Web e Angular.js). Per informazioni su come aggiungere il codice di autenticazione di Azure AD, vedere [Protezione di app AngularJS a singola pagina con Azure AD](../active-directory/active-directory-devquickstarts-angular.md).

## Risoluzione dei problemi

[AZURE.INCLUDE [risoluzione dei problemi](../../includes/app-service-api-auth-troubleshooting.md)]

* Assicurarsi di non confondere ToDoListAPI (livello intermedio) e ToDoListDataAPI (livello dati). Ad esempio, in questa esercitazione viene aggiunta l'autenticazione per l'app per le API di livello dati, **ma la chiave dell'app deve provenire dall'applicazione Azure AD creata per l'app per le API di livello intermedio**.

## Passaggi successivi

Questa è l'ultima esercitazione della serie sulle app per le API.

Per altre informazioni su Azure Active Directory, vedere le risorse seguenti.

* [Guida per sviluppatori Azure AD](http://aka.ms/aaddev)
* [Scenari per Azure AD](http://aka.ms/aadscenarios)
* [Esempi di Azure AD](http://aka.ms/aadsamples)

	L'esempio [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) è simile a quanto illustrato in questa esercitazione, ma non usa l'autenticazione del servizio app.

Per informazioni su altre modalità di distribuzione di progetti Visual Studio in app per le API usando Visual Studio o [automatizzando la distribuzione](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) da un [sistema di controllo del codice sorgente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), vedere [Distribuire l'app nel servizio app di Azure](../app-service-web/web-sites-deploy.md).

<!---HONumber=AcomDC_0713_2016-->