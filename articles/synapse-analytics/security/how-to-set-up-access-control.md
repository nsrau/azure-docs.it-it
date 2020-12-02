---
title: Come configurare il controllo di accesso per l'area di lavoro sinapsi
description: Questo articolo illustra come controllare l'accesso a un'area di lavoro sinapsi usando i ruoli di Azure, i ruoli sinapsi, le autorizzazioni SQL e le autorizzazioni git.
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7243d24204c8e15ae4246718cafb24d31f804d02
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96519179"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Come configurare il controllo di accesso per l'area di lavoro sinapsi 

Questo articolo illustra come controllare l'accesso a un'area di lavoro sinapsi usando i ruoli di Azure, i ruoli sinapsi, le autorizzazioni SQL e le autorizzazioni git.   

In questa guida si imposterà un'area di lavoro e si configurerà un sistema di controllo di accesso di base adatto per molti progetti sinapsi.  Vengono quindi descritte le opzioni più avanzate per un controllo più granulare, se necessario.  

Il controllo di accesso sinapsi può essere semplificato usando i gruppi di sicurezza che sono allineati con i ruoli e gli utenti dell'organizzazione.  È sufficiente aggiungere e rimuovere utenti dai gruppi di sicurezza per gestire l'accesso.

Prima di iniziare questa procedura dettagliata, leggere la [panoramica sul controllo di accesso sinapsi](./synapse-workspace-access-control-overview.md) per acquisire familiarità con i meccanismi di controllo di accesso usati da sinapsi.   

## <a name="access-control-mechanisms"></a>Meccanismi di controllo degli accessi

> [!NOTE]
> L'approccio adottato in questa guida consiste nel creare diversi gruppi di sicurezza e quindi assegnare i ruoli a tali gruppi. Una volta configurati i gruppi, è sufficiente gestire l'appartenenza all'interno dei gruppi di sicurezza per controllare l'accesso all'area di lavoro.

Per proteggere un'area di lavoro sinapsi, si seguirà un modello di configurazione degli elementi seguenti:

- **Gruppi di sicurezza**, per raggruppare gli utenti con requisiti di accesso simili.
- **Ruoli di Azure** per controllare chi può creare e gestire pool SQL, Apache Spark pool e runtime di integrazione e accedere ADLS Gen2 archiviazione.
- **Ruoli sinapsi**, per controllare l'accesso agli artefatti di codice pubblicati, uso di Apache Spark risorse di calcolo e runtime di integrazione 
- **Autorizzazioni SQL**, per controllare l'accesso al piano dati e amministrativo ai pool SQL. 
- **Autorizzazioni git**, per controllare gli utenti che possono accedere agli elementi di codice nel controllo del codice sorgente se si configura il supporto Git per l'area di lavoro 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Passaggi per proteggere un'area di lavoro Synapse

Per semplificare le istruzioni, in questa documentazione vengono usati nomi standard. Sostituirli con i nomi scelti.

|Impostazione | Nome standard | Descrizione |
| :------ | :-------------- | :---------- |
| **Area di lavoro Synapse** | `workspace1` |  Il nome che verrà assegnato all'area di lavoro Synapse. |
| **Account ADLSGEN2** | `storage1` | L'account ADLS da usare con l'area di lavoro. |
| **Contenitore** | `container1` | Il contenitore in STG1 che verrà usato nell'area di lavoro per impostazione predefinita. |
| **Tenant di Active Directory** | `contoso` | il nome del tenant di Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>PASSAGGIO 1: configurare i gruppi di sicurezza

>[!Note] 
>Durante l'anteprima, si consiglia di creare gruppi di sicurezza di cui è stato eseguito il mapping ai ruoli di amministratore di **SQL** sinapsi sinapsi e **sinapsi Apache Spark** .  Con l'introduzione di nuovi ruoli e ambiti delle sinapsi con granularità fine, è ora consigliabile usare queste nuove funzionalità per controllare l'accesso all'area di lavoro.  Questi nuovi ruoli e ambiti forniscono una maggiore flessibilità di configurazione e riconoscono che gli sviluppatori usano spesso una combinazione di SQL e Spark per la creazione di applicazioni di analisi e potrebbe essere necessario concedere l'accesso a risorse specifiche all'interno dell'area di lavoro. [Altre informazioni](./synapse-workspace-synapse-rbac.md)

Creare i seguenti gruppi di sicurezza per l'area di lavoro:

- **`workspace1_SynapseAdministrators`**, per gli utenti che devono avere il controllo completo sull'area di lavoro.  Aggiungere manualmente a questo gruppo di sicurezza, almeno inizialmente.
- **`workspace1_SynapseContributors`**, per gli sviluppatori che hanno la necessità di sviluppare, eseguire il debug e pubblicare codice nel servizio.   
- **`workspace1_SynapseComputeOperators`**, per gli utenti che devono gestire e monitorare Apache Spark pool e runtime di integrazione.
- **`workspace1_SynapseCredentialUsers`**, per gli utenti che devono eseguire il debug e l'esecuzione di pipeline di orchestrazione usando le credenziali dell'area di lavoro MSI (identità del servizio gestito) e di annullamento della pipeline.   

A breve verranno assegnati i ruoli sinapsi a questi gruppi nell'ambito dell'area di lavoro.  

Creare anche questo gruppo di sicurezza: 
- **`workspace1_SQLAdministrators`**, gruppo per gli utenti che necessitano di Active Directory autorità di amministrazione all'interno di pool SQL nell'area di lavoro. 

Il `workspace1_SynapseSQLAdministrators` gruppo verrà usato quando si configurano le autorizzazioni SQL nei pool SQL durante la creazione. 

Per una configurazione di base, questi cinque gruppi sono sufficienti. Successivamente, è possibile aggiungere gruppi di sicurezza per gestire gli utenti che necessitano di un accesso più specializzato o per concedere agli utenti l'accesso solo a risorse specifiche.

> [!NOTE]
>- Informazioni su come creare un gruppo di sicurezza in [questo articolo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).
>- Informazioni su come aggiungere un gruppo di sicurezza da un altro gruppo di sicurezza in [questo articolo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-membership-azure-portal).

>[!Tip]
>I singoli utenti di sinapsi possono usare Azure Active Directory nel portale di Azure per visualizzare le appartenenze ai gruppi per determinare quali ruoli sono stati concessi.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>PASSAGGIO 2: preparare l'account di archiviazione ADLS Gen2

Un'area di lavoro sinapsi usa un contenitore di archiviazione predefinito per:
  - Archiviare i file di dati di backup per le tabelle Spark
  - Eseguire i log per i processi Spark

Identificare le seguenti informazioni sull'archiviazione:

- Account ADLS Gen2 da usare per l'area di lavoro. Questo documento lo chiama `storage1` . `storage1` viene considerato l'account di archiviazione "primario" per l'area di lavoro.
- Il contenitore all'interno dell' `workspace1` area di lavoro sinapsi utilizzerà per impostazione predefinita. Questo documento lo chiama `container1` . 

- Uso della portale di Azure assegnare i seguenti ruoli `container1` di Azure ai gruppi di sicurezza 

  - Assegnare il ruolo di **collaboratore dati BLOB di archiviazione** a `workspace1_SynapseAdmins` 
  - Assegnare il ruolo di **collaboratore dati BLOB di archiviazione** a `workspace1_SynapseContributors`
  - Assegnare il ruolo di **collaboratore dati BLOB di archiviazione** a `workspace1_SynapseComputeOperators` **<< Validate**  

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>PASSAGGIO 3: creare e configurare l'area di lavoro Synapse

Nel portale di Azure, creare un'area di lavoro Synapse:

- Selezionare la propria sottoscrizione
- Selezionare o creare un gruppo di risorse per cui si dispone del ruolo di **proprietario** di Azure.
- Assegnare un nome all'area di lavoro `workspace1`
- Scegliere `storage1` l'account di archiviazione
- Scegliere `container1` per il contenitore usato come "filesystem".
- Aprire WS1 in Synapse Studio
- Passare a **Gestisci**  >  **controllo di accesso** e assegnare i ruoli sinapsi seguenti nell' *ambito dell'area di lavoro* ai gruppi di sicurezza.
  - Assegnare il ruolo di **amministratore sinapsi** a `workspace1_SynapseAdministrators` 
  - Assegnare il ruolo **collaboratore sinapsi** a `workspace1_SynapseContributors` 
  - Assegnare il ruolo **operatore di calcolo SQL di sinapsi** a `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>PASSAGGIO 4: concedere all'area di lavoro MSI l'accesso al contenitore di archiviazione predefinito

Per eseguire pipeline ed eseguire attività di sistema, è necessario che l'identità del servizio gestito dall'area di lavoro (MSI) debba accedere a `container1` nell'account predefinito ADLS Gen2.

- Aprire il portale di Azure
- Individuare l'account di archiviazione, `storage1` , quindi `container1`
- Usando il **controllo di accesso (IAM)**, verificare che il ruolo di **collaboratore dati BLOB di archiviazione** sia assegnato all'area di lavoro MSI
  - Se non è assegnato, assegnarlo.
  - L'identità del servizio gestita presenta lo stesso nome dell'area di lavoro. In questo articolo, sarebbe `workspace1` .

## <a name="step-5-grant-the-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>PASSAGGIO 5: concedere agli amministratori di sinapsi il ruolo di collaboratore di Azure nell'area di lavoro 

Per creare pool SQL, pool di Apache Spark e runtime di integrazione, gli utenti devono avere almeno l'accesso di collaboratore Azure nell'area di lavoro. Il ruolo Collaboratore consente inoltre a questi utenti di gestire le risorse, incluse la sospensione e la scalabilità.

- Aprire il portale di Azure
- Individuare l'area di lavoro, `workspace1`
- Assegnare il ruolo di **collaboratore di Azure** a `workspace1` `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>PASSAGGIO 6: assegnare il ruolo di amministratore di SQL Active Directory

La workstation Creator viene configurata automaticamente come amministratore Active Directory per l'area di lavoro.  Questo ruolo può essere concesso solo a un singolo utente o gruppo. In questo passaggio si assegna il Active Directory amministratore nell'area di lavoro al `workspace1_SynapseSQLAdministrators` gruppo di sicurezza.  L'assegnazione di questo ruolo concede a questo gruppo l'accesso amministratore con privilegi elevati a tutti i pool SQL.   

- Aprire il portale di Azure
- Passare a `workspace1`.
- In **Impostazioni** selezionare **amministratore di SQL Active Directory**
- Selezionare **imposta amministratore** e scegliere **`workspace1_SynapseSQLAdministrators`**

>[!Note]
>Questo passaggio è facoltativo.  È possibile scegliere di concedere al gruppo Administrators di SQL un ruolo con privilegi di minore importanza. Per assegnare `db_owner` o altri ruoli SQL, è necessario eseguire gli script in ogni database SQL. 

## <a name="step-7-grant-access-to-sql-pools"></a>PASSAGGIO 7: concedere l'accesso ai pool SQL

Per impostazione predefinita, a tutti gli utenti assegnati al ruolo di amministratore di sinapsi viene assegnato anche il `db_owner` ruolo SQL nel pool SQL senza server, ovvero "predefinito".

L'accesso ai pool SQL per altri utenti e per l'area di lavoro MSI viene controllato mediante le autorizzazioni SQL.  Per l'assegnazione delle autorizzazioni SQL è necessario che gli script SQL vengano eseguiti in ogni pool SQL dopo la creazione.  Esistono tre casi in cui è necessario eseguire gli script seguenti:
1. Concessione ad altri utenti di accedere al pool SQL senza server,' predefinito '
2. Concessione dell'accesso a un pool dedicato a tutti gli utenti
3. Concessione dell'accesso MSI dell'area di lavoro a un pool SQL per consentire la corretta esecuzione delle pipeline che richiedono l'accesso al pool SQL.

Di seguito sono riportati gli script SQL di esempio.

Per concedere l'accesso a un pool SQL dedicato, gli script possono essere eseguiti dal creatore dell'area di lavoro o da qualsiasi membro del `workspace1_SynapseSQL Administrators` gruppo.  

Per concedere l'accesso al pool SQL senza server,' built-in ', gli script possono essere eseguiti anche da qualsiasi membro del  `workspace1_SynapseAdministrators` gruppo. 

> [!TIP]
> È necessario eseguire i passaggi seguenti per **ogni** pool SQL per concedere agli utenti l'accesso a tutti i database SQL tranne che nella sezione [autorizzazione con ambito area di lavoro](#workspace-scoped-permission) in cui è possibile assegnare un ruolo sysadmin all'utente.

### <a name="step-71-serverless-sql-pools"></a>PASSAGGIO 7,1: pool SQL senza server

In questa sezione sono disponibili esempi su come concedere a un utente un'autorizzazione per un determinato database o autorizzazioni complete per il server.

> [!NOTE]
> Negli esempi di script sostituire *alias* con l'alias dell'utente o del gruppo a cui viene concesso l'accesso e il *dominio* con il dominio aziendale in uso.

#### <a name="pool-scoped-permission"></a>Autorizzazione con ambito pool

Per concedere l'accesso a un utente a un **singolo** pool SQL senza server, seguire i passaggi in questo esempio:

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

#### <a name="workspace-scoped-permission"></a>Autorizzazione con ambito area di lavoro

Per concedere l'accesso completo a **tutti** i pool SQL senza server nell'area di lavoro, usare lo script in questo esempio:

```sql
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE  sysadmin  ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>PASSAGGIO 7,2: pool SQL dedicati

Per concedere l'accesso a un **singolo** pool SQL dedicato, attenersi alla seguente procedura nell'editor di script sinapsi SQL:

1. Creare l'utente nel database eseguendo il comando seguente nel database di destinazione, selezionato usando l'elenco *a discesa Connetti a* :

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
> *db_datareader* e *db_datawriter* possono funzionare per le autorizzazioni di lettura/scrittura se non si desidera concedere *db_owner* autorizzazione.
> Per fare in modo che un utente Spark legga e scriva direttamente da Spark in o da un pool SQL, è richiesta l'autorizzazione *db_owner* .

Dopo aver creato gli utenti, verificare che il pool SQL senza server sia in grado di eseguire query sull'account di archiviazione.

### <a name="step-73-sl-access-control-for-workspace-pipeline-runs"></a>PASSAGGIO 7,3: controllo di accesso SL per le esecuzioni di pipeline dell'area di lavoro

### <a name="workspace-managed-identity"></a>Identità gestita dell'area di lavoro

> [!IMPORTANT]
> Per eseguire correttamente le pipeline che includono set di impostazioni o attività che fanno riferimento a un pool SQL, è necessario concedere all'identità dell'area di lavoro l'accesso al pool SQL.

Eseguire i comandi seguenti in ogni pool SQL per consentire all'identità gestita dell'area di lavoro di eseguire le pipeline nel database del pool SQL:

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

## <a name="step-8-add-users-to-security-groups"></a>PASSAGGIO 8: aggiungere utenti ai gruppi di sicurezza

La configurazione iniziale per il sistema di controllo degli accessi è stata completata.

Per gestire l'accesso, è possibile aggiungere e rimuovere utenti dai gruppi di sicurezza configurati.  Sebbene sia possibile assegnare manualmente gli utenti ai ruoli sinapsi, le autorizzazioni non verranno configurate in modo coerente. In alternativa, limitarsi ad aggiungere o rimuovere gli utenti nei gruppi di sicurezza.

## <a name="step-9-network-security"></a>PASSAGGIO 9: sicurezza di rete

Come passaggio finale per proteggere l'area di lavoro, è necessario proteggere l'accesso alla rete usando:
- [Firewall area di lavoro](./synapse-workspace-ip-firewall.md)
- [Rete virtuale gestita](./synapse-workspace-managed-vnet.md) 
- [Endpoint privati](./synapse-workspace-managed-private-endpoints.md)
- [Collegamento privato](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>PASSAGGIO 10: completamento

L'area di lavoro è ora completamente configurata e protetta.

## <a name="supporting-more-advanced-scenarios"></a>Supporto di scenari più avanzati

Questa guida è incentrata sulla configurazione di un sistema di controllo di accesso di base. È possibile supportare scenari più avanzati creando gruppi di sicurezza aggiuntivi e assegnando questi gruppi a ruoli più granulari in ambiti più specifici. Si considerino i casi seguenti:

**Abilitare git-supporto** per l'area di lavoro per scenari di sviluppo più avanzati, tra cui ci/CD.  In modalità Git le autorizzazioni git determineranno se un utente può eseguire il commit delle modifiche nel ramo di lavoro.  La pubblicazione nel servizio si verifica solo dal ramo collaborazione.  Si consiglia di creare un gruppo di sicurezza per gli sviluppatori che devono sviluppare ed eseguire il debug degli aggiornamenti in un ramo di lavoro, ma non devono pubblicare le modifiche al servizio Live.

**Limitare l'accesso degli sviluppatori** a risorse specifiche.  Creare gruppi di sicurezza aggiuntivi per gli sviluppatori che necessitano dell'accesso solo a risorse specifiche.  Assegnare a questi gruppi i ruoli sinapsi appropriati che hanno come ambito pool Spark specifici, Runtime di integrazione o credenziali.

**Impedire agli operatori di accedere agli elementi del codice**.  Creare gruppi di sicurezza per gli operatori che devono monitorare lo stato operativo delle risorse di calcolo sinapsi e visualizzare i log, ma che non necessitano dell'accesso al codice o per pubblicare aggiornamenti al servizio. Assegnare a questi gruppi il ruolo di operatore di calcolo con ambito per pool Spark specifici e runtime di integrazione.  

## <a name="next-steps"></a>Passaggi successivi

Informazioni [su come gestire le assegnazioni di ruolo RBAC per le sinapsi](./how-to-manage-synapse-rbac-role-assignments.md) creare un' [area di lavoro sinapsi](../quickstart-create-workspace.md)
