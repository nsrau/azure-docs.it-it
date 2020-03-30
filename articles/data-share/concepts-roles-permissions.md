---
title: Ruoli e requisiti per Condivisione dati di Azure
description: Informazioni sulle autorizzazioni necessarie per condividere e ricevere dati tramite La condivisione dati di Azure.Learn about the permissions required to share and receive data using Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265506"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Ruoli e requisiti per Condivisione dati di Azure 

Questo articolo descrive i ruoli e le autorizzazioni necessari per condividere e ricevere dati usando il servizio Condivisione dati di Azure.This article describes roles and permissions required to share and receive data using Azure Data Share service. 

## <a name="roles-and-requirements"></a>Ruoli e requisiti

Con il servizio Condivisione dati di Azure è possibile condividere i dati senza scambiare le credenziali tra il provider di dati e il consumer. Il servizio Condivisione dati di Azure usa le identità gestite (precedentemente note come MSI) per l'autenticazione nell'archivio dati di Azure.Azure Data Share service uses Managed Identities (previously known as MSIs) to authenticate to Azure data store. 

L'identità gestita della risorsa di condivisione dati di Azure deve essere concesso l'accesso all'archivio dati di Azure.Azure Data Share resource's managed identity needs to be granted access to Azure data store. Il servizio di condivisione dati di Azure usa quindi questa identità gestita per leggere e scrivere dati per la condivisione basata su snapshot e per stabilire un collegamento simbolico per la condivisione sul posto. 

Per condividere o ricevere dati da un archivio dati di Azure, l'utente deve disporre almeno delle autorizzazioni seguenti. Sono necessarie autorizzazioni aggiuntive per la condivisione basata su SQL.
* Autorizzazione alla scrittura nell'archivio dati di Azure.Permission to write to the Azure data store. In genere, questa autorizzazione esiste nel ruolo **Collaboratore.**
* Autorizzazione per creare l'assegnazione di ruolo nell'archivio dati di Azure.Permission to create role assignment in the Azure data store. In genere, l'autorizzazione per creare assegnazioni di ruolo esiste nel ruolo **Proprietario,** Amministratore accesso utenti o un ruolo personalizzato a cui è assegnata l'autorizzazione Microsoft.Authorization/Role Assignments/write. Questa autorizzazione non è necessaria se all'identità gestita della risorsa condivisione dati è già concesso l'accesso all'archivio dati di Azure.This permission is not required if the data share resource's managed identity is already granted access to the Azure data store. Vedere la tabella seguente per il ruolo richiesto.

Di seguito è riportato un riepilogo dei ruoli assegnati all'identità gestita della risorsa Condivisione dati:

| |  |  |
|---|---|---|
|**Tipo di archivio dati**|**Data Provider Source Data Store**|**Data Consumer Target Data Store**|
|Archiviazione BLOB di Azure| Lettore dei dati del BLOB di archiviazione | Collaboratore ai dati del BLOB di archiviazione
|Azure Data Lake Gen1 | Proprietario | Non supportato
|Azure Data Lake Gen2 | Lettore dei dati del BLOB di archiviazione | Collaboratore ai dati del BLOB di archiviazione
|Azure SQL Server | Collaboratore database SQL | Collaboratore database SQL
|Azure Data Explorer Cluster | Collaboratore | Collaboratore
|

Per la condivisione basata su SQL, un utente SQL deve essere creato da un provider esterno nel database SQL con lo stesso nome della risorsa di condivisione dati di Azure.For SQL-based sharing, a SQL user needs to be created from an external provider in the SQL database with the same name as the Azure Data Share resource. Di seguito è riportato un riepilogo delle autorizzazioni richieste dall'utente SQL.

| |  |  |
|---|---|---|
|**Tipo di database SQL**|**Autorizzazione utente SQL del provider di datiData Provider SQL User Permission**|**Autorizzazione utente SQL consumer datiData Consumer SQL User Permission**|
|database SQL di Azure | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (in precedenza SQL Data Warehouse) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Provider di dati 
Per aggiungere un set di dati nella condivisione dati di Azure, l'identità gestita della risorsa di condivisione dati del provider deve disporre dell'accesso all'archivio dati di Azure di origine. Ad esempio, nel caso dell'account di archiviazione, all'identità gestita della risorsa di condivisione dati viene concesso il ruolo Lettore dati BLOB di archiviazione. 

Questa operazione viene eseguita automaticamente dal servizio Condivisione dati di Azure quando l'utente aggiunge set di dati tramite il portale di Azure e l'utente dispone dell'autorizzazione appropriata. Ad esempio, user è un proprietario dell'archivio dati di Azure o è membro di un ruolo personalizzato a cui è assegnata l'autorizzazione Microsoft.Authorization/Role Assignments/write. 

In alternativa, l'utente può avere il proprietario dell'archivio dati di Azure aggiungere manualmente l'identità gestita della risorsa condivisione dati all'archivio dati di Azure.Alternatively, user can have owner of the Azure data store add the data share resource's managed identity to the Azure data store manually. Questa azione deve essere eseguita una sola volta per ogni risorsa condivisione dati.

Per creare un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati, attenersi alla seguente procedura:

1. Passare all'archivio dati di Azure.Navigate to the Azure data store.
1. Selezionare **Controllo di accesso (IAM)**.
1. Selezionare **Aggiungi assegnazione ruolo**.
1. In *Ruolo*selezionare il ruolo nella tabella delle assegnazioni di ruolo precedente, ad esempio per l'account di archiviazione, selezionare *Lettore dati BLOB di archiviazione.*
1. In Seleziona digitare il nome della risorsa Condivisione dati di Azure.Under *Select*, type in the name of your Azure Data Share resource.
1. Fare clic su *Salva*.

Per le origini basate su SQL, oltre ai passaggi precedenti, un utente SQL deve essere creato da un provider esterno nel database SQL con lo stesso nome della risorsa di condivisione dati di Azure.For SQL-based sources, in addition to the above steps, a SQL user needs to be created from an external provider in the SQL database with the same name as the Azure Data Share resource. A questo utente deve essere concessa *db_datareader* l'autorizzazione. Uno script di esempio insieme ad altri prerequisiti per la condivisione basata su SQL è disponibile nell'esercitazione [sulla condivisione](share-your-data.md) dei dati. 

### <a name="data-consumer"></a>Consumatore di dati
Per ricevere i dati, l'identità gestita della risorsa condivisione dati consumer deve disporre dell'accesso all'archivio dati di Azure di destinazione. Ad esempio, nel caso dell'account di archiviazione, all'identità gestita della risorsa di condivisione dati viene concesso il ruolo Collaboratore dati BLOB di archiviazione. 

Questa operazione viene eseguita automaticamente dal servizio Di condivisione dati di Azure se l'utente specifica un archivio dati di destinazione tramite il portale di Azure e l'utente dispone dell'autorizzazione appropriata. Ad esempio, user è un proprietario dell'archivio dati di Azure o è membro di un ruolo personalizzato a cui è assegnata l'autorizzazione Microsoft.Authorization/Role Assignments/write. 

In alternativa, l'utente può avere il proprietario dell'archivio dati di Azure aggiungere manualmente l'identità gestita della risorsa condivisione dati all'archivio dati di Azure.Alternatively, user can have owner of the Azure data store add the data share resource's managed identity to the Azure data store manually. Questa azione deve essere eseguita una sola volta per ogni risorsa condivisione dati.

Per creare manualmente un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati, attenersi alla seguente procedura:

1. Passare all'archivio dati di Azure.Navigate to the Azure data store.
1. Selezionare **Controllo di accesso (IAM)**.
1. Selezionare **Aggiungi assegnazione ruolo**.
1. In *Ruolo*selezionare il ruolo nella tabella delle assegnazioni di ruolo precedente, ad esempio per l'account di archiviazione, selezionare *Lettore dati BLOB di archiviazione.*
1. In Seleziona digitare il nome della risorsa Condivisione dati di Azure.Under *Select*, type in the name of your Azure Data Share resource.
1. Fare clic su *Salva*.

Per la destinazione basata su SQL, oltre ai passaggi precedenti, un utente SQL deve essere creato da un provider esterno nel database SQL con lo stesso nome della risorsa di condivisione dati di Azure.For SQL-based target, in addition to the above steps, a SQL user needs to be created from an external provider in the SQL database with the same name as the Azure Data Share resource. A questo utente deve essere concessa l'autorizzazione *db_datareader, db_datawriter db_ddladmin.* Uno script di esempio insieme ad altri prerequisiti per la condivisione basata su SQL è disponibile nell'esercitazione [sull'accettazione e](subscribe-to-data-share.md) ricezione dei dati. 

Se si condividono dati usando le API REST, è necessario creare manualmente queste assegnazioni di ruolo. 

Per ulteriori informazioni su come aggiungere un'assegnazione di ruolo, fare riferimento a [questa documentazione,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Registrazione del provider di risorse 

Per visualizzare l'invito alla condivisione dati di Azure per la prima volta nel tenant di Azure, potrebbe essere necessario registrare manualmente il provider di risorse Microsoft.DataShare nella sottoscrizione di Azure.To view Azure Data Share invitation for first time in your Azure tenant, you may need to manually register the Microsoft.DataShare resource provider into your Azure subscription. Seguire questi passaggi per registrare il provider di risorse Microsoft.DataShare nella sottoscrizione di Azure.Follow these steps to register the Microsoft.DataShare resource provider into your Azure Subscription. Per registrare il provider di risorse, è necessario l'accesso *come collaboratore* alla sottoscrizione di Azure.You need Contributor access to the Azure subscription to register resource provider.

1. Nel portale di Azure passare a **Sottoscrizioni**.
1. Selezionare la sottoscrizione usata per la condivisione dati di Azure.Select the subscription that you're using for Azure Data Share.
1. Fare clic su **Provider di risorse**.
1. Cercare Microsoft.DataShare.Search for Microsoft.DataShare.
1. Fare clic su **Registra**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui ruoli in Azure - [Comprendere le definizioni dei ruoli](../role-based-access-control/role-definitions.md)

