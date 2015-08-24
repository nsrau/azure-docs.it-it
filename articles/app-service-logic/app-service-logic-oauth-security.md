<properties
	pageTitle="Sicurezza OAUTH nei connettori SaaS e applicazioni API | Azure"
	description="Informazioni sulla sicurezza OAUTH nei Connettori e nelle app API nel Servizio di Azure; architettura dei microservizi:saas"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2015"
	ms.author="mandia"/>


# Informazioni sui connettori SaaS di sicurezza OAUTH
Molti Software come connettori di Servizio (SaaS), quali Facebook, Twitter, DropBox e così via, richiedono agli utenti di autorizzare l’uso del protocollo OAUTH. Quando si utilizzano questi connettori SaaS dalle App per la logica, si forniscono un'esperienza utente semplificata nella quale si fa clic su "Authorize" nella finestra di progettazione di applicazioni di logica. Quando si fa clic su **Authorize** viene chiesto di accedere in (se non è stato già fatto) e di fornire il consenso per connettersi al servizio SaaS per conto dell'utente. Quando si fornisce il consenso e l’autorizzazione, le app per la logica possono poi accedere a questi servizi SaaS.

## Creare la propria applicazione SaaS
Questa esperienza semplificata è possibile perché è stata precedentemente creata e registrata l'applicazione in tali servizi SaaS. In alcuni casi, si desidera registrare e utilizzare la propria applicazione. Questo è necessario, ad esempio, quando si desidera utilizzare questi connettori SaaS nelle applicazioni personalizzate, ad esempio in [Distribuire un'app API del connettore SaaS](app-service-api-connnect-your-app-to-saas-connector.md). In questo esempio si utilizza il connettore DropBox, ma il processo è lo stesso per tutti i connettori che si basano su OAUTH.

Anche nel contesto dell’app per la logica, è possibile utilizzare la propria applicazione invece di utilizzare l'applicazione predefinita che offriamo. Se il pulsante "Authorize" non riesce a connettersi, è possibile provare a creare la propria app. Di seguito sono elencati questi passaggi per il connettore Twitter:

1. Aprire il connettore Twitter nel portale di anteprima di Azure. Andare a **Sfoglia** > **App API**. Selezionare il connettore Twitter:![][1]

2. Selezionare **Impostazioni** > **autenticazione**:![][2]

3. Copiare il valore **Reindirizzamento URI**: ![][3]

4. Andare a [Twitter](http://apps.twitter.com) e **Creare una nuova app**. Nella proprietà **URL Callback**, incollare il valore **URI di reindirizzamento** copiato dal connettore Twitter: ![][4]
5. Quando viene creata l'app Twitter, selezionare **Chiave e token di accesso**. Copiare questi valori.
6. Nelle impostazioni di autenticazione del connettore Twitter, incollare questi valori nell’**ID Client** e **Client segreto **proprietà:![][5]  
7. Salvare le impostazioni del connettore.  

A questo punto, sarà possibile utilizzare il connettore delle App per la logica. Quando si utilizza questo connettore dalle App per la logica, si utilizza l’applicazione invece dell’applicazione predefinita.

> [AZURE.NOTE]Se si è autorizzato un'app in precedenza, potrebbe essere necessario autorizzare l'app.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

<!---HONumber=August15_HO7-->