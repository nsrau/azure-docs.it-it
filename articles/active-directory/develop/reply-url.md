---
title: URI di reindirizzamento & restrizioni dell'URL di risposta - Piattaforma di identità Microsoft Azure
description: Restrizioni URls di risposta/reindirizzamento & limitazioni
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656739"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Restrizioni e limitazioni degli URI di reindirizzamento/URL di risposta

Un URI di reindirizzamento, o URL di risposta, è il percorso a cui il server di autorizzazione invierà all'utente una volta che l'app è stata autorizzata correttamente e a cui è stato concesso un codice di autorizzazione o un token di accesso. Il codice o il token è contenuto nell'URI di reindirizzamento o nel token di risposta, pertanto è importante registrare la posizione corretta come parte del processo di registrazione dell'app.

 Per gli URL di risposta si applicano le restrizioni seguenti:

    * L'URL di risposta `https`deve iniziare con lo schema .
    * L'URL di risposta fa distinzione tra maiuscole e minuscole. Il caso deve corrispondere al caso del percorso URL dell'applicazione in esecuzione. Ad esempio, se l'applicazione include `.../abc/response-oidc`come parte `.../ABC/response-oidc` del relativo percorso , non specificare nell'URL di risposta. Poiché il browser Web considera i percorsi `.../abc/response-oidc` come maiuscole/minuscole, i cookie associati `.../ABC/response-oidc` potrebbero essere esclusi se reindirizzati all'URL senza corrispondenza tra maiuscole e minuscole.
    
## <a name="maximum-number-of-redirect-uris"></a>Numero massimo di URI di reindirizzamento

La tabella seguente mostra il numero massimo di URI di reindirizzamento che puoi aggiungere quando registri l'app.

| Account connessi | Numero massimo di URI di reindirizzamento | Descrizione |
|--------------------------|---------------------------------|-------------|
| Account aziendali o dell'istituto di istruzione Microsoft nel tenant di Azure Active Directory (Azure AD) di qualsiasi organizzazione | 256 | `signInAudience`nel manifesto dell'applicazione è impostato su *AzureADMyOrg* o *AzureADMultipleOrgs* |
| Account Microsoft personali e account aziendali e dell'istituto di istruzione | 100 | `signInAudience`nel manifesto dell'applicazione è impostato su *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Lunghezza massima URI

Puoi usare un massimo di 256 caratteri per ogni URI di reindirizzamento aggiunto alla registrazione di un'app.

## <a name="supported-schemes"></a>Schemi supportati
Il modello di applicazione di Azure AD supporta oggi entrambi gli schemi HTTP e HTTPS per le app che accedono agli account aziendali o dell'istituto di istruzione Microsoft nel tenant di Azure Active Directory (Azure AD) di qualsiasi organizzazione. Il `signInAudience` campo nel manifesto dell'applicazione è impostato su *AzureADMyOrg* o *AzureADMultipleOrgs*. Per le app che accedono agli account Microsoft `signInAudience` personali e agli account aziendali e dell'istituto di istruzione (impostato su *AzureADandPersonalMicrosoftAccount*) è consentito solo lo schema HTTPS.

> [!NOTE]
> La nuova esperienza di [registrazione](https://go.microsoft.com/fwlink/?linkid=2083908) di app non consente agli sviluppatori di aggiungere URI con schema HTTP nell'interfaccia utente. L'aggiunta di URI HTTP per le app che accedono ad account aziendali o dell'istituto di istruzione è supportata solo tramite l'editor del manifesto dell'app. In futuro, le nuove app non saranno in grado di utilizzare schemi HTTP nell'URI di reindirizzamento. Tuttavia, le app meno recenti che contengono schemi HTTP negli URI di reindirizzamento continueranno a funzionare. Gli sviluppatori devono utilizzare schemi HTTPS negli URI di reindirizzamento.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrizioni relative all'utilizzo di un carattere jolly negli URI

Gli URI con `https://*.contoso.com`caratteri jolly, ad esempio , sono convenienti, ma devono essere evitati. L'utilizzo di caratteri jolly nell'URI di reindirizzamento ha implicazioni sulla sicurezza. In base alla specifica OAuth 2.0[(sezione 3.1.2 di RFC 6749),](https://tools.ietf.org/html/rfc6749#section-3.1.2)un URI dell'endpoint di reindirizzamento deve essere un URI assoluto. 

Il modello di applicazione di Azure AD non supporta gli URI con caratteri jolly per le app configurate per l'accesso di account Microsoft personali e di account aziendali o dell'istituto di istruzione. Tuttavia, gli URI con caratteri jolly sono consentiti per le app configurate per l'accesso di account aziendali o dell'istituto di istruzione nel tenant di Azure AD di un'organizzazione oggi. 
 
> [!NOTE]
> La nuova esperienza di [registrazione](https://go.microsoft.com/fwlink/?linkid=2083908) di app non consente agli sviluppatori di aggiungere URI con caratteri jolly nell'interfaccia utente. L'aggiunta di URI wilcard per le app che accedono agli account aziendali o dell'istituto di istruzione è supportata solo tramite l'editor del manifesto dell'app. In futuro, le nuove app non saranno in grado di usare i caratteri jolly nell'URI di reindirizzamento. Tuttavia, le app meno recenti che contengono caratteri jolly negli URI di reindirizzamento continueranno a funzionare.

Se lo scenario richiede più URI di reindirizzamento rispetto al limite massimo consentito, anziché aggiungere un URI di reindirizzamento con caratteri jolly, considerare l'approccio seguente.

### <a name="use-a-state-parameter"></a>Usare un parametro di statoUse a state parameter

Se si dispone di un numero di sottodomini e se lo scenario richiede di reindirizzare gli utenti dopo l'autenticazione corretta alla stessa pagina in cui sono stati avviati, l'utilizzo di un parametro di stato potrebbe essere utile. 

In questo approccio:

1. Creare un URI di reindirizzamento "condiviso" per ogni applicazione per elaborare i token di sicurezza ricevuti dall'endpoint di autorizzazione.
1. L'applicazione può inviare parametri specifici dell'applicazione (ad esempio l'URL del sottodominio in cui l'utente ha avuto origine o qualsiasi elemento simile alle informazioni di personalizzazione) nel parametro state. Quando si utilizza un parametro di stato, evitare la protezione CSRF come specificato nella [sezione 10.12 di RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. I parametri specifici dell'applicazione includeranno tutte le informazioni necessarie all'applicazione per eseguire il rendering dell'esperienza corretta per l'utente, ovvero costruire lo stato dell'applicazione appropriato. L'endpoint di autorizzazione di Azure AD rimuove il codice HTML dal parametro state, pertanto assicurarsi di non passare contenuto HTML in questo parametro.
1. Quando Azure AD invia una risposta all'URI di reindirizzamento "condiviso", invierà il parametro state all'applicazione.
1. L'applicazione può quindi utilizzare il valore nel parametro state per determinare l'URL a cui inviare ulteriormente l'utente. Assicurarsi di eseguire la convalida della protezione CSRF.

> [!NOTE]
> Questo approccio consente a un client compromesso di modificare i parametri aggiuntivi inviati nel parametro state, reindirizzando in tal modo l'utente a un URL diverso, ovvero la [minaccia del redirector aperto](https://tools.ietf.org/html/rfc6819#section-4.2.4) descritta in RFC 6819. Pertanto, il client deve proteggere questi parametri crittografando lo stato o verificandolo con altri mezzi, ad esempio la convalida del nome di dominio nell'URI di reindirizzamento rispetto al token.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [manifesto dell'applicazione](reference-app-manifest.md)
