---
title: Reindirizzare l'URL di risposta/URI restrizioni e limitazioni - piattaforma delle identità Microsoft
description: Limitazioni e restrizioni di URL/reindirizzamento URL di risposta
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486233"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Restrizioni e limitazioni degli URI di reindirizzamento/URL di risposta

Un reindirizzamento URI o URL di risposta, è il percorso che il server di autorizzazione Invia l'utente a una volta l'app è stato autorizzato e concesso un codice di autorizzazione o un accesso token. Il codice o token è contenuta nel reindirizzamento URI o risposta del token è importante che si registra il percorso corretto come parte del processo di registrazione delle app.

## <a name="maximum-number-of-redirect-uris"></a>Numero massimo di URI di reindirizzamento

Nella tabella seguente mostra il numero massimo di redirect URI che è possibile aggiungere quando si registra l'app. 

| Account l'accesso | Numero massimo di URI di reindirizzamento | Descrizione |
|--------------------------|---------------------------------|-------------|
| Microsoft aziendale o dell'istituto di istruzione account nel tenant di Azure Active Directory (Azure AD) di qualsiasi organizzazione | 256 | `signInAudience` campo nel manifesto dell'applicazione è impostata su *AzureADMyOrg* o *AzureADMultipleOrgs* |
| Personale Microsoft account e di lavoro e gli account dell'istituto di istruzione | 100 | `signInAudience` campo nel manifesto dell'applicazione è impostato su *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Lunghezza massima consentita URI

È possibile usare un massimo di 256 caratteri per ogni reindirizzamento URI che si aggiunge a una registrazione dell'app.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrizioni di utilizzo di un carattere jolly negli URI

Wildcard URIs, ad esempio `https://*.contoso.com`, sono comodi ma devono essere evitate. Utilizzo dei caratteri jolly nel reindirizzamento URI presenta implicazioni di sicurezza. In base alla specifica di OAuth 2.0 ([sezione 3.1.2 di RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), un URI dell'endpoint di reindirizzamento deve essere un URI assoluto. 

Il modello di applicazione di Azure AD non supporta caratteri jolly gli URI per le app che sono configurate per accedere gli account Microsoft personali e di lavoro o dell'istituto di istruzione. Tuttavia, con caratteri jolly gli URI sono consentiti per le app che sono configurate per l'accesso lavoro o dell'istituto di istruzione attualmente gli account nel tenant di Azure AD dell'organizzazione. 
 
> [!NOTE]
> Il nuovo [registrazioni per l'App](https://go.microsoft.com/fwlink/?linkid=2083908) esperienza non consente agli sviluppatori di aggiungere con caratteri jolly URI nell'interfaccia utente. Aggiunta di carattere jolly URI per le app che accedi lavoro o scuola account è supportato solo tramite l'editor del manifesto dell'app. In futuro, nuove app non è possibile usare caratteri jolly nell'URI di reindirizzamento. Tuttavia, le applicazioni meno recenti che contengono i caratteri jolly nel reindirizzamento URI continueranno a funzionare.

Se lo scenario richiede che più URI di reindirizzamento rispetto al limite massimo consentito, invece di aggiungere un carattere jolly URI di reindirizzamento, considerare uno degli approcci seguenti.

### <a name="use-a-state-parameter"></a>Usare un parametro di stato

Se si dispone di un numero di sottodomini e se lo scenario richiede di reindirizzare gli utenti al termine dell'autenticazione alla stessa pagina in cui partito, con un parametro di stato può essere utile. 

In questo approccio:

1. Creare un URI di reindirizzamento "condivisi" per ogni applicazione di elaborare i token di sicurezza che si riceve dall'endpoint di autorizzazione.
1. L'applicazione può inviare parametri specifici dell'applicazione (ad esempio in cui l'utente ha avuto origine o qualsiasi elemento, ad esempio informazioni personalizzate distintive dell'URL di sottodominio) nel parametro state. Quando si usa un parametro di stato, salvaguardarsi da protezione CSRF come specificato nella [sezione 10.12 del RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. I parametri specifici dell'applicazione includerà tutte le informazioni necessarie per l'applicazione eseguire il rendering corretto esperienza dell'utente, vale a dire, costruire lo stato dell'applicazione appropriata. Le strisce di endpoint di autorizzazione di Azure AD HTML dal parametro di stato pertanto assicurarsi di non passano HTML contenuti in questo parametro.
1. Quando Azure AD invia una risposta all'URI di reindirizzamento "condivisi", il parametro di stato invierà all'applicazione.
1. L'applicazione può quindi utilizzare il valore nel parametro di stato per determinare quali URL per un'ulteriore inviare all'utente. Assicurarsi che convalidare per la protezione CSRF.

> [!NOTE]
> Questo approccio consente a un client compromesso modificare i parametri aggiuntivi inviati nel parametro di stato, in tal modo reindirizzando l'utente a un URL diverso, ovvero il [aprire threat redirector](https://tools.ietf.org/html/rfc6819#section-4.2.4) descritto in RFC 6819. Pertanto, il client deve proteggere questi parametri da crittografare lo stato o verificarlo con altri mezzi, ad esempio la convalida del nome di dominio nell'URI di reindirizzamento a fronte del token.

### <a name="add-redirect-uris-to-service-principals"></a>Aggiungere gli URI di reindirizzamento per le entità servizio

Un altro approccio consiste nell'aggiungere redirect URI per il [entità servizio](app-objects-and-service-principals.md#application-and-service-principal-relationship) che rappresentano la registrazione dell'app in qualsiasi tenant Azure AD. È possibile usare questo approccio quando non è possibile utilizzare un parametro di stato o lo scenario richiede è quindi necessario aggiungere di nuovo URI di reindirizzamento per la registrazione dell'app per ogni nuovo tenant che è supportare. 

## <a name="next-steps"></a>Passaggi successivi

- Scopri il [manifesto dell'applicazione](reference-app-manifest.md)
