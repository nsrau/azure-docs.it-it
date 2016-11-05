---
title: Aggiungere il connettore Utenti di Office 365 alle app per la logica | Microsoft Docs
description: Panoramica del connettore Office 365 Users con i parametri dell'API REST
services: ''
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# Introduzione al connettore Office 365 Users
Connettersi a Office 365 Users per ottenere profili, cercare utenti e altro ancora.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.
> 
> 

Con Office 365 Users è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Office 365 Users.
* Usare azioni per ottenere i dipendenti diretti, il profilo utente di un manager e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, ottenere i dipendenti diretti di una persona e quindi sfruttare queste informazioni per aggiornare un database SQL Azure.

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Nel connettore Office 365 Users sono disponibili le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni |
| --- | --- |
| None |<ul><li>Get manager (Ottieni manager)</li><li>Get my profile (Ottieni profilo)</li><li>Ottieni dipendenti diretti</li><li>Get user profile (Ottieni profilo utente) </li><li>Cerca utenti</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Office 365 Users
Quando si aggiunge questo connettore alle app per la logica, è necessario accedere all'account Office 365 Users e consentire alle app per la logica di connettersi all'account.

> [!INCLUDE [Passaggi per la creazione di una connessione a Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Dopo aver creato la connessione, immettere le proprietà di Office 365 Users, ad esempio l'ID utente. Il **riferimento all'API REST** in questo articolo descrive tali proprietà.

> [!TIP]
> È possibile usare la stessa connessione di Office 365 Users in altre app per la logica.
> 
> 

## Informazioni di riferimento sull'API REST di Office 365 Users
Si applica alla versione 1.0.

### Recupera il mio profilo
Recupera il profilo dell'utente corrente. ```GET: /users/me```

Non sono disponibili parametri per questa chiamata.

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### Recupera profilo utente
Recupera il profilo di un utente specifico. ```GET: /users/{userId}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| userId |string |yes |path |nessuno |Nome dell'entità utente o ID di posta elettronica |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### Recupera manager
Recupera il profilo utente del manager dell'utente specificato. ```GET: /users/{userId}/manager```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| userId |string |yes |path |nessuno |Nome dell'entità utente o ID di posta elettronica |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### Recupera i dipendenti diretti
Recupera i dipendenti diretti. ```GET: /users/{userId}/directReports```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| userId |stringa |yes |path |nessuno |Nome dell'entità utente o ID di posta elettronica |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### Ricerca utenti
Recupera i risultati della ricerca di profili utente. ```GET: /users```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| searchTerm |stringa |no |query |nessuno |Stringa di ricerca (si applica a nome visualizzato, nome, cognome, posta elettronica, nome alternativo posta elettronica e nome dell'entità utente) |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## Definizioni oggetti
#### User: classe di modello utente
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| DisplayName |string |no |
| GivenName |stringa |no |
| Surname |string |no |
| Mail |stringa |no |
| MailNickname |stringa |no |
| TelephoneNumber |string |no |
| AccountEnabled |boolean |no |
| ID |stringa |sì |
| UserPrincipalName |stringa |no |
| Department |stringa |no |
| JobTitle |stringa |no |
| mobilePhone |stringa |no |

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

<!---HONumber=AcomDC_0824_2016-->