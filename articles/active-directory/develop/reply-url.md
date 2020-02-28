---
title: URI di reindirizzamento & restrizioni URL di risposta-piattaforma di identità Microsoft | Azure
description: Restrizioni URL di risposta/URL di reindirizzamento & limitazioni
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
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656739"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Restrizioni e limitazioni degli URI di reindirizzamento/URL di risposta

Un URI di reindirizzamento o un URL di risposta è il percorso a cui il server di autorizzazione invierà l'utente dopo che l'app è stata autorizzata correttamente e ha concesso un codice di autorizzazione o un token di accesso. Il codice o il token è contenuto nell'URI di reindirizzamento o nel token di risposta, quindi è importante registrare il percorso corretto come parte del processo di registrazione dell'app.

 Agli URL di risposta si applicano le restrizioni seguenti:

    * L'URL di risposta deve iniziare con lo schema `https`.
    * L'URL di risposta fa distinzione tra maiuscole e minuscole. Il caso deve corrispondere al caso del percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc`, non specificare `.../ABC/response-oidc` nell'URL di risposta. Poiché il Web browser considera i percorsi come distinzione tra maiuscole e minuscole, i cookie associati a `.../abc/response-oidc` possono essere esclusi se reindirizzati all'URL `.../ABC/response-oidc` senza corrispondenza maiuscole/minuscole.
    
## <a name="maximum-number-of-redirect-uris"></a>Numero massimo di URI di Reindirizzamento

La tabella seguente illustra il numero massimo di URI di reindirizzamento che è possibile aggiungere quando si registra l'app.

| Account che hanno eseguito l'accesso | Numero massimo di URI di Reindirizzamento | Descrizione |
|--------------------------|---------------------------------|-------------|
| Account Microsoft aziendali o dell'Istituto di istruzione nel tenant Azure Active Directory (Azure AD) di qualsiasi organizzazione | 256 | `signInAudience` campo nel manifesto dell'applicazione è impostato su *AzureADMyOrg* o *AzureADMultipleOrgs* |
| Account Microsoft personali e account aziendali e dell'Istituto di istruzione | 100 | `signInAudience` campo nel manifesto dell'applicazione è impostato su *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Lunghezza massima URI

È possibile usare un massimo di 256 caratteri per ogni URI di reindirizzamento aggiunto a una registrazione dell'app.

## <a name="supported-schemes"></a>Schemi supportati
Il modello di applicazione Azure AD attualmente supporta sia gli schemi HTTP che HTTPS per le app che firmano gli account Microsoft aziendali o dell'Istituto di istruzione nel tenant Azure Active Directory (Azure AD) di qualsiasi organizzazione. Ovvero `signInAudience` campo nel manifesto dell'applicazione è impostato su *AzureADMyOrg* o *AzureADMultipleOrgs*. Per le app che consentono di accedere a account Microsoft personali e account aziendali o dell'Istituto di istruzione, ovvero `signInAudience` impostati su *AzureADandPersonalMicrosoftAccount*, è consentito solo lo schema HTTPS.

> [!NOTE]
> La nuova esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) non consente agli sviluppatori di aggiungere URI con lo schema http nell'interfaccia utente. L'aggiunta di URI HTTP per le app che registrano account aziendali o dell'Istituto di istruzione è supportata solo tramite l'editor del manifesto dell'applicazione. In futuro, le nuove app non saranno in grado di usare gli schemi HTTP nell'URI di reindirizzamento. Tuttavia, le app precedenti che contengono schemi HTTP negli URI di reindirizzamento continueranno a funzionare. Gli sviluppatori devono usare schemi HTTPS negli URI di reindirizzamento.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrizioni relative all'utilizzo di un carattere jolly negli URI

Gli URI con caratteri jolly, ad esempio `https://*.contoso.com`, sono pratici, ma devono essere evitati. L'uso di caratteri jolly nell'URI di reindirizzamento presenta implicazioni di sicurezza. In base alla specifica OAuth 2,0 ([sezione 3.1.2 di RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), un URI dell'endpoint di reindirizzamento deve essere un URI assoluto. 

Il modello di applicazione Azure AD non supporta gli URI con caratteri jolly per le app configurate per l'accesso agli account Microsoft personali e aziendali o dell'Istituto di istruzione. Tuttavia, gli URI con caratteri jolly sono consentiti per le app configurate per l'accesso agli account aziendali o dell'Istituto di istruzione nel tenant Azure AD di un'organizzazione. 
 
> [!NOTE]
> La nuova esperienza [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) non consente agli sviluppatori di aggiungere URI con caratteri jolly nell'interfaccia utente. L'aggiunta dell'URI carattere jolly per le app che registrano account aziendali o dell'Istituto di istruzione è supportata solo tramite l'editor del manifesto dell'applicazione. In futuro, le nuove app non saranno in grado di usare i caratteri jolly nell'URI di reindirizzamento. Tuttavia, le app precedenti che contengono caratteri jolly negli URI di reindirizzamento continueranno a funzionare.

Se lo scenario richiede più URI di reindirizzamento rispetto al limite massimo consentito, anziché aggiungere un URI di reindirizzamento con caratteri jolly, prendere in considerazione l'approccio seguente.

### <a name="use-a-state-parameter"></a>Usare un parametro di stato

Se si dispone di un numero di sottodomini e se lo scenario richiede di reindirizzare gli utenti dopo l'autenticazione corretta per la stessa pagina in cui sono stati avviati, potrebbe essere utile usare un parametro di stato. 

In questo approccio:

1. Creare un URI di reindirizzamento "condiviso" per ogni applicazione per elaborare i token di sicurezza ricevuti dall'endpoint di autorizzazione.
1. L'applicazione può inviare parametri specifici dell'applicazione (ad esempio, l'URL del sottodominio in cui l'utente ha originato o qualcosa di simile a informazioni di personalizzazione) nel parametro state. Quando si usa un parametro di stato, proteggersi dalla protezione CSRF come specificato nella [sezione 10,12 della specifica RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. I parametri specifici dell'applicazione includeranno tutte le informazioni necessarie all'applicazione per eseguire il rendering dell'esperienza corretta per l'utente, ovvero costruire lo stato dell'applicazione appropriato. Il Azure AD l'endpoint di autorizzazione esegue il striping del codice HTML dal parametro di stato, quindi assicurarsi che non si stia passando contenuto HTML in questo parametro.
1. Quando Azure AD invia una risposta all'URI di reindirizzamento "Shared", invierà di nuovo il parametro di stato all'applicazione.
1. L'applicazione può quindi usare il valore nel parametro state per determinare l'URL a cui inviare ulteriormente l'utente. Assicurarsi di eseguire la convalida per la protezione CSRF.

> [!NOTE]
> Questo approccio consente a un client compromesso di modificare i parametri aggiuntivi inviati nel parametro di stato, in modo da reindirizzare l'utente a un URL diverso, ovvero la [minaccia del redirector aperto](https://tools.ietf.org/html/rfc6819#section-4.2.4) descritta nella specifica RFC 6819. Pertanto, il client deve proteggere questi parametri crittografando lo stato o verificando altri metodi, ad esempio la convalida del nome di dominio nell'URI di reindirizzamento rispetto al token.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [manifesto dell'applicazione](reference-app-manifest.md)
