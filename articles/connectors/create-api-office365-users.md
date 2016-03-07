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
ms.date="02/22/2016"
ms.author="deonhe"/>

# Introduzione all'API di Office 365 Users

Il provider di connessione di Office 365 Users consente di accedere ai profili utente nell'organizzazione usando l'account Office 365. Permette di eseguire diverse azioni, ad esempio ottenere il proprio profilo, il profilo di un utente, il profilo del manager di un utente o dei suoi dipendenti diretti e anche aggiornare il profilo di un utente.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica. Per la versione dello schema 2014-12-01-preview, fare clic su [API di Office 365](../app-service-logic/app-service-logic-connector-office365.md).


Con Office 365 Users è possibile:

* Compilare app per la logica
* Compilare app avanzate

Per informazioni su come aggiungere un'API in PowerApps Enterprise, vedere l'articolo relativo alla [registrazione di un'API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Informazioni su trigger e azioni

È possibile usare l'API Office 365 Users come azione. Non sono disponibili trigger. Tutte le API supportano dati nei formati JSON e XML.

 Nell'API di Office 365 Users sono disponibili le azioni e/o i trigger seguenti:

### Azioni di Office 365 Users
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|MyProfile|Recupera il profilo dell'utente corrente|
|UserProfile|Recupera il profilo di un utente specifico|
|Manager|Recupera il profilo utente del manager dell'utente specificato|
|DirectReports|Recupera i dipendenti diretti|
|SearchUser|Recupera i risultati di ricerca dei profili utente|
## Creare una connessione a Office 365 Users
Per usare l'API di Office 365 Users, creare prima di tutto una **connessione**, quindi indicare i dettagli di queste proprietà:

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornire le credenziali di Office 365 Users|


>[AZURE.TIP] È possibile usare questa connessione in altre app per la logica.

## Informazioni di riferimento sull'API REST di Office 365 Users
#### Questa documentazione è relativa alla versione 1.0


### Recupera il mio profilo 


 Recupera il profilo dell'utente corrente ```GET: /users/me```

Non sono disponibili parametri per questa chiamata
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
------



### Recupera profilo utente 


 Recupera il profilo di un utente specifico ```GET: /users/{userId}```



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
------



### Recupera manager 


 Recupera il profilo utente del manager dell'utente specificato ```GET: /users/{userId}/manager```



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
------



### Recupera i dipendenti diretti 


 Recupera i dipendenti diretti ```GET: /users/{userId}/directReports```



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
------



### Ricerca utenti 


 Recupera i risultati di ricerca dei profili utente ```GET: /users```



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
------



## Definizioni degli oggetti: 

 **User**: classe di modello utente.

Proprietà obbligatorie per User:

ID

**Tutte le proprietà**:


| Nome | Tipo di dati |
|---|---|
|DisplayName|string|
|GivenName|string|
|Surname|string|
|Mail|string|
|MailNickname|string|
|TelephoneNumber|string|
|AccountEnabled|boolean|
|ID|string|
|UserPrincipalName|string|
|Department|string|
|JobTitle|string|


## Passaggi successivi
Dopo aver aggiunto l'API di Office 365 a PowerApps Enterprise, [concedere autorizzazioni agli utenti](../power-apps/powerapps-manage-api-connection-user-access.md) per l'uso dell'API nelle proprie app.

[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->