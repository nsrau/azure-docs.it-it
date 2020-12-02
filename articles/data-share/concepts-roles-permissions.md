---
title: Ruoli e requisiti per Condivisione dati di Azure
description: Informazioni sulle autorizzazioni necessarie per condividere e ricevere dati tramite la condivisione di dati di Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ed4b4d9c1de1e9024e8ea86d4661b42d6c68b0ae
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460986"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Ruoli e requisiti per Condivisione dati di Azure 

Questo articolo descrive i ruoli e le autorizzazioni necessarie per condividere e ricevere dati tramite il servizio di condivisione dati di Azure. 

## <a name="roles-and-requirements"></a>Ruoli e requisiti

Con il servizio Condivisione dati di Azure è possibile condividere i dati senza scambiare le credenziali tra il provider di dati e il consumer. Il servizio di condivisione dati di Azure Usa identità gestite (precedentemente note come MSI) per l'autenticazione nell'archivio dati di Azure. 

È necessario concedere l'identità gestita della risorsa di condivisione dati di Azure all'archivio dati di Azure. Il servizio di condivisione dati di Azure usa quindi questa identità gestita per leggere e scrivere i dati per la condivisione basata su snapshot e per stabilire un collegamento simbolico per la condivisione sul posto. 

Per condividere o ricevere dati da un archivio dati di Azure, l'utente deve disporre almeno delle autorizzazioni seguenti. Per la condivisione basata su SQL sono necessarie autorizzazioni aggiuntive.

* Autorizzazione per la scrittura nell'archivio dati di Azure. In genere, questa autorizzazione esiste nel ruolo **collaboratore** .
* Autorizzazione per creare un'assegnazione di ruolo nell'archivio dati di Azure. In genere, l'autorizzazione per creare assegnazioni di ruolo è disponibile nel ruolo **proprietario** , ruolo amministratore accesso utenti o un ruolo personalizzato con Microsoft. autorizzazione/assegnazione ruolo/autorizzazione di scrittura assegnata. Questa autorizzazione non è necessaria se all'identità gestita della risorsa di condivisione dati è già stato concesso l'accesso all'archivio dati di Azure. Vedere la tabella seguente per il ruolo obbligatorio.

Di seguito è riportato un riepilogo dei ruoli assegnati all'identità gestita della risorsa di condivisione dati:

|**Tipo di archivio dati**|**Archivio dati di origine provider di dati**|**Archivio dati di destinazione del consumer di dati**|
|---|---|---|
|Archiviazione BLOB di Azure| Lettore dei dati del BLOB di archiviazione | Collaboratore ai dati del BLOB di archiviazione
|Azure Data Lake Gen1 | Proprietario | Non supportato
|Azure Data Lake Gen2 | Lettore dei dati del BLOB di archiviazione | Collaboratore ai dati del BLOB di archiviazione
|Cluster di Esplora dati di Azure | Autore di contributi | Autore di contributi
|

Per la condivisione basata su SQL, è necessario creare un utente SQL da un provider esterno nel database SQL di Azure con lo stesso nome della risorsa di condivisione dati di Azure. Per creare questo utente, è necessaria l'autorizzazione Azure Active Directory amministratore. Di seguito è riportato un riepilogo dell'autorizzazione richiesta dall'utente SQL.

|**Tipo di database SQL**|**provider di dati autorizzazione utente SQL**|**Autorizzazione utente SQL per consumer di dati**|
|---|---|---|
|Database SQL di Azure | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter, db_ddladmin
|

### <a name="data-provider"></a>Provider di dati

Per aggiungere un set di dati nella condivisione dati di Azure, è necessario concedere all'identità gestita della risorsa di condivisione dati del provider l'accesso all'archivio dati di Azure di origine. Ad esempio, nel caso dell'account di archiviazione, all'identità gestita della risorsa di condivisione dati viene concesso il ruolo lettore dati BLOB di archiviazione. 

Questa operazione viene eseguita automaticamente dal servizio Condivisione dati di Azure quando l'utente aggiunge un set di dati tramite portale di Azure e l'utente dispone dell'autorizzazione appropriata. Ad esempio, l'utente è un proprietario dell'archivio dati di Azure o è un membro di un ruolo personalizzato a cui è assegnata l'autorizzazione Microsoft. Authorization/Role/Write. 

In alternativa, l'utente può avere il proprietario dell'archivio dati di Azure aggiungere manualmente l'identità gestita della risorsa di condivisione dati all'archivio dati di Azure. Questa azione deve essere eseguita una sola volta per ogni risorsa di condivisione dati.

Per creare manualmente un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati, seguire questa procedura.  

1. Passare all'archivio dati di Azure.
1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi un'assegnazione di ruolo**.
1. In *ruolo* selezionare il ruolo nella tabella di assegnazione ruolo precedente (ad esempio, per account di archiviazione selezionare *lettore dati BLOB di archiviazione*).
1. In *Seleziona* Digitare il nome della risorsa di condivisione dati di Azure.
1. Fare clic su *Salva*.

Per altre informazioni sull'assegnazione di ruolo, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment). Se si condividono dati usando le API REST, è possibile creare un'assegnazione di ruolo usando l'API facendo riferimento a [Aggiungi o Rimuovi assegnazioni di ruolo di Azure usando l'API REST](../role-based-access-control/role-assignments-rest.md). 

Per le origini basate su SQL, è necessario creare un utente SQL da un provider esterno nel database SQL con lo stesso nome della risorsa di condivisione dati di Azure durante la connessione al database SQL tramite l'autenticazione Azure Active Directory. A questo utente deve essere concessa l'autorizzazione *db_datareader* . Uno script di esempio insieme ad altri prerequisiti per la condivisione basata su SQL è disponibile nell'esercitazione [condividere da database SQL di Azure o da Azure sinapsi Analytics](how-to-share-from-sql.md) . 

### <a name="data-consumer"></a>Consumer di dati
Per ricevere dati, l'identità gestita della risorsa di condivisione dati del consumer deve essere concessa all'archivio dati di Azure di destinazione. Ad esempio, nel caso dell'account di archiviazione, all'identità gestita della risorsa di condivisione dati viene concesso il ruolo di collaboratore dati BLOB di archiviazione. 

Questa operazione viene eseguita automaticamente dal servizio Condivisione dati di Azure se l'utente specifica un archivio dati di destinazione tramite portale di Azure e l'utente dispone delle autorizzazioni appropriate. Ad esempio, l'utente è un proprietario dell'archivio dati di Azure o è un membro di un ruolo personalizzato a cui è assegnata l'autorizzazione Microsoft. Authorization/Role/Write. 

In alternativa, l'utente può avere il proprietario dell'archivio dati di Azure aggiungere manualmente l'identità gestita della risorsa di condivisione dati all'archivio dati di Azure. Questa azione deve essere eseguita una sola volta per ogni risorsa di condivisione dati.

Per creare manualmente un'assegnazione di ruolo per l'identità gestita della risorsa di condivisione dati, seguire questa procedura. 

1. Passare all'archivio dati di Azure.
1. Selezionare **Controllo di accesso (IAM)** .
1. Selezionare **Aggiungi un'assegnazione di ruolo**.
1. In *ruolo* selezionare il ruolo nella tabella di assegnazione ruolo precedente (ad esempio, per account di archiviazione selezionare *lettore dati BLOB di archiviazione*).
1. In *Seleziona* Digitare il nome della risorsa di condivisione dati di Azure.
1. Fare clic su *Salva*.

Per altre informazioni sull'assegnazione di ruolo, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment). Se si ricevono dati usando le API REST, è possibile creare un'assegnazione di ruolo usando l'API facendo riferimento a [Aggiungi o Rimuovi assegnazioni di ruolo di Azure usando l'API REST](../role-based-access-control/role-assignments-rest.md). 

Per la destinazione basata su SQL, è necessario creare un utente SQL da un provider esterno nel database SQL con lo stesso nome della risorsa di condivisione dati di Azure durante la connessione al database SQL tramite l'autenticazione Azure Active Directory. A questo utente deve essere concessa l'autorizzazione *db_datareader, db_datawriter db_ddladmin* . Uno script di esempio insieme ad altri prerequisiti per la condivisione basata su SQL è disponibile nell'esercitazione [condividere da database SQL di Azure o da Azure sinapsi Analytics](how-to-share-from-sql.md) . 

## <a name="resource-provider-registration"></a>Registrazione del provider di risorse 

Potrebbe essere necessario registrare manualmente il provider di risorse Microsoft. DataShare nella sottoscrizione di Azure negli scenari seguenti: 

* Visualizzare l'invito di condivisione dati di Azure per la prima volta nel tenant di Azure
* Condividere i dati da un archivio dati di Azure in una sottoscrizione di Azure diversa dalla risorsa di condivisione dati di Azure
* Ricevere dati in un archivio dati di Azure in una sottoscrizione di Azure diversa dalla risorsa di condivisione dati di Azure

Seguire questa procedura per registrare il provider di risorse Microsoft. DataShare nella sottoscrizione di Azure. Per registrare il provider di risorse, è necessario l'accesso come *collaboratore* alla sottoscrizione di Azure.

1. Nella portale di Azure passare a **sottoscrizioni**.
1. Selezionare la sottoscrizione che si sta usando per la condivisione di dati di Azure.
1. Fare clic su **provider di risorse**.
1. Cercare Microsoft. DataShare.
1. Fare clic su **Register**.
 
Per altre informazioni sul provider di risorse, vedere [provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui ruoli in Azure - [Comprendere le definizioni dei ruoli](../role-based-access-control/role-definitions.md)