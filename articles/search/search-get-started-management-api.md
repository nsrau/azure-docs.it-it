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
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Introduzione all'API REST di gestione di Ricerca di Azure

L'API REST di gestione di Ricerca di Azure è un'alternativa a livello di codice per eseguire attività amministrative nel portale. Le operazioni di gestione del servizio includono la creazione o l'eliminazione del servizio, la scalabilità del servizio e la gestione delle chiavi. Questa esercitazione include un'applicazione client di esempio per dimostrare l'API di gestione del servizio. Include anche i passaggi di configurazione necessari per eseguire l'esempio nell'ambiente di sviluppo locale.

Per completare questa esercitazione, è necessario:

- Visual Studio 2012 o 2013
- Il download dell'applicazione client di esempio

Completamento dell'esercitazione, verranno eseguito il provisioning di due servizi: Ricerca di Azure e Azure Active Directory (AD). Inoltre, si creerà un'applicazione di Active Directory che stabilisce una relazione di trust tra l'applicazione client e l'endpoint di gestione risorse in Azure.

È necessario un account Azure per completare questa esercitazione.


##Scaricare l'applicazione di esempio

Questa esercitazione si basa su un'applicazione console di Windows scritta in C#, che è possibile modificare ed eseguire in Visual Studio 2012 o 2013.

È possibile trovare l'applicazione client in Codeplex all'indirizzo [Demo API di gestione di Ricerca di Azure](https://azuresearchmgmtapi.codeplex.com/).


##Configurare l'applicazione

Prima di poter eseguire l'applicazione di esempio, è necessario abilitare l'autenticazione in modo tale che le richieste inviate dall'applicazione client all'endpoint di gestione risorse possano essere accettate. Il requisito di autenticazione ha origine dalle [Gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790568.aspx), che costituisce la base per tutte le operazioni correlate al portale richiesto tramite un'API, inclusi quelli relativi alla gestione del servizio di ricerca. L'API di gestione del servizio per Ricerca di Azure è semplicemente un'estensione di Gestione risorse di Azure e pertanto ne eredita le dipendenze.

Gestione risorse di Azure richiede il servizio Azure Active Directory come provider di identità.

Per ottenere un token di accesso che consentirà alle richieste di raggiungere il servizio Gestione risorse, l'applicazione client include un segmento di codice che chiama Active Directory. Il segmento di codice, nonché le operazioni preliminari all'utilizzo del segmento di codice, sono stati presi in prestito da questo articolo: [richiede l'autenticazione di gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

È possibile seguire le istruzioni riportate nel collegamento precedente o usare i passaggi presenti in questo documento, se si preferisce completare l'esercitazione dettagliata.

In questa sezione verranno eseguite le attività seguenti:

1. Creare un servizio Active Directory
1. Creare un'applicazione di Active Directory
1. Configurare l'applicazione di Active Directory registrando i dati dell'applicazione client di esempio scaricata
1. Caricare l'applicazione client di esempio con i valori che verranno usati per ottenere l'autorizzazione per le richieste

> [AZURE.NOTE]Questi collegamenti forniscono sfondo sull'utilizzo di Azure Active Directory per autenticare le richieste client al gestore di risorse: [Azureresourcemanager](http://msdn.microsoft.com/library/azure/dn790568.aspx), [richiede l'autenticazione di gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx), e [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

###Creare un servizio Active Directory

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).

2. Scorrere il riquadro di spostamento a sinistra e fare clic su **Active Directory**.

4. Fare clic su **nuovo** per aprire **servizi App** | **Active Directory**. In questo passaggio si creerà un nuovo servizio Active Directory. Questo servizio ospiterà l'applicazione di Active Directory che verrà definita nei passaggi successivi. La creazione di un nuovo servizio consente di isolare l'esercitazione dalle altre applicazioni che potrebbero già essere ospitate in Azure.

5. Fare clic su **Directory** | **Creazione personalizzata**.

6. Immettere il nome, il dominio e la posizione geografica del servizio. Il dominio deve essere univoco. Fare clic sul segno di spunta per creare il servizio.

     ![][5]

###Creare una nuova applicazione di Active Directory per questo servizio

1. Selezionare il servizio Active Directory "SearchTutorial" appena creato.

2. Nel menu principale fare clic su **applicazioni**.
 
3. Fare clic su **aggiungere un'applicazione**. Un'applicazione di Active Directory archivia le informazioni sulle applicazioni client che verranno usate come provider di identità.
 
4. Scegliere **aggiunta di un'applicazione che l'organizzazione sta sviluppando**. Questa opzione fornisce le impostazioni di registrazione per le applicazioni che non vengono pubblicate nella raccolta di applicazioni. Poiché l'applicazione client non fa parte della raccolta di applicazioni, questa è la scelta giusta per questa esercitazione.

     ![][6]
 
5. Immettere un nome, ad esempio "Azure-Search-Manager".

6. Scegliere **l'applicazione client nativa** per tipo di applicazione. Si tratta dell'opzione corretta per l'applicazione di esempio poiché è un'applicazione client (console) di Windows e non un'applicazione Web.

     ![][7]
 
7. In URI di reindirizzamento immettere "http://localhost/Azure-Search-Manager-App". Si tratta di un URI a cui Azure Active Directory reindirizzerà l'agente utente in risposta a una richiesta di autorizzazione OAuth 2.0. Il valore non deve necessariamente essere un endpoint fisico, ma un URI valido.

    Ai fini di questa esercitazione, è possibile immettere qualsiasi valore, ma il valore immesso diventerà un input obbligatorio per la connessione amministrativa nell'applicazione di esempio.
 
7. Fare clic sul segno di spunta per creare l'applicazione di Active Directory. Nel pannello di navigazione sinistro verrà visualizzato "Azure-Search-Manager-App".

###Configurare l'applicazione di Active Directory
 
9. Fare clic sull'applicazione di Active Directory, "Azure-Search-Manager-App", appena creata. Viene elencata nel pannello di navigazione sinistro.

10. Fare clic su **Configura** nel menu superiore.
 
11. Scorrere fino autorizzazioni e selezionare **API di gestione Azure**. In questo passaggio specificare l'API, in questo caso l'API di Gestione risorse di Azure, a cui l'applicazione client deve accedere, insieme al livello di accesso necessario.

12. In Autorizzazioni delegate scegliere l'elenco a discesa elenco e selezionare **Gestione servizi di accesso Azure (anteprima**)
 
     ![][8]
 
13. Salvare le modifiche.

Tenere aperta la pagina di configurazione dell'applicazione. Nel passaggio successivo sarà necessario copiare i valori da questa pagina e immetterli nell'applicazione di esempio.

###Caricare il programma dell'applicazione di esempio con i valori di registrazione e sottoscrizione

In questa sezione verrà illustrato come modificare la soluzione in Visual Studio, sostituendo i valori validi ottenuti dal portale. I valori da aggiungere vengono visualizzati nella parte superiore del file Program.cs:

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Se non ancora [scaricata l'applicazione di esempio da Codeplex](https://azuresearchmgmtapi.codeplex.com/), è necessario per questo passaggio.

1. Aprire il **ManagementAPI.sln** in Visual Studio..

2. Aprire Program.cs.

3. Fornire `ClientId`. Dalla pagina di configurazione dell'applicazione di Active Directory nel portale, lasciata aperta dal passaggio precedente, copiare l'ID client e incollarlo in Program.cs.

4. Fornire `RedirectUrl`. Copiare l'URI di reindirizzamento dalla stessa pagina del portale e incollarlo in Program.cs.

	![][9]

5. Fornire `TenantID.`
	- Tornare ad Active Directory | SearchTutorial (servizio). 
	- Fare clic su **applicazioni** nella barra superiore. 
	- Fare clic su **Visualizza endpoint** nella parte inferiore della pagina. 
	- Copiare l'endpoint di autorizzazione OAUTH 2.0 nella parte inferiore dell'elenco. 
	- Incollare l'endpoint in TenantID, rimuovendo il valore di tutti i parametri URI tranne l'ID tenant.

    Supponendo di avere "https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0", eliminare tutto tranne "55e324c7-1656-4afe-8dc3-43efcd4ffa50".

	![][10]

6. Fornire `SubscriptionID`.
	- Andare nella pagina principale del portale.
	- Fare clic su **impostazioni** nella parte inferiore del riquadro di spostamento a sinistra.
	- Dalla scheda Sottoscrizioni copiare l'ID sottoscrizione e incollarlo in Program.cs.

7. Salvare e quindi compilare la soluzione.


##Esplorare l'applicazione

Aggiungere un punto di interruzione alla prima chiamata al metodo in modo da poter eseguire gli altri passaggi del programma. Premere **F5** per eseguire l'applicazione, quindi premere **F11** per eseguire il codice.

L'applicazione di esempio crea un servizio Ricerca di Azure gratuito per una sottoscrizione di Azure esistente. Se esiste già un servizio gratuito per la sottoscrizione, l'applicazione di esempio non riuscirà. È consentito un solo servizio di ricerca gratuito per ogni sottoscrizione.

1. Aprire Program.cs da Esplora soluzioni e passare alla funzione Main(string void). 
 
3. Si noti che **ExecuteArmRequest** viene utilizzato per eseguire le richieste all'endpoint di gestione risorse di Azure, `https://management.azure.com/subscriptions` per un oggetto specificato `subscriptionID`. Questo metodo viene usato in tutto il programma per eseguire le operazioni con l'API di Gestione risorse o con l'API di gestione di Ricerca di Azure.

3. Le richieste inviate a Gestione risorse di Azure devono essere autenticate e autorizzate. Ciò avviene utilizzando il **GetAuthorizationHeader** metodo, chiamato dal **ExecuteArmRequest** metodo preso in prestito da [richiede l'autenticazione di gestione risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx). Si noti che **GetAuthorizationHeader** chiamate `https://management.core.windows.net` per ottenere un token di accesso.

4. Viene richiesto di eseguire l'accesso con un nome utente e una password validi per la sottoscrizione.

5. Un nuovo servizio Ricerca di Azure viene quindi registrato con il provider di Gestione risorse di Azure. Anche questa è la **ExecuteArmRequest** metodo, utilizzato questa volta per creare il servizio di Ricerca in Azure per la sottoscrizione tramite `providers/Microsoft.Search/register`..

6. Il resto del programma utilizza il [API REST di gestione di Ricerca di Azure](http://msdn.microsoft.com/library/dn832684.aspx). Si noti che il `api-version` per questa API è diversa dalla versione api di gestione risorse di Azure.ure. Ad esempio, `/listAdminKeys?api-version=2014-07-31-Preview` viene illustrato il `api-version` dell'API REST gestione Ricerca di Azure.

	La serie successiva di operazioni consente di recuperare la definizione del servizio appena creato, le chiavi api di amministrazione, di rigenerare e recuperare le chiavi, di modificare la replica e la partizione e infine di eliminare il servizio.

	Se si modifica il numero di partizioni o di repliche del servizio e si usa una versione gratuita, questa azione non riuscirà. Solo con la versione standard è possibile aggiungere altre partizioni e repliche.

	L'eliminazione del servizio è l'ultima operazione.

##Passaggi successivi

Dopo avere completato questa esercitazione, è possibile volere altre informazioni sulla gestione dei servizi o sull'autenticazione con il servizio Active Directory.

- Altre informazioni sull'integrazione di un'applicazione client con Active Directory. Vedere [integrazione di applicazioni in Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Informazioni sulle altre operazioni di gestione dei servizi in Azure. Vedere [gestione dei servizi](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 

<!---HONumber=August15_HO6-->