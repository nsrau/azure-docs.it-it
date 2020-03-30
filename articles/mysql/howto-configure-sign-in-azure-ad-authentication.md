---
title: Usare Azure Active Directory - Database di Azure per MySQLUse Azure Active Directory - Azure Database for MySQL
description: Informazioni su come configurare Azure Active Directory (Azure AD) per l'autenticazione con Azure Database per MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299006"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Usare Azure Active Directory per l'autenticazione con MySQLUse Azure Active Directory for authenticating with MySQL

Questo articolo illustra i passaggi per configurare l'accesso ad Azure Active Directory con il database di Azure per MySQL e come connettersi usando un token di Azure AD.

> [!IMPORTANT]
> L'autenticazione di Azure AD per il database di Azure per MySQL è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Impostazione dell'utente Admin di Azure ADSetting the Azure AD Admin user

Solo un utente Admin di Azure AD può creare/abilitare gli utenti per l'autenticazione basata su Azure AD. Per creare e utente amministratore di Azure AD, seguire la procedura seguenteTo create and Azure AD Admin user, please follow the following steps

1. Nel portale di Azure selezionare l'istanza del database di Azure per MySQL che si vuole abilitare per Azure AD.
2. In Impostazioni selezionare Amministratore di Active Directory:

![impostare azure ad administrator][2]

3. Selezionare un utente di Azure AD valido nel tenant del cliente come amministratore di Azure AD.

> [!IMPORTANT]
> Quando si imposta l'amministratore, viene aggiunto un nuovo utente al database di Azure per il server MySQL con autorizzazioni di amministratore complete.

È possibile creare un solo amministratore di Azure AD per ogni server MySQL e la selezione di un altro sovrascriverà l'amministratore di Azure AD esistente configurato per il server.

In una versione futura sarà supportata la specifica di un gruppo di Azure AD anziché di un singolo utente per avere più amministratori, tuttavia questo non è ancora supportato.

Dopo aver configurato l'amministratore, è ora possibile accedere:After configuring the administrator, you can now sign in:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Connessione al database di Azure per MySQL tramite Azure ADConnecting to Azure Database for MySQL using Azure AD

The following high-level diagram summarizes the workflow of using Azure AD authentication with Azure Database for MySQL:

![flusso di autenticazione][1]

Abbiamo progettato l'integrazione di Azure AD per funzionare con gli strumenti mySQL comuni come mysql CLI, che non supportano Solo la specifica di nome utente e password per la connessione a MySQL.We've designed the Azure AD integration to work with common MySQL tools like the mysql CLI, which are not Azure AD aware and only supporting username and password when connecting to MySQL. Passiamo il token di Azure AD come password, come illustrato nell'immagine precedente.

Attualmente abbiamo testato i seguenti clienti:

- MySQLWorkbench (micaè per MySQLWorkbench) 
- Mysql CLI

Abbiamo anche testato i driver di applicazione più comuni, è possibile vedere i dettagli alla fine di questa pagina.

Di seguito sono riportati i passaggi che un utente/applicazione dovrà eseguire l'autenticazione con Azure AD descritti di seguito:These are the steps that a user/application will need to do authenticate with Azure AD described below:

### <a name="step-1-authenticate-with-azure-ad"></a>Passaggio 1: Eseguire l'autenticazione con Azure ADStep 1: Authenticate with Azure AD

Assicurarsi che sia [installata l'interfaccia della riga di comando](/cli/azure/install-azure-cli)di Azure.

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per eseguire l'autenticazione con Azure AD. Richiede di assegnare l'ID utente di Azure AD e la password.

```
az login
```

Questo comando avvierà una finestra del browser nella pagina di autenticazione di Azure AD.

> [!NOTE]
> È anche possibile usare Azure Cloud Shell per eseguire questi passaggi.
> Tenere presente che quando si recupera il token di accesso di `az login` Azure AD in Azure Cloud Shell sarà necessario chiamare in modo esplicito e accedere nuovamente (nella finestra separata con un codice). Dopo tale segno `get-access-token` nel comando funzionerà come previsto.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passaggio 2: Recuperare il token di accesso di Azure ADStep 2: Retrieve Azure AD access token

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per acquisire un token di accesso per l'utente autenticato di Azure AD dal passaggio 1 per accedere al database di Azure per MySQL.Invoke the Azure CLI tool to acquire an access token for the Azure AD authenticated user from step 1 to access Azure Database for MySQL.

Esempio (per il cloud pubblico):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Il valore della risorsa precedente deve essere specificato esattamente come illustrato. Per altri cloud, il valore della risorsa può essere cercato usando:For other clouds, the resource value can be looked up using:

```shell
az cloud show
```

Per l'interfaccia della riga di comando di Azure versione 2.0.71 e successive, il comando può essere specificato nella versione più comoda seguente per tutti i cloud:For Azure CLI version 2.0.71 and later, the command can be specified in the following more convenient version for all clouds:

```shell
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

Il token è una stringa base 64 che codifica tutte le informazioni sull'utente autenticato e destinata al servizio Database di Azure per MySQL.The token is a Base 64 string that encodes all the information about the authenticated user, and which is targeted to the Azure Database for MySQL service.

> [!NOTE]
> La validità del token di accesso è compresa tra 5 minuti e 60 minuti. È consigliabile ottenere il token di accesso appena prima di iniziare l'account di accesso a Database di Azure per MySQL.We recommend you get the access token just before initiating the login to Azure Database for MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Passaggio 3: Utilizzare il token come password per l'accesso con MySQLStep 3: Use token as password for logging in with MySQL

Quando ci si connette è necessario utilizzare il token di accesso come password utente MySQL. Quando si utilizzano client GUI come MySQLWorkbench, è possibile utilizzare il metodo precedente per recuperare il token. 

Quando si utilizza l'interfaccia della riga di comando, è possibile utilizzare questa scorciatoia per collegare: 

**Esempio (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Si noti l'impostazione "enable-cleartext-plugin": è necessario utilizzare una configurazione simile con altri client per assicurarsi che il token venga inviato al server senza essere sottoposto a hashing.

A questo punto si è autenticati nel server MySQL usando l'autenticazione di Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Creazione di utenti di Azure AD nel database di Azure per MySQLCreating Azure AD users in Azure Database for MySQL

Per aggiungere un utente di Azure AD al database di Azure per il database MySQL, eseguire la procedura seguente dopo la connessione (vedere la sezione successiva su come connettersi):To add an Azure AD user to your Azure Database for MySQL database, perform the following steps after connecting (see later section on how to connect):

1. Verificare innanzitutto che `<user>@yourtenant.onmicrosoft.com` l'utente di Azure AD sia un utente valido nel tenant di Azure AD.
2. Accedere al database di Azure per l'istanza MySQL come utente amministratore di Azure AD.
3. Creare `<user>@yourtenant.onmicrosoft.com` un utente nel database di Azure per MySQL.Create user in Azure Database for MySQL.

**Esempio:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Per i nomi utente che superano i 32 caratteri, si consiglia di utilizzare un alias, da utilizzare per la connessione: 

Esempio:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> L'autenticazione di un utente tramite Azure AD non concede all'utente le autorizzazioni per accedere agli oggetti all'interno del database di Azure per il database MySQL.Authenticating a user through Azure AD does not give the user any permissions to access objects within the Azure Database for MySQL database. È necessario concedere manualmente all'utente le autorizzazioni necessarie.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Creazione di gruppi di Azure AD nel database di Azure per MySQLCreating Azure AD groups in Azure Database for MySQL

Per abilitare un gruppo di Azure AD per l'accesso al database, usare lo stesso meccanismo utilizzato per gli utenti, ma specificare invece il nome del gruppo:To enable an Azure AD group for access to your database, use the same mechanism as for for users, but instead specify the group name:

**Esempio:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Quando si effettua l'accesso, i membri del gruppo utilizzeranno i propri token di accesso personali, ma firmeranno con il nome del gruppo specificato come nome utente.

## <a name="token-validation"></a>Convalida token

L'autenticazione di Azure AD nel database di Azure per MySQL garantisce che l'utente esista nel server MySQL e controlla la validità del token convalidando il contenuto del token. Vengono eseguiti i passaggi di convalida del token seguenti:The following token validation steps are performed:

-   Il token è firmato da Azure AD e non è stato manomesso
-   Il token è stato rilasciato da Azure AD per il tenant associato al server
-   Il token non è scaduto
-   Il token è per il database di Azure per la risorsa MySQL (e non per un'altra risorsa di Azure)Token is for the Azure Database for MySQL resource (and not another Azure resource)

## <a name="compatibility-with-application-drivers"></a>Compatibilità con i driver di applicazione

La maggior parte dei driver sono supportati, tuttavia assicurarsi di utilizzare le impostazioni per l'invio della password in testo non crittografato, in modo che il token viene inviato senza modifiche.

* C/C++
  * libmysqlclient: supportato
  * mysql-connector-c: Supportato
* Java
  * Connettore/J (mysql-connector-java): supportato, deve utilizzare `useSSL` l'impostazione
* Python
  * Connettore/Python: Supportato
* Ruby
  * mysql2: supportato
* .NET
  * mysql-connector-net: supportato, è necessario aggiungere un plug-in per mysql_clear_password
  * mysql-net/MySqlConnector: supportato
* Node.js
  * mysqljs: non supportato (non invia token in testo non crittografato senza patch)
  * node-mysql2: supportato
* Perl
  * DBD::mysql: Supportato
  * Net::MySQL: Non supportato
* Go
  * go-sql-driver: supportato, aggiungi `?tls=true&allowCleartextPasswords=true` alla stringa di connessione

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i concetti generali per l'autenticazione di [Azure Active Directory con il database di Azure per MySQLReview](concepts-azure-ad-authentication.md) the general concepts for Azure Active Directory authentication with Azure Database for MySQL

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
