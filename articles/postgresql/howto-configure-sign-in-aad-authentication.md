---
title: Usare Azure Active Directory - Database di Azure per PostgreSQL - Server singoloUse Azure Active Directory - Azure Database for PostgreSQL - Single Server
description: Informazioni su come configurare Azure Active Directory (AAD) per l'autenticazione con Azure Database for PostgreSQL - Single Server
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9f12849525daeea69ece6e81077446f062e8889
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384399"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Usare Azure Active Directory per l'autenticazione con PostgreSQLUse Azure Active Directory for authenticating with PostgreSQL

Questo articolo illustra i passaggi per configurare l'accesso ad Azure Active Directory con il database di Azure per PostgreSQL e come connettersi usando un token di Azure AD.

> [!IMPORTANT]
> L'autenticazione di Azure AD per il database di Azure per PostgreSQL è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Impostazione dell'utente Admin di Azure ADSetting the Azure AD Admin user

Solo un utente Admin di Azure AD può creare/abilitare gli utenti per l'autenticazione basata su Azure AD. Per creare e utente amministratore di Azure AD, seguire la procedura seguenteTo create and Azure AD Admin user, please follow the following steps

1. Nel portale di Azure selezionare l'istanza del database di Azure per PostgreSQL che si vuole abilitare per Azure AD.
2. In Impostazioni selezionare Amministratore di Active Directory:

![impostare azure ad administrator][2]

3. Selezionare un utente di Azure AD valido nel tenant del cliente come amministratore di Azure AD.

> [!IMPORTANT]
> Quando si imposta l'amministratore, viene aggiunto un nuovo utente al database di Azure per il server PostgreSQL con autorizzazioni di amministratore complete. L'utente Admin di Azure AD nel database `azure_ad_admin`di Azure per PostgreSQL avrà il ruolo .

È possibile creare un solo amministratore di Azure AD per ogni server PostgreSQL e la selezione di un altro sovrascriverà l'amministratore di Azure AD esistente configurato per il server. È possibile specificare un gruppo di Azure AD anziché un singolo utente per avere più amministratori. Si noti che si accederà con il nome del gruppo per scopi amministrativi.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Creazione di utenti di Azure AD nel database di Azure per PostgreSQLCreating Azure AD users in Azure Database for PostgreSQL

Per aggiungere un utente di Azure AD al database di Azure Database per postgreSQL, eseguire la procedura seguente dopo la connessione (vedere la sezione successiva su come connettersi):To add an Azure AD user to your Azure Database for PostgreSQL database, perform the following steps after connecting (see later section on how to connect):

1. Verificare innanzitutto che `<user>@yourtenant.onmicrosoft.com` l'utente di Azure AD sia un utente valido nel tenant di Azure AD.
2. Accedere all'istanza di Azure Database for PostgreSQL come utente Admin di Azure AD.
3. Creare `<user>@yourtenant.onmicrosoft.com` un ruolo nel database di Azure per PostgreSQL.Create role in Azure Database for PostgreSQL.
4. Rendere `<user>@yourtenant.onmicrosoft.com` azure_ad_user un membro del ruolo. Questo valore deve essere assegnato solo agli utenti di Azure AD.

**Esempio:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> L'autenticazione di un utente tramite Azure AD non concede all'utente le autorizzazioni per accedere agli oggetti all'interno del database di Azure per il database PostgreSQL. È necessario concedere manualmente all'utente le autorizzazioni necessarie.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Creazione di gruppi di Azure AD nel database di Azure per PostgreSQLCreating Azure AD groups in Azure Database for PostgreSQL

Per abilitare un gruppo di Azure AD per l'accesso al database, usare lo stesso meccanismo utilizzato per gli utenti, ma specificare invece il nome del gruppo:To enable an Azure AD group for access to your database, use the same mechanism as for for users, but instead specify the group name:

**Esempio:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Quando si effettua l'accesso, i membri del gruppo utilizzeranno i propri token di accesso personali, ma firmeranno con il nome del gruppo specificato come nome utente.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Connessione al database di Azure per PostgreSQL tramite Azure ADConnecting to Azure Database for PostgreSQL using Azure AD

The following high-level diagram summarizes the workflow of using Azure AD authentication with Azure Database for PostgreSQL:

![flusso di autenticazione][1]

Abbiamo progettato l'integrazione di Azure AD per funzionare con gli strumenti PostgreSQL comuni come psql, che non supportano solo la specifica di nome utente e password quando ci si connette a PostgreSQL.We've designed the Azure AD integration to work with common PostgreSQL tools like psql, which are not Azure AD aware and only supporting username and password when connecting to PostgreSQL. Passiamo il token di Azure AD come password, come illustrato nell'immagine precedente.

Attualmente abbiamo testato i seguenti clienti:

- psql riga di comando (utilizzare la variabile PGPASSWORD per passare il token, vedere di seguito)
- Azure Data Studio (tramite l'estensione PostgreSQL)Azure Data Studio (using the PostgreSQL extension)
- Altri client basati su libpq (ad esempio framework applicativi e ORM comuni)

> [!NOTE]
> Tenere presente che l'uso del token di Azure AD con pgAdmin non è attualmente supportato, poiché presenta una limitazione hardcoded di 256 caratteri per le password (che il token supera).

Di seguito sono riportati i passaggi che un utente/applicazione dovrà eseguire l'autenticazione con Azure AD descritti di seguito:These are the steps that a user/application will need to do authenticate with Azure AD described below:

### <a name="step-1-authenticate-with-azure-ad"></a>Passaggio 1: Eseguire l'autenticazione con Azure ADStep 1: Authenticate with Azure AD

Assicurarsi che sia [installata l'interfaccia della riga di comando](/cli/azure/install-azure-cli)di Azure.

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per eseguire l'autenticazione con Azure AD. Richiede di assegnare l'ID utente di Azure AD e la password.

```azurecli-interactive
az login
```

Questo comando avvierà una finestra del browser nella pagina di autenticazione di Azure AD.

> [!NOTE]
> È anche possibile usare Azure Cloud Shell per eseguire questi passaggi.
> Tenere presente che quando si recupera il token di accesso di `az login` Azure AD in Azure Cloud Shell sarà necessario chiamare in modo esplicito e accedere nuovamente (nella finestra separata con un codice). Dopo tale segno `get-access-token` nel comando funzionerà come previsto.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passaggio 2: Recuperare il token di accesso di Azure ADStep 2: Retrieve Azure AD access token

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per acquisire un token di accesso per l'utente autenticato di Azure AD dal passaggio 1 per accedere al database di Azure per PostgreSQL.Invoke the Azure CLI tool to acquire an access token for the Azure AD authenticated user from step 1 to access Azure Database for PostgreSQL.

Esempio (per il cloud pubblico):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Il valore della risorsa precedente deve essere specificato esattamente come illustrato. Per altri cloud, il valore della risorsa può essere cercato usando:For other clouds, the resource value can be looked up using:

```azurecli-interactive
az cloud show
```

Per l'interfaccia della riga di comando di Azure versione 2.0.71 e successive, il comando può essere specificato nella versione più comoda seguente per tutti i cloud:For Azure CLI version 2.0.71 and later, the command can be specified in the following more convenient version for all clouds:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```

Al termine dell'autenticazione, Azure AD restituirà un token di accesso:After authentication is successful, Azure AD will return an access token:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Il token è una stringa base 64 che codifica tutte le informazioni sull'utente autenticato e destinata al servizio Database di Azure per PostgreSQL.The token is a Base 64 string that encodes all the information about the authenticated user, and which is targeted to the Azure Database for PostgreSQL service.

> [!NOTE]
> La validità del token di accesso è compresa tra 5 minuti e 60 minuti. È consigliabile ottenere il token di accesso appena prima di iniziare l'account di accesso a Database di Azure per PostgreSQL.We recommend you get the access token just before initiating the login to Azure Database for PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Passaggio 3: Utilizzare il token come password per l'accesso con PostgreSQLStep 3: Use token as password for logging in with PostgreSQL

Quando ci si connette è necessario utilizzare il token di accesso come password utente PostgreSQL.

Quando si `psql` usa il client della riga di `PGPASSWORD` comando, il token di accesso deve `psql` essere passato attraverso la variabile di ambiente, poiché il token di accesso supera la lunghezza della password che può accettare direttamente:

Esempio di Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Esempio Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

A questo punto è possibile avviare una connessione con Database di Azure per PostgreSQL come si farebbe normalmente:Now you can initiate a connection with Azure Database for PostgreSQL like you normally would:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

A questo punto si è autenticati nel server PostgreSQL usando l'autenticazione di Azure AD.

## <a name="token-validation"></a>Convalida token

L'autenticazione di Azure AD nel database di Azure per PostgreSQL garantisce che l'utente esista nel server PostgreSQL e controlla la validità del token convalidando il contenuto del token. Vengono eseguiti i passaggi di convalida del token seguenti:The following token validation steps are performed:

- Il token è firmato da Azure AD e non è stato manomesso
- Il token è stato rilasciato da Azure AD per il tenant associato al server
- Il token non è scaduto
- Il token è per il database di Azure per la risorsa PostgreSQL (e non per un'altra risorsa di Azure)Token is for the Azure Database for PostgreSQL resource (and not another Azure resource)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrazione degli utenti PostgreSQL esistenti all'autenticazione basata su Azure ADMigrating existing PostgreSQL users to Azure AD-based authentication

È possibile abilitare l'autenticazione di Azure AD per gli utenti esistenti. Ci sono due casi da considerare:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Caso 1: il nome utente PostgreSQL corrisponde al nome dell'entità utente di Azure ADCase 1: PostgreSQL username matches the Azure AD User Principal Name

Nell'improbabile caso in cui gli utenti esistenti corrispondano `azure_ad_user` già ai nomi utente di Azure AD, è possibile concedere il ruolo per abilitarli per l'autenticazione di Azure AD:In the unlikely case that your existing users already match the Azure AD user names, you can grant the role to them in order to enable them for Azure AD authentication:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Potranno ora accedere con le credenziali di Azure AD anziché usare la password utente PostgreSQL configurata in precedenza.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Caso 2: PostgreSQL nome utente è diverso dal nome dell'entità utente di Azure ADCase 2: PostgreSQL username is different than the Azure AD User Principal Name

Se un utente PostgreSQL non esiste in Azure AD o ha un nome utente diverso, è possibile usare i gruppi di Azure AD per l'autenticazione come utente PostgreSQL.If a PostgreSQL user either does not exist in Azure AD or has a different username, you can use Azure AD groups to authenticate as this PostgreSQL user. È possibile eseguire la migrazione del database di Azure esistente per gli utenti PostgreSQL in Azure AD creando un gruppo di Azure AD con un nome corrispondente all'utente PostgreSQL e quindi concedendo il ruolo azure_ad_user all'utente PostgreSQL esistente:You can migrate existing Azure Database for PostgreSQL users to Azure AD by creating an Azure AD group with a name that matches the PostgreSQL user, and then granting role azure_ad_user to the existing PostgreSQL user:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Si presuppone che sia stato creato un gruppo "DBReadUser" in Azure AD. Gli utenti appartenenti a tale gruppo potranno accedere al database come utente.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i concetti generali per l'autenticazione di Azure Active Directory con il database di [Azure per PostgreSQL - Server singoloReview](concepts-aad-authentication.md) the general concepts for Azure Active Directory authentication with Azure Database for PostgreSQL - Single Server

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
