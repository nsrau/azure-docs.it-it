---
title: Informazioni sulle definizioni di ruolo in Controllo degli accessi in base al ruolo di Azure | Microsoft Docs
description: Informazioni sulle definizioni di ruolo in Controllo degli accessi in base al ruolo e su come definire ruoli personalizzati per la gestione delle autorizzazioni di accesso con granularità fine delle risorse in Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 9bb7808f2b483fe9cd7d22c6df3fe80d4a98f1f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266857"
---
# <a name="understand-role-definitions"></a>Informazioni sulle definizioni del ruolo

Per capire come funziona un ruolo o creare un [ruolo personalizzato](custom-roles.md), è utile comprendere il modo in cui vengono definiti i ruoli. Questo articolo illustra in dettaglio le definizioni di ruolo e presenta alcuni esempi.

## <a name="role-definition-structure"></a>Struttura delle definizioni di ruolo

Una *definizione di ruolo* è una raccolta di autorizzazioni, talvolta semplicemente chiamata *ruolo*. Una definizione di ruolo elenca le operazioni che è possibile eseguire, ad esempio lettura, scrittura ed eliminazione, È anche possibile elencare le operazioni che non possono essere eseguite o le operazioni correlate ai dati sottostanti. Una definizione di ruolo presenta la struttura seguente:

```
assignableScopes []
description
id
name
permissions []
  actions []
  dataActions []
  notActions []
  notDataActions []
roleName
roleType
type
```

Le operazioni vengono specificate con stringhe che hanno il formato seguente:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

La parte `{action}` di una stringa relativa a un'operazione specifica il tipo di operazioni che è possibile eseguire su un tipo di risorsa. In `{action}` possono ad esempio essere elencate le sottostringhe seguenti:

| Sottostringa azione    | Descrizione         |
| ------------------- | ------------------- |
| `*` | Il carattere jolly concede l'accesso a tutte le operazioni che corrispondono alla stringa. |
| `read` | Abilita le operazioni di lettura (GET). |
| `write` | Abilita le operazioni di scrittura (PUT, POST e PATCH). |
| `delete` | Abilita le operazioni di eliminazione (DELETE). |

Di seguito è riportata la definizione del ruolo [Collaboratore](built-in-roles.md#contributor) in formato JSON. L'operazione identificata dal carattere jolly (`*`) in `actions` indica che l'entità di sicurezza assegnata a questo ruolo può eseguire qualsiasi azione, anche quelle definite in futuro in seguito all'aggiunta di nuovi tipi di risorse da parte di Azure. Le operazioni sotto `notActions` vengono sottratte a `actions`. Nel caso del ruolo [Collaboratore](built-in-roles.md#contributor), `notActions` rimuove la capacità di gestire e assegnare l'accesso alle risorse.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-and-data-operations-preview"></a>Gestione e operazioni sui dati (anteprima)

Il controllo degli accessi in base al ruolo per le operazioni di gestione è specificato nelle sezioni `actions` e `notActions` di una definizione di ruolo. Di seguito sono riportati alcuni esempi di operazioni di gestione in Azure:

- Gestire l'accesso a un account di archiviazione
- Creare, aggiornare o eliminare un contenitore BLOB
- Eliminare un gruppo di risorse e tutte le risorse incluse nel gruppo

L'accesso in modalità di gestione non viene ereditato nei dati. Questa separazione impedisce ai ruoli con caratteri jolly (`*`) di avere accesso illimitato ai dati. Se ad esempio un utente ha un ruolo [Lettore](built-in-roles.md#reader) in una sottoscrizione, può visualizzare l'account di archiviazione, ma per impostazione predefinita non può visualizzare i dati sottostanti.

In precedenza, il controllo degli accessi in base al ruolo non veniva usato per le operazioni sui dati. L'autorizzazione per le operazioni sui dati variava a seconda del provider di risorse. Lo stesso modello di autorizzazione del controllo degli accessi in base al ruolo usato per le operazioni di gestione è stato esteso alle operazioni sui dati (attualmente in anteprima).

Per supportare le operazioni sui dati sono state aggiunte nuove sezioni dati alla struttura di definizione dei ruoli. Le operazioni sui dati sono specificate nelle sezioni `dataActions` e `notDataActions`. Con l'aggiunta di queste sezioni, viene mantenuta la separazione tra gestione e dati. In tal modo, si impedisce alle assegnazioni di ruolo correnti con caratteri jolly (`*`) di accedere immediatamente ai dati. Ecco alcune operazioni sui dati che possono essere specificate in `dataActions` e `notDataActions`:

- Leggere un elenco di BLOB in un contenitore
- Scrivere su un BLOB di archiviazione in un contenitore
- Leggere un messaggio in una coda

Ecco la definizione di ruolo [Lettore dei dati dei BLOB di archiviazione (anteprima)](built-in-roles.md#storage-blob-data-reader-preview) che include le operazioni in entrambe le sezioni `actions` e `dataActions`. Questo ruolo consente di leggere il contenitore BLOB e anche i dati di BLOB sottostanti.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Allows for read access to Azure Storage blob containers and data.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/read"
        ],
        "additionalProperties": {},
        "dataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
        ],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Storage Blob Data Reader (Preview)",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Alle sezioni `dataActions` e `notDataActions` possono essere aggiunte solo operazioni sui dati. I provider di risorse identificano le operazioni sui dati impostando la proprietà `isDataAction` su `true`. Per un elenco delle operazioni in cui `isDataAction` è `true`, vedere [Operazioni dei provider di risorse](resource-provider-operations.md). In una definizione di ruolo che non prevede operazioni sui dati non è necessario che siano presenti le sezioni `dataActions` e `notDataActions`.

Le autorizzazioni per tutte le chiamate API delle operazioni di gestione vengono gestite da Azure Resource Manager. Le autorizzazioni per le chiamate API delle operazioni sui dati vengono gestite da un provider di risorse o da Azure Resource Manager.

### <a name="data-operations-example"></a>Esempio di operazioni sui dati

Per comprendere meglio il funzionamento delle operazioni di gestione e di quelle sui dati, è possibile considerare un esempio specifico. Ad Alice è stato assegnato il ruolo [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. A Bob è stato assegnato il ruolo [Collaboratore ai dati del BLOB di archiviazione (anteprima)](built-in-roles.md#storage-blob-data-contributor-preview) nell'ambito dell'account di archiviazione. L'esempio è illustrato dal diagramma seguente.

![Il controllo degli accessi in base al ruolo è stato esteso per supportare sia le operazioni di gestione sia quelle sui dati](./media/role-definitions/rbac-management-data.png)

Il ruolo [Proprietario](built-in-roles.md#owner) per Alice e il ruolo [Collaboratore ai dati del BLOB di archiviazione (anteprima)](built-in-roles.md#storage-blob-data-contributor-preview) per Bob prevedono le azioni seguenti:

Proprietario

&nbsp;&nbsp;&nbsp;&nbsp;actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Collaboratore ai dati del BLOB di archiviazione (anteprima)

&nbsp;&nbsp;&nbsp;&nbsp;actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;dataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Poiché Alice ha un'azione con carattere jolly (`*`) nell'ambito della sottoscrizione, eredita tutte le autorizzazioni della gerarchia e può quindi eseguire tutte le azioni di gestione, anche se non può eseguire operazioni sui dati. Ad esempio, per impostazione predefinita, non può leggere i BLOB all'interno di un contenitore, ma può eseguire operazioni di lettura, scrittura ed eliminazione relative ai contenitori.

Le autorizzazioni di Bob sono limitate alle sezioni `actions` e `dataActions` specificate nel ruolo [Collaboratore ai dati del BLOB di archiviazione (anteprima)](built-in-roles.md#storage-blob-data-contributor-preview). In base al ruolo, Bob può eseguire sia operazioni di gestione sia operazioni sui dati. Ad esempio, può scrivere sui contenitori, leggerli ed eliminarli nell'account di archiviazione specificato e anche scrivere sui BLOB, leggerli ed eliminarli.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Strumenti che supportano l'uso di controllo degli accessi in base al ruolo per le operazioni sui dati

Per visualizzare ed eseguire le operazioni sui dati, è necessario disporre delle versioni corrette degli strumenti o degli SDK:

| Strumento  | Versione  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-azurerm-ps) | 5.6.0 o successiva |
| [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) | 2.0.30 o successiva |
| [Azure per .NET](/dotnet/azure/) | 2.8.0-Preview o successiva |
| [Azure SDK per Go](/go/azure/azure-sdk-go-install) | 15.0.0 o successiva |
| [Azure per Java](/java/azure/) | 1.9.0 o successiva |
| [Azure per Python](/python/azure) | 0.40.0 o successiva |
| [Azure SDK per Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 o successiva |

## <a name="actions"></a>actions

L'autorizzazione `actions` specifica le operazioni di gestione a cui il ruolo concede l'accesso. Si tratta di una raccolta di stringhe di operazione che identificano operazioni a protezione diretta dei provider di risorse di Azure. Di seguito sono riportati alcuni esempi di operazioni di gestione che possono essere usate in `actions`.

| Stringa operazione    | Descrizione         |
| ------------------- | ------------------- |
| `*/read` | Concede l'accesso a operazioni di lettura per tutti i tipi di risorse di tutti i provider di risorse di Azure.|
| `Microsoft.Compute/*` | Concede l'accesso a tutte le operazioni per tutti i tipi di risorse nel provider di risorse Microsoft.Compute.|
| `Microsoft.Network/*/read` | Concede l'accesso alle operazioni di lettura per tutti i tipi di risorse nel provider di risorse Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | Concede l'accesso a tutte le operazioni delle macchine virtuali e ai relativi tipi di risorse figlio.|
| `microsoft.web/sites/restart/Action` | Concede l'accesso per il riavvio di un'app Web.|

## <a name="notactions"></a>notActions

L'autorizzazione `notActions` specifica le operazioni di gestione che sono escluse dall'autorizzazione `actions`. Usare l'autorizzazione `notActions` se il set di operazioni che si vuole consentire viene definito più facilmente escludendo le operazioni non consentite. L'accesso concesso da un ruolo (ovvero le autorizzazioni effettive) viene calcolato sottraendo le operazioni `notActions` alle operazioni `actions`.

> [!NOTE]
> Se a un utente si assegna un ruolo che esclude un'operazione in `notActions` e quindi si assegna un secondo ruolo che concede l'accesso alla stessa operazione, l'utente può eseguire tale operazione. `notActions` non è una regola di negazione. È semplicemente un modo comodo per creare un set di operazioni consentite quando è necessario escludere operazioni specifiche.
>

## <a name="dataactions-preview"></a>dataActions (anteprima)

L'autorizzazione `dataActions` specifica le operazioni sui dati a cui il ruolo concede l'accesso ai dati all'interno dell'oggetto. Ad esempio, se un utente dispone dell'accesso in lettura ai dati di BLOB per un account di archiviazione, può leggere i BLOB all'interno di tale account. Di seguito sono riportati alcuni esempi di operazioni sui dati che possono essere usate in `dataActions`.

| Stringa operazione    | Descrizione         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Restituisce un BLOB o un elenco di BLOB. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Restituisce il risultato della scrittura su un BLOB. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Restituisce un messaggio. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Restituisce un messaggio o il risultato della scrittura o dell'eliminazione di un messaggio. |

## <a name="notdataactions-preview"></a>notDataActions (anteprima)

L'autorizzazione `notDataActions` specifica le operazioni sui dati che sono escluse dall'autorizzazione `dataActions`. L'accesso concesso da un ruolo (ovvero le autorizzazioni effettive) viene calcolato sottraendo le operazioni `notDataActions` alle operazioni `dataActions`. Ogni provider di risorse fornisce il rispettivo set di API per consentire l'esecuzione delle operazioni sui dati.

> [!NOTE]
> Se a un utente si assegna un ruolo che esclude un'operazione sui dati in `notDataActions` e quindi si assegna un secondo ruolo che concede l'accesso alla stessa operazione, l'utente può eseguire tale operazione. `notDataActions` non è una regola di negazione. È semplicemente un modo comodo per creare un set di operazioni sui dati consentite quando è necessario escludere operazioni specifiche.
>

## <a name="assignablescopes"></a>assignableScopes

La sezione `assignableScopes` specifica gli ambiti (gruppi di gestione, attualmente in anteprima, sottoscrizioni, gruppi di risorse o risorse) in cui il ruolo è disponibile per l'assegnazione. È possibile rendere disponibile il ruolo per l'assegnazione solo nelle sottoscrizioni o nei gruppi di risorse che lo richiedono, in modo da non complicare l'esperienza utente per le altre sottoscrizioni o gli altri gruppi di risorse. È necessario usare almeno un gruppo di gestione, una sottoscrizione, un gruppo di risorse o un ID di risorsa.

Per i ruoli predefiniti, `assignableScopes` è impostato sull'ambito radice (`"/"`), per indicare che il ruolo è disponibile per l'assegnazione in tutti gli ambiti. Non è possibile usare l'ambito radice nei ruoli personalizzati. Se si esegue questo tentativo, si ottiene un errore di autorizzazione.

Ecco alcuni esempi di ambiti assegnabili validi:

| Scenario | Esempio |
|----------|---------|
| Ruolo disponibile per l'assegnazione in una singola sottoscrizione | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Ruolo disponibile per l'assegnazione in due sottoscrizioni | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Ruolo disponibile per l'assegnazione solo nel gruppo di risorse Network | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Ruolo disponibile per l'assegnazione in tutti gli ambiti | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes e ruoli personalizzati

La sezione `assignableScopes` per un ruolo personalizzato controlla anche chi può creare, eliminare, modificare o visualizzare il ruolo personalizzato.

| Attività | Operazione | Descrizione |
| --- | --- | --- |
| Creare o eliminare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/write` | Gli utenti a cui viene concessa questa operazione su tutti gli ambiti `assignableScopes` del ruolo personalizzato possono creare (o eliminare) ruoli personalizzati da usare in tali ambiti. Ad esempio, i ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) di sottoscrizioni, gruppi di risorse e risorse. |
| Modificare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/write` | Gli utenti a cui viene concessa questa autorizzazione su tutti gli ambiti `assignableScopes` del ruolo personalizzato possono modificare ruoli personalizzati in tali ambiti. Ad esempio, i ruoli [Proprietario](built-in-roles.md#owner) e [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator) di sottoscrizioni, gruppi di risorse e risorse. |
| Visualizzare un ruolo personalizzato | `Microsoft.Authorization/ roleDefinition/read` | Gli utenti a cui viene concessa questa operazione a livello di ambito possono visualizzare i ruoli personalizzati disponibili per l'assegnazione in tale ambito. Tutti i ruoli predefiniti consentono la disponibilità dei ruoli personalizzati per l'assegnazione. |

## <a name="role-definition-examples"></a>Esempi di definizioni di ruolo

L'esempio seguente mostra la definizione del ruolo [Lettore](built-in-roles.md#reader), come visualizzata tramite l'interfaccia della riga di comando di Azure:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

L'esempio seguente mostra un ruolo personalizzato per il monitoraggio e il riavvio di macchine virtuali, come visualizzato tramite Azure PowerShell:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Vedere anche 

* [Ruoli predefiniti](built-in-roles.md)
* [Ruoli personalizzati](custom-roles.md)
* [Operazioni di provider di risorse con Azure Resource Manager](resource-provider-operations.md)
