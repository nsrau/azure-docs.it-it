---
title: Gestire i ruoli nell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come accedere a un'area di lavoro di Azure Machine Learning usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18, devx-track-azurecli
ms.openlocfilehash: aa84d7cce09b370ab35ef67029f4dbe2ca29cabb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320849"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gestire gli accessi all'area di lavoro di Azure Machine Learning

Questo articolo illustra come gestire l'accesso a un'area di lavoro Azure Machine Learning. Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) viene usato per gestire l'accesso alle risorse di Azure. Agli utenti nella Azure Active Directory vengono assegnati ruoli specifici che consentono di accedere alle risorse. Azure fornisce ruoli predefiniti e la possibilità di creare ruoli personalizzati.

## <a name="default-roles"></a>Ruoli predefiniti

Un'area di lavoro di Azure Machine Learning è una risorsa di Azure. Analogamente ad altre risorse di Azure, quando viene creata una nuova area di lavoro di Azure Machine Learning, essa presenta tre ruoli predefiniti. È possibile aggiungere utenti all'area di lavoro e assegnarli a uno di questi ruoli predefiniti.

| Ruolo | Livello di accesso |
| --- | --- |
| **Lettore** | Azioni di sola lettura nell'area di lavoro. I lettori possono elencare e visualizzare le risorse, incluse le credenziali dell' [archivio dati](how-to-access-data.md) , in un'area di lavoro. I lettori non possono creare o aggiornare tali asset. |
| **Collaboratore** | Consente di visualizzare, creare, modificare o eliminare risorse, ove applicabile, in un'area di lavoro. I collaboratori possono, ad esempio, creare un esperimento, creare o allegare un cluster di calcolo, inviare un'esecuzione e distribuire un servizio Web. |
| **Proprietario** | Accesso completo all'area di lavoro, inclusa la possibilità di visualizzare, creare, modificare o eliminare le risorse (ove applicabile) in un'area di lavoro. Inoltre, è possibile modificare le assegnazioni di ruolo. |
| **Ruolo personalizzato** | Consente di personalizzare l'accesso a specifiche operazioni di controllo o piano dati all'interno di un'area di lavoro. Ad esempio, l'invio di un'esecuzione, la creazione di un calcolo, la distribuzione di un modello o la registrazione di un set di dati. |

> [!IMPORTANT]
> L'accesso ai ruoli può essere limitato a più livelli in Azure. Ad esempio, un utente con accesso proprietario a un'area di lavoro potrebbe non avere accesso proprietario al gruppo di risorse che contiene l'area di lavoro. Per altre informazioni, vedere funzionamento di [Azure RBAC](../role-based-access-control/overview.md#how-azure-rbac-works).

Per altre informazioni sui ruoli predefiniti specifici, vedere [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md).

## <a name="manage-workspace-access"></a>Gestisci l'accesso all'area di lavoro

Se si è proprietari di un'area di lavoro, è possibile aggiungere e rimuovere ruoli per l'area di lavoro. È anche possibile assegnare ruoli agli utenti. Usare i collegamenti seguenti per scoprire come gestire l'accesso:
- [Interfaccia utente del portale di Azure](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Modelli di Gestione risorse di Azure](../role-based-access-control/role-assignments-template.md)

Se è stata installata l' [interfaccia](reference-azure-machine-learning-cli.md)della riga di comando Azure Machine Learning, è possibile usare i comandi dell'interfaccia della riga di comando per assegnare ruoli agli utenti

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Il `user` campo è l'indirizzo di posta elettronica di un utente esistente nell'istanza di Azure Active Directory in cui risiede la sottoscrizione padre dell'area di lavoro. Di seguito è riportato un esempio di come usare questo comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> il comando "AZ ml Workspace Share" non funziona per l'account federato da Azure Active Directory B2B. Usare il portale dell'interfaccia utente di Azure anziché il comando.


## <a name="azure-machine-learning-operations"></a>Operazioni di Azure Machine Learning

Azure Machine Learning azioni predefinite per molte operazioni e attività. Per un elenco completo, vedere [operazioni del provider di risorse di Azure](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices).

## <a name="mlflow-operations-in-azure-machine-learning"></a>Operazioni MLflow in Azure Machine Learning

In questa tabella viene descritto l'ambito di autorizzazione che deve essere aggiunto alle azioni nel ruolo personalizzato creato per eseguire operazioni MLflow.

| Operazione MLflow | Ambito |
| --- | --- |
| Elencare tutti gli esperimenti nell'archivio di rilevamento dell'area di lavoro, ottenere un esperimento in base all'ID, ottenere un esperimento per nome | Microsoft. MachineLearningServices/Workspaces/Experiments/Read |
| Creare un esperimento con un nome, impostare un tag per un esperimento, ripristinare un esperimento contrassegnato per l'eliminazione| Microsoft. MachineLearningServices/Workspaces/Experiments/Write | 
| Eliminare un esperimento | Microsoft. MachineLearningServices/Workspaces/Experiments/Delete |
| Ottenere un'esecuzione e i dati e i metadati correlati, ottenere un elenco di tutti i valori per la metrica specificata per un'esecuzione specificata, elencare gli elementi per un'esecuzione | Microsoft. MachineLearningServices/Workspaces/Experiments/Run/Read |
| Creare una nuova esecuzione all'interno di un esperimento, eliminare le esecuzioni, ripristinare le esecuzioni eliminate, registrare le metriche nell'esecuzione corrente, impostare i tag in un'esecuzione, eliminare tag in un'esecuzione, parametri di log (coppia chiave-valore) usati per un'esecuzione, registrare un batch di metriche, parametri e tag per un'esecuzione, stato dell'esecuzione dell'aggiornamento | Microsoft. MachineLearningServices/Workspaces/Experiments/Run/Write |
| Ottenere un modello registrato per nome, recuperare un elenco di tutti i modelli registrati nel registro di sistema, cercare i modelli registrati, i modelli di versione più recenti per ogni fase delle richieste, ottenere la versione di un modello registrato, cercare le versioni del modello, ottenere l'URI in cui sono archiviati gli artefatti della versione di un modello, cercare le esecuzioni per ID esperimento | Microsoft. MachineLearningServices/aree di lavoro/modelli/lettura |
| Creazione di un nuovo modello registrato, aggiornamento del nome/descrizione di un modello registrato, ridenominazione del modello registrato esistente, creazione di una nuova versione del modello, aggiornamento della descrizione di una versione del modello, transizione di un modello registrato a una delle fasi | Microsoft. MachineLearningServices/aree di lavoro/modelli/scrittura |
| Eliminare un modello registrato insieme a tutte le relative versioni, eliminare versioni specifiche di un modello registrato | Microsoft. MachineLearningServices/Workspaces/Models/Delete |


## <a name="create-custom-role"></a>Creare un ruolo personalizzato

Se i ruoli predefiniti non sono sufficienti, è possibile creare ruoli personalizzati. I ruoli personalizzati potrebbero avere autorizzazioni di risorse di lettura, scrittura, eliminazione e calcolo in tale area di lavoro. È possibile rendere il ruolo disponibile a livello di area di lavoro specifico, a livello di gruppo di risorse specifico o a un livello di sottoscrizione specifico.

> [!NOTE]
> Per creare ruoli personalizzati all'interno di tale risorsa, è necessario essere un proprietario della risorsa a tale livello.

Per creare un ruolo personalizzato, creare prima di tutto un file JSON di definizione del ruolo che specifichi l'autorizzazione e l'ambito per il ruolo. Nell'esempio seguente viene definito un ruolo personalizzato denominato "data scientist Custom" con ambito a un livello specifico dell'area di lavoro:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> È possibile modificare il `AssignableScopes` campo per impostare l'ambito di questo ruolo personalizzato a livello di sottoscrizione, a livello di gruppo di risorse o a livello di area di lavoro specifico.
> Il ruolo personalizzato precedente è solo un esempio, vedere alcuni [ruoli personalizzati suggeriti per il servizio Azure Machine Learning](#customroles).

Questo ruolo personalizzato può eseguire tutte le operazioni nell'area di lavoro, ad eccezione delle azioni seguenti:

- Non è possibile creare o aggiornare una risorsa di calcolo.
- Non è possibile eliminare una risorsa di calcolo.
- Non è possibile aggiungere, eliminare o modificare le assegnazioni di ruolo.
- Non è possibile eliminare l'area di lavoro.

Per distribuire questo ruolo personalizzato, usare il comando dell'interfaccia della riga di comando di Azure seguente:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Dopo la distribuzione, questo ruolo diventa disponibile nell'area di lavoro specificata. A questo punto è possibile aggiungere e assegnare questo ruolo nella portale di Azure. In alternativa, è possibile assegnare questo ruolo a un utente usando il `az ml workspace share` comando dell'interfaccia della riga di comando:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Per altre informazioni sui ruoli personalizzati, vedere [ruoli personalizzati di Azure](../role-based-access-control/custom-roles.md). Per altre informazioni sulle operazioni (azioni e non azioni) utilizzabili con i ruoli personalizzati, vedere [operazioni del provider di risorse](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices).

## <a name="frequently-asked-questions"></a>Domande frequenti


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>Q. Quali sono le autorizzazioni necessarie per eseguire alcuni scenari comuni nel servizio Azure Machine Learning?

La tabella seguente è un riepilogo delle attività Azure Machine Learning e delle autorizzazioni necessarie per eseguirle nel minor ambito. Se, ad esempio, un'attività può essere eseguita con un ambito dell'area di lavoro (colonna 4), anche tutti gli ambiti più elevati con tale autorizzazione funzioneranno automaticamente:

> [!IMPORTANT]
> Tutti i percorsi della tabella che iniziano con `/` sono **percorsi relativi** a `Microsoft.MachineLearningServices/` :

| Attività | Ambito a livello di sottoscrizione | Ambito a livello di gruppo di risorse | Ambito a livello di area di lavoro |
| ----- | ----- | ----- | ----- |
| Creare una nuova area di lavoro | Non richiesto | Proprietario o collaboratore | N/d (diventa proprietario o eredita un ruolo con ambito superiore dopo la creazione) |
| Richiedi quota Amlcompute a livello di sottoscrizione o imposta quota a livello di area di lavoro | Proprietario, collaboratore o ruolo personalizzato </br>permettendo `/locations/updateQuotas/action`</br> nell'ambito della sottoscrizione | Non autorizzato | Non autorizzato |
| Crea nuovo cluster di calcolo | Non richiesto | Non richiesto | Proprietario, collaboratore o ruolo personalizzato che consente: `/workspaces/computes/write` |
| Crea nuova istanza di calcolo | Non richiesto | Non richiesto | Proprietario, collaboratore o ruolo personalizzato che consente: `/workspaces/computes/write` |
| Invio di qualsiasi tipo di esecuzione | Non richiesto | Non richiesto | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Pubblicazione di un endpoint della pipeline | Non richiesto | Non richiesto | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Distribuzione di un modello registrato su una risorsa AKS/ACI | Non richiesto | Non richiesto | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Assegnazione dei punteggi a un endpoint AKS distribuito | Non richiesto | Non richiesto | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (se non si usa l'autenticazione Azure Active Directory) o `"/workspaces/read"` (quando si usa l'autenticazione del token) |
| Accesso all'archiviazione tramite notebook interattivi | Non richiesto | Non richiesto | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Crea nuovo ruolo personalizzato | Proprietario, collaboratore o ruolo personalizzato che consente `Microsoft.Authorization/roleDefinitions/write` | Non richiesto | Proprietario, collaboratore o ruolo personalizzato che consente: `/workspaces/computes/write` |

> [!TIP]
> Se viene visualizzato un errore durante il tentativo di creare un'area di lavoro per la prima volta, assicurarsi che il ruolo sia consentito `Microsoft.MachineLearningServices/register/action` . Questa azione consente di registrare il provider di risorse Azure Machine Learning con la sottoscrizione di Azure.

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>Q. Si pubblicano i ruoli predefiniti di Azure per il servizio Machine Learning?

Attualmente non vengono pubblicati i [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md) per il servizio Machine Learning. Un ruolo incorporato dopo la pubblicazione non può essere aggiornato e le definizioni di ruolo vengono ancora stabilite in base agli scenari e ai commenti dei clienti. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>Q. Esistono alcuni modelli di ruolo personalizzati per gli scenari più comuni nel servizio Machine Learning?

Sì Ecco alcuni scenari comuni con le definizioni di ruolo proposte personalizzate che è possibile usare come base per definire ruoli personalizzati:

* __Custom Data Scientist__ : consente a un data scientist di eseguire tutte le operazioni all'interno di un'area di lavoro, **ad eccezione** di:

    * Creazione di calcolo
    * Distribuzione di modelli in un cluster AKS di produzione
    * Distribuzione di un endpoint della pipeline nell'ambiente di produzione

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Custom Data Scientist con restrizioni__ : una definizione di ruolo più limitata senza caratteri jolly nelle azioni consentite. Può eseguire tutte le operazioni all'interno di un'area di lavoro **eccetto** :

    * Creazione di calcolo
    * Distribuzione di modelli in un cluster AKS di produzione
    * Distribuzione di un endpoint della pipeline nell'ambiente di produzione

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```
     
* __MLflow data scientist personalizzato__ : consente a un data scientist di eseguire tutte le operazioni supportate da MLflow AzureML **ad eccezione** di:

   * Creazione di calcolo
   * Distribuzione di modelli in un cluster AKS di produzione
   * Distribuzione di un endpoint della pipeline nell'ambiente di produzione

   `mlflow_data_scientist_custom_role.json` :
   ```json
   {
        "Name": "MLFlow Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/experiments/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/delete",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/models/read",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/models/delete"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
     "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```   

* __MLOps Custom__ : consente di assegnare un ruolo a un'entità servizio e usarlo per automatizzare le pipeline MLOps. Ad esempio, per inviare le esecuzioni in una pipeline già pubblicata:

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Area di lavoro amministratore__ : consente di eseguire tutte le operazioni nell'ambito di un'area di lavoro, **ad eccezione** di:

    * Creazione di una nuova area di lavoro
    * Assegnazione delle quote a livello di sottoscrizione o area di lavoro

    L'amministratore dell'area di lavoro non è inoltre in grado di creare un nuovo ruolo. Può assegnare i ruoli predefiniti o personalizzati esistenti solo nell'ambito dell'area di lavoro:

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Labeler Custom__ : consente di definire un ruolo con ambito solo per l'assegnazione di etichette ai dati:

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. Ricerca per categorie elencare tutti i ruoli personalizzati nella sottoscrizione?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>Q. Ricerca per categorie individuare le operazioni supportate dal servizio Machine Learning?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

Sono disponibili anche nell'elenco delle [operazioni del provider di risorse](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices).


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>Q. Quali sono alcuni problemi comuni relativi all'uso di RBAC di Azure?

Di seguito sono riportate alcune considerazioni da tenere presente quando si usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure):

- Quando si crea una risorsa in Azure, ad indicare un'area di lavoro, non si è direttamente il proprietario dell'area di lavoro. Il ruolo viene ereditato dal ruolo con ambito più elevato su cui si è autorizzati in tale sottoscrizione. Ad esempio, se si è un amministratore di rete e si dispone delle autorizzazioni per creare un'area di lavoro Machine Learning, a tale area di lavoro verrà assegnato il ruolo di amministratore di rete e non il ruolo proprietario.
- Quando sono presenti due assegnazioni di ruolo per lo stesso utente Azure Active Directory con sezioni in conflitto di azioni o notact, le operazioni elencate in notacts da un ruolo potrebbero non essere effettive se sono anche elencate come azioni in un altro ruolo. Per altre informazioni su come Azure analizza le assegnazioni di ruolo, vedere in [che modo RBAC di Azure determina se un utente ha accesso a una risorsa](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)
- Per distribuire le risorse di calcolo all'interno di una VNet, è necessario disporre esplicitamente delle autorizzazioni per le azioni seguenti:
    - "Microsoft. Network/virtualNetworks/join/Action" sulla risorsa VNet.
    - "Microsoft. Network/virtualNetworks/subnet/join/Action" sulla risorsa della subnet.
    
    Per altre informazioni sul controllo degli accessi in base al ruolo di Azure con la rete, vedere i [ruoli predefiniti di rete](../role-based-access-control/built-in-roles.md#networking).

- A volte può essere necessaria fino a un'ora prima che le nuove assegnazioni di ruolo abbiano effetto sulle autorizzazioni memorizzate nella cache dello stack.

### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>Q. Quali autorizzazioni sono necessarie per usare un'identità gestita assegnata dall'utente con i cluster Amlcompute?

Per assegnare un'identità assegnata all'utente nei cluster Amlcompute, è necessario disporre delle autorizzazioni di scrittura per creare il calcolo e avere un [ruolo di operatore di identità gestito](../role-based-access-control/built-in-roles.md#managed-identity-operator). Per altre informazioni sul controllo degli accessi in base al ruolo di Azure con identità gestite, vedere [come gestire l'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>Q. È supportato il controllo degli accessi in base al ruolo nel portale di studio?

Azure Machine Learning Studio supporta il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). 

> [!IMPORTANT]
> Dopo aver assegnato un ruolo personalizzato con autorizzazioni specifiche a un data scientist nell'area di lavoro, le azioni corrispondenti, ad esempio l'aggiunta di un pulsante di calcolo, vengono automaticamente nascoste agli utenti. Nascondendo questi elementi si evitano confusione nella visualizzazione dei controlli che restituiscono una notifica di accesso non autorizzato dal servizio quando vengono utilizzati.

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. Ricerca per categorie trovare la definizione di ruolo per un ruolo nella sottoscrizione?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente. `<role-name>`Deve avere lo stesso formato restituito dal comando precedente.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. Ricerca per categorie aggiornare una definizione di ruolo?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

È necessario disporre delle autorizzazioni per l'intero ambito della nuova definizione di ruolo. Se, ad esempio, il nuovo ruolo dispone di un ambito in tre sottoscrizioni, è necessario disporre delle autorizzazioni per tutte e tre le sottoscrizioni. 

> [!NOTE]
> Gli aggiornamenti dei ruoli possono richiedere da 15 minuti a un'ora per essere applicati a tutte le assegnazioni di ruolo in tale ambito.


### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. Quali autorizzazioni sono necessarie per eseguire operazioni di quota in un'area di lavoro? 

È necessario disporre delle autorizzazioni a livello di sottoscrizione per eseguire qualsiasi operazione relativa alla quota nell'area di lavoro. Ciò significa che l'impostazione della quota a livello di sottoscrizione o della quota a livello di area di lavoro per le risorse di calcolo gestite può verificarsi solo se si dispone di autorizzazioni di scrittura nell'ambito della sottoscrizione. 


## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sulla sicurezza aziendale](concept-enterprise-security.md)
- [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)
- [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
- [Operazioni del provider di risorse](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)