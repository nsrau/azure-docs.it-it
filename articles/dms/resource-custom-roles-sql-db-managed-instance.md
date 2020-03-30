---
title: 'Ruoli personalizzati: migrazioni di istanze gestite da SQL Server online a SQL'
titleSuffix: Azure Database Migration Service
description: Informazioni su come usare i ruoli personalizzati per le migrazioni online dell'istanza gestita dal database SQL di SQL Server sql ad Azure.Learn to use the custom roles for SQL Server to Azure SQL Database managed instance online migrations.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: e9a1024ca3ab68841474ab051c029042df4915b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254933"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Ruoli personalizzati per le migrazioni online dell'istanza gestita da SQL Server a SQL Database

Azure Database Migration Service uses an APP ID to interact with Azure Services. L'ID APP richiede il ruolo Collaboratore a livello di sottoscrizione (che molti reparti di sicurezza aziendale non consentiranno) o la creazione di ruoli personalizzati che concedono le autorizzazioni specifiche richieste dal servizio Migrazioni del database di Azure.The APP ID requires either the Contributor role at the Subscription level (which many Corporate security department won't allow) or creation of custom roles that grant the specific permissions that Azure database Migrations Service requires. Poiché esiste un limite di 2.000 ruoli personalizzati in Azure Active Directory, è possibile combinare tutte le autorizzazioni necessarie in modo specifico dall'ID APP in uno o due ruoli personalizzati e quindi concedere all'ID APP il ruolo personalizzato in oggetti o gruppi di risorse specifici (rispetto al livello di abbonamento). Se il numero di ruoli personalizzati non è un problema, è possibile dividere i ruoli personalizzati per tipo di risorsa, per creare tre ruoli personalizzati in totale, come descritto di seguito.

La sezione AssignableScopes della stringa json della definizione di ruolo consente di controllare la posizione delle autorizzazioni visualizzate nell'interfaccia utente **Aggiungi assegnazione ruolo** nel portale. È probabile che si vorrà definire il ruolo a livello di gruppo di risorse o anche a livello di risorsa per evitare di ingombrare l'interfaccia utente con ruoli aggiuntivi. Si noti che in questo modo non viene eseguita l'assegnazione di ruolo effettiva.

## <a name="minimum-number-of-roles"></a>Numero minimo di ruoli

È attualmente consigliabile creare un minimo di due ruoli personalizzati per l'ID APP, uno a livello di risorsa e l'altro a livello di sottoscrizione.

> [!NOTE]
> L'ultimo requisito del ruolo personalizzato può essere rimosso, poiché il nuovo codice dell'istanza gestita del database SQL viene distribuito in Azure.The last custom role requirement may eventually be removed, as new SQL Database managed instance code is deployed to Azure.

**Ruolo personalizzato per l'ID APP.** Questo ruolo è necessario per la migrazione del servizio di migrazione del database di Azure a livello di *risorsa* o *di gruppo di risorse* (per altre informazioni sull'ID APP, vedere l'articolo Usare il portale per creare [un'applicazione Azure AD e un'entità servizio che può accedere alle risorse).](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Ruolo personalizzato per l'ID APP - sottoscrizione**. Questo ruolo è necessario per la migrazione del servizio di migrazione del database di Azure a livello di *sottoscrizione.*

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

Il codice json precedente deve essere archiviato in tre file di testo ed è possibile usare i cmdlet di AzureRM, di PowerShell o dell'interfaccia della riga di comando di Azure per creare i ruoli usando **New-AzureRmRoleDefinition (AzureRM)** o **New-AzRoleDefinition (A).**

Per altre informazioni, vedere l'articolo Ruoli personalizzati per le risorse di [Azure.For](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)more information, see the article Custom roles for Azure resources.

Dopo aver creato questi ruoli personalizzati, è necessario aggiungere assegnazioni di ruolo agli utenti e agli ID APP alle risorse o ai gruppi di risorse appropriati:After you create these custom roles, you must add role assignments to users and APP ID(s) to the appropriate resources or resource groups:

* Il ruolo "Ruolo DMS - ID app" deve essere concesso all'ID APP che verrà usato per le migrazioni, nonché all'account di archiviazione, all'istanza del servizio di migrazione del database di Azure e ai livelli di risorse dell'istanza gestita del database SQL.
* Il ruolo "Ruolo DMS - ID app - Sub" deve essere concesso all'ID APP a livello di sottoscrizione (la concessione a livello di risorsa o di gruppo di risorse avrà esito negativo). Questo requisito è temporaneo fino a quando non viene distribuito un aggiornamento del codice.

## <a name="expanded-number-of-roles"></a>Numero di ruoli espanso

Se il numero di ruoli personalizzati in Azure Active Directory non è un problema, è consigliabile creare un totale di tre ruoli. Sarà comunque necessario il ruolo "Ruolo DMS - ID app - Sub", ma il ruolo "Ruolo DMS - ID app" precedente è suddiviso per tipo di risorsa in due ruoli diversi.

**Ruolo personalizzato per l'ID APP per l'istanza gestita del database SQLCustom role for the APP ID for SQL Database managed instance**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Ruolo personalizzato per l'ID APP per l'archiviazioneCustom role for the APP ID for Storage**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Assegnazione di ruolo

Per assegnare un ruolo agli utenti/ID APP, aprire il portale di Azure, eseguire la procedura seguente:To assign a role to users/APP ID, open the Azure portal, perform the following steps:

1. Passare al gruppo di risorse o alla risorsa (ad eccezione del ruolo che deve essere concesso nella sottoscrizione), passare a **Controllo**di accesso e quindi scorrere fino a individuare i ruoli personalizzati appena creati.

2. Selezionare il ruolo appropriato, selezionare l'ID APP e quindi salvare le modifiche.

  Gli ID APP vengono ora visualizzati nella scheda **Assegnazioni ruolo.**

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le indicazioni per la migrazione per lo scenario in Microsoft [Database Migration Guide](https://datamigration.microsoft.com/).
