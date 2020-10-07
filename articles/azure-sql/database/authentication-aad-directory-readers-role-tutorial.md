---
title: Assegnare il ruolo con autorizzazioni di lettura nella directory a un gruppo di Azure AD e gestire le assegnazioni di ruolo
description: Questo articolo illustra come abilitare il ruolo con autorizzazioni di lettura nella directory usando i gruppi di Azure AD per gestire le assegnazioni di ruolo di Azure AD con il database SQL di Azure, l'istanza gestita di SQL di Azure e Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: d6c447deedbdcc4f2439fc069f368db88b3560b9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278040"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>Esercitazione: Assegnare il ruolo con autorizzazioni di lettura nella directory a un gruppo di Azure AD e gestire le assegnazioni di ruolo

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> L'assegnazione del **ruolo con autorizzazioni di lettura nella directory** a un gruppo in questo articolo è in **anteprima pubblica**. 

Questo articolo illustra come creare un gruppo in Azure Active Directory (Azure AD) e come assegnare tale gruppo al [**ruolo con autorizzazioni di lettura nella directory**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers). Le autorizzazioni del ruolo con autorizzazioni di lettura nella directory consentono ai proprietari del gruppo di aggiungere altri membri al gruppo, ad esempio un'[identità gestita](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) di [database SQL di Azure](sql-database-paas-overview.md), [Istanza gestita di SQL di Azure ](../managed-instance/sql-managed-instance-paas-overview.md) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). In questo modo si evita che un [amministratore globale](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) o un [amministratore ruolo con privilegi](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) debba assegnare il ruolo con autorizzazioni di lettura nella directory direttamente per ogni identità del server logico SQL di Azure nel tenant.

Questa esercitazione usa la funzionalità introdotta in [Usare i gruppi cloud per gestire le assegnazioni di ruolo in Azure Active Directory (anteprima)](../../active-directory/users-groups-roles/roles-groups-concept.md). 

Per altre informazioni sui vantaggi derivanti dall'assegnazione del ruolo con autorizzazioni di lettura nella directory a un gruppo di Azure AD per Azure SQL, vedere [Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="prerequisites"></a>Prerequisiti

- Istanza di Azure AD. Per altre informazioni, vedere [Configurare e gestire l'autenticazione di Azure AD con Azure SQL](authentication-aad-configure.md).
- Un database SQL, un'istanza gestita di SQL o Azure Synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Assegnazione del ruolo con autorizzazioni di lettura nella directory con il portale di Azure

### <a name="create-a-new-group-and-assign-owners-and-role"></a>Creare un nuovo gruppo e assegnare i proprietari e i ruoli

1. Per questa configurazione iniziale è necessario un utente con autorizzazioni di [amministratore globale](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) o [amministratore ruolo con privilegi](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).
1. L'utente con privilegi dovrà accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla risorsa **Azure Active Directory**. In **Gestita** passare a **Gruppi**. Selezionare **Nuovo gruppo** per creare un nuovo gruppo.
1. Selezionare **Sicurezza** come tipo di gruppo e compilare gli altri campi. Assicurarsi che l'impostazione **I ruoli di Azure AD possono essere assegnati al gruppo (anteprima)** venga impostata su **Sì**. Assegnare quindi al gruppo il **ruolo con autorizzazioni di lettura nella directory** di Azure AD.
1. Assegnare gli utenti di Azure AD come proprietari al gruppo che è stato creato. Il proprietario di un gruppo può essere un utente di Active Directory normale a cui non è assegnato alcun ruolo amministrativo di Azure AD. Il proprietario deve essere un utente che gestisce il database SQL, l'Istanza gestita di SQL o Azure Synapse.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="aad-new-group":::

1. Selezionare **Crea**

### <a name="checking-the-group-that-was-created"></a>Verifica del gruppo creato

> [!NOTE]
> Assicurarsi che il **Tipo di gruppo** sia **Sicurezza**. I gruppi di *Microsoft 365* non sono supportati per Azure SQL.

Per verificare e gestire il gruppo che è stato creato, tornare al riquadro **Gruppi** nel portale di Azure e cercare il nome del gruppo. È possibile aggiungere altri proprietari e membri nel menu **Proprietari** e **Membri** dell'impostazione **Gestisci** dopo aver selezionato il gruppo. È anche possibile esaminare i **Ruoli assegnati** per il gruppo.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="aad-new-group":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>Aggiungere l'identità gestita di SQL di Azure al gruppo

> [!NOTE]
> Per questo esempio si userà un'Istanza gestita di SQL, ma è possibile applicare procedure simili per ottenere lo stesso risultato per il database SQL o Azure Synapse.

Per i passaggi successivi, l'amministratore globale o l'utente amministratore ruolo con privilegi non è più necessario.

1. Accedere al portale di Azure come l'utente che gestisce l'istanza gestita di SQL, che è anche un proprietario del gruppo creato in precedenza.

1. Trovare il nome della risorsa **Istanza gestita di SQL** nel portale di Azure.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="aad-new-group":::

   Durante la creazione dell'istanza gestita di SQL, è stata creata un'identità di Azure per l'istanza. L'identità creata ha lo stesso nome del prefisso del nome dell'istanza gestita di SQL. Per trovare l'entità servizio per l'identità dell'istanza gestita di SQL creata come un'applicazione Azure AD, seguire questa procedura:

    - Passare alla risorsa **Azure Active Directory**. Nell'impostazione **Gestisci** selezionare **Applicazioni aziendali**. L'**ID oggetto** è l'identità dell'istanza.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="aad-new-group":::

1. Passare alla risorsa **Azure Active Directory**. In **Gestita** passare a **Gruppi**. Selezionare il gruppo creato. Nell'impostazione **Gestisci** del gruppo selezionare **Membri**. Selezionare **Aggiungi membri** e aggiungere l'entità servizio dell'istanza gestita di SQL come membro del gruppo cercando il nome trovato sopra.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="aad-new-group":::

> [!NOTE]
> Potrebbero essere necessari alcuni minuti per propagare le autorizzazioni dell'entità servizio attraverso il sistema di Azure e consentire l'accesso all'API Graph di Azure AD. Potrebbe essere necessario attendere alcuni minuti prima di effettuare il provisioning di un amministratore di Azure AD per l'istanza gestita di SQL.

### <a name="remarks"></a>Osservazioni

Per il database SQL e Azure Synapse, è possibile creare l'identità del server durante la creazione del server logico SQL di Azure o dopo la creazione del server. Per altre informazioni su come creare o impostare l'identità del server nel database SQL o in Azure Synapse, vedere [Abilitare le entità servizio per creare gli utenti di Azure AD](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Per l'istanza gestita di SQL, è necessario assegnare il **ruolo con autorizzazioni di lettura nella directory** a un'identità dell'istanza gestita prima di poter [configurare un amministratore di Azure AD per l'istanza gestita](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Quando si configura un amministratore di Azure AD per il server logico, non è necessario assegnare il **ruolo con autorizzazioni di lettura nella directory** all'identità del server per il database SQL o Azure Synapse. Tuttavia, per abilitare la creazione di un oggetto Azure AD nel database SQL o in Azure Synapse per conto di un'applicazione Azure AD, è necessario il **ruolo con autorizzazioni di lettura nella directory**. Se il ruolo non è assegnato all'identità del server logico SQL, la creazione degli utenti di Azure AD in Azure SQL avrà esito negativo. Per altre informazioni, vedere [Entità servizio di Azure Active Directory con Azure SQL](authentication-aad-service-principal.md).

## <a name="directory-readers-role-assignment-using-powershell"></a>Assegnazione del ruolo con autorizzazioni di lettura nella directory con PowerShell

> [!IMPORTANT]
> Queste procedure iniziali dovranno essere eseguite da un [amministratore globale](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) o [amministratore ruolo con privilegi](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator). Oltre a PowerShell, Azure AD offre l'API Microsoft Graph per [Creare un gruppo a cui è possibile assegnare ruoli in Azure AD](../../active-directory/users-groups-roles/roles-groups-create-eligible.md#using-microsoft-graph-api).

1. Importare quindi il modulo di Azure AD PowerShell (anteprima) usando questo comando. Potrebbe essere necessario eseguire PowerShell come amministratore.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Connettersi al tenant di Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Creare un gruppo di sicurezza per assegnare il **ruolo con autorizzazioni di lettura nella directory**.

    - È possibile modificare `DirectoryReaderGroup`, `Directory Reader Group` e `DirRead` in base alle preferenze.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. Assegnare il **ruolo con autorizzazioni di lettura nella directory** al gruppo.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. Assegnare i proprietari al gruppo.

    - Sostituire `<username>` con l'utente che sarà il proprietario del gruppo. Per aggiungere più proprietari ripetere questa procedura.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    Controllare i proprietari del gruppo usando il comando seguente:

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    È anche possibile verificare i proprietari del gruppo nel [portale di Azure](https://portal.azure.com). Seguire la procedura illustrata in [Verifica del gruppo creato](#checking-the-group-that-was-created).

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>Assegnazione dell'entità servizio come membro del gruppo

Per i passaggi successivi, l'amministratore globale o l'utente amministratore ruolo con privilegi non è più necessario.

1. Usando un proprietario del gruppo, che gestisce anche la risorsa SQL di Azure, eseguire questo comando per connettersi ad Azure AD.

    ```powershell
    Connect-AzureAD
    ```

1. Assegnare l'entità servizio come membro del gruppo che è stato creato.

    - Sostituire `<ServerName>` con il nome del server logico SQL di Azure o il nome dell'istanza gestita. Per altre informazioni, vedere la sezione [Aggiungere l'identità del servizio Azure SQL al gruppo](#add-azure-sql-managed-identity-to-the-group)

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    Il comando seguente restituisce l'ID oggetto dell'entità servizio che indica che l'aggiunta al gruppo è riuscita:

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL](authentication-aad-directory-readers-role.md)
- [Esercitazione: Creare utenti Azure AD con applicazioni di Azure AD](authentication-aad-service-principal-tutorial.md)
- [Configurare e gestire l'autenticazione di Azure AD con Azure SQL](authentication-aad-configure.md)