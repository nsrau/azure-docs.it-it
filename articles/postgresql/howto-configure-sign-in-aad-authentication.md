---
title: Usare Azure Active Directory - Database di Azure per PostgreSQL - Server singolo
description: Informazioni su come configurare Azure Active Directory (AAD) per l'autenticazione con Database di Azure per PostgreSQL - Server singolo
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 07/23/2020
ms.openlocfilehash: 178c339f6f47569160a9a748794678c610f35734
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171633"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>Usare Azure Active Directory per l'autenticazione con PostgreSQL

Questo articolo consente di eseguire in modo semplificato i passaggi necessari per configurare l'accesso di Azure Active Directory con Database di Azure per PostgreSQL e connettersi tramite un token di Azure AD.

## <a name="setting-the-azure-ad-admin-user"></a>Impostazione dell'utente amministratore di Azure AD

Solo gli utenti amministratori di Azure AD possono creare o abilitare gli utenti per l'autenticazione basata su Azure AD. Poiché l'amministratore di Azure AD dispone di autorizzazioni utente elevate (ad esempio CREATEDB), si sconsiglia di utilizzarlo per eseguire le normali operazioni di database.

Per impostare l'amministratore di Azure AD (è possibile usare un utente o un gruppo), attenersi alla procedura seguente:

1. Nel portale di Azure selezionare l'istanza di Database di Azure per PostgreSQL da abilitare per Azure AD.
2. Selezionare Amministratore di Active Directory in Impostazioni:

![impostare l'amministratore di azure ad][2]

3. Selezionare un utente di Azure AD valido nel tenant del cliente come amministratore di Azure AD.

> [!IMPORTANT]
> Quando si imposta l'amministratore, nel server di Database di Azure PostgreSQL viene aggiunto un nuovo utente con autorizzazioni di amministratore complete. L'utente amministratore di Azure AD in Database di Azure per PostgreSQL avrà il ruolo `azure_ad_admin`.

Per ogni server PostgreSQL è possibile creare un solo amministratore di Azure AD. Se ne viene selezionato un altro, l'amministratore di Azure AD esistente configurato per il server verrà sovrascritto. È possibile specificare un gruppo di Azure AD, anziché un singolo utente, per avere più amministratori. Tenere presente che in questo caso sarà necessario accedere con il nome del gruppo per finalità amministrative.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Connessione a Database di Azure per PostgreSQL tramite Azure AD

Il diagramma generale seguente riepiloga il flusso di lavoro relativo all'uso dell'autenticazione di Azure AD con Database di Azure per PostgreSQL:

![flusso di autenticazione][1]

L'integrazione Azure AD è progettata per funzionare con i comuni strumenti PostgreSQL (come psql) che non sono compatibili con Azure AD e supportano soltanto la specifica di nome utente e password per la connessione a PostgreSQL. Il token di Azure AD viene passato come password, come illustrato nella figura riportata sopra.

Attualmente sono stati testati i client seguenti:

- Riga di comando psql (utilizzare la variabile PGPASSWORD per passare il token, vedere più avanti)
- Azure Data Studio (tramite l'estensione PostgreSQL)
- Altri client basati su libpq (ad esempio framework applicazioni e ORM comuni)

> [!NOTE]
> Tenere presente che l'uso del token di Azure AD con pgAdmin non è attualmente supportato, perché contiene una limitazione hardcoded di 256 caratteri per le password (superata dal token).

Di seguito sono riportati i passaggi da seguire per l'autenticazione dell'utente o dell'applicazione con Azure AD:

### <a name="prerequisites"></a>Prerequisiti

È possibile seguire la procedura Azure Cloud Shell, una macchina virtuale di Azure o nel computer locale. Assicurarsi di aver [installato l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="step-1-authenticate-with-azure-ad"></a>Passaggio 1: Eseguire l'autenticazione con Azure AD

Per iniziare, eseguire l'autenticazione con Azure AD usando lo strumento dell'interfaccia della riga di comando di Azure Questo passaggio non è necessario in Azure Cloud Shell.

```
az login
```

Il comando avvierà una finestra del browser alla pagina di autenticazione del Azure AD. È necessario specificare l'ID utente e la password di Azure AD.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passaggio 2: Recuperare il token di accesso di Azure AD

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per acquisire un token di accesso per l'utente autenticato di Azure AD dal passaggio 1 e accedere a Database di Azure per PostgreSQL.

Esempio (per il cloud pubblico):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Il valore della risorsa riportato sopra deve essere specificato esattamente come illustrato. Per gli altri cloud, è possibile cercare il valore della risorsa usando:

```azurecli-interactive
az cloud show
```

Per l'interfaccia della riga di comando di Azure versione 2.0.71 e successive, è possibile specificare il comando in questa versione più agevole per tutti i cloud:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Dopo aver completato l'autenticazione, Azure AD restituirà un token di accesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Il token è una stringa Base 64 che codifica tutte le informazioni sull'utente autenticato e che è indirizzata al servizio Database di Azure per PostgreSQL.

> [!NOTE]
> La validità del token di accesso è un qualsiasi valore compreso tra 5 e 60 minuti. È consigliabile ottenere il token di accesso appena prima di accedere a Database di Azure per PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Passaggio 3: Usare il token come password per l'accesso con PostgreSQL

Durante la connessione è necessario usare il token di accesso come password utente PostgreSQL.

Se si usa il client della riga di comando `psql`, il token di accesso deve essere passato tramite la variabile di ambiente `PGPASSWORD`, poiché quest'ultimo supera la lunghezza della password che `psql` è in grado di accettare direttamente:

Esempio Windows:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Esempio Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

A questo punto è possibile avviare una connessione con Database di Azure per PostgreSQL come di consueto:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Considerazioni importanti per la connessione:

* `user@tenant.onmicrosoft.com`nome dell'utente o del gruppo Azure AD cui si sta tentando di connettersi
* Aggiungere sempre il nome del server dopo il nome dell'utente o del gruppo di Azure AD (ad esempio `@mydb` )
* Assicurarsi di usare il modo esatto in cui è stato digitato il nome dell'utente o del gruppo di Azure AD
* Azure AD nomi di utenti e gruppi fanno distinzione tra maiuscole e minuscole
* Quando ci si connette come gruppo, utilizzare solo il nome del gruppo (ad esempio `GroupName@mydb` )
* Se il nome contiene spazi, usare `\` prima di ogni spazio per l'escape

L'autenticazione al server PostgreSQL tramite l'autenticazione di Azure AD è stata eseguita.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Creazione di utenti di Azure AD in Database di Azure per PostgreSQL

Per aggiungere un utente di Azure AD a Database di Azure per PostgreSQL, attenersi alla procedura seguente dopo aver eseguito la connessione (vedere la sezione successiva per informazioni su come eseguire la connessione):

1. Assicurarsi prima di tutto che l'utente di Azure AD `<user>@yourtenant.onmicrosoft.com` sia un utente valido nel tenant di Azure AD.
2. Accedere all'istanza di Database di Azure per PostgreSQL come utente amministratore di Azure AD.
3. Creare il ruolo `<user>@yourtenant.onmicrosoft.com` in Database di Azure per PostgreSQL.
4. Rendere `<user>@yourtenant.onmicrosoft.com` un membro del ruolo azure_ad_user. Questo ruolo deve essere assegnato solo agli utenti di Azure AD.

**Esempio:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> L'autenticazione di un utente tramite Azure AD non concede a quest'ultimo alcuna autorizzazione per accedere agli oggetti all'interno del database del servizio Database di Azure per PostgreSQL. È necessario concedere manualmente le autorizzazioni necessarie all'utente.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Creazione di gruppi di Azure AD in Database di Azure per PostgreSQL

Per abilitare l'accesso al database da parte di un gruppo di Azure AD, usare lo stesso meccanismo adottato per gli utenti, ma specificare invece il nome del gruppo:

**Esempio:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Quando si esegue l'accesso, i membri del gruppo useranno i propri token di accesso personali, ma firmeranno usando il nome del gruppo specificato come nome utente.

## <a name="token-validation"></a>Convalida dei token

L'autenticazione di Azure AD in Database di Azure per PostgreSQL garantisce che l'utente sia presente nel server PostgreSQL e controlla la validità del token convalidandone il contenuto. Vengono eseguiti i passaggi di convalida del token seguenti:

- Il token è firmato da Azure AD e non è stato alterato
- Il token è stato emesso da Azure AD per il tenant associato al server
- Il token non è scaduto
- Il token è relativo alla risorsa Database di Azure per PostgreSQL (e non a un'altra risorsa di Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrazione degli utenti di PostgreSQL esistenti all'autenticazione basata su Azure AD

È possibile abilitare l'autenticazione di Azure AD per gli utenti esistenti. Considerare i due casi seguenti:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Caso 1: Il nome utente di PostgreSQL corrisponde al nome dell'entità utente di Azure AD

Nel caso (improbabile) in cui il nome utente degli utenti esistenti corrisponda già a quello di Azure AD, è possibile concedere il ruolo `azure_ad_user` a tali utenti allo scopo di abilitarli per l'autenticazione di Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Questi utenti potranno quindi accedere con le credenziali di Azure AD anziché usare la password utente PostgreSQL configurata in precedenza.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Caso 2: Il nome utente PostgreSQL è diverso dal nome dell'entità utente di Azure AD

Se un utente di PostgreSQL non esiste in Azure AD o ha un nome utente diverso, è possibile usare i gruppi di Azure AD per eseguire l'autenticazione con le credenziali di questo specifico utente di PostgreSQL. È possibile eseguire la migrazione degli utenti di Database di Azure per PostgreSQL esistenti ad Azure AD creando un gruppo di Azure AD con un nome che corrisponda a quello dell'utente di PostgreSQL e quindi concedendo il ruolo azure_ad_user a tale utente di PostgreSQL esistente:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

L'esempio sopra presuppone che sia stato creato un gruppo "DBReadUser" in Azure AD. Gli utenti che appartengono a tale gruppo ora possono accedere al database con le credenziali di questo utente.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i concetti generali relativi all'[autenticazione di Azure Active Directory con Database di Azure per PostgreSQL - Server singolo](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
