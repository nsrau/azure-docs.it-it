---
title: Entità servizio di Azure Active Directory con Azure SQL
description: Le applicazioni Azure AD (entità servizio) supportano Azure AD la creazione di utenti in database SQL di Azure, Istanza gestita SQL di Azure e Azure sinapsi Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: 57d24c824782bdc6530b78450fc55a879a511ddc
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367687"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Entità servizio di Azure Active Directory con Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Il supporto per la creazione di utenti Azure Active Directory (Azure AD) nel database SQL di Azure (database SQL) e nell' [analisi delle sinapsi di Azure](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) per conto di applicazioni Azure ad (entità servizio) è attualmente disponibile in **anteprima pubblica**.

> [!NOTE]
> Questa funzionalità è già supportata per SQL Istanza gestita.

## <a name="service-principal-azure-ad-applications-support"></a>Supporto per entità servizio (applicazioni Azure AD)

Questo articolo si applica alle applicazioni integrate con Azure AD e che fanno parte della registrazione Azure AD. Queste applicazioni richiedono spesso l'autenticazione e l'autorizzazione per l'accesso a SQL di Azure per eseguire varie attività. Questa funzionalità di **anteprima pubblica** consente ora alle entità servizio di creare Azure ad utenti nel database SQL e nella sinapsi di Azure. Si è verificata una limitazione che impedisce Azure AD la creazione di oggetti per conto di Azure AD applicazioni che sono state rimosse.

Quando un'applicazione Azure AD viene registrata usando il portale di Azure o un comando di PowerShell, vengono creati due oggetti nel tenant Azure AD:

- Un oggetto applicazione
- Un oggetto entità servizio

Per altre informazioni sulle applicazioni Azure AD, vedere [oggetti applicazione e oggetti entità servizio in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) e [creare un'entità servizio di Azure con Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.2.0).

Il database SQL, la sinapsi di Azure e SQL Istanza gestita supportano gli oggetti di Azure AD seguenti:

- Utenti Azure AD (gestito, federato e Guest)
- Gruppi di Azure AD (gestito e federato)
-  Applicazioni Azure AD 

Il comando T-SQL `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` per conto di un'applicazione Azure ad è ora supportato per il database SQL e la sinapsi di Azure.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Funzionalità della creazione Azure AD utente mediante entità servizio

Il supporto di questa funzionalità è utile in Azure AD processi di automazione dell'applicazione in cui vengono creati e gestiti Azure AD oggetti nel database SQL e nella sinapsi di Azure senza interazione umana. Le entità servizio possono essere un amministratore Azure AD per il server logico SQL, come parte di un gruppo o di un singolo utente. L'applicazione può automatizzare Azure AD la creazione di oggetti nel database SQL e nella sinapsi di Azure quando viene eseguita come amministratore di sistema e non richiede privilegi SQL aggiuntivi. Ciò consente un'automazione completa della creazione di un utente di database. Questa funzionalità è supportata anche per l'identità gestita assegnata dal sistema e l'identità gestita assegnata dall'utente. Per altre informazioni, vedere [che cosa sono le identità gestite per le risorse di Azure?](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Abilitare le entità servizio per creare Azure AD utenti

Per abilitare la creazione di un oggetto Azure AD nel database SQL e nella sinapsi di Azure per conto di un'applicazione Azure AD, sono necessarie le impostazioni seguenti:

1. Assegnare l'identità del server
    - Per un nuovo server logico SQL di Azure, eseguire il comando PowerShell seguente:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Per ulteriori informazioni, vedere il comando [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) .

    - Per i server logici SQL di Azure esistenti, eseguire il comando seguente:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Per altre informazioni, vedere il comando [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver).

    - Per verificare se l'identità del server è assegnata al server, eseguire il comando Get-AzSqlServer.

    > [!NOTE]
    > È possibile assegnare l'identità del server anche usando i comandi dell'interfaccia della riga di comando. Per ulteriori informazioni, vedere [AZ SQL Server create](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) e [AZ SQL Server Update](https://docs.microsoft.com/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-update).

2. Concedere l'autorizzazione Azure AD [**directory Readers**](../../active-directory/roles/permissions-reference.md#directory-readers) all'identità del server creata o assegnata al server.
    - Per concedere questa autorizzazione, seguire la descrizione utilizzata per SQL Istanza gestita disponibile nell'articolo relativo al [Provisioning Azure ad amministratore (SQL istanza gestita)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance) .
    - L'utente Azure AD che concede questa autorizzazione deve far parte del ruolo amministratore **globale** Azure ad o amministratore **ruoli con privilegi** .

> [!IMPORTANT]
> I passaggi 1 e 2 devono essere eseguiti nell'ordine precedente. Prima di tutto, creare o assegnare l'identità del server, quindi concedere l'autorizzazione per i [**lettori di directory**](../../active-directory/roles/permissions-reference.md#directory-readers) . Se si omette uno di questi passaggi, si verificherà un errore di esecuzione durante la creazione di un oggetto Azure AD in Azure SQL per conto di un'applicazione Azure AD. Per istruzioni dettagliate su come creare un utente Azure AD per conto di un'applicazione Azure AD, vedere [esercitazione: creare Azure ad utenti che usano applicazioni Azure ad](authentication-aad-service-principal-tutorial.md).
>
> Nell' **anteprima pubblica**è possibile assegnare il ruolo **Readers di directory** a un gruppo in Azure ad. I proprietari del gruppo possono quindi aggiungere l'identità gestita come membro di questo gruppo, evitando la necessità di un amministratore **globale** o di **ruoli con privilegi** per concedere il ruolo dei **lettori di directory** . Per altre informazioni su questa funzionalità, vedere [Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL](authentication-aad-directory-readers-role.md).

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Risoluzione dei problemi e limitazioni per l'anteprima pubblica

- Quando si creano oggetti Azure AD in SQL di Azure per conto di un'applicazione Azure AD senza abilitare l'identità del server e concedere l'autorizzazione per i **lettori di directory** , l'operazione avrà esito negativo con i seguenti errori possibili. L'errore di esempio seguente è relativo all'esecuzione di un comando di PowerShell per creare un utente del database SQL `myapp` nell'articolo [esercitazione: creare Azure ad utenti che usano Azure ad applicazioni](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - Per l'errore precedente, seguire la procedura per [assegnare un'identità al server logico SQL di Azure](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) e [assegnare le autorizzazioni per i lettori di directory all'identità del server logico SQL](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > I messaggi di errore indicati in precedenza verranno modificati prima della funzionalità GA per identificare chiaramente i requisiti di installazione mancanti per Azure AD il supporto dell'applicazione.
- L'impostazione dell'applicazione Azure AD come amministratore Azure AD per SQL Istanza gestita è supportata solo con il comando CLI e con il comando PowerShell con [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) o versione successiva. Per ulteriori informazioni, vedere i comandi [AZ SQL mi ad-admin create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin?view=azure-cli-latest#az-sql-mi-ad-admin-create) e [set-AzSqlInstanceActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) . 
    - Se si vuole usare la portale di Azure per SQL Istanza gestita per impostare l'amministratore Azure AD, una possibile soluzione alternativa consiste nel creare un gruppo di Azure AD. Aggiungere quindi l'entità servizio (Azure AD applicazione) a questo gruppo e impostare questo gruppo come amministratore Azure AD per il Istanza gestita SQL.
    - L'impostazione dell'entità servizio (Azure AD applicazione) come amministratore Azure AD per il database SQL e la sinapsi di Azure è supportata tramite i comandi portale di Azure, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)e [CLI](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) .
- L'uso di un'applicazione Azure AD con un'entità servizio da un altro tenant di Azure AD avrà esito negativo durante l'accesso al database SQL o al Istanza gestita SQL creato in un tenant diverso. Un'entità servizio assegnata a questa applicazione deve provenire dallo stesso tenant del server logico SQL o Istanza gestita.
- Un modulo [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) o versioni successive se si usa PowerShell per configurare una singola applicazione di Azure AD come amministratore Azure AD per Azure SQL. Assicurarsi di aver effettuato l'aggiornamento al modulo più recente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Creare utenti Azure AD con applicazioni di Azure AD](authentication-aad-service-principal-tutorial.md)


