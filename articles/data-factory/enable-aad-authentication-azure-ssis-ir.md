---
title: Abilitare AAD per Azure SSIS Integration Runtime
description: Questo articolo descrive come abilitare l'autenticazione di Azure Active Directory con l'identità gestita per Azure Data Factory per creare Azure-SSIS Integration Runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: fd9433c2482c4ddd907f7e30c0028dc2a15faed2
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187677"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Abilitare l'autenticazione di Azure Active Directory per Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra come abilitare l'autenticazione Azure Active Directory (Azure AD) con l'identità gestita per il Azure Data Factory (ADF) e usarla invece dei metodi di autenticazione convenzionali, ad esempio l'autenticazione SQL, per:

- Creare un Azure-SSIS Integration Runtime (IR) che effettuerà il provisioning del database del catalogo SSIS (SSISDB) nel database SQL o in SQL Istanza gestita per conto dell'utente.

- Connettersi a varie risorse di Azure durante l'esecuzione di pacchetti SSIS in Azure-SSIS IR.

Per altre informazioni sull'identità gestita per il file ADF, vedere [identità gestita per data factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>
> - In questo scenario, Azure AD l'autenticazione con l'identità gestita per il file ADF viene usata solo nella creazione e nelle successive operazioni di avvio del runtime di integrazione SSIS che effettuerà a sua volta il provisioning e la connessione a SSISDB. Per le esecuzioni di pacchetti SSIS, il runtime di integrazione SSIS si connette ancora a SSISDB usando l'autenticazione SQL con account completamente gestiti creati durante il provisioning di SSISDB.
> - Se il runtime di integrazione SSIS è già stato creato con l'autenticazione SQL, non è possibile riconfigurarlo per usare l'autenticazione Azure AD tramite PowerShell, ma è possibile farlo tramite l'app portale di Azure/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Abilitare Azure Active Directory nel database SQL di Azure

Il database SQL supporta la creazione di un database con un utente Azure AD. È prima necessario creare un gruppo di Azure AD con l'identità gestita per ADF come membro. Successivamente, è necessario impostare un utente Azure AD come amministratore Active Directory per il database SQL e quindi connettersi ad esso in SQL Server Management Studio (SSMS) usando tale utente. È infine necessario creare un utente indipendente che rappresenta il gruppo di Azure AD, in modo che l'identità gestita per ADF possa essere usata da Azure-SSIS IR per creare SSISDB per conto dell'utente.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Creare un gruppo di Azure AD con l'identità gestita per ADF come membro

È possibile usare un gruppo di Azure AD esistente o crearne uno nuovo con Azure AD PowerShell.

1.  Installare il modulo [Azure ad PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) .

2.  Accedere usando  `Connect-AzureAD`, eseguire il cmdlet seguente per creare un gruppo e salvarlo in una variabile:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Il risultato sarà analogo all'esempio seguente, che visualizza anche il valore della variabile:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Aggiungere l'identità gestita per Azure Data Factory al gruppo. Per ottenere l'ID dell'oggetto identità gestita principale (ad esempio, 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ma non usare l'ID dell'applicazione di identità gestita per questo scopo, è possibile seguire l'articolo [identità gestita per data factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) .

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    È anche possibile controllare l'appartenenza a gruppi in un secondo momento.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-sql-database"></a>Configurare l'autenticazione Azure AD per il database SQL

È possibile  [configurare e gestire l'autenticazione di Azure AD con SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) seguendo questa procedura:

1.  In portale di Azure selezionare **tutti i servizi**  ->  **server SQL** dal lato sinistro.

2.  Selezionare il server nel database SQL da configurare con l'autenticazione Azure AD.

3.  Nella sezione **Impostazioni** del pannello selezionare **Amministratore di Active Directory**.

4.  Sulla barra dei comandi selezionare **Imposta amministratore**.

5.  Selezionare un account utente Azure AD come amministratore del server e quindi selezionare **Seleziona.**

6.  Sulla barra dei comandi selezionare **Salva**.

### <a name="create-a-contained-user-in-sql-database-representing-the-azure-ad-group"></a>Creare un utente indipendente nel database SQL che rappresenta il gruppo di Azure AD

Per questo passaggio successivo, è necessario [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   (SSMS).

1. Avviare SSMS.

2. Nella finestra di dialogo **Connetti al server** immettere il nome del server nel campo **nome server** .

3. Nel campo **autenticazione** selezionare **Active Directory universale con supporto** dell'autenticazione a più fattori (è anche possibile usare gli altri due tipi di autenticazione Active Directory, vedere [configurare e gestire l'autenticazione di Azure ad con SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. Nel campo **nome utente** immettere il nome dell'account Azure ad impostato come amministratore del server, ad esempio testuser@xxxonline.com .

5. Selezionare **Connetti** e completare il processo di accesso.

6. In **Esplora oggetti** espandere la cartella **Database** -> **Database di sistema**.

7. Fare clic con il pulsante destro del mouse su database **Master** e selezionare **nuova query**.

8. Nella finestra query immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Il comando dovrebbe completare l'operazione correttamente, creando un utente indipendente per rappresentare il gruppo.

9. Cancellare il contenuto della finestra di query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di creare un database (SSISDB).

10. Se il database SSISDB è stato creato usando l'autenticazione SQL e si vuole passare a usare Azure AD autenticazione per il Azure-SSIS IR per accedervi, fare clic con il pulsante destro del mouse sul database **SSISDB** e scegliere **nuova query**.

11. Nella finestra query immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Il comando dovrebbe completare l'operazione correttamente, creando un utente indipendente per rappresentare il gruppo.

12. Cancellare il contenuto della finestra di query, immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di creare database SSISDB.

## <a name="enable-azure-ad-on-sql-managed-instance"></a>Abilitare Azure AD in SQL Istanza gestita

SQL Istanza gestita supporta la creazione diretta di un database con l'identità gestita per il file ADF. Non è necessario aggiungere l'identità gestita per il file ADF a un gruppo Azure AD né creare un utente indipendente che rappresenti tale gruppo in SQL Istanza gestita.

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Configurare l'autenticazione Azure AD per Istanza gestita SQL di Azure

Seguire i passaggi descritti in effettuare il [provisioning di un amministratore Azure Active Directory per SQL istanza gestita](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-sql-managed-instance"></a>Aggiungere l'identità gestita per il file ADF come utente in SQL Istanza gestita

Per questo passaggio successivo, è necessario [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   (SSMS).

1.  Avviare SSMS.

2.  Connettersi a SQL Istanza gestita usando un account SQL Server che sia un **amministratore di ruolo**. Si tratta di una limitazione temporanea che verrà rimossa una volta Azure AD entità server (account di accesso) per Azure SQL Istanza gestita diventerà GA. Se si tenta di usare un account amministratore Azure AD per creare l'account di accesso, verrà visualizzato il seguente errore: messaggio 15247, livello 16, stato 1, riga 1 utente non dispone dell'autorizzazione per eseguire questa azione.

3.  In **Esplora oggetti** espandere la cartella **Database** -> **Database di sistema**.

4.  Fare clic con il pulsante destro del mouse su database **Master** e selezionare **nuova query**.

5.  Nella finestra query eseguire lo script T-SQL seguente per aggiungere l'identità gestita per il file ADF come utente

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Il comando dovrebbe completare l'operazione correttamente, concedendo all'identità gestita per ADF la possibilità di creare un database (SSISDB).

6.  Se il database SSISDB è stato creato usando l'autenticazione SQL e si vuole passare a usare Azure AD autenticazione per il Azure-SSIS IR per accedervi, fare clic con il pulsante destro del mouse sul database **SSISDB** e scegliere **nuova query**.

7.  Nella finestra query immettere il comando T-SQL seguente e selezionare **Esegui** sulla barra degli strumenti.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Il comando dovrebbe completare l'operazione correttamente, concedendo all'identità gestita per ADF la possibilità di accedere a SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Effettuare il provisioning di Azure-SSIS IR nel portale di Azure o nell'app ADF

Quando si effettua il provisioning di Azure-SSIS IR nel portale di Azure o nell'app ADF, nella pagina **Impostazioni SQL** selezionare l'opzione **Use AAD authentication with the managed identity for your ADF** (Usa autenticazione AAD con l'identità gestita per Azure Data Factory). La schermata seguente mostra le impostazioni per il runtime di integrazione con database SQL che ospita SSISDB. Per IR con SQL Istanza gestita che ospita SSISDB, il **livello di servizio del database del catalogo** e **consentire ai servizi di Azure di accedere alle** impostazioni non sono applicabili, mentre altre impostazioni sono le stesse.

Per altre informazioni su come creare Azure-SSIS IR, vedere [Creare un runtime di integrazione SSIS di Azure in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Impostazioni per il runtime di integrazione Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Effettuare il provisioning di Azure-SSIS IR con PowerShell

Per eseguire il provisioning del runtime di integrazione Azure-SSIS con PowerShell, procedere come segue:

1.  Installare [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)   modulo.

2.  Nello script non impostare il parametro `CatalogAdminCredential`. Ad esempio:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Eseguire pacchetti SSIS con autenticazione identità gestita

Quando si eseguono pacchetti SSIS in Azure-SSIS IR, è possibile usare l'autenticazione dell'identità gestita per connettersi a varie risorse di Azure. Attualmente è già supportata l'autenticazione dell'identità gestita nelle gestioni connessioni seguenti.

- [gestione connessione OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestione connessione ADO.NET](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Gestione connessione dell'archiviazione di Azure](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
