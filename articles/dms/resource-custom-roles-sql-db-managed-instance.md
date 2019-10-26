---
title: Ruoli personalizzati per SQL Server alle migrazioni online dell'istanza gestita di database SQL | Microsoft Docs
description: Informazioni su come usare i ruoli personalizzati per SQL Server alle migrazioni online dell'istanza gestita di database SQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: 8148e029bf343613a230b20d0397fa7851c96712
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952355"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>Ruoli personalizzati per SQL Server alle migrazioni online dell'istanza gestita di database SQL

Il servizio migrazione del database di Azure usa un ID APP per interagire con i servizi di Azure. L'ID APP richiede il ruolo Collaboratore a livello di sottoscrizione (che molti reparti di sicurezza aziendali non consentiranno) o la creazione di ruoli personalizzati che concedono le autorizzazioni specifiche richieste dal servizio migrazione del database di Azure. Poiché è presente un limite di 2.000 ruoli personalizzati in Azure Active Directory, è possibile combinare tutte le autorizzazioni richieste in modo specifico dall'ID APP in uno o due ruoli personalizzati, quindi concedere all'ID APP il ruolo personalizzato per oggetti specifici o gruppi di risorse (rispetto alle Subs livello crizione). Se il numero di ruoli personalizzati non è un problema, è possibile suddividere i ruoli personalizzati per tipo di risorsa per creare tre ruoli personalizzati in totale, come descritto di seguito.

La sezione AssignableScopes della stringa JSON di definizione del ruolo consente di controllare la posizione in cui vengono visualizzate le autorizzazioni nell'interfaccia utente di aggiunta di un' **assegnazione di ruolo** nel portale. È probabile che si desideri definire il ruolo a livello di gruppo di risorse o di risorse per evitare di ingombrare l'interfaccia utente con ruoli aggiuntivi. Si noti che questa operazione non esegue l'assegnazione di ruolo effettiva.

## <a name="minimum-number-of-roles"></a>Numero minimo di ruoli

È attualmente consigliabile creare almeno due ruoli personalizzati per l'ID APP, uno a livello di risorsa e l'altro a livello di sottoscrizione.

> [!NOTE]
> L'ultimo requisito del ruolo personalizzato potrebbe essere rimosso, perché il nuovo codice dell'istanza gestita di database SQL viene distribuito in Azure.

**Ruolo personalizzato per l'ID app**. Questo ruolo è necessario per la migrazione del servizio migrazione del database di Azure a livello di *risorsa* o di *gruppo di risorse* . per ulteriori informazioni sull'ID app, vedere l'articolo [usare il portale per creare un'applicazione Azure ad e un'entità servizio che può accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)).

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

**Ruolo personalizzato per l'ID app: sottoscrizione**. Questo ruolo è necessario per la migrazione del servizio migrazione del database di Azure a livello di *sottoscrizione* .

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

Il codice JSON precedente deve essere archiviato in tre file di testo ed è possibile usare i cmdlet AzureRM, AZ PowerShell o l'interfaccia della riga di comando di Azure per creare i ruoli usando **New-AzureRmRoleDefinition (AzureRM)** o **New-AzRoleDefinition (AZ)** .

Per altre informazioni, vedere l'articolo [ruoli personalizzati per le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

Dopo aver creato questi ruoli personalizzati, è necessario aggiungere assegnazioni di ruolo a utenti e ID APP alle risorse o ai gruppi di risorse appropriati:

* È necessario concedere il ruolo "ID app-ruolo DMS" all'ID APP che verrà usato per le migrazioni e anche all'account di archiviazione, all'istanza del servizio migrazione del database di Azure e ai livelli di risorse dell'istanza gestita di database SQL.
* Il ruolo "DMS Role-App ID-sub" deve essere concesso all'ID APP a livello di sottoscrizione (la concessione della risorsa o del gruppo di risorse avrà esito negativo). Questo requisito è temporaneo finché non viene distribuito un aggiornamento del codice.

## <a name="expanded-number-of-roles"></a>Numero espanso di ruoli

Se il numero di ruoli personalizzati nell'Azure Active Directory non costituisce un problema, è consigliabile creare un totale di tre ruoli. Sarà comunque necessario il ruolo "DMS Role-App ID-sub", ma il ruolo "DMS Role-App ID" precedente viene suddiviso per tipo di risorsa in due ruoli diversi.

**Ruolo personalizzato per l'ID APP per istanza gestita di database SQL**

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

**Ruolo personalizzato per l'ID APP per l'archiviazione**

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

Per assegnare un ruolo a utenti/ID APP, aprire il portale di Azure, seguire questa procedura:

1. Passare al gruppo di risorse o alla risorsa (ad eccezione del ruolo che deve essere concesso per la sottoscrizione), passare a **controllo di accesso**e quindi scorrere per trovare i ruoli personalizzati appena creati.

2. Selezionare il ruolo appropriato, selezionare l'ID APP e quindi salvare le modifiche.

  Gli ID APP verranno ora visualizzati nella scheda **assegnazioni di ruolo** .

## <a name="next-steps"></a>Passaggi successivi

* Esaminare le linee guida per la migrazione dello scenario nella [Guida alla migrazione del database](https://datamigration.microsoft.com/)Microsoft.
