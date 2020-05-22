---
title: Route in App Web statiche di Azure
description: Informazioni sulle regole di routing back-end e su come proteggere le route con i ruoli.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4a9639343827ebc5bb17a6d62d9b65d0b561e932
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595130"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Route in App Web statiche di Azure (anteprima)

Il routing nelle app Web statiche di Azure definisce le regole di routing back-end e il comportamento di autorizzazione per il contenuto statico e le API. Le regole sono definite come una matrice di regole nel file _routes.json_.

- Il file _routes.json_ deve essere presente nella radice della cartella dell'artefatto della compilazione dell'app.
- Le regole vengono eseguite nell'ordine in cui sono visualizzate nella matrice `routes`.
- La valutazione delle regole si arrestata alla prima corrispondenza. Le regole di routing non sono concatenate.
- I ruoli vengono definiti nel file _routes.json_ e gli utenti sono associati ai ruoli tramite [inviti](authentication-authorization.md).
- L'utente ha il controllo completo sui nomi dei ruoli.

L'argomento sul routing si sovrappone in modo significativo ai concetti relativi all'autenticazione e all'autorizzazione. Leggere la guida all'[autenticazione e all'autorizzazione](authentication-authorization.md) insieme a questo articolo.

## <a name="location"></a>Location

Il file _routes.json_ deve essere presente nella radice della cartella dell'artefatto della compilazione dell'app. Se l'app Web include un'istruzione di compilazione che copia i file compilati da una cartella specifica alla cartella dell'artefatto della compilazione, il file _routes.json_ deve essere presente in quella cartella specifica.

La tabella seguente elenca il percorso appropriato in cui collocare il file _routes.json_ per un diversi framework e librerie JavaScript front-end.

|Framework/Libreria | Location  |
|---------|----------|
| Angular | _assets_   |
| React   | _pubblico_  |
| Svelte  | _pubblico_   |
| Vue     | _pubblico_ |

## <a name="defining-routes"></a>Definizione delle route

Le route sono definite nel file _routes.json_ come una matrice di regole di route nella proprietà `routes`. Ogni regola è costituita da un modello di route, insieme a una o più proprietà della regola facoltative. Per gli esempi di utilizzo, vedere il [file di route di esempio](#example-route-file).

| Proprietà regola  | Obbligatoria | Valore predefinito | Comment                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Sì      | n/d          | Modello di route richiesto dal chiamante.<ul><li>I [caratteri jolly](#wildcards) sono supportati alla fine dei percorsi di route. Ad esempio, la route _admin/\*_ cerca le corrispondenze con qualsiasi route nel percorso _admin_.<li>Il file predefinito di una route è _index.html_.</ul>|
| `serve`        | No       | n/d          | Definisce il file o il percorso restituito dalla richiesta. Il percorso e il nome del file possono essere diversi dal percorso richiesto. Se viene definito un valore per `serve`, viene usato il percorso richiesto. |
| `allowedRoles` | No       | anonymous     | Matrice di nomi di ruolo. <ul><li>I caratteri validi includono `a-z`, `A-Z`, `0-9` e `_`.<li>Il ruolo predefinito `anonymous` si applica a tutti gli utenti non autenticati.<li>Il ruolo predefinito `authenticated` si applica a qualsiasi utente connesso.<li>Gli utenti devono appartenere ad almeno un ruolo.<li>Per la corrispondenza dei ruoli viene usato l'operatore _OR_. Se un utente è incluso in uno dei ruoli elencati, viene concesso l'accesso.<li>I singoli utenti sono associati ai ruoli tramite [inviti](authentication-authorization.md).</ul> |
| `statusCode`   | No       | 200           | [Codice di stato HTTP](https://wikipedia.org/wiki/List_of_HTTP_status_codes) di risposta per la richiesta. |

## <a name="securing-routes-with-roles"></a>Protezione delle route con i ruoli

Per proteggere le route è necessario aggiungere uno o più nomi di ruolo in una matrice `allowedRoles` di una regola. Per gli esempi di utilizzo, vedere il [file di route di esempio](#example-route-file).

Per impostazione predefinita, ogni utente appartiene al ruolo `anonymous` predefinito e tutti gli utenti connessi sono membri del ruolo `authenticated`. Ad esempio, per limitare una route solo agli utenti autenticati, aggiungere il ruolo `authenticated` predefinito alla matrice `allowedRoles`.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

È possibile creare nuovi ruoli in base alle esigenze nella matrice `allowedRoles`. Per limitare una route solo agli amministratori, è possibile definire un ruolo `administrator` nella matrice `allowedRoles`.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Si ha il controllo completo sui nomi dei ruoli. Non è previsto alcun elenco master a cui devono attenersi i ruoli.
- I singoli utenti sono associati ai ruoli tramite [inviti](authentication-authorization.md).

## <a name="wildcards"></a>Caratteri jolly

Le regole con caratteri jolly trovano la corrispondenza con tutte le richieste in un modello di route specificato. Se si definisce un valore `serve` nella regola, il percorso o il file specificato viene restituito come risposta.

Ad esempio, per implementare le route per un'applicazione calendario, è possibile eseguire il mapping di tutti gli URL inclusi nella route _calendar_ per restituire un singolo file.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

Il file _calendar.html_ può quindi usare il routing lato client per restituire una visualizzazione diversa per le variazioni degli URL, ad esempio `/calendar/january/1`, `/calendar/2020` e `/calendar/overview`.

È anche possibile proteggere le route con i caratteri jolly. Nell'esempio seguente tutti i file richiesti nel percorso _admin_ richiedono un utente autenticato che è membro del ruolo _administrator_.

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Le richieste per i file a cui fa riferimento un file restituito vengono valutate solo per i controlli di autenticazione. Ad esempio, le richieste a un file CSS in un percorso con caratteri jolly restituiscono il file CSS e quanto definito come file `serve`. Il file CSS viene restituito purché l'utente soddisfi i requisiti di autenticazione richiesti.

## <a name="fallback-routes"></a>Route di fallback

I framework o le librerie JavaScript front-end si basano spesso sul routing lato client per l'esplorazione delle app Web. Queste regole di routing lato client aggiornano la posizione della finestra del browser senza inviare di nuovo richieste al server. Se si aggiorna la pagina o si passa direttamente a posizioni generate dalle regole di routing lato client, è necessaria una route di fallback sul lato server per restituire la pagina HTML appropriata.

Una route di fallback comune è illustrata nell'esempio seguente:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

La route di fallback deve essere elencata per ultima nelle regole di routing, in quanto intercetta tutte le richieste non rilevate dalle regole definite in precedenza.

## <a name="redirects"></a>Reindirizzamenti

Per reindirizzare le richieste da una route a un'altra, è possibile usare i codici di stato HTTP [301](https://en.wikipedia.org/wiki/HTTP_301) e [302](https://en.wikipedia.org/wiki/HTTP_302).

Ad esempio, la regola seguente crea un reindirizzamento 301 da _old-page.html_ a _new-page.html_.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

I reindirizzamenti funzionano anche con i percorsi che non definiscono file distinti.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Pagine di errore personalizzate

Gli utenti potrebbero riscontrare numerose situazioni diverse che restituiscono un errore. Usando la matrice `platformErrorOverrides`, è possibile fornire un'esperienza personalizzata in risposta a questi errori. Per il posizionamento della matrice nel file [routes.json](#example-route-file), vedere il _file di route di esempio_.

La tabella seguente elenca gli override degli errori della piattaforma disponibili:

| Tipo di errore  | Stato codice HTTP | Descrizione |
|---------|---------|---------|
| `NotFound` | 404  | La pagina non è stata trovata nel server. |
| `Unauthenticated` | 401 | L'utente non è connesso con un [provider di autenticazione](authentication-authorization.md). |
| `Unauthorized_InsufficientUserInformation` | 401 | L'account dell'utente nel provider di autenticazione non è configurato per esporre i dati necessari. Questo errore può verificarsi nei casi in cui l'app richiede l'indirizzo di posta elettronica dell'utente al provider di autenticazione, ma l'utente ha scelto di limitare l'accesso all'indirizzo di posta elettronica. |
| `Unauthorized_InvalidInvitationLink` | 401 | Un invito è scaduto oppure l'utente ha seguito un collegamento di invito generato per un altro destinatario.  |
| `Unauthorized_MissingRoles` | 401 | L'utente non è un membro di un ruolo necessario. |
| `Unauthorized_TooManyUsers` | 401 | Il sito ha raggiunto il numero massimo di utenti e il server limita ulteriori aggiunte. Questo errore viene esposto al client perché non esiste alcun limite per il numero di [inviti](authentication-authorization.md) che è possibile generare e alcuni utenti potrebbero non accettare mai il proprio invito.|
| `Unauthorized_Unknown` | 401 | Si è verificato un problema sconosciuto durante il tentativo di autenticazione dell'utente. È possibile che l'utente non sia stato riconosciuto perché non ha concesso il consenso all'applicazione.|

## <a name="example-route-file"></a>File di route di esempio

L'esempio seguente mostra come creare le regole di route per il contenuto statico e le API in un file _routes.json_. Alcune route usano la [cartella di sistema _/.auth_](authentication-authorization.md) per l'accesso agli endpoint correlati all'autenticazione.

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "serve": "/login"
    }
  ]
}
```

Gli esempi seguenti descrivono cosa accade quando una richiesta corrisponde a una regola.

|Richieste a...  | Risultato... |
|---------|---------|---------|
| _/profile_ | Agli utenti autenticati viene restituito il file _/profile/index.html_. Gli utenti non autenticati vengono reindirizzati a _/login_. |
| _/admin/reports_ | Agli utenti autenticati nel ruolo _administrators_ viene restituito il file _/admin/reports/index.html_. Agli utenti autenticati non appartenenti al ruolo _administrators_ viene restituito un errore di tipo 401 <sup>1</sup>. Gli utenti non autenticati vengono reindirizzati a _/login_. |
| _/api/admin_ | Le richieste provenienti da utenti autenticati nel ruolo _administrators_ vengono inviate all'API. Agli utenti autenticati non appartenenti al ruolo _administrators_ e agli utenti non autenticati viene restituito un errore di tipo 401. |
| _/customers/contoso_ | Agli utenti autenticati che appartengono al _administrators_ o _customers\_contoso_ viene restituito il file _/customers/contoso/index.html_<sup>1</sup>. Agli utenti autenticati non appartenenti al ruolo _administrators_ o _customers\_contoso_ viene restituito un errore di tipo 401. Gli utenti non autenticati vengono reindirizzati a _/login_. |
| _/login_     | Agli utenti non autenticati viene richiesto di eseguire l'autenticazione con GitHub. |
| _/.auth/login/twitter_     | L'autorizzazione con Twitter è disabilitata. Il server risponde con un errore di tipo 404. |
| _/logout_     | Gli utenti vengono disconnessi da qualsiasi provider di autenticazione. |
| _/calendar/2020/01_ | Al browser viene restituito il file _/calendar.html_. |
| _/specials_ | Il browser viene reindirizzato a _/deals_. |
| _/unknown-folder_     | Viene restituito il file _/custom-404.html_. |

<sup>1</sup> È possibile specificare una pagina di errore personalizzata definendo una regola `Unauthorized_MissingRoles` nella matrice `platformErrorOverrides`.

## <a name="restrictions"></a>Restrizioni

- Le dimensioni del file _routes.json_ non possono essere maggiori di 100 kB
- Il file _routes.json_ supporta un massimo di 50 ruoli distinti

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare l'autenticazione e l'autorizzazione](authentication-authorization.md)
