---
title: Gestire l'accesso ad aree di lavoro, dati e pipeline
description: Informazioni su come gestire il controllo di accesso ad aree di lavoro, dati e pipeline in un'area di lavoro di Azure Synapse Analytics (anteprima).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 5d95ddcc610fb0350f47e0e5b494cbd16b95468c
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255197"
---
# <a name="manage-access-to-workspaces-data-and-pipelines"></a>Gestire l'accesso ad aree di lavoro, dati e pipeline

Informazioni su come gestire il controllo di accesso ad aree di lavoro, dati e pipeline in un'area di lavoro di Azure Synapse Analytics (anteprima).

> [!NOTE]
> Per la disponibilità generale, il controllo degli accessi in base al ruolo di Azure verrà ulteriormente sviluppato con l'introduzione di ruoli di Azure specifici di Synapse

## <a name="access-control-for-workspace"></a>Controllo di accesso per l'area di lavoro

Per una distribuzione di produzione in un'area di lavoro di Azure Synapse, è consigliabile organizzare l'ambiente in modo da semplificare il provisioning di utenti e amministratori.

> [!NOTE]
> L'approccio adottato in questo articolo consiste nel creare diversi gruppi di sicurezza e quindi configurare l'area di lavoro per usarli in modo coerente. Una volta configurati i gruppi di sicurezza, un amministratore deve solo gestirne l'appartenenza al loro interno.

### <a name="step-1-set-up-security-groups-with-names-following-this-pattern"></a>Passaggio 1: Configurare i gruppi di sicurezza con nomi che seguono questo modello

1. Creare un gruppo di sicurezza denominato `Synapse_WORKSPACENAME_Users`
2. Creare un gruppo di sicurezza denominato `Synapse_WORKSPACENAME_Admins`
3. Aggiunta di `Synapse_WORKSPACENAME_Admins` a `Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Informazioni su come creare un gruppo di sicurezza in [questo articolo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>
> Informazioni su come aggiungere un gruppo di sicurezza da un altro gruppo di sicurezza in [questo articolo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).
>
> WORKSPACENAME: sostituire questa parte con il nome effettivo dell'area di lavoro.

### <a name="step-2-prepare-the-default-adls-gen2-account"></a>Passaggio 2: Preparare l'account predefinito di ADLS Gen2

Quando è stato effettuato il provisioning dell'area di lavoro, è stato necessario selezionare un account [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) e un contenitore per il filesystem da usare per l'area di lavoro.

1. Aprire il [portale di Azure](https://portal.azure.com)
2. Passare all'account Azure Data Lake Storage Gen2
3. Passare al contenitore (filesystem) selezionato per l'area di lavoro di Azure Synapse
4. Selezionare **Controllo di accesso (IAM)**
5. Assegnare i ruoli seguenti:
   1. Ruolo **Lettore** a: `Synapse_WORKSPACENAME_Users`
   2. Ruolo **Proprietario dei dati dei BLOB di archiviazione** a: `Synapse_WORKSPACENAME_Admins`
   3. Ruolo **Collaboratore ai dati dei BLOB di archiviazione** a: `Synapse_WORKSPACENAME_Users`
   4. Ruolo **Proprietario dei dati dei BLOB di archiviazione** a: `WORKSPACENAME`

> [!NOTE]
> WORKSPACENAME: sostituire questa parte con il nome effettivo dell'area di lavoro.

### <a name="step-3-configure-the-workspace-admin-list"></a>Passaggio 3: Configurare l'elenco di amministratori dell'area di lavoro

1. Passare all'[**interfaccia utente Web di Azure Synapse**](https://web.azuresynapse.net)
2. Passare a **Gestisci**  > **sicurezza** > **Controllo di accesso**
3. Selezionare **Aggiungi amministratore** e quindi `Synapse_WORKSPACENAME_Admins`

### <a name="step-4-configure-sql-admin-access-for-the-workspace"></a>Passaggio 4: Configurare l'accesso dell'amministratore di SQL per l'area di lavoro

1. Accedere al [portale di Azure](https://portal.azure.com)
2. Passare all'area di lavoro
3. Passare a **Impostazioni** > **Amministratore di Active Directory**
4. Selezionare **Imposta amministratore**
5. Selezionare `Synapse_WORKSPACENAME_Admins`
6. Scegliere **Seleziona**
7. Selezionare **Salva**

> [!NOTE]
> WORKSPACENAME: sostituire questa parte con il nome effettivo dell'area di lavoro.

### <a name="step-5-add-and-remove-users-and-admins-to-security-groups"></a>Passaggio 5: Aggiungere e rimuovere utenti e amministratori dai gruppi di sicurezza

1. Aggiungere utenti che richiedono l'accesso amministrativo a `Synapse_WORKSPACENAME_Admins`
2. Aggiungere tutti gli altri utenti a`Synapse_WORKSPACENAME_Users`

> [!NOTE]
> Informazioni su come aggiungere un utente come membro a un gruppo di sicurezza in [questo articolo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal)
> 
> WORKSPACENAME: sostituire questa parte con il nome effettivo dell'area di lavoro.

## <a name="access-control-to-data"></a>Controllo di accesso ai dati

Il controllo di accesso ai dati sottostanti è diviso in tre parti:

- Accesso del piano dati all'account di archiviazione (già configurato in precedenza nel passaggio 2)
- Accesso del piano dati ai database SQL (sia per i pool SQL dedicati che per il pool SQL serverless)
- Creazione di credenziali per i database del pool SQL serverless sull'account di archiviazione

## <a name="access-control-to-sql-databases"></a>Controllo di accesso ai database SQL

> [!TIP]
> È necessario eseguire i passaggi seguenti per **ogni** database SQL per concedere all'utente l'accesso a tutti i database SQL tranne che nella sezione [Autorizzazione a livello di server](#server-level-permission) in cui è possibile assegnare un ruolo sysadmin all'utente.

### <a name="serverless-sql-pool"></a>Pool SQL serverless

In questa sezione sono disponibili esempi su come concedere all'utente un'autorizzazione per un determinato database o autorizzazioni complete per il server.

#### <a name="database-level-permission"></a>Autorizzazione a livello di database

Per concedere a un utente l'accesso a un **singolo** database del pool SQL serverless, seguire la procedura di questo esempio:

1. CREATE LOGIN

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. CREATE USER

    ```sql
    use yourdb -- Use your DB name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Aggiungere USER ai membri del ruolo specificato

    ```sql
    use yourdb -- Use your DB name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

> [!NOTE]
> Sostituire l'alias con l'alias dell'utente a cui si vuole concedere l'accesso e il dominio con il dominio aziendale in uso.

#### <a name="server-level-permission"></a>Autorizzazione a livello di server

Per concedere a un utente l'accesso completo a **tutti** i database del pool SQL serverless, seguire la procedura di questo esempio:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="dedicated-sql-pool"></a>Pool SQL dedicato

Per concedere a un utente l'accesso a un **singolo** database SQL, seguire questa procedura:

1. Creare l'utente nel database eseguendo il comando seguente che fa riferimento al database desiderato nel selettore di contesto (elenco a discesa per selezionare i database):

    ```sql
    --Create user in SQL DB
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Concedere all'utente un ruolo per l'accesso al database:

    ```sql
    --Create user in SQL DB
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* e *db_datawriter* possono funzionare per le autorizzazioni di lettura/scrittura se non si desidera concedere l'autorizzazione *db_owner*.
> Per consentire a un utente Spark di leggere e scrivere direttamente da Spark in/da un pool SQL dedicato, è necessaria l'autorizzazione *db_owner*.

Dopo aver creato gli utenti, verificare che sia possibile eseguire query sull'account di archiviazione usando il pool SQL serverless.

## <a name="access-control-to-workspace-pipeline-runs"></a>Controllo di accesso alle esecuzioni di pipeline dell'area di lavoro

### <a name="workspace-managed-identity"></a>Identità gestita dell'area di lavoro

> [!IMPORTANT]
> Per eseguire correttamente le pipeline che includono set di dati o attività che fanno riferimento a un pool SQL dedicato, l'identità dell'area di lavoro deve essere concessa direttamente al pool SQL.

Eseguire i comandi seguenti in ogni pool SQL dedicato per consentire all'identità gestita dell'area di lavoro di eseguire le pipeline nel database del pool SQL:

```sql
--Create user in DB
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;
```

Questa autorizzazione può essere rimossa eseguendo lo script seguente nello stesso pool SQL:

```sql
--Revoking permission to the identity
REVOKE CONTROL ON DATABASE::<SQLpoolname> TO <workspacename>;

--Deleting the user in the DB
DROP USER [<workspacename>];
```

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica dell'identità gestita dell'area di lavoro di Synapse, vedere [Identità gestita dell'area di lavoro Azure Synapse](../security/synapse-workspace-managed-identity.md). Per altre informazioni sulle entità di database, vedere [Entità](https://msdn.microsoft.com/library/ms181127.aspx). Per altre informazioni sui ruoli del database, vedere l'articolo [Ruoli del database](https://msdn.microsoft.com/library/ms189121.aspx).
