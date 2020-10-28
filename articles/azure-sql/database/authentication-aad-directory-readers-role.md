---
title: Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL
description: Informazioni sul ruolo del lettore di directory in Azure AD per SQL di Azure.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 2374b1fb7f355b336c713a8a3240eacc8b1f188c
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675072"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Ruolo con autorizzazioni di lettura nella directory in Azure Active Directory per Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Questa funzionalità in questo articolo è in **anteprima pubblica** .

Azure Active Directory (Azure AD) ha introdotto l' [uso di gruppi cloud per gestire le assegnazioni di ruolo in Azure Active Directory (anteprima)](../../active-directory/roles/groups-concept.md). In questo modo è possibile assegnare i ruoli Azure AD ai gruppi.

Quando si Abilita un' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) per il database SQL di Azure, azure SQL istanza gestita o Azure sinapsi Analytics, il ruolo [**readers**](../../active-directory/roles/permissions-reference.md#directory-readers) di Azure ad directory deve essere assegnato all'identità per consentire l'accesso in lettura al [Azure ad API Graph](../../active-directory/develop/active-directory-graph-api.md). L'identità gestita del database SQL e della sinapsi di Azure viene definita identità del server. L'identità gestita di SQL Istanza gestita viene definita identità dell'istanza gestita e viene assegnata automaticamente quando viene creata l'istanza. Per altre informazioni sull'assegnazione di un'identità server al database SQL o a una sinapsi di Azure, vedere [abilitare le entità servizio per la creazione di Azure ad utenti](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

Il ruolo **Readers directory** è necessario per:

- Crea account di accesso Azure AD per SQL Istanza gestita
- Rappresentare Azure AD utenti in Azure SQL
- Eseguire la migrazione di SQL Server utenti che utilizzano l'autenticazione di Windows a SQL Istanza gestita con l'autenticazione Azure AD (tramite il comando [ALTER USER (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) )
- Modificare l'amministratore di Azure AD per SQL Istanza gestita
- Consentire alle [entità servizio (applicazioni)](authentication-aad-service-principal.md) di creare Azure ad utenti in Azure SQL

## <a name="assigning-the-directory-readers-role"></a>Assegnazione del ruolo lettori directory

Per assegnare il ruolo [**lettore directory**](../../active-directory/roles/permissions-reference.md#directory-readers) a un'identità, è necessario disporre delle autorizzazioni amministratore [globale](../../active-directory/roles/permissions-reference.md#global-administrator--company-administrator) o [amministratore ruolo con privilegi](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) . Gli utenti che spesso gestiscono o distribuiscono il database SQL, SQL Istanza gestita o la sinapsi di Azure potrebbero non avere accesso a questi ruoli con privilegi elevati. Questo può causare spesso complicazioni per gli utenti che creano risorse SQL di Azure non pianificate o che necessitano di assistenza da membri del ruolo con privilegi elevati spesso inaccessibili nelle organizzazioni di grandi dimensioni.

Per l'istanza gestita di SQL, è necessario assegnare il **ruolo con autorizzazioni di lettura nella directory** a un'identità dell'istanza gestita prima di poter [configurare un amministratore di Azure AD per l'istanza gestita](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

Quando si configura un amministratore di Azure AD per il server logico, non è necessario assegnare il **ruolo con autorizzazioni di lettura nella directory** all'identità del server per il database SQL o Azure Synapse. Tuttavia, per abilitare la creazione di un oggetto Azure AD nel database SQL o in Azure Synapse per conto di un'applicazione Azure AD, è necessario il **ruolo con autorizzazioni di lettura nella directory** . Se il ruolo non è assegnato all'identità del server logico SQL, la creazione degli utenti di Azure AD in Azure SQL avrà esito negativo. Per altre informazioni, vedere [Entità servizio di Azure Active Directory con Azure SQL](authentication-aad-service-principal.md).

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>Concessione del ruolo lettori directory a un gruppo di Azure AD

Attualmente in **anteprima pubblica** , è ora possibile avere un amministratore [globale](../../active-directory/roles/permissions-reference.md#global-administrator--company-administrator) o un [amministratore del ruolo con privilegi](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) creare un gruppo di Azure ad e assegnare l'autorizzazione per la lettura della [**directory**](../../active-directory/roles/permissions-reference.md#directory-readers) al gruppo. Questo consentirà l'accesso alla Azure AD API Graph per i membri di questo gruppo. Inoltre, Azure AD utenti proprietari di questo gruppo possono assegnare nuovi membri per questo gruppo, incluse le identità dei server logici SQL di Azure.

Questa soluzione richiede ancora un utente con privilegi elevati (amministratore globale o amministratore del ruolo con privilegi) per creare un gruppo e assegnare gli utenti come attività una volta, ma i proprietari del gruppo di Azure AD potranno assegnare altri membri in futuro. In questo modo si elimina la necessità di coinvolgere un utente con privilegi elevati in futuro per configurare tutti i database SQL, le istanze gestite di SQL o i server di Azure sinapsi nel tenant Azure AD.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Assegnare il ruolo con autorizzazioni di lettura nella directory a un gruppo di Azure AD e gestire le assegnazioni di ruolo](authentication-aad-directory-readers-role-tutorial.md)