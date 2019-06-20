---
title: Informazioni sulle definizioni del ruolo nel controllo degli accessi in base al ruolo per le risorse di Azure | Microsoft Docs
description: Informazioni sulle definizioni del ruolo nel controllo degli accessi in base al ruolo per la gestione granulare degli accessi per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 9f5f9b3595074c26c80c824052727e962b01162a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275053"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Informazioni sulle definizioni del ruolo per le risorse di Azure

Per capire come funziona un ruolo o creare un [ruolo personalizzato per le risorse di Azure](custom-roles.md), è utile comprendere il modo in cui vengono definiti i ruoli. Questo articolo illustra in dettaglio le definizioni di ruolo e presenta alcuni esempi.

## <a name="role-definition-structure"></a>Struttura delle definizioni di ruolo

Una *definizione di ruolo* è una raccolta di autorizzazioni, talvolta semplicemente chiamata *ruolo*. Una definizione di ruolo elenca le operazioni che è possibile eseguire, ad esempio lettura, scrittura ed eliminazione, È anche possibile elencare le operazioni che non possono essere eseguite o le operazioni correlate ai dati sottostanti. Una definizione di ruolo presenta la struttura seguente:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Le operazioni vengono specificate con stringhe che hanno il formato seguente:

- `{Company}.{ProviderName}/{resourceType}/{action}`

La parte `{action}` di una stringa relativa a un'operazione specifica il tipo di operazioni che è possibile eseguire su un tipo di risorsa. In `{action}` possono ad esempio essere elencate le sottostringhe seguenti:

| Sottostringa azione    | Descrizione         |
| ------------------- | ------------------- |
| `*` | Il carattere jolly concede l'accesso a tutte le operazioni che corrispondono alla stringa. |
| `read` | Abilita le operazioni di lettura (GET). |
| `write` | Consente di scrivere operazioni (PUT o PATCH). |
| `action` | Abilita operazioni personalizzate, ad esempio il riavvio delle macchine virtuali (POST). |
| `delete` | Abilita le operazioni di eliminazione (DELETE). |

Di seguito è riportata la definizione del ruolo [Collaboratore](built-in-roles.md#contributor) in formato JSON. L'operazione identificata dal carattere jolly (`*`) in `Actions` indica che l'entità di sicurezza assegnata a questo ruolo può eseguire qualsiasi azione, anche quelle definite in futuro in seguito all'aggiunta di nuovi tipi di risorse da parte di Azure. Le operazioni sotto `NotActions` vengono sottratte a `Actions`. Nel caso del ruolo [Collaboratore](built-in-roles.md#contributor), `NotActions` rimuove la capacità di gestire e assegnare l'accesso alle risorse.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Operazioni di gestione e dei dati

Il controllo degli accessi in base al ruolo per le operazioni di gestione è specificato nelle proprietà `Actions` e `NotActions` di una definizione di ruolo. Di seguito sono riportati alcuni esempi di operazioni di gestione in Azure:

- Gestire l'accesso a un account di archiviazione
- Creare, aggiornare o eliminare un contenitore BLOB
- Eliminare un gruppo di risorse e tutte le risorse incluse nel gruppo

L'accesso in modalità di gestione non viene ereditato nei dati. Questa separazione impedisce ai ruoli con caratteri jolly (`*`) di avere accesso illimitato ai dati. Se ad esempio un utente ha un ruolo [Lettore](built-in-roles.md#reader) in una sottoscrizione, può visualizzare l'account di archiviazione, ma per impostazione predefinita non può visualizzare i dati sottostanti.

In precedenza, il controllo degli accessi in base al ruolo non veniva usato per le operazioni sui dati. L'autorizzazione per le operazioni sui dati variava a seconda del provider di risorse. Lo stesso modello autorizzazione di accesso basato sui ruoli controllo utilizzato per operazioni di gestione è stato esteso alle operazioni sui dati.

Per supportare le operazioni sui dati sono state aggiunte nuove proprietà di dati alla struttura di definizione dei ruoli. Le operazioni sui dati vengono specificate nelle proprietà `DataActions` e `NotDataActions`. Con l'aggiunta di queste proprietà di dati viene mantenuta la separazione tra gestione e dati. In tal modo, si impedisce alle assegnazioni di ruolo correnti con caratteri jolly (`*`) di accedere immediatamente ai dati. Ecco alcune operazioni sui dati che possono essere specificate in `DataActions` e `NotDataActions`:

- Leggere un elenco di BLOB in un contenitore
- Scrivere su un BLOB di archiviazione in un contenitore
- Leggere un messaggio in una coda

Di seguito è riportato il [lettore dati di archiviazione Blob](built-in-roles.md#storage-blob-data-reader) definizione di ruolo che include le operazioni in entrambe le `Actions` e `DataActions` proprietà. Questo ruolo consente di leggere il contenitore BLOB e anche i dati di BLOB sottostanti.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Alle proprietà `DataActions` e `NotDataActions` possono essere aggiunte solo operazioni sui dati. I provider di risorse identificano le operazioni sui dati impostando la proprietà `isDataAction` su `true`. Per un elenco delle operazioni in cui `isDataAction` è `true`, vedere [Operazioni dei provider di risorse](resource-provider-operations.md). In una definizione di ruolo che non prevede operazioni sui dati non è necessario che siano presenti le proprietà `DataActions` e `NotDataActions`.

Le autorizzazioni per tutte le chiamate API delle operazioni di gestione vengono gestite da Azure Resource Manager. Le autorizzazioni per le chiamate API delle operazioni sui dati vengono gestite da un provider di risorse o da Azure Resource Manager.

### <a name="data-operations-example"></a>Esempio di operazioni sui dati

Per comprendere meglio il funzionamento delle operazioni di gestione e di quelle sui dati, è possibile considerare un esempio specifico. Ad Alice è stato assegnato il ruolo [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. Bob è stato assegnato la [collaboratore ai dati Blob di archiviazione](built-in-roles.md#storage-blob-data-contributor) ruolo a livello di ambito di account di archiviazione. L'esempio è illustrato dal diagramma seguente.

![Il controllo degli accessi in base al ruolo è stato esteso per supportare sia le operazioni di gestione sia quelle sui dati](./media/role-definitions/rbac-management-data.png)

Il [Owner](built-in-roles.md#owner) ruolo per Alice e il [collaboratore ai dati di archiviazione Blob](built-in-roles.md#storage-blob-data-contributor) ruolo per Bob avere le azioni seguenti:

Proprietario

&nbsp;&nbsp;&nbsp;&nbsp;actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Collaboratore ai dati dei BLOB di archiviazione

&nbsp;&nbsp;&nbsp;&nbsp;actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;dataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Poiché Alice dispone di un carattere jolly (`*`) azione di un ambito della sottoscrizione, le relative autorizzazioni ereditano verso il basso per consentire loro di eseguire tutte le azioni di gestione. Alice può leggere, scrivere ed eliminare i contenitori. Tuttavia non può eseguire operazioni sui dati senza eseguire passaggi aggiuntivi. Ad esempio, per impostazione predefinita, non può leggere i BLOB all'interno di un contenitore. Per leggere i BLOB, Alice deve prima recuperare le chiavi di accesso alle risorse di archiviazione e usarle per accedere ai BLOB.

Le autorizzazioni di Bob sono limitate al solo il `Actions` e `DataActions` specificato nella [collaboratore ai dati Blob di archiviazione](built-in-roles.md#storage-blob-data-contributor) ruolo. In base al ruolo, Bob può eseguire sia operazioni di gestione sia operazioni sui dati. Ad esempio, Bob può leggere, scrivere ed eliminare i contenitori nell'account di archiviazione specificato e può anche leggere, scrivere ed eliminare i BLOB.

Per altre informazioni sulla gestione e sulla sicurezza del piano dati per l'archiviazione, vedere [Guida alla sicurezza di Archiviazione di Azure](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Strumenti che supportano l'uso di controllo degli accessi in base al ruolo per le operazioni sui dati

Per visualizzare ed eseguire le operazioni sui dati, è necessario disporre delle versioni corrette degli strumenti o degli SDK:

| Strumento  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 o versione successiva |
| [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) | 2.0.30 o successiva |
| [Azure per .NET](/dotnet/azure/) | 2.8.0-Preview o successiva |
| [Azure SDK per Go](/go/azure/azure-sdk-go-install) | 15.0.0 o successiva |
| [Azure per Java](/java/azure/) | 1.9.0 o successiva |
| [Azure per Python](/python/azure) | 0.40.0 o successiva |
| [Azure SDK per Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 o successiva |

Per visualizzare e usare le operazioni di dati di API REST, è necessario impostare il parametro **api-version** per la seguente versione o versioni successive:

- 2018-07-01

## <a name="actions"></a>Azioni

L'autorizzazione `Actions` specifica le operazioni di gestione che il ruolo consente di eseguire. Si tratta di una raccolta di stringhe di operazione che identificano operazioni a protezione diretta dei provider di risorse di Azure. Di seguito sono riportati alcuni esempi di operazioni di gestione che possono essere usate in `Actions`.

| Stringa operazione    | Descrizione         |
| ------------------- | ------------------- |
| `*/read` | Concede l'accesso a operazioni di lettura per tutti i tipi di risorse di tutti i provider di risorse di Azure.|
| `Microsoft.Compute/*` | Concede l'accesso a tutte le operazioni per tutti i tipi di risorse nel provider di risorse Microsoft.Compute.|
| `Microsoft.Network/*/read` | Concede l'accesso alle operazioni di lettura per tutti i tipi di risorse nel provider di risorse Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | Concede l'accesso a tutte le operazioni delle macchine virtuali e ai relativi tipi di risorse figlio.|
| `microsoft.web/sites/restart/Action` | Concede l'accesso per il riavvio di un'app Web.|

## <a name="notactions"></a>NotActions

L'autorizzazione `NotActions` specifica le operazioni di gestione che sono escluse dall'autorizzazione `Actions`. Usare l'autorizzazione `NotActions` se il set di operazioni che si vuole consentire viene definito più facilmente escludendo le operazioni non consentite. L'accesso concesso da un ruolo (ovvero le autorizzazioni effettive) viene calcolato sottraendo le operazioni `NotActions` alle operazioni `Actions`.

> [!NOTE]
> Se a un utente si assegna un ruolo che esclude un'operazione in `NotActions` e quindi si assegna un secondo ruolo che concede l'accesso alla stessa operazione, l'utente può eseguire tale operazione. `NotActions` non è una regola di negazione. È semplicemente un modo comodo per creare un set di operazioni consentite quando è necessario escludere operazioni specifiche.
>

## <a name="dataactions"></a>DataActions

L'autorizzazione `DataActions` specifica le operazioni sui dati che il ruolo consente di eseguire sui dati all'interno dell'oggetto. Ad esempio, se un utente dispone dell'accesso in lettura ai dati di BLOB per un account di archiviazione, può leggere i BLOB all'interno di tale account. Di seguito sono riportati alcuni esempi di operazioni sui dati che possono essere usate in `DataActions`.

| Stringa operazione    | Descrizione         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Restituisce un BLOB o un elenco di BLOB. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Restituisce il risultato della scrittura su un BLOB. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Restituisce un messaggio. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Restituisce un messaggio o il risultato della scrittura o dell'eliminazione di un messaggio. |

## <a name="notdataactions"></a>NotDataActions

L'autorizzazione `NotDataActions` specifica le operazioni sui dati che sono escluse dall'autorizzazione `DataActions`. L'accesso concesso da un ruolo (ovvero le autorizzazioni effettive) viene calcolato sottraendo le operazioni `NotDataActions` alle operazioni `DataActions`. Ogni provider di risorse fornisce il rispettivo set di API per consentire l'esecuzione delle operazioni sui dati.

> [!NOTE]
> Se a un utente si assegna un ruolo che esclude un'operazione sui dati in `NotDataActions` e quindi si assegna un secondo ruolo che concede l'accesso alla stessa operazione, l'utente può eseguire tale operazione. `NotDataActions` non è una regola di negazione. È semplicemente un modo comodo per creare un set di operazioni sui dati consentite quando è necessario escludere operazioni specifiche.
>

## <a name="assignablescopes"></a>AssignableScopes

Il `AssignableScopes` proprietà consente di specificare gli ambiti (sottoscrizioni, gruppi di risorse o risorse) che hanno questa definizione di ruolo disponibile. È possibile rendere disponibili per l'assegnazione del ruolo in solo le sottoscrizioni o i gruppi di risorse che lo richiedono e non complicare l'esperienza utente per il resto sottoscrizioni o gruppi di risorse. È necessario usare almeno una sottoscrizione, un gruppo di risorse o un ID della risorsa.

Per i ruoli predefiniti, `AssignableScopes` è impostato sull'ambito radice (`"/"`), per indicare che il ruolo è disponibile per l'assegnazione in tutti gli ambiti. Ecco alcuni esempi di ambiti assegnabili validi:

| Scenario | Esempio |
|----------|---------|
| Ruolo disponibile per l'assegnazione in una singola sottoscrizione | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Ruolo disponibile per l'assegnazione in due sottoscrizioni | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Ruolo disponibile per l'assegnazione solo nel gruppo di risorse Network | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Ruolo è disponibile per l'assegnazione in tutti gli ambiti (si applica a ruoli predefiniti solo a) | `"/"` |

Per informazioni su `AssignableScopes` per i ruoli personalizzati, vedere [Ruoli personalizzati per le risorse di Azure](custom-roles.md).

## <a name="next-steps"></a>Passaggi successivi

* [Ruoli predefiniti per le risorse di Azure](built-in-roles.md)
* [Ruoli personalizzati per le risorse di Azure](custom-roles.md)
* [Operazioni di provider di risorse con Azure Resource Manager](resource-provider-operations.md)
