<properties
	pageTitle="Aggiungere l'API di Office 365 Users alle app per la logica | Microsoft Azure"
	description="Panoramica dell'API di Office 365 Users con i parametri dell'API REST"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/25/2016"
ms.author="deonhe"/>

# Introduzione all'API di Office 365 Users

Connettersi a Office 365 Users per ottenere profili, cercare utenti e altro ancora. L'API di Office 365 Users può essere usata da:

- PowerApps 
- App per la logica 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione dello schema 2014-12-01-preview, fare clic su [API di Office 365](../app-service-logic/app-service-logic-connector-office365.md).


Con Office 365 Users è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Office 365 Users. 
- Usare azioni per ottenere i dipendenti diretti, il profilo utente di un manager e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, ottenere i dipendenti diretti di una persona e quindi sfruttare queste informazioni per aggiornare un database SQL Azure. 
- Aggiungere l'API di Office 365 Users in PowerApps Enterprise in modo che gli utenti possano usarla nelle proprie app. 

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Nell'API di Office 365 Users sono disponibili le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni|
| --- | --- |
|Nessuno | <ul><li>Recupera manager</li><li>Recupera il mio profilo</li><li>Recupera i dipendenti diretti</li><li>Recupera profilo utente</li><li>Ricerca utenti</li></ul>|

Tutte le API supportano dati nei formati JSON e XML.


## Creare una connessione a Office 365 Users

### Aggiungere configurazioni aggiuntive in PowerApps
Quando si aggiunge questa API a PowerApps Enterprise, immettere i valori **ID client** e **Segreto client** dell'applicazione Office 365 per Azure Active Directory (AAD). Il valore dell'**URL di reindirizzamento** viene usato anche nell'applicazione Office 365. Se non si dispone di un'applicazione Office 365, è possibile seguire la procedura riportata sotto per creare l'applicazione:

1. Nel [portale di Azure][5] aprire **Active Directory** e selezionare il nome del tenant dell'organizzazione.
2. Selezionare la scheda **Applicazioni** e selezionare **Aggiungi**: ![Applicazioni tenant ADD][7]

3. In **Aggiungi applicazione**:

	1. Immettere un **Nome** per l'applicazione.  
	2. Lasciare il tipo di applicazione come **Web**.  
	3. Selezionare **Avanti**.  

	![Aggiungere l'applicazione ADD - info app][8]

4. In **Proprietà app**:

	1. Immettere l'**URL DI ACCESSO** dell'applicazione. Poiché si intende eseguire l'autenticazione con AAD per PowerApps, impostare l'URL di accesso su \__https://login.windows.net_.
2. Immettere un valore valido di **URI ID APP** per l'app.  
	3. Selezionare **OK**.  

	![Aggiungere l'applicazione ADD - proprietà app][9]

5. Al termine si apre la nuova applicazione AAD. Selezionare **Configura**: ![App ADD Contoso][10]

6. Nella sezione **OAuth2** impostare l'**URL di risposta** sull'URL di reindirizzamento visualizzato quando è stata aggiunta l'API di Office 365 Users nel portale di Azure. Selezionare **Aggiungi applicazione**: ![Configurare l'applicazione ADD Contoso][11]

7. In **Autorizzazioni per altre applicazioni** selezionare **API di Office 365 Unified (Anteprima)** e selezionare **OK**:

	Nella pagina di configurazione, notare che _API di Office 365 Unified (Anteprima)_ viene aggiunto all’elenco _Autorizzazioni per altre applicazioni_.

8. Per **Office 365 Exchange Online** selezionare **Autorizzazioni delegate** e l'autorizzazione **Leggere i profili di base di tutti gli utenti**.

Viene creata una nuova applicazione Azure Active Directory A questo punto, copiare e incollare i valori di **ID client** e **Segreto client** nella configurazione dell'API Office 365 Users nel portale di Azure.

Per alcune utili informazioni sulle applicazioni AAD vedere [Come vengono aggiunte le applicazioni in Azure AD e perché](../active-directory/active-directory-how-applications-are-added.md).


### Aggiungere configurazioni aggiuntive nelle app per la logica
Quando si aggiunge questa API alle app per la logica, è necessario accedere all'account Office 365 Users e consentire alle app per la logica di connettersi all'account.

1. Accedere all'account Office 365 Users.
2. Consentire alle app per la logica di connettersi e di usare l'account Office 365. 

Dopo aver creato la connessione, immettere le proprietà di Office 365 Users, ad esempio l'ID utente. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

>[AZURE.TIP] È possibile usare la stessa connessione di Office 365 Users in altre app per la logica.


## Informazioni di riferimento sull'API REST di Office 365 Users
Si applica alla versione 1.0.

### Recupera il mio profilo 
Recupera il profilo dell'utente corrente. ```GET: /users/me```

Non sono disponibili parametri per questa chiamata.

#### Risposta

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|202|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Recupera profilo utente 
Recupera il profilo di un utente specifico. ```GET: /users/{userId}```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|userId|string|yes|path|nessuno|Nome dell'entità utente o ID di posta elettronica|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|202|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|


### Recupera manager 
Recupera il profilo utente del manager dell'utente specificato. ```GET: /users/{userId}/manager```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|userId|string|yes|path|nessuno|Nome dell'entità utente o ID di posta elettronica|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|202|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|



### Recupera i dipendenti diretti 
Recupera i dipendenti diretti. ```GET: /users/{userId}/directReports```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|userId|string|yes|path|nessuno|Nome dell'entità utente o ID di posta elettronica|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|202|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|



### Ricerca utenti 
Recupera i risultati di ricerca dei profili utente. ```GET: /users```

| Nome| Tipo di dati|Obbligatorio|Posizione|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|searchTerm|string|no|query|nessuno|Stringa di ricerca (si applica a nome visualizzato, nome, cognome, posta elettronica, nome alternativo posta elettronica e nome dell'entità utente)|

#### Response

|Nome|Descrizione|
|---|---|
|200|Operazione riuscita|
|202|Operazione riuscita|
|400|RichiestaNonValida|
|401|Non autorizzata|
|403|Accesso negato|
|500|Internal Server Error|
|default|Operazione non riuscita.|



## Definizioni degli oggetti

#### User: classe di modello utente

| Nome | Tipo di dati |Obbligatorio
|---|---|---|
|DisplayName|string|no|
|GivenName|string|no|
|Surname|string|no|
|Mail|string|no|
|MailNickname|string|no|
|TelephoneNumber|string|no|
|AccountEnabled|boolean|no|
|ID|string|yes
|UserPrincipalName|string|no|
|Department|string|no|
|JobTitle|string|no|
|mobilePhone|string|no|


## Passaggi successivi
Dopo aver aggiunto l'API di Office 365 a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0302_2016-->