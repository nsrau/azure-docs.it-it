---
title: Restrizioni degli URI di reindirizzamento e URL di risposta - Microsoft Identity Platform | Azure
description: Restrizioni e limitazioni degli URL di risposta/URI di reindirizzamento
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: b7aefc54a20e23ae969750532e7e3bc824f69c56
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725313"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Restrizioni e limitazioni degli URI di reindirizzamento/URL di risposta

Un URI di reindirizzamento o un URL di risposta è il percorso a cui il server di autorizzazione invia l'utente dopo che l'app è stata autorizzata correttamente e ha ottenuto un codice di autorizzazione o un token di accesso. Il codice o il token è contenuto nell'URI di reindirizzamento o nel token di risposta, quindi è importante registrare il percorso corretto nell’ambito del processo di registrazione dell'app.

 Agli URL di risposta si applicano le restrizioni seguenti:

* L'URL di risposta deve iniziare con lo schema `https`.

* L'URL di risposta rileva la distinzione tra maiuscole e minuscole. Le maiuscole e le minuscole devono corrispondere a quelle nel percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc`, non specificare `.../ABC/response-oidc` nell'URL di risposta. Poiché il Web browser rileva la distinzione tra maiuscole e minuscole nei percorsi, è possibile che i cookie associati a `.../abc/response-oidc` vengano esclusi se reindirizzati all'URL `.../ABC/response-oidc` senza la corrispondenza tra maiuscole e minuscole.
    
## <a name="maximum-number-of-redirect-uris"></a>Numero massimo di URI di reindirizzamento

La tabella seguente mostra il numero massimo di URI di reindirizzamento che è possibile aggiungere quando si registra l'app.

| Account visitati | Numero massimo di URI di reindirizzamento | Descrizione |
|--------------------------|---------------------------------|-------------|
| Account Microsoft aziendali o dell'istituto di istruzione nel tenant di Azure Active Directory (Azure AD) di qualsiasi organizzazione | 256 | Il campo `signInAudience` nel manifesto dell'applicazione è impostato su *AzureADMyOrg* o su *AzureADMultipleOrgs* |
| Account Microsoft personali e account aziendali e dell'istituto di istruzione | 100 | Il campo `signInAudience` nel manifesto dell'applicazione è impostato su *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Lunghezza massima dell'URI

È possibile usare un massimo di 256 caratteri per ogni URI di reindirizzamento aggiunto a una registrazione di app.

## <a name="supported-schemes"></a>Schemi supportati
Il modello di applicazione Azure AD attualmente supporta sia gli schemi HTTP che HTTPS per le app che accedono ad account Microsoft aziendali o dell'istituto di istruzione nel tenant di Azure Active Directory (Azure AD) di qualsiasi organizzazione. Il campo `signInAudience` nel manifesto dell'applicazione è impostato su *AzureADMyOrg* o su *AzureADMultipleOrgs*. Per le app che accedono ad account Microsoft personali e account aziendali o dell'istituto di istruzione, dove `signInAudience` è impostato su *AzureADandPersonalMicrosoftAccount*, è consentito solo lo schema HTTPS.

> [!NOTE]
> La nuova esperienza di [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) non consente agli sviluppatori di aggiungere URI con lo schema HTTP nell'interfaccia utente. La loro aggiunta per le app che accedono ad account aziendali o dell'istituto di istruzione è supportata solo tramite l'editor del manifesto dell'applicazione. In futuro, le nuove app non saranno in grado di usare gli schemi HTTP nell'URI di reindirizzamento. Tuttavia, le app meno recenti che contengono schemi HTTP negli URI di reindirizzamento continueranno a funzionare. Gli sviluppatori devono quindi usare gli schemi HTTPS negli URI di reindirizzamento.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrizioni relative all'uso di un carattere jolly negli URI

Gli URI con caratteri jolly, ad esempio `https://*.contoso.com`, sono comodi, ma è bene evitarli. L'uso di caratteri jolly nell'URI di reindirizzamento presenta implicazioni riguardo alla sicurezza. In base alla specifica OAuth 2.0 ([sezione 3.1.2 del documento RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), un URI di endpoint di reindirizzamento deve essere un URI assoluto. 

Il modello di applicazione Azure AD non supporta gli URI con caratteri jolly per le app configurate per l'accesso ad account Microsoft personali e aziendali o dell'istituto di istruzione. Tuttavia, gli URI con caratteri jolly sono oggi consentiti per le app configurate per l'accesso ad account aziendali o dell'istituto di istruzione nel tenant di Azure AD di un'organizzazione. 
 
> [!NOTE]
> La nuova esperienza di [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) non consente agli sviluppatori di aggiungere URI con caratteri jolly nell'interfaccia utente. La loro aggiunta per le app che accedono ad account aziendali o dell'istituto di istruzione è supportata solo tramite l'editor del manifesto dell'applicazione. In futuro, le nuove app non saranno in grado di usare i caratteri jolly nell'URI di reindirizzamento. Tuttavia, le app meno recenti che contengono caratteri jolly negli URI di reindirizzamento continueranno a funzionare.

Se lo scenario richiede più URI di reindirizzamento rispetto al limite massimo consentito, invece di aggiungere un URI di reindirizzamento con caratteri jolly, prendere in considerazione l'approccio seguente.

### <a name="use-a-state-parameter"></a>Uso di un parametro di stato

Se si dispone di vari sottodomini e se lo scenario richiede di reindirizzare gli utenti correttamente autenticati alla stessa pagina da cui hanno iniziato, potrebbe essere utile usare un parametro di stato. 

In questo approccio:

1. Creare un URI di reindirizzamento "condiviso" per ogni applicazione per elaborare i token di sicurezza che si ricevono dall'endpoint di autorizzazione.
1. L'applicazione può inviare parametri specifici dell'applicazione (ad esempio, l'URL del sottodominio da cui l'utente ha iniziato o qualcosa di simile a informazioni di personalizzazione) nel parametro di stato. Quando si usa un parametro di stato, prestare attenzione alla protezione CSRF come specificato nella sezione [10.12 del documento RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12). 
1. I parametri specifici dell’applicazione includono tutte le informazioni necessarie all’applicazione per funzionare correttamente, vale a dire, creare lo stato dell'applicazione appropriato. L’endpoint di autorizzazione di Azure AD esegue lo striping del codice HTML dal parametro di stato. È quindi necessario assicurarsi che non si stia passando contenuto HTML in questo parametro.
1. Quando Azure AD invia una risposta all’URI di reindirizzamento "condiviso", restituisce il parametro di stato all’applicazione.
1. L’applicazione può quindi usare il valore del parametro di stato per determinare a quale URL indirizzare l’utente. Assicurarsi di convalidare la protezione CSRF.

> [!NOTE]
> Questo approccio consente a un client compromesso di modificare i parametri aggiuntivi inviati nel parametro di stato, reindirizzando quindi l'utente a un URL diverso. Questa è la [minaccia del redirector aperto](https://tools.ietf.org/html/rfc6819#section-4.2.4) descritta nella specifica RFC 6819. Pertanto, il client deve proteggere questi parametri crittografando lo stato o verificandolo con altri mezzi, ad esempio con la convalida del nome di dominio nell'URI di reindirizzamento rispetto al token.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Manifesto dell'applicazione](reference-app-manifest.md)
