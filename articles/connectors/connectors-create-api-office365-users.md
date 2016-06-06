<properties
    pageTitle="Aggiungere il connettore Office 365 Users a PowerApps Enterprise o alle app per la logica | Microsoft Azure"
    description="Panoramica del connettore Office 365 Users con i parametri dell'API REST"
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
ms.date="05/18/2016"
ms.author="deonhe"/>

# Introduzione al connettore Office 365 Users

Connettersi a Office 365 Users per ottenere profili, cercare utenti e altro ancora. Il connettore Office 365 Users può essere usato da:

- App per la logica 
- PowerApps

> [AZURE.SELECTOR]
- [App per la logica](../articles/connectors/connectors-create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

&nbsp;

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.


Con Office 365 Users è possibile:

- Creare il flusso aziendale in base ai dati ottenuti da Office 365 Users. 
- Usare azioni per ottenere i dipendenti diretti, il profilo utente di un manager e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, ottenere i dipendenti diretti di una persona e quindi sfruttare queste informazioni per aggiornare un database SQL Azure. 
- Aggiungere il connettore Office 365 Users a PowerApps Enterprise. in modo che gli utenti possano usarlo nelle proprie app. 

Per informazioni su come aggiungere un connettore in PowerApps Enterprise, vedere la pagina relativa alla [registrazione dei connettori in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni

Nel connettore Office 365 Users sono disponibili le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni|
| --- | --- |
|Nessuno | <ul><li>Get manager</li><li>Get my profile</li><li>Get direct reports</li><li>Get user profile</li><li>Search for users</li></ul>|

Tutti i connettori supportano dati nei formati JSON e XML.


## Creare una connessione a Office 365 Users

Quando si aggiunge questo connettore alle app per la logica, è necessario accedere all'account Office 365 Users e consentire alle app per la logica di connettersi all'account.

>[AZURE.INCLUDE [Passaggi per la creazione di una connessione a Office 365 Users](../../includes/connectors-create-api-office365users.md)]

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

|Nome proprietà | Tipo di dati |Obbligatorio
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

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0525_2016-->