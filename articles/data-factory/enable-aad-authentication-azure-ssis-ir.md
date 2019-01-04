---
title: Abilitare l'autenticazione di Azure Active Directory per il runtime di integrazione Azure-SSIS | Microsoft Docs
description: Questo articolo descrive come configurare il runtime di integrazione Azure-SSIS per abilitare le connessioni che usano l'autenticazione di Azure Active Directory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: d2000e626166304e92556e3c965df175a27046ad
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321068"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Abilitare l'autenticazione di Azure Active Directory per il runtime di integrazione Azure-SSIS

Questo articolo illustra come creare un runtime di integrazione Azure-SSIS con l'identità del servizio di Azure Data Factory. È possibile usare l'autenticazione di Azure Active Directory (Azure AD) con l'identità gestita per Azure Data Factory anziché l'autenticazione SQL per creare un runtime di integrazione Azure-SSIS.

Per altre informazioni sull'identità gestita per Azure Data Factory, vedere [Identità del servizio di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
> Se è già stato creato un runtime di integrazione Azure-SSIS con autenticazione SQL, ora è possibile riconfigurarlo tramite PowerShell in modo da usare l'autenticazione di Azure AD.

## <a name="enable-azure-ad-on-azure-sql-database"></a>Abilitare Azure Active Directory nel database SQL di Azure

Il database SQL di Azure supporta la creazione di un database con un utente di Azure AD. Di conseguenza, è possibile impostare un utente di Azure AD come amministratore di Active Directory e quindi accedere a SQL Server Management Studio (SSMS) usando tale utente. Successivamente si può creare un utente indipendente per il gruppo di Azure AD, in modo da consentire al runtime di integrazione di creare il catalogo di SQL Server Integration Services (SSIS) nel server.

### <a name="create-a-group-in-azure-ad-and-make-the-managed-identity-for-your-data-factory-a-member-of-the-group"></a>Creare un gruppo in Azure AD e fare in modo che l'identità gestita per Azure Data Factory diventi membro del gruppo

È possibile usare un gruppo di Azure AD esistente o crearne uno nuovo con PowerShell di Azure AD.

1.  Installare il [modulo Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

2.  Accedere usando  `Connect-AzureAD` ed eseguire il comando seguente per creare il gruppo, quindi salvarlo in una variabile:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Il risultato sarà analogo all'esempio seguente, che esamina anche il valore della variabile:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Aggiungere l'identità gestita per Azure Data Factory al gruppo. È possibile seguire la procedura in [Identità del servizio di Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) per ottenere l'ID IDENTITÀ dell'entità SERVIZIO (ad esempio 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ma non usare l'ID APPLICAZIONE IDENTITÀ DEL SERVIZIO per questo scopo).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    È anche possibile esaminare l'appartenenza a gruppi in un secondo momento.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Abilitare l'autenticazione di Azure AD per il database SQL di Azure

È possibile  [configurare l'autenticazione di Azure AD per il database SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) seguendo questa procedura:

1.  Nel portale di Azure selezionare  **Tutti i servizi** -> **SQL Server**  dal menu a sinistra.

2.  Selezionare il database SQL da abilitare per l'autenticazione di Azure AD.

3.  Nella sezione  **Impostazioni**  del pannello selezionare  **Amministratore di Active Directory**.

4.  Sulla barra dei comandi selezionare  **Imposta amministratore**.

5.  Selezionare un account utente di Azure AD da rendere amministratore del server e quindi scegliere  **Seleziona**.

6.  Sulla barra dei comandi selezionare  **Salva**.

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Creare un utente indipendente nel database che rappresenta il gruppo di Azure AD

Per il passaggio successivo è necessario  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Avviare SQL Server Management Studio.

2.  Nella finestra di dialogo  **Connetti al server**  immettere il nome del server SQL nel campo  **Nome server** .

3.  Nel campo  **Autenticazione**  selezionare  **Active Directory - Universale con supporto MFA**. (È possibile usare anche altri due tipi di autenticazione di Active Directory. Vedere [Configurare e gestire l'autenticazione di Azure Active Directory con il database SQL, con Istanza gestita oppure con SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).)

4.  Nel campo  **Nome utente**  immettere il nome dell'account di Azure AD impostato come amministratore del server, ad esempio testuser@xxxonline.com.

5.  selezionare **Connetti**. Completare il processo di accesso.

6.  In  **Esplora oggetti** espandere la cartella  **Database** -> Database di sistema.

7.  Fare clic con il pulsante destro del mouse sul database **master** e scegliere  **Nuova query**.

8.  Nella finestra della query immettere la riga seguente e selezionare  **Esegui**  sulla barra degli strumenti:

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Il comando dovrebbe completare l'operazione correttamente, creando l'utente indipendente per il gruppo.

9.  Cancellare il contenuto della finestra di query, immettere la riga seguente e selezionare **Esegui** sulla barra degli strumenti:

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    Il comando dovrebbe completare l'operazione correttamente, concedendo all'utente indipendente la possibilità di creare database.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Abilitare Azure AD in Istanza gestita di database SQL di Azure

L'istanza gestita di database SQL di Azure supporta la creazione di un database direttamente con identità del servizio gestita. Non è necessario unire l'identità del servizio gestita data factory a un gruppo AD o creare l'utente contenuto in istanza gestita.

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database-managed-instance"></a>Abilitare l'autenticazione di Azure AD per l'Istanza gestita di database SQL di Azure

1.   Nel portale di Azure selezionare **Tutti i servizi** -> **SQL Server** dal menu a sinistra.

1.   Selezionare il server SQL da abilitare per l'autenticazione di Azure AD.

1.   Nella sezione **Impostazioni** del pannello selezionare **Amministratore di Active Directory**.

1.   Sulla barra dei comandi selezionare **Imposta amministratore**.

1.   Selezionare un account utente di Azure AD da rendere amministratore del server e quindi scegliere **Seleziona**.

1.   Sulla barra dei comandi selezionare **Salva**.

### <a name="add-data-factory-msi-as-a-user-to-the-azure-sql-database-managed-instance"></a>Aggiungere l'identità del servizio gestita data factory come un utente all'istanza gestita di database SQL di Azure

1.  Avviare SQL Server Management Studio.

2.  Accedere con un account amministratore SQL o un account amministratore di Active Directory.

3.  In Esplora oggetti espandere la cartella Database -> Database di sistema.

4.  Fare clic con il pulsante destro del mouse sul database master e scegliere **Nuova query**.

5.  È possibile usare l'articolo [Identità del servizio di Azure Data Factory](data-factory-service-identity.md) per ottenere l'ID APPLICAZIONE IDENTITÀ DEL SERVIZIO dell'entità di sicurezza. (Non usare l'ID IDENTITÀ DEL SERVIZIO per questo scopo.)

6.  Nella finestra di query eseguire lo script seguente per convertire l'ID APPLICAZIONE IDENTITÀ DEL SERVIZIO nel tipo binario:

    ```sql
    DECLARE @applicationId uniqueidentifier = {your service identity application id}
    select CAST(@applicationId AS varbinary)
    ```

7.  È possibile ottenere il valore nella finestra dei risultati.

8.  Cancellare la finestra di query ed eseguire lo script seguente:

    ```sql
    CREATE LOGIN [{MSI name}] FROM EXTERNAL PROVIDER with SID ={your service identity application id in binary type}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{MSI name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{MSI name}]
    ```

9.  Il comando viene completato correttamente.

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>Eseguire il provisioning del runtime di integrazione Azure-SSIS nel portale

Quando si esegue il provisioning del runtime di integrazione Azure-SSIS nel portale di Azure, nella pagina **Impostazioni SQL** selezionare l'opzione "Use AAD authentication with the managed identity for your ADF" (Usa autenticazione AAD con l'identità gestita per Azure Data Factory). (La schermata seguente mostra le impostazioni per il runtime di integrazione con il database SQL di Azure. Per il runtime di integrazione con Istanza gestita, la proprietà "Catalog Database Service Tier" (Livello di servizio database di catalogo) non è disponibile, mentre le altre impostazioni sono uguali.)

Per altre informazioni su come creare un runtime di integrazione Azure-SSIS, vedere [Creare un runtime di integrazione SSIS di Azure in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

![Impostazioni per il runtime di integrazione Azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>Eseguire il provisioning del runtime di integrazione Azure-SSIS con PowerShell

Per eseguire il provisioning del runtime di integrazione Azure-SSIS con PowerShell, procedere come segue:

1.  Installare il modulo [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) .

2.  Nello script non impostare il parametro *CatalogAdminCredential*. Ad esempio: 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
