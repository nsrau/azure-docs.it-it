---
title: Autenticazione e autorizzazione per App Web statiche di Azure
description: Informazioni su come usare diversi provider di autorizzazione per proteggere l'app statica.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e95cd313d341844eabf4f5c5feae8a8ca3dc9c2e
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826549"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Autenticazione e autorizzazione per App Web statiche di Azure (anteprima)

App Web statiche di Azure semplifica l'esperienza di autenticazione grazie alla gestione dell'autenticazione con i provider seguenti:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

Gli [inviti](#invitations) specifici del provider associano gli utenti ai ruoli e agli utenti autorizzati viene concesso l'accesso alle [route](routes.md) dalle regole definite nel file _routes.json_.

Tutti i provider di autenticazione sono abilitati per impostazione predefinita. Per limitare un provider di autenticazione, [bloccare l'accesso](#block-an-authorization-provider) con una regola di route personalizzata.

Gli argomenti su autenticazione e autorizzazione si sovrappongono in modo significativo ai concetti relativi al routing. Leggere la [guida al routing](routes.md) insieme a questo articolo.

## <a name="roles"></a>Ruoli

Ogni utente che accede a un'app Web statica appartiene a uno o più ruoli.  Esistono due ruoli predefiniti a cui gli utenti possono appartenere:

- **anonymous**: Tutti gli utenti appartengono automaticamente al ruolo _anonymous_.
- **authenticated**: Tutti gli utenti connessi appartengono al ruolo _authenticated_.

Oltre ai ruoli predefiniti, è possibile creare nuovi ruoli, assegnarli agli utenti tramite inviti e farvi riferimento nel file _routes.json_.

## <a name="role-management"></a>Gestione dei ruoli

### <a name="add-a-user-to-a-role"></a>Aggiungere un utente a un ruolo

Per aggiungere utenti al sito Web, è possibile generare inviti che consentono di associare gli utenti a ruoli specifici. I ruoli vengono definiti e mantenuti nel file _routes.json_.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Creare un invito

Gli inviti sono specifici dei singoli provider di autorizzazioni, quindi occorre valutare le esigenze dell'app quando si selezionano i provider da supportare. Alcuni provider espongono l'indirizzo di posta elettronica di un utente, mentre altri forniscono solo il nome utente del sito.

<a name="provider-user-details" id="provider-user-details"></a>

| Provider di autorizzazione | Espone:  |
| ---------------------- | ----------------- |
| Azure Active Directory | Indirizzo di posta elettronica     |
| Facebook               | Indirizzo di posta elettronica     |
| GitHub                 | username          |
| Google<sup>1</sup>     | Indirizzo di posta elettronica     |
| Twitter                | username          |

1. Nel [portale di Azure](https://portal.azure.com) passare alla risorsa App Web statiche.
1. In _Impostazioni_ fare clic sulla scheda **Gestione ruoli**.
1. Fare clic sul pulsante **Invita**.
1. Selezionare un _Provider di autorizzazione_ dall'elenco di opzioni.
1. Aggiungere il nome utente o l'indirizzo di posta elettronica del destinatario nella casella _Dettagli dell'invitato_.
    - Per GitHub e Twitter, immettere il nome utente. Per tutti gli altri, immettere l'indirizzo di posta elettronica del destinatario.
1. Selezionare il dominio del sito statico dall'elenco a discesa _Dominio_.
    - Il dominio selezionato è il dominio che verrà visualizzato nell'invito. Se al sito è associato un dominio personalizzato, è probabile che si desideri scegliere il dominio personalizzato.
1. Aggiungere un elenco delimitato da virgole di nomi di ruolo nella casella _Ruolo_.
1. Immettere il numero massimo di ore di validità per l'invito.
    - Il limite massimo consentito è 168 ore, ovvero 7 giorni.
1. Fare clic sul pulsante **Genera**.
1. Copiare il collegamento dalla casella _Collegamento di invito_.
1. Inviare tramite posta elettronica il collegamento di invito per l'utente a cui si sta concedendo l'accesso all'app.

Quando l'utente fa clic sul collegamento nell'invito, gli verrà richiesto di accedere con l'account corrispondente. Una volta eseguito correttamente l'accesso, l'utente viene associato ai ruoli selezionati.

> [!CAUTION]
> Assicurarsi che le regole di route non siano in conflitto con i provider di autenticazione selezionati. Il blocco di un provider con una regola di route impedisce agli utenti di accettare gli inviti.

### <a name="update-role-assignments"></a>Aggiornare le assegnazioni di ruolo

1. Nel [portale di Azure](https://portal.azure.com) passare alla risorsa App Web statiche.
1. In _Impostazioni_ fare clic sulla scheda **Gestione ruoli**.
1. Fare clic sull'utente nell'elenco.
1. Modificare l'elenco dei ruoli nella casella _Ruolo_.
1. Fare clic sul pulsante **Aggiorna**.

### <a name="remove-user"></a>Rimuovere un utente

1. Nel [portale di Azure](https://portal.azure.com) passare alla risorsa App Web statiche.
1. In _Impostazioni_ fare clic sulla scheda **Gestione ruoli**.
1. Individuare l'utente nell'elenco.
1. Selezionare la casella di controllo sulla riga dell'utente.
1. Fare clic sul pulsante **Elimina**.

Quando si rimuove un utente, tenere presente quanto segue:

1. La rimozione di un utente invalida le autorizzazioni associate.
1. La propagazione in tutto il mondo potrebbe richiedere alcuni minuti.
1. Se l'utente viene aggiunto nuovamente all'app, il valore di [`userId` verrà modificato](user-information.md).

## <a name="remove-personal-identifying-information"></a>Rimuovere informazioni personali

Quando si concede il consenso a un'applicazione come utente finale, l'applicazione può accedere all'indirizzo di posta elettronica o al nome utente a seconda del provider di identità. Una volta fornite queste informazioni, il proprietario dell'applicazione decide come gestire le informazioni personali.

Gli utenti finali devono contattare gli amministratori di singole app Web per revocare tali informazioni dai rispettivi sistemi.

Per rimuovere le informazioni personali dalla piattaforma App Web statiche di Azure e impedire alla piattaforma di fornire queste informazioni in caso di richieste future, inviare una richiesta usando l'URL:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Per impedire alla piattaforma di fornire queste informazioni in caso di richieste future a singole app, inviare una richiesta all'URL seguente:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Cartella di sistema

App Web statiche di Azure usa la cartella di sistema `/.auth` per fornire l'accesso alle API correlate all'autorizzazione. Anziché esporre direttamente le route nella cartella `/.auth` agli utenti finali, è consigliabile configurare [regole di routing](routes.md) per creare URL intuitivi.

## <a name="login"></a>Login

Usare la tabella seguente per trovare la route di accesso specifica del provider.

| Provider di autorizzazione | Route di accesso             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Per eseguire l'accesso con GitHub, ad esempio, è possibile includere un collegamento di accesso come il frammento di codice seguente:

```html
<a href="/.auth/login/github">Login</a>
```

Se si sceglie di supportare più di un provider, è necessario esporre un collegamento specifico del provider per ognuno nel sito Web.

È possibile usare una [regola di route](routes.md) per eseguire il mapping di un provider predefinito a una route intuitiva come _/login_.

```json
{
  "route": "/login",
  "serve": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Reindirizzamento dopo l'accesso

Se si desidera che un utente torni a una pagina specifica dopo l'accesso, specificare un URL nel parametro della stringa di query `post_login_redirect_uri`.


## <a name="logout"></a>Logout

La route `/.auth/logout` disconnette gli utenti dal sito Web. È possibile aggiungere un collegamento all'esplorazione del sito per consentire all'utente di disconnettersi come illustrato nell'esempio seguente.

```html
<a href="/.auth/logout">Log out</a>
```

È possibile usare una [regola di route](routes.md) per eseguire il mapping di una route intuitiva come _/logout_.

```json
{
  "route": "/logout",
  "serve": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Reindirizzamento dopo la disconnessione

Se si desidera che un utente torni a una pagina specifica dopo la disconnessione, specificare un URL nel parametro della stringa di query `post_logout_redirect_uri`.

## <a name="block-an-authorization-provider"></a>Bloccare un provider di autorizzazione

È possibile impedire all'app di usare un provider di autorizzazione. Ad esempio, l'app potrebbe voler standardizzare l'uso esclusivo dei [provider che espongono gli indirizzi di posta elettronica](#provider-user-details).

Per bloccare un provider, è possibile creare [regole di route](routes.md) per restituire un codice errore di tipo 404 per le richieste di route specifiche del provider bloccate. Ad esempio, per bloccare il provider Twitter, aggiungere la regola di route seguente.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Restrizioni

Vedere l' [articolo sulle quote](quotas.md) per limitazioni e limitazioni generali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Accedere ai dati di autenticazione e autorizzazione degli utenti](user-information.md)

<sup>1</sup> In attesa di certificazione esterna.
