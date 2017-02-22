---
title: Aggiungere il connettore Utenti di Office 365 alle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Office 365 Users con i parametri dell&quot;API REST
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2e4af1c9369a97d518d027dc4679b9f01ca53d4c


---
# <a name="get-started-with-the-office-365-users-connector"></a>Introduzione al connettore Office 365 Users
Connettersi a Office 365 Users per ottenere profili, cercare utenti e altro ancora. 

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.
> 
> 

Con Office 365 Users è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Office 365 Users. 
* Usare azioni per ottenere i dipendenti diretti, il profilo utente di un manager e altro ancora. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, ottenere i dipendenti diretti di una persona e quindi sfruttare queste informazioni per aggiornare un database SQL Azure. 

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Nel connettore Office 365 Users sono disponibili le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni |
| --- | --- |
| Nessuno |<ul><li>Recupera manager</li><li>Recupera il mio profilo</li><li>Recupera i dipendenti diretti</li><li>Recupera profilo utente</li><li>Ricerca utenti</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML. 

## <a name="create-a-connection-to-office-365-users"></a>Creare una connessione a Office 365 Users
Quando si aggiunge questo connettore alle app per la logica, è necessario accedere all'account Office 365 Users e consentire alle app per la logica di connettersi all'account.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Dopo aver creato la connessione, immettere le proprietà di Office 365 Users, ad esempio l'ID utente. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

> [!TIP]
> È possibile usare la stessa connessione di Office 365 Users in altre app per la logica.
> 
> 

## <a name="office-365-users-rest-api-reference"></a>Informazioni di riferimento sull'API REST di Office 365 Users
Si applica alla versione 1.0.

### <a name="get-my-profile"></a>Recupera il mio profilo
Recupera il profilo dell'utente corrente.  
```GET: /users/me``` 

Non sono disponibili parametri per questa chiamata.

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="get-user-profile"></a>Recupera profilo utente
Recupera il profilo di un utente specifico.  
```GET: /users/{userId}``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| userId |string |Sì |path |Nessuno |Nome dell'entità utente o ID di posta elettronica |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="get-manager"></a>Recupera manager
Recupera il profilo utente del manager dell'utente specificato.  
```GET: /users/{userId}/manager``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| userId |string |Sì |path |Nessuno |Nome dell'entità utente o ID di posta elettronica |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="get-direct-reports"></a>Recupera i dipendenti diretti
Recupera i dipendenti diretti.  
```GET: /users/{userId}/directReports``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| userId |string |Sì |path |Nessuno |Nome dell'entità utente o ID di posta elettronica |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

### <a name="search-for-users"></a>Ricerca utenti
Recupera i risultati di ricerca dei profili utente.  
```GET: /users``` 

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| searchTerm |string |no |query |Nessuno |Stringa di ricerca (si applica a nome visualizzato, nome, cognome, posta elettronica, nome alternativo posta elettronica e nome dell'entità utente) |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |Operazione riuscita |
| 202 |Operazione riuscita |
| 400 |RichiestaNonValida |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 500 |Internal Server Error |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni degli oggetti
#### <a name="user-user-model-class"></a>User: classe di modello utente
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| DisplayName |string |no |
| GivenName |string |no |
| Surname |string |no |
| Mail |string |no |
| MailNickname |string |no |
| TelephoneNumber |string |no |
| AccountEnabled |boolean |no |
| ID |string |yes |
| UserPrincipalName |string |no |
| Department |string |no |
| JobTitle |string |no |
| mobilePhone |string |no |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

Tornare all' [elenco di API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG



<!--HONumber=Feb17_HO3-->


