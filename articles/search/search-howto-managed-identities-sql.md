---
title: Configurare una connessione al database SQL di Azure usando un'identità gestita (anteprima)
titleSuffix: Azure Cognitive Search
description: Informazioni su come configurare una connessione dell'indicizzatore al database SQL di Azure usando un'identità gestita (anteprima)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 8dabf69af8628bb0b168bfea94af5333df341423
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924130"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity-preview"></a>Configurare una connessione dell'indicizzatore al database SQL di Azure usando un'identità gestita (anteprima)

> [!IMPORTANT] 
> Il supporto per la configurazione di una connessione a un'origine dati tramite un'identità gestita è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione.

Questa pagina descrive come configurare una connessione dell'indicizzatore al database SQL di Azure usando un'identità gestita anziché fornire le credenziali nella stringa di connessione dell'oggetto origine dati.

Prima di acquisire maggiore familiarità con questa funzionalità, è consigliabile sapere che cos'è un indicizzatore e come lo si configura per l'origine dati. Altre informazioni sono disponibili nei collegamenti seguenti:

* [Panoramica degli indicizzatori](search-indexer-overview.md)
* [Indicizzatore di Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Configurare una connessione usando un'identità gestita

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - Attivare l'identità gestita assegnata dal sistema

Quando un'identità gestita assegnata dal sistema è abilitata, Azure crea un'identità per il servizio di ricerca che può essere usata per eseguire l'autenticazione ad altri servizi di Azure all'interno dello stesso tenant e della stessa sottoscrizione. È quindi possibile usare questa identità nelle assegnazioni di controllo degli accessi in base al ruolo che consentono l'accesso ai dati durante l'indicizzazione.

![Attivare l'identità gestita assegnata dal sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Attivare l'identità gestita assegnata dal sistema")

Dopo aver selezionato **Salva**, viene visualizzato un ID oggetto che è stato assegnato al servizio di ricerca.

![ID oggetto](./media/search-managed-identities/system-assigned-identity-object-id.png "ID dell'oggetto.")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 - Effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server

Durante la connessione al database nel passaggio successivo, sarà necessario connettersi con un account di Azure Active Directory (Azure AD) che disponga di accesso amministrativo al database, allo scopo di concedere al servizio di ricerca l'autorizzazione per l'accesso al database.

Seguire le istruzioni disponibili [qui ](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) per concedere l'accesso al database all'amministratore dell'account Azure AD.

### <a name="3---assign-the-search-service-permissions"></a>3 - Assegnare le autorizzazioni del servizio di ricerca

Attenersi ai passaggi seguenti per assegnare l'autorizzazione del servizio di ricerca per la lettura del database.

1. Connettersi a Visual Studio

    ![Connettersi a Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Connettersi a Visual Studio")

2. Eseguire l'autenticazione con l'account di Azure AD

    ![Autentica](./media/search-managed-identities/visual-studio-authentication.png "Authenticate")

3. Eseguire i comandi seguenti:

    Racchiudere il nome del servizio di ricerca tra parentesi quadre.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Nuova query](./media/search-managed-identities/visual-studio-new-query.png "Nuova query")

    ![Eseguire la query](./media/search-managed-identities/visual-studio-execute-query.png "Eseguire la query")

>[!NOTE]
> Se l'identità del servizio di ricerca ottenuta nel passaggio 1 viene modificata dopo aver completato questo passaggio, è necessario rimuovere l'appartenenza al ruolo e rimuovere l'utente nel database SQL, quindi aggiungere nuovamente le autorizzazioni completando il passaggio 3.
> La rimozione dell'appartenenza al ruolo e dell'utente può essere effettuata eseguendo i comandi seguenti:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 - Aggiungere un'assegnazione di ruolo

In questo passaggio si autorizza il servizio Ricerca cognitiva di Azure a leggere i dati in SQL Server.

1. Nel portale di Azure passare alla pagina Server di Azure SQL.
2. Selezionare **Controllo di accesso (IAM)** .
3. Selezionare **Aggiungi** e quindi **Aggiungi assegnazione di ruolo**.

    ![Aggiungi assegnazione di ruolo](./media/search-managed-identities/add-role-assignment-sql-server.png "Aggiungi un'assegnazione di ruolo")

4. Selezionare il ruolo **Lettore** appropriato.
5. Lasciare **Assegna accesso a** impostato su **Utente, gruppo o entità servizio di Azure AD**
6. Cercare il servizio di ricerca di proprio interesse, selezionarlo e quindi selezionare **Salva**

    ![Aggiungere l'assegnazione di ruolo Lettore](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Aggiungere l'assegnazione di ruolo Lettore")

### <a name="5---create-the-data-source"></a>5 - Creare l'origine dati

L' [API REST](/rest/api/searchservice/create-data-source), portale di Azure e [.NET SDK](/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) supportano la stringa di connessione dell'identità gestita. Di seguito è riportato un esempio di come creare un'origine dati per indicizzare i dati da un database SQL di Azure usando l' [API REST](/rest/api/searchservice/create-data-source) e una stringa di connessione identità gestita. Il formato della stringa di connessione identità gestita è lo stesso per l'API REST, .NET SDK e il portale di Azure.

Quando si crea un'origine dati usando l' [API REST](/rest/api/searchservice/create-data-source), l'origine dati deve avere le proprietà obbligatorie seguenti:

* **name** è il nome univoco dell'origine dati all'interno del servizio di ricerca.
* **type** è `azuresql`
* **credentials**
    * Quando si usa un'identità gestita per l'autenticazione, il formato delle **credenziali** è diverso rispetto alla situazione in cui non viene usata un'identità gestita. In questo caso, si fornirà un nome di database o di catalogo iniziale e un valore ResourceId senza chiave o password dell'account. Il ResourceId deve includere l'ID sottoscrizione del database SQL di Azure, il gruppo di risorse del database SQL e il nome del database SQL. 
    * Formato della stringa di connessione con identità gestita:
        * *Initial Catalog|Database=**nome del database**;ResourceId=/subscriptions/**ID sottoscrizione**/resourceGroups/**nome del gruppo di risorse**/providers/Microsoft.Sql/servers/**nome del server SQL**/;Connection Timeout=**durata del timeout per la connessione**;*
* **container** specifica il nome della tabella o della vista da indicizzare.

Esempio di creazione di un oggetto origine dati di Azure SQL usando l'[API REST ](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 - Creare l'indice

L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca.

Ecco come creare un indice con un campo `booktitle` ricercabile:   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Per altre informazioni sulla creazione di indici, vedere [Creare un indice](/rest/api/searchservice/create-index)

### <a name="7---create-the-indexer"></a>7 - Creare l'indicizzatore

Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e consente di pianificare l'automatizzazione dell'aggiornamento dei dati.

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore.

Esempio di definizione dell'indicizzatore per un indicizzatore di Azure SQL:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Questo indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](/rest/api/searchservice/create-indexer).

Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [Come pianificare gli indicizzatori per Ricerca cognitiva di Azure](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se quando l'indicizzatore tenta di connettersi all'origine dati, si verifica un errore che indica che il client non è autorizzato ad accedere al server, consultare gli [errori comuni dell'indicizzatore](./search-indexer-troubleshooting.md).

## <a name="see-also"></a>Vedere anche

Altre informazioni sull'indicizzatore di Azure SQL:
* [Indicizzatore di Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)