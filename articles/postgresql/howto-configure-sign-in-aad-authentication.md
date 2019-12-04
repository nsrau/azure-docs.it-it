---
title: Usare Azure Active Directory-database di Azure per PostgreSQL-server singolo
description: Informazioni su come configurare Azure Active Directory (AAD) per l'autenticazione con database di Azure per PostgreSQL-server singolo
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c929ac1c171547a4ff485fc43f0f329440f9c3b5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763641"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Usare Azure Active Directory per l'autenticazione con PostgreSQL

Questo articolo illustra i passaggi per configurare Azure Active Directory l'accesso con database di Azure per PostgreSQL e come connettersi usando un token di Azure AD.

> [!IMPORTANT]
> L'autenticazione Azure AD per database di Azure per PostgreSQL è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Impostazione dell'utente amministratore di Azure AD

Solo un utente amministratore Azure AD può creare o abilitare gli utenti per l'autenticazione basata su Azure AD. Per creare e Azure AD utente amministratore, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare l'istanza di database di Azure per PostgreSQL che si vuole abilitare per l'Azure AD.
2. In impostazioni selezionare Active Directory amministratore:

![imposta amministratore di Azure ad][2]

3. Selezionare un utente Azure AD valido nel tenant del cliente da Azure AD amministratore.

> [!IMPORTANT]
> Quando si imposta l'amministratore, viene aggiunto un nuovo utente al database di Azure per il server PostgreSQL con autorizzazioni di amministratore completo. L'utente amministratore di Azure AD nel database di Azure per PostgreSQL avrà il ruolo `azure_ad_admin`.

Per ogni server PostgreSQL è possibile creare solo un Azure AD amministratore. la selezione di un altro amministratore sovrascriverà l'amministratore di Azure AD esistente configurato per il server. È possibile specificare un gruppo di Azure AD anziché un singolo utente per avere più amministratori. Si noti che sarà quindi possibile accedere con il nome del gruppo per scopi amministrativi.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Creazione di Azure AD utenti nel database di Azure per PostgreSQL

Per aggiungere un utente Azure AD al database di Azure per PostgreSQL, seguire questa procedura dopo la connessione (vedere la sezione successiva sulla connessione):

1. Assicurarsi prima di tutto che l'utente Azure AD `<user>@yourtenant.onmicrosoft.com` sia un utente valido in Azure AD tenant.
2. Accedere all'istanza del database di Azure per PostgreSQL come utente amministratore Azure AD.
3. Creare `<user>@yourtenant.onmicrosoft.com` di ruolo nel database di Azure per PostgreSQL.
4. Rendere `<user>@yourtenant.onmicrosoft.com` un membro del ruolo azure_ad_user. Questa operazione deve essere assegnata solo agli utenti Azure AD.

**Esempio:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> L'autenticazione di un utente tramite Azure AD non concede all'utente alcuna autorizzazione per accedere agli oggetti nel database di Azure per PostgreSQL. È necessario concedere manualmente all'utente le autorizzazioni necessarie.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Creazione di gruppi di Azure AD nel database di Azure per PostgreSQL

Per abilitare un gruppo di Azure AD per l'accesso al database, usare lo stesso meccanismo utilizzato per gli utenti, ma specificare invece il nome del gruppo:

**Esempio:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Quando si esegue l'accesso, i membri del gruppo utilizzeranno i propri token di accesso personali, ma firmeranno con il nome del gruppo specificato come nome utente.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Connessione a database di Azure per PostgreSQL con Azure AD

Il diagramma di alto livello riportato di seguito riepiloga il flusso di lavoro di uso dell'autenticazione Azure AD con database di Azure per PostgreSQL:

![flusso di autenticazione][1]

Abbiamo progettato l'integrazione Azure AD per lavorare con strumenti PostgreSQL comuni come PSQL, che non sono Azure AD consapevoli e supportano solo la specifica di nome utente e password quando ci si connette a PostgreSQL. Viene passato il token di Azure AD come password, come illustrato nella figura precedente.

Attualmente sono stati testati i client seguenti:

- PSQL CommandLine (usare la variabile PGPASSWORD per passare il token, vedere più avanti)
- Azure Data Studio (usando l'estensione PostgreSQL)
- Altri client basati su libpq (ad esempio framework applicazioni comuni e ORM)

> [!NOTE]
> Tenere presente che l'uso del token di Azure AD con pgAdmin non è attualmente supportato, perché contiene una limitazione hardcoded di 256 caratteri per le password (il token supera).

Questi sono i passaggi necessari per eseguire l'autenticazione di un utente o un'applicazione con Azure AD descritto di seguito:

### <a name="step-1-authenticate-with-azure-ad"></a>Passaggio 1: eseguire l'autenticazione con Azure AD

Assicurarsi che sia installata l' [interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure.

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per l'autenticazione con Azure AD. È necessario fornire il Azure AD ID utente e la password.

```
az login
```

Questo comando avvierà una finestra del browser nella pagina di autenticazione del Azure AD.

> [!NOTE]
> È inoltre possibile utilizzare Azure Cloud Shell per eseguire questi passaggi.
> Tenere presente che quando si recupera Azure AD token di accesso nella Azure Cloud Shell sarà necessario chiamare in modo esplicito `az login` e accedere di nuovo (nella finestra separata con un codice). Dopo tale accesso, il `get-access-token` comando funzionerà come previsto.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passaggio 2: recuperare Azure AD token di accesso

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per acquisire un token di accesso per l'utente autenticato Azure AD dal passaggio 1 per accedere a database di Azure per PostgreSQL.

Esempio (per il cloud pubblico):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Il valore della risorsa precedente deve essere specificato esattamente come illustrato. Per gli altri cloud, il valore della risorsa può essere cercato usando:

```shell
az cloud show
```

Per l'interfaccia della riga di comando di Azure versione 2.0.71 e successive, il comando può essere specificato nella versione più comoda seguente per tutti i cloud:

```shell
az account get-access-token --resource-type oss-rdbms
```

Una volta completata l'autenticazione, Azure AD restituirà un token di accesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Il token è una stringa di base 64 che codifica tutte le informazioni sull'utente autenticato e che è destinata al servizio database di Azure per PostgreSQL.

> [!NOTE]
> La validità del token di accesso è qualsiasi tra 5 minuti e 60 minuti. Si consiglia di ottenere il token di accesso appena prima di avviare l'accesso a database di Azure per PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>Passaggio 3: usare il token come password per l'accesso con PostgreSQL

Quando ci si connette è necessario usare il token di accesso come password utente PostgreSQL.

Quando si usa il `psql` client della riga di comando, il token di accesso deve essere passato tramite la variabile di ambiente `PGPASSWORD`, poiché il token di accesso supera la lunghezza della password che `psql` può accettare direttamente:

Esempio di Windows:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Esempio di Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

A questo punto è possibile avviare una connessione con database di Azure per PostgreSQL come di consueto:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres"
```

A questo punto è stata eseguita l'autenticazione al server PostgreSQL usando l'autenticazione Azure AD.

## <a name="token-validation"></a>Convalida di token

Azure AD autenticazione in database di Azure per PostgreSQL garantisce che l'utente esista nel server PostgreSQL e controlla la validità del token convalidando il contenuto del token. Vengono eseguiti i seguenti passaggi di convalida del token:

-   Il token è firmato da Azure AD e non è stato alterato
-   Il token è stato emesso da Azure AD per il tenant associato al server
-   Il token non è scaduto
-   Il token è per la risorsa database di Azure per PostgreSQL (e non un'altra risorsa di Azure)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrazione degli utenti PostgreSQL esistenti all'autenticazione basata su Azure AD

È possibile abilitare l'autenticazione Azure AD per gli utenti esistenti. Esistono due casi da considerare:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Caso 1: il nome utente di PostgreSQL corrisponde al nome dell'entità utente Azure AD

Nel caso improbabile che gli utenti esistenti corrispondano già ai nomi utente di Azure AD, è possibile concedere il ruolo `azure_ad_user` per abilitarli per l'autenticazione Azure AD:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Saranno ora in grado di accedere con Azure AD credenziali invece di usare la password utente PostgreSQL configurata in precedenza.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Caso 2: il nome utente di PostgreSQL è diverso dal nome dell'entità utente Azure AD

Se un utente PostgreSQL non esiste in Azure AD o ha un nome utente diverso, è possibile usare i gruppi di Azure AD per l'autenticazione come utente di PostgreSQL. È possibile eseguire la migrazione degli utenti del database di Azure per PostgreSQL esistente a Azure AD creando un gruppo di Azure AD con un nome che corrisponda all'utente di PostgreSQL e quindi concedendo il ruolo azure_ad_user all'utente PostgreSQL esistente:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Si presuppone che sia stato creato un gruppo "DBReadUser" nella Azure AD. Gli utenti che appartengono a tale gruppo saranno ora in grado di accedere al database come questo utente.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i concetti generali per [l'autenticazione Azure Active Directory con database di Azure per PostgreSQL-server singolo](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
