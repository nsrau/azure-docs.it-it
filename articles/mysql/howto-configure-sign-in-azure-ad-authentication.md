---
title: Usare Azure Active Directory - Database di Azure per MySQL
description: Informazioni su come configurare Azure Active Directory (Azure AD) per l'autenticazione con Database di Azure per MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: how-to
ms.date: 01/22/2019
ms.openlocfilehash: ff5d2e5546c8b29ed486c587a555f47fa2c7e31b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86101427"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>Usare Azure Active Directory per l'autenticazione con MySQL

Questo articolo illustra in modo dettagliato i passaggi per configurare l'accesso di Azure Active Directory con Database di Azure per MySQL e per connettersi tramite un token di Azure AD.

> [!IMPORTANT]
> Azure Active Directory autenticazione è disponibile solo per MySQL 5,7 e versioni successive.

## <a name="setting-the-azure-ad-admin-user"></a>Impostazione dell'utente amministratore di Azure AD

Solo un utente amministratore di Azure AD può creare o abilitare gli utenti per l'autenticazione basata su Azure AD. Per creare un utente amministratore di Azure AD, seguire questa procedura

1. Nel portale di Azure selezionare l'istanza di Database di Azure per MySQL da abilitare per Azure AD.
2. In Impostazioni selezionare Amministratore di Active Directory:

![impostare l'amministratore di azure ad][2]

3. Selezionare un utente di Azure AD valido nel tenant del cliente come amministratore di Azure AD.

> [!IMPORTANT]
> Quando si imposta l'amministratore, nel server di Database di Azure per MySQL viene aggiunto un nuovo utente con autorizzazioni di amministratore complete.

Per ogni server MySQL è possibile creare un solo amministratore di Azure AD. Se ne viene selezionato un altro, l'amministratore di Azure AD esistente configurato per il server verrà sovrascritto.

In una versione futura verrà supportata l'impostazione di un gruppo di Azure AD invece che di un singolo utente per avere più amministratori, tuttavia questa funzione non è ancora supportata.

Dopo aver configurato l'amministratore, è possibile accedere:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Connessione a Database di Azure per MySQL tramite Azure AD

Il diagramma generale seguente riepiloga il flusso di lavoro relativo all'uso dell'autenticazione di Azure AD con Database di Azure per MySQL:

![Flusso di autenticazione][1]

L'integrazione di Azure AD è stata progettata per funzionare con gli strumenti MySQL comuni come l'interfaccia della riga di comando di MySQL, che non sono compatibili con Azure AD e supportano soltanto la specifica di nome utente e password per la connessione a MySQL. Il token di Azure AD viene passato come password, come illustrato nella figura riportata sopra.

Attualmente sono stati testati i client seguenti:

- MySQLWorkbench 
- Interfaccia della riga di comando di MySQL

Sono stati anche testati i driver di applicazione più comuni. I dettagli sono disponibili alla fine di questa pagina.

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

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per acquisire un token di accesso per l'utente autenticato di Azure AD del passaggio 1 e accedere a Database di Azure per MySQL.

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

Il token è una stringa Base 64 che codifica tutte le informazioni sull'utente autenticato e che è indirizzata al servizio Database di Azure per MySQL.

> [!NOTE]
> La validità del token di accesso può essere compresa tra 5 e 60 minuti. È consigliabile ottenere il token di accesso appena prima di accedere a Database di Azure per MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Passaggio 3: Usare il token come password per l'accesso con MySQL

Durante la connessione è necessario usare il token di accesso come password utente MySQL. Quando si usano client con interfaccia utente grafica come MySQLWorkbench, è possibile usare il metodo precedente per recuperare il token. 

Quando si usa l'interfaccia della riga di comando, è possibile usare questa sintassi abbreviata per connettersi: 

**Esempio (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Considerazioni importanti per la connessione:

* `user@tenant.onmicrosoft.com`nome dell'utente o del gruppo Azure AD cui si sta tentando di connettersi
* Aggiungere sempre il nome del server dopo il nome dell'utente o del gruppo di Azure AD (ad esempio `@mydb` )
* Assicurarsi di usare il modo esatto in cui è stato digitato il nome dell'utente o del gruppo di Azure AD
* Azure AD nomi di utenti e gruppi fanno distinzione tra maiuscole e minuscole
* Quando ci si connette come gruppo, utilizzare solo il nome del gruppo (ad esempio `GroupName@mydb` )
* Se il nome contiene spazi, usare `\` prima di ogni spazio per l'escape

Si noti l'impostazione "enable-cleartext-plugin". È necessario usare una configurazione simile con altri client per assicurarsi che il token venga inviato al server senza hash.

L'autenticazione al server MySQL tramite l'autenticazione di Azure AD è stata eseguita.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Creazione di utenti di Azure AD in Database di Azure per MySQL

Per aggiungere un utente di Azure AD a Database di Azure per MySQL, attenersi alla procedura seguente dopo aver stabilito la connessione (vedere la sezione successiva per informazioni su come stabilire la connessione):

1. Assicurarsi prima di tutto che l'utente di Azure AD `<user>@yourtenant.onmicrosoft.com` sia un utente valido nel tenant di Azure AD.
2. Accedere all'istanza di Database di Azure per MySQL come utente amministratore di Azure AD.
3. Creare l'utente `<user>@yourtenant.onmicrosoft.com` in Database di Azure per MySQL.

**Esempio:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Per i nomi utente che superano i 32 caratteri, è consigliabile usare invece un alias per la connessione: 

Esempio:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> L'autenticazione di un utente tramite Azure AD non concede a quest'ultimo alcuna autorizzazione per accedere agli oggetti all'interno del database del servizio Database di Azure per MySQL. È necessario concedere manualmente le autorizzazioni necessarie all'utente.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Creazione di gruppi di Azure AD in Database di Azure per MySQL

Per abilitare l'accesso al database da parte di un gruppo di Azure AD, usare lo stesso meccanismo adottato per gli utenti, ma specificare invece il nome del gruppo:

**Esempio:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Quando si esegue l'accesso, i membri del gruppo useranno i propri token di accesso personali, ma firmeranno usando il nome del gruppo specificato come nome utente.

## <a name="token-validation"></a>Convalida dei token

L'autenticazione di Azure AD in Database di Azure per MySQL garantisce che l'utente sia presente nel server MySQL e controlla la validità del token convalidandone il contenuto. Vengono eseguiti i passaggi di convalida del token seguenti:

-   Il token è firmato da Azure AD e non è stato alterato
-   Il token è stato emesso da Azure AD per il tenant associato al server
-   Il token non è scaduto
-   Il token è relativo alla risorsa Database di Azure per MySQL (e non a un'altra risorsa di Azure)

## <a name="compatibility-with-application-drivers"></a>Compatibilità con i driver delle applicazioni

È disponibile il supporto per la maggior parte dei driver, ma assicurarsi di usare le impostazioni per l'invio della password in testo non crittografato, in modo che il token venga inviato senza modifiche.

* C/C++
  * libmysqlclient: supportato
  * mysql-connector-c++: supportato
* Java
  * Connettore/J (mysql-connector-java): supportato, è necessario usare l'impostazione `useSSL`
* Python
  * Connettore/Python: supportato
* Ruby
  * mysql2: supportato
* .NET
  * mysql-connector-net: supportato, è necessario aggiungere il plug-in per mysql_clear_password
  * mysql-net/MySqlConnector: supportato
* Node.js
  * mysqljs: non supportato (il token non viene inviato in testo non crittografato senza patch)
  * node-mysql2: supportato
* Perl
  * DBD::mysql: supportato
  * Net::MySQL: Non supportate
* Go
  * go-sql-driver: supportato, aggiungere `?tls=true&allowCleartextPasswords=true` alla stringa di connessione

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i concetti generali per l'[autenticazione di Azure Active Directory con Database di Azure per MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
