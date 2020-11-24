---
title: Restrizioni URI di reindirizzamento (URL di risposta) | Azure
titleSuffix: Microsoft identity platform
description: Descrizione delle restrizioni e delle limitazioni relative al formato dell'URI di reindirizzamento (URL di risposta) applicato dalla piattaforma di identità Microsoft.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 11/23/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: 30ea74b249937544a0bf9811cad60f02c1ca45c7
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95752788"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Limitazioni e limitazioni dell'URI di reindirizzamento (URL di risposta)

Un URI di reindirizzamento o un URL di risposta è il percorso in cui il server di autorizzazione Invia l'utente dopo che l'app è stata autorizzata e ha concesso un codice di autorizzazione o un token di accesso. Il server di autorizzazione Invia il codice o il token all'URI di reindirizzamento, quindi è importante registrare il percorso corretto come parte del processo di registrazione dell'app.

 Agli URL di reindirizzamento si applicano le restrizioni seguenti:

* L'URI di reindirizzamento deve iniziare con lo schema `https` . Esistono alcune [eccezioni per](#localhost-exceptions) gli URI di reindirizzamento localhost.

* L'URI di Reindirizzamento fa distinzione tra maiuscole e minuscole. Le maiuscole e le minuscole devono corrispondere a quelle nel percorso URL dell'applicazione in esecuzione. Se, ad esempio, l'applicazione include come parte del percorso `.../abc/response-oidc` , non specificare `.../ABC/response-oidc` nell'URI di reindirizzamento. Poiché il Web browser rileva la distinzione tra maiuscole e minuscole nei percorsi, è possibile che i cookie associati a `.../abc/response-oidc` vengano esclusi se reindirizzati all'URL `.../ABC/response-oidc` senza la corrispondenza tra maiuscole e minuscole.

## <a name="maximum-number-of-redirect-uris"></a>Numero massimo di URI di reindirizzamento

Questa tabella mostra il numero massimo di URI di reindirizzamento che è possibile aggiungere alla registrazione di un'app nella piattaforma di identità Microsoft.

| Account visitati | Numero massimo di URI di reindirizzamento | Descrizione |
|--------------------------|---------------------------------|-------------|
| Account Microsoft aziendali o dell'istituto di istruzione nel tenant di Azure Active Directory (Azure AD) di qualsiasi organizzazione | 256 | Il campo `signInAudience` nel manifesto dell'applicazione è impostato su *AzureADMyOrg* o su *AzureADMultipleOrgs* |
| Account Microsoft personali e account aziendali e dell'istituto di istruzione | 100 | Il campo `signInAudience` nel manifesto dell'applicazione è impostato su *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Lunghezza massima dell'URI

È possibile usare un massimo di 256 caratteri per ogni URI di reindirizzamento aggiunto a una registrazione dell'app.

## <a name="supported-schemes"></a>Schemi supportati

Il modello di applicazione Azure Active Directory (Azure AD) supporta attualmente sia gli schemi HTTP che HTTPS per le app che firmano account aziendali o dell'Istituto di istruzione nel tenant Azure AD di qualsiasi organizzazione. Questi tipi di conto sono specificati dai `AzureADMyOrg` `AzureADMultipleOrgs` valori e nel `signInAudience` campo del manifesto dell'applicazione. Per le app che firmano account Microsoft personali (MSA) *e* account aziendali o dell'Istituto di istruzione (ovvero, `signInAudience` è impostato su `AzureADandPersonalMicrosoftAccount` ), è consentito solo lo schema HTTPS.

Per aggiungere gli URI di reindirizzamento con uno schema HTTP alle registrazioni di app che consentono l'accesso agli account aziendali o dell'Istituto di istruzione, è necessario usare l'editor del manifesto dell'applicazione in [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) nel portale di Azure. Tuttavia, sebbene sia possibile impostare un URI di reindirizzamento basato su HTTP mediante l'editor manifesto, *è consigliabile utilizzare* lo schema HTTPS per gli URI di reindirizzamento.

## <a name="localhost-exceptions"></a>Eccezioni localhost

Per le [sezioni RFC 8252 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) e [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), gli URI di reindirizzamento "loopback" o "localhost" vengono fornite con due considerazioni speciali:

1. `http` Gli schemi URI sono accettabili perché il reindirizzamento non lascia mai il dispositivo. Di conseguenza, entrambi questi URI sono accettabili:
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. A causa degli intervalli di porte effimeri richiesti spesso dalle applicazioni native, il componente della porta, ad esempio `:5001` o, `:443` viene ignorato ai fini della corrispondenza di un URI di reindirizzamento. Di conseguenza, tutti questi URI sono considerati equivalenti:
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Dal punto di vista dello sviluppo, questo significa che:

* Non registrare più URI di reindirizzamento in cui è diversa solo la porta. Il server di accesso ne sceglierà uno arbitrario e utilizzerà il comportamento associato a tale URI di reindirizzamento (ad esempio, se si tratta di un `web` Reindirizzamento di tipo-, `native` -o `spa` ).

    Questo è particolarmente importante quando si vogliono usare flussi di autenticazione diversi nella stessa registrazione dell'applicazione, ad esempio sia la concessione del codice di autorizzazione che il flusso implicito. Per associare il comportamento corretto della risposta a ogni URI di reindirizzamento, il server di accesso deve essere in grado di distinguere gli URI di reindirizzamento e non può farlo quando solo la porta è diversa.
* Se è necessario registrare più URI di reindirizzamento in localhost per testare flussi diversi durante lo sviluppo, distinguerli usando il componente *path* dell'URI. Ad esempio, `http://localhost/MyWebApp` non corrisponde a `http://localhost/MyNativeApp` .
* L'indirizzo di loopback IPv6 ( `[::1]` ) non è attualmente supportato.

#### <a name="prefer-127001-over-localhost"></a>Preferisci 127.0.0.1 su localhost

Per impedire che l'app venga interruppe da firewall o interfacce di rete rinominate in modo errato, usare l'indirizzo di loopback del valore letterale IP `127.0.0.1` nell'URI di reindirizzamento anziché `localhost` . Ad esempio: `https://127.0.0.1`.

Tuttavia, non è possibile utilizzare la casella di testo **URI di reindirizzamento** nel portale di Azure per aggiungere un URI di reindirizzamento basato su loopback che utilizza lo `http` schema:

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Finestra di dialogo di errore in portale di Azure che mostra l'URI di reindirizzamento del loopback basato su http non consentito":::

Per aggiungere un URI di reindirizzamento che usa lo `http` schema con l' `127.0.0.1` indirizzo di loopback, è necessario attualmente modificare l'attributo [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) nel [manifesto dell'applicazione](reference-app-manifest.md).

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Restrizioni relative ai caratteri jolly negli URI di Reindirizzamento

Gli URI con caratteri jolly come `https://*.contoso.com` possono sembrare pratici, ma devono essere evitati a causa delle implicazioni di sicurezza. In base alla specifica OAuth 2.0 ([sezione 3.1.2 del documento RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), un URI di endpoint di reindirizzamento deve essere un URI assoluto.

Gli URI con caratteri jolly non sono attualmente supportati nelle registrazioni di app configurate per l'accesso a account Microsoft personali e account aziendali o dell'Istituto di istruzione. Gli URI con caratteri jolly sono consentiti, tuttavia, per le app configurate per l'accesso solo agli account aziendali o dell'Istituto di istruzione nel tenant Azure AD di un'organizzazione.

Per aggiungere gli URI di reindirizzamento con caratteri jolly alle registrazioni di app che consentono l'accesso agli account aziendali o dell'Istituto di istruzione, è necessario usare l'editor del manifesto dell'applicazione in [registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) nel portale di Azure. Sebbene sia possibile impostare un URI di reindirizzamento con un carattere jolly usando l'editor del manifesto, è *consigliabile rispettare* la [sezione 3.1.2 della RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) e usare solo URI assoluti.

Se lo scenario richiede più URI di reindirizzamento rispetto al limite massimo consentito, prendere in considerazione il seguente [approccio al parametro di stato](#use-a-state-parameter) anziché aggiungere un URI di reindirizzamento con caratteri jolly.

#### <a name="use-a-state-parameter"></a>Uso di un parametro di stato

Se si dispone di diversi sottodomini e lo scenario richiede che, al completamento dell'autenticazione, gli utenti vengano reindirizzati alla stessa pagina dal quale sono stati avviati, potrebbe essere utile usare un parametro di stato.

In questo approccio:

1. Creare un URI di reindirizzamento "condiviso" per ogni applicazione per elaborare i token di sicurezza che si ricevono dall'endpoint di autorizzazione.
1. L'applicazione può inviare parametri specifici dell'applicazione (ad esempio, l'URL del sottodominio in cui l'utente ha originato o qualcosa di simile a informazioni di personalizzazione) nel parametro state. Quando si usa un parametro di stato, prestare attenzione alla protezione CSRF come specificato nella sezione [10.12 del documento RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12).
1. I parametri specifici dell’applicazione includono tutte le informazioni necessarie all’applicazione per funzionare correttamente, vale a dire, creare lo stato dell'applicazione appropriato. L’endpoint di autorizzazione di Azure AD esegue lo striping del codice HTML dal parametro di stato. È quindi necessario assicurarsi che non si stia passando contenuto HTML in questo parametro.
1. Quando Azure AD invia una risposta all’URI di reindirizzamento "condiviso", restituisce il parametro di stato all’applicazione.
1. L’applicazione può quindi usare il valore del parametro di stato per determinare a quale URL indirizzare l’utente. Assicurarsi di convalidare la protezione CSRF.

> [!WARNING]
> Questo approccio consente a un client compromesso di modificare i parametri aggiuntivi inviati nel parametro di stato, reindirizzando quindi l'utente a un URL diverso. Questa è la [minaccia del redirector aperto](https://tools.ietf.org/html/rfc6819#section-4.2.4) descritta nella specifica RFC 6819. Per questo motivo, il client deve proteggere questi parametri crittografando lo stato o eseguendo la verifica in altri modi, ad esempio convalidando il nome di dominio nell'URI di reindirizzamento rispetto al token.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [manifesto dell'applicazione](reference-app-manifest.md)registrazione app.
