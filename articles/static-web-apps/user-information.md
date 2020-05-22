---
title: Accesso alle informazioni utente in App Web statiche di Azure
description: Informazioni su come leggere i dati utente restituiti dal provider di autorizzazione.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 273ab5c20cf41b7305d72f1bfef5deeeb046c49a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595140"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Accesso alle informazioni utente in App Web statiche di Azure (anteprima)

App Web statiche di Azure fornisce informazioni sull'utente correlate all'autenticazione tramite un [endpoint di accesso diretto](#direct-access-endpoint) e a [funzioni API](#api-functions).

Molte interfacce utente si basano principalmente sui dati di autenticazione utente. L'endpoint di accesso diretto è un'API di utilità che espone informazioni utente senza dover implementare una funzione personalizzata. Senza considerare la praticità, l'endpoint di accesso diretto non è soggetto a ritardi di avvio a freddo associati all'architettura serverless.

## <a name="client-principal-data"></a>Dati dell'entità di sicurezza client

L'oggetto dati dell'entità di sicurezza client espone all'app le informazioni identificabili dall'utente. L'oggetto entità di sicurezza client prevede le proprietà seguenti:

| Proprietà  | Descrizione |
|-----------|---------|
| `identityProvider` | Nome del [provider di identità](authentication-authorization.md). |
| `userId` | Identificatore univoco specifico di App Web statiche di Azure per l'utente. <ul><li>Il valore è univoco in base alla singola app. Ad esempio, lo stesso utente restituisce un valore `userId` diverso in un'altra risorsa di App Web statiche.<li>Il valore viene mantenuto per la durata di un utente. Se si elimina e si aggiunge di nuovo lo stesso utente all'app, viene generato un nuovo `userId`.</ul>|
| `userDetails` | Nome utente o indirizzo di posta elettronica dell'utente. Alcuni provider restituiscono l'[indirizzo di posta elettronica dell'utente](authentication-authorization.md), mentre altri inviano il [nome utente](authentication-authorization.md). |
| `userRoles`     | Matrice dei [ruoli assegnati dell'utente](authentication-authorization.md). |

L'esempio seguente si riferisce a un oggetto entità di sicurezza client di esempio:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Endpoint di accesso diretto

È possibile inviare una richiesta `GET` alla route `/.auth/me` e ricevere l'accesso diretto ai dati dell'entità di sicurezza client. Quando lo stato della visualizzazione si basa sui dati di autorizzazione, questo approccio garantisce prestazioni ottimali.

Per gli utenti connessi, la risposta contiene un oggetto entità di sicurezza client JSON. Le richieste che provengono da utenti non autenticati restituiscono `null`.

Usando l'API [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup>, è possibile accedere ai dati dell'entità di sicurezza client con la sintassi seguente.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>Funzioni API

I dati dell'entità di sicurezza client vengono passati alle funzioni API nell'intestazione della richiesta `x-ms-client-principal`. I dati dell'entità di sicurezza client vengono inviati come stringa con codifica [Base 64](https://www.wikipedia.org/wiki/Base64) che contiene un oggetto JSON serializzato.

La funzione di esempio seguente mostra come leggere e restituire informazioni utente.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Presupponendo che la funzione sopra indicata sia denominata `user`, è possibile usare l'API [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> del browser per accedere alla risposta dell'API con la sintassi seguente.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> L'API [fetch](https://caniuse.com/#feat=fetch) e l'operatore [await](https://caniuse.com/#feat=mdn-javascript_operators_await) non sono supportati in Internet Explorer.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare le impostazioni dell'app](application-settings.md)
