<properties 
	pageTitle="Introduzione all'API REST di gestione di Ricerca di Azure" 
	description="Introduzione all'API REST di gestione di Ricerca di Azure" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/16/2015" 
	ms.author="heidist"/>
# Introduzione all'API REST di gestione di Ricerca di Azure #

L'API REST di gestione di Ricerca di Azure è un'alternativa a livello di codice per eseguire attività amministrative nel portale. Le operazioni di gestione del servizio includono la creazione o l'eliminazione del servizio, la scalabilità del servizio e la gestione delle chiavi. Questa esercitazione include un'applicazione client di esempio per dimostrare l'API di gestione del servizio. Include anche i passaggi di configurazione necessari per eseguire l'esempio nell'ambiente di sviluppo locale.

Per completare questa esercitazione, è necessario:

- Visual Studio 2012 o 2013
- Il download dell'applicazione client di esempio

Durante l'esercitazione verrà effettuato il provisioning di due servizi: Ricerca di Azure e Azure Active Directory (AD). Inoltre, si creerà un'applicazione di Active Directory che stabilisce una relazione di trust tra l'applicazione client e l'endpoint di gestione risorse in Azure.

Per completare l'esercitazione, è necessario un account Azure.


<h2 id="download">Scaricare l'applicazione di esempio</h2>

Questa esercitazione si basa su un'applicazione console di Windows scritta in C#, che è possibile modificare ed eseguire in Visual Studio 2012 o 2013.

È possibile trovare l'applicazione client in Codeplex nella [demo dell'API di gestione di Ricerca di Azure](https://azuresearchmgmtapi.codeplex.com/).


<h2 id="config">Configurare l'applicazione</h2>

Prima di poter eseguire l'applicazione di esempio, è necessario abilitare l'autenticazione in modo tale che le richieste inviate dall'applicazione client all'endpoint di gestione risorse possano essere accettate. Il requisito di autenticazione deriva da [Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790568.aspx), che costituisce la base per tutte le operazioni correlate al portale richieste tramite un'API, incluse quelle relative alla gestione del servizio di ricerca. L'API di gestione del servizio per Ricerca di Azure è semplicemente un'estensione di Gestione risorse di Azure e pertanto ne eredita le dipendenze.  

Gestione risorse di Azure richiede il servizio Azure Active Directory come provider di identità. 

Per ottenere un token di accesso che consentirà alle richieste di raggiungere il servizio Gestione risorse, l'applicazione client include un segmento di codice che chiama Active Directory. Il segmento di codice, incluse le operazioni preliminari per l'uso del segmento di codice, è stato preso in prestito dall'articolo [Autenticazione delle richieste di Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

È possibile seguire le istruzioni riportate nel collegamento precedente o usare i passaggi presenti in questo documento, se si preferisce completare l'esercitazione dettagliata.

In questa sezione verranno eseguite le attività seguenti:

1. Creare un servizio Active Directory
1. Creare un'applicazione di Active Directory
1. Configurare l'applicazione di Active Directory registrando i dati dell'applicazione client di esempio scaricata
1. Caricare l'applicazione client di esempio con i valori che verranno usati per ottenere l'autorizzazione per le richieste

> [WACOM.NOTE] I collegamenti seguenti forniscono il background per poter usare Azure Active Directory per autenticare le richieste client nel servizio Gestione risorse: [Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790568.aspx), [Autenticazione delle richieste di Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx) e [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

<h3>Creare un servizio Active Directory</h3>

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).

2. Scorrere verso il basso il pannello di navigazione sinistro e fare clic su **Active Directory**.

4. Fare clic su **NUOVO** per aprire **Servizi app** | **Active Directory**. In questo passaggio si creerà un nuovo servizio Active Directory. Questo servizio ospiterà l'applicazione di Active Directory che verrà definita nei passaggi successivi. La creazione di un nuovo servizio consente di isolare l'esercitazione dalle altre applicazioni che potrebbero già essere ospitate in Azure.

5. Fare clic su **Directory** | **Creazione personalizzata**.

6. Immettere il nome, il dominio e la posizione geografica del servizio. Il dominio deve essere univoco. Fare clic sul segno di spunta per creare il servizio.

     ![][5]

<h3>Creare una nuova applicazione di Active Directory per questo servizio</h3>

1. Selezionare il servizio Active Directory "SearchTutorial" appena creato.

2. Nel menu in alto fare clic su **Applicazioni**. 
 
3. Fare clic su **Aggiungi applicazione**. Un'applicazione di Active Directory archivia le informazioni sulle applicazioni client che verranno usate come provider di identità.  
 
4. Scegliere **Aggiungi un'applicazione che l'organizzazione sta sviluppando**. Questa opzione fornisce le impostazioni di registrazione per le applicazioni che non vengono pubblicate nella raccolta di applicazioni. Poiché l'applicazione client non fa parte della raccolta di applicazioni, questa è la scelta giusta per questa esercitazione.

     ![][6]
 
5. Immettere un nome, ad esempio "Azure-Search-Manager".

6. Scegliere **Applicazione client nativa** per il tipo di applicazione. Si tratta dell'opzione corretta per l'applicazione di esempio poiché è un'applicazione client (console) di Windows e non un'applicazione Web.

     ![][7]
 
7. In URI di reindirizzamento immettere "http://localhost/Azure-Search-Manager-App". Si tratta di un URI a cui Azure Active Directory reindirizzerà l'agente utente in risposta a una richiesta di autorizzazione OAuth 2.0. Il valore non deve necessariamente essere un endpoint fisico, ma un URI valido. 

    Ai fini di questa esercitazione, è possibile immettere qualsiasi valore, ma il valore immesso diventerà un input obbligatorio per la connessione amministrativa nell'applicazione di esempio. 
 
7. Fare clic sul segno di spunta per creare l'applicazione di Active Directory. Nel pannello di navigazione sinistro verrà visualizzato "Azure-Search-Manager-App".

<h3>Configurare l'applicazione di Active Directory</h3>
 
9. Fare clic sull'applicazione di Active Directory, "Azure-Search-Manager-App", appena creata. Viene elencata nel pannello di navigazione sinistro.

10. Fare clic su **Configura** nel menu in alto.
 
11. Scorrere verso il basso fino ad Autorizzazioni e selezionare **API di gestione di Microsoft Azure**. In questo passaggio specificare l'API, in questo caso l'API di Gestione risorse di Azure, a cui l'applicazione client deve accedere, insieme al livello di accesso necessario.

12. In Autorizzazioni delegate fare clic sull'elenco a discesa e selezionare **Accesso a Gestione del servizio Azure (anteprima**).
 
     ![][8]
 
13. Salvare le modifiche. 

Tenere aperta la pagina di configurazione dell'applicazione. Nel passaggio successivo sarà necessario copiare i valori da questa pagina e immetterli nell'applicazione di esempio.

<h3>Caricare il programma dell'applicazione di esempio con i valori di registrazione e sottoscrizione</h3>

In questa sezione verrà illustrato come modificare la soluzione in Visual Studio, sostituendo i valori validi ottenuti dal portale.
I valori da aggiungere vengono visualizzati nella parte superiore del file Program.cs:

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Scaricare [l'applicazione di esempio da Codeplex](linkTBD), se non è ancora stato fatto, perché servirà per questo passaggio.

1. Aprire **ManagementAPI.sln** in Visual Studio.

2. Aprire Program.cs.

3. Specificare `ClientId`. Dalla pagina di configurazione dell'applicazione di Active Directory nel portale, lasciata aperta dal passaggio precedente, copiare l'ID client e incollarlo in Program.cs.

4. Specificare `RedirectUrl`. Copiare l'URI di reindirizzamento dalla stessa pagina del portale e incollarlo in Program.cs.

	![][9]

5. Specificare `TenantID`. 
	- Tornare ad Active Directory | SearchTutorial (servizio). 
	- Fare clic su **Applicazioni** nella barra in alto. 
	- Fare clic su **Visualizza endpoint** nella parte inferiore della pagina. 
	- Copiare l'endpoint di autorizzazione OAUTH 2.0 nella parte inferiore dell'elenco. 
	- Incollare l'endpoint in TenantID, rimuovendo il valore di tutti i parametri URI tranne l'ID tenant.

    Supponendo di avere "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0", eliminare tutto tranne "55e324c7-1656-4afe-8dc3-43efcd4ffa50".

	![][10]

6. Specificare `SubscriptionID`.
	- Andare nella pagina principale del portale.
	- Fare clic su **Impostazioni** nella parte inferiore del pannello di navigazione sinistro.
	- Dalla scheda Sottoscrizioni copiare l'ID sottoscrizione e incollarlo in Program.cs.

7. Salvare e quindi compilare la soluzione.


<h3>Risoluzione degli errori di compilazione</h3>

I problemi con i riferimenti agli assembly possono impedire la compilazione di una soluzione. Questa sezione fornisce le soluzioni alternative per alcuni di esse.

- È necessario che sia installato Microsoft.IdentityModel.Clients.ActiveDirectory (vedere [Autenticazione delle richieste di Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx) per informazioni su come installare [Active Directory Authentication Library](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)).
- Provare a risolvere il problema reinstallando l'assembly. Andare in **Strumenti** | **Gestione pacchetti NuGet** | **Console di Gestione pacchetti**. Al prompt PM> immettere *Update-package -reinstall Microsoft.IdentityModel.Clients.ActiveDirectory*.
- In questa soluzione viene fatto riferimento anche a Newtonsoft.Json. Se si verifica un errore per questo assembly, eliminarlo dal progetto e quindi aggiungerlo nuovamente. Fare clic con il pulsante destro del mouse su click **Riferimenti** | **Gestisci pacchetti NuGet** | **Pacchetti installati**. Selezionare **Json.Net** e disinstallarlo. Espandere **Online**, selezionare **Json.Net** | **Installa**.


<h2 id="explore">Esplorare l'applicazione</h2>

Aggiungere un punto di interruzione alla prima chiamata al metodo in modo da poter eseguire gli altri passaggi del programma. Premere **F5** per eseguire l'applicazione, quindi premere **F11** per eseguire gli altri passaggi del codice.

L'applicazione di esempio crea un servizio Ricerca di Azure gratuito per una sottoscrizione di Azure esistente. Se esiste già un servizio gratuito per la sottoscrizione, l'applicazione di esempio non riuscirà. È consentito un solo servizio di ricerca gratuito per ogni sottoscrizione.

1. Aprire Program.cs da Esplora soluzioni e passare alla funzione Main(string[] void). 
 
3. Notare che **ExecuteArmRequest** viene usato per eseguire le richieste sull'endpoint di Gestione risorse di Azure, `https://management.azure.com/subscriptions` per il valore `subscriptionID` specificato. Questo metodo viene usato in tutto il programma per eseguire le operazioni con l'API di Gestione risorse o con l'API di gestione di Ricerca di Azure.

3. Le richieste inviate a Gestione risorse di Azure devono essere autenticate e autorizzate. A tale scopo, usare il metodo **GetAuthorizationHeader**, chiamato dal metodo **ExecuteArmRequest**, preso in prestito dall'articolo [Autenticazione delle richieste di Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx). Tenere presente che **GetAuthorizationHeader** chiama `https://management.core.windows.net` per ottenere un token di accesso.

4. Viene richiesto di eseguire l'accesso con un nome utente e una password validi per la sottoscrizione.

5. Un nuovo servizio Ricerca di Azure viene quindi registrato con il provider di Gestione risorse di Azure. Viene di nuovo chiamato il metodo **ExecuteArmRequest**, questa volta però usato per creare il servizio Ricerca di Azure per la sottoscrizione tramite `providers/Microsoft.Search/register`. 

6. Il resto del programma usa l'[API REST di gestione di Ricerca di Azure](http://msdn.microsoft.com/library/dn832684.aspx). Tenere presente che il valore `api-version` per questa API è diverso dalla versione api di Gestione risorse di Azure. Ad esempio, `/listAdminKeys?api-version=2014-07-31-Preview` mostra il valore `api-version` dell'API REST di gestione di Ricerca di Azure.

	La serie successiva di operazioni consente di recuperare la definizione del servizio appena creato, le chiavi api di amministrazione, di rigenerare e recuperare le chiavi, di modificare la replica e la partizione e infine di eliminare il servizio.

	Se si modifica il numero di partizioni o di repliche del servizio e si usa una versione gratuita, questa azione non riuscirà. Solo con la versione standard è possibile aggiungere altre partizioni e repliche.

	L'eliminazione del servizio è l'ultima operazione.

<h2 id="next-steps">Passaggi successivi</h2>

Dopo avere completato questa esercitazione, è possibile volere altre informazioni sulla gestione dei servizi o sull'autenticazione con il servizio Active Directory.

- Altre informazioni sull'integrazione di un'applicazione client con Active Directory. Vedere [Integrazione di applicazioni in Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Informazioni sulle altre operazioni di gestione dei servizi in Azure. Vedere [Gestione dei servizi](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Scaricare l'applicazione di esempio]: #Download
[Configurare l'applicazione]: #config
[Esplorare l'applicazione]: #explore
[Passaggi successivi]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Gestire la soluzione di ricerca in Microsoft Azure]: ../search-manage/
[Flusso di lavoro per lo sviluppo di Ricerca di Azure]: ../search-workflow/
[Creare la prima soluzione di ricerca con Ricerca di Azure]: ../search-create-first-solution/
[Creare un'app di ricerca geospaziale mediante Ricerca di Azure]: ../search-create-geospatial/



<!--HONumber=46--> 

<!--HONumber=46--> 
