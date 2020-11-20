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
ms.date: 11/09/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperfq2
ms.openlocfilehash: 6cd4bbec89e955c398f7cb6e37ba5c3dcc6427ea
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961227"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gestire gli accessi all'area di lavoro di Azure Machine Learning

Questo articolo illustra come gestire l'accesso (autorizzazione) a un'area di lavoro Azure Machine Learning. Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md) viene usato per gestire l'accesso alle risorse di Azure, ad esempio la possibilità di creare nuove risorse o usare quelle esistenti. Agli utenti del Azure Active Directory (Azure AD) vengono assegnati ruoli specifici che concedono l'accesso alle risorse. Azure fornisce ruoli predefiniti e la possibilità di creare ruoli personalizzati.

> [!TIP]
> Questo articolo è incentrato sulla Azure Machine Learning, i singoli servizi basati su Azure ML che forniscono le proprie impostazioni RBAC. Ad esempio, usando le informazioni contenute in questo articolo, è possibile configurare chi può inviare richieste di assegnazione dei punteggi a un modello distribuito come servizio Web nel servizio Azure Kubernetes. Il servizio Azure Kubernetes fornisce tuttavia un proprio set di ruoli di Azure. Per informazioni RBAC specifiche del servizio che possono essere utili con Azure Machine Learning, vedere i collegamenti seguenti:
>
> * [Controllare l'accesso alle risorse cluster di Azure Kubernetes](../aks/azure-ad-rbac.md)
> * [Usa RBAC di Azure per l'autorizzazione Kubernetes](../aks/manage-azure-rbac.md)
> * [Usare il controllo degli accessi in base al ruolo di Azure](/storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> L'applicazione di alcuni ruoli può limitare la funzionalità dell'interfaccia utente in Azure Machine Learning Studio per altri utenti. Se, ad esempio, il ruolo di un utente non è in grado di creare un'istanza di calcolo, l'opzione per creare un'istanza di calcolo non sarà disponibile in studio. Questo comportamento è previsto e impedisce all'utente di provare a eseguire operazioni che restituiscono un errore di accesso negato.

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

Attualmente non sono presenti ruoli predefiniti aggiuntivi specifici per Azure Machine Learning. Per altre informazioni sui ruoli predefiniti, vedere [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md).

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

Per altre informazioni sui ruoli personalizzati, vedere [ruoli personalizzati di Azure](../role-based-access-control/custom-roles.md). 

### <a name="azure-machine-learning-operations"></a>Operazioni di Azure Machine Learning

Per altre informazioni sulle operazioni (azioni e non azioni) utilizzabili con i ruoli personalizzati, vedere [operazioni del provider di risorse](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices). È anche possibile usare il comando dell'interfaccia della riga di comando di Azure seguente per elencare le operazioni:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Per visualizzare la definizione di ruolo per un ruolo personalizzato specifico, usare il comando seguente dell'interfaccia della riga di comando di Azure. `<role-name>`Deve avere lo stesso formato restituito dal comando precedente:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

È necessario disporre delle autorizzazioni per l'intero ambito della nuova definizione di ruolo. Se, ad esempio, il nuovo ruolo dispone di un ambito in tre sottoscrizioni, è necessario disporre delle autorizzazioni per tutte e tre le sottoscrizioni. 

> [!NOTE]
> Gli aggiornamenti dei ruoli possono richiedere da 15 minuti a un'ora per essere applicati a tutte le assegnazioni di ruolo in tale ambito.

## <a name="common-scenarios"></a>Scenari comuni

La tabella seguente è un riepilogo delle attività Azure Machine Learning e delle autorizzazioni necessarie per eseguirle nel minor ambito. Se, ad esempio, un'attività può essere eseguita con un ambito dell'area di lavoro (colonna 4), anche tutti gli ambiti più elevati con tale autorizzazione funzioneranno automaticamente:

> [!IMPORTANT]
> Tutti i percorsi della tabella che iniziano con `/` sono **percorsi relativi** a `Microsoft.MachineLearningServices/` :

| Attività | Ambito a livello di sottoscrizione | Ambito a livello di gruppo di risorse | Ambito a livello di area di lavoro |
| ----- | ----- | ----- | ----- |
| Creare una nuova area di lavoro | Facoltativo | Proprietario o collaboratore | N/d (diventa proprietario o eredita un ruolo con ambito superiore dopo la creazione) |
| Richiedi quota Amlcompute a livello di sottoscrizione o imposta quota a livello di area di lavoro | Proprietario, collaboratore o ruolo personalizzato </br>permettendo `/locations/updateQuotas/action`</br> nell'ambito della sottoscrizione | Non autorizzato | Non autorizzato |
| Crea nuovo cluster di calcolo | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente: `/workspaces/computes/write` |
| Crea nuova istanza di calcolo | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente: `/workspaces/computes/write` |
| Invio di qualsiasi tipo di esecuzione | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Pubblicazione di un endpoint della pipeline | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Distribuzione di un modello registrato su una risorsa AKS/ACI | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Assegnazione dei punteggi a un endpoint AKS distribuito | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (se non si usa l'autenticazione Azure Active Directory) o `"/workspaces/read"` (quando si usa l'autenticazione del token) |
| Accesso all'archiviazione tramite notebook interattivi | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Crea nuovo ruolo personalizzato | Proprietario, collaboratore o ruolo personalizzato che consente `Microsoft.Authorization/roleDefinitions/write` | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente: `/workspaces/computes/write` |

> [!TIP]
> Se viene visualizzato un errore durante il tentativo di creare un'area di lavoro per la prima volta, assicurarsi che il ruolo sia consentito `Microsoft.MachineLearningServices/register/action` . Questa azione consente di registrare il provider di risorse Azure Machine Learning con la sottoscrizione di Azure.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Identità gestita assegnata dall'utente con cluster di calcolo di Azure ML

Per assegnare un'identità assegnata all'utente a un cluster di calcolo di Azure Machine Learning, è necessario disporre delle autorizzazioni di scrittura per creare il ruolo calcolo e [operatore identità gestito](../role-based-access-control/built-in-roles.md#managed-identity-operator). Per altre informazioni sul controllo degli accessi in base al ruolo di Azure con identità gestite, vedere [come gestire l'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

### <a name="mlflow-operations"></a>Operazioni MLflow

Per eseguire operazioni MLflow con l'area di lavoro Azure Machine Learning, usare gli ambiti seguenti per il ruolo personalizzato:

| Operazione MLflow | Scope |
| --- | --- |
| Elencare tutti gli esperimenti nell'archivio di rilevamento dell'area di lavoro, ottenere un esperimento in base all'ID, ottenere un esperimento per nome | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Creare un esperimento con un nome, impostare un tag per un esperimento, ripristinare un esperimento contrassegnato per l'eliminazione| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Eliminare un esperimento | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Ottenere un'esecuzione e i dati e i metadati correlati, ottenere un elenco di tutti i valori per la metrica specificata per un'esecuzione specificata, elencare gli elementi per un'esecuzione | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Creare una nuova esecuzione all'interno di un esperimento, eliminare le esecuzioni, ripristinare le esecuzioni eliminate, registrare le metriche nell'esecuzione corrente, impostare i tag in un'esecuzione, eliminare tag in un'esecuzione, parametri di log (coppia chiave-valore) usati per un'esecuzione, registrare un batch di metriche, parametri e tag per un'esecuzione, stato dell'esecuzione dell'aggiornamento | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| Ottenere un modello registrato per nome, recuperare un elenco di tutti i modelli registrati nel registro di sistema, cercare i modelli registrati, i modelli di versione più recenti per ogni fase delle richieste, ottenere la versione di un modello registrato, cercare le versioni del modello, ottenere l'URI in cui sono archiviati gli artefatti della versione di un modello, cercare le esecuzioni per ID esperimento | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Creazione di un nuovo modello registrato, aggiornamento del nome/descrizione di un modello registrato, ridenominazione del modello registrato esistente, creazione di una nuova versione del modello, aggiornamento della descrizione di una versione del modello, transizione di un modello registrato a una delle fasi | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Eliminare un modello registrato insieme a tutte le relative versioni, eliminare versioni specifiche di un modello registrato | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Ruoli personalizzati di esempio

### <a name="data-scientist"></a>Data scientist

Consente a un data scientist di eseguire tutte le operazioni all'interno di un'area di lavoro **eccetto**:

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

### <a name="data-scientist-restricted"></a>Data Scientist con restrizioni

Definizione di ruolo più limitata senza caratteri jolly nelle azioni consentite. Può eseguire tutte le operazioni all'interno di un'area di lavoro **eccetto**:

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
     
### <a name="mlflow-data-scientist"></a>MLflow data scientist

Consente a un data scientist di eseguire tutte le operazioni supportate da MLflow AzureML **eccetto**:

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

### <a name="mlops"></a>MLOps

Consente di assegnare un ruolo a un'entità servizio e di usarlo per automatizzare le pipeline MLOps. Ad esempio, per inviare le esecuzioni in una pipeline già pubblicata:

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

### <a name="workspace-admin"></a>Amministratore area di lavoro

Consente di eseguire tutte le operazioni nell'ambito di un'area di lavoro, **ad eccezione** di:

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
### <a name="data-labeler"></a>Etichetta dati

Consente di definire un ruolo con ambito solo per l'assegnazione di etichette ai dati:

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito sono riportate alcune considerazioni da tenere presente quando si usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure):

- Quando si crea una risorsa in Azure, ad esempio un'area di lavoro, non si è direttamente il proprietario della risorsa. Il ruolo viene ereditato dal ruolo con ambito più elevato su cui si è autorizzati in tale sottoscrizione. Ad esempio, se si è un amministratore di rete e si hanno le autorizzazioni per creare un'area di lavoro Machine Learning, a tale area di lavoro verrà assegnato il ruolo di amministratore di rete e non il ruolo proprietario.

- Per eseguire operazioni di quota in un'area di lavoro, è necessario disporre delle autorizzazioni a livello di sottoscrizione. Ciò significa che l'impostazione della quota a livello di sottoscrizione o della quota a livello di area di lavoro per le risorse di calcolo gestite può verificarsi solo se si dispone di autorizzazioni di scrittura nell'ambito della sottoscrizione.

- Quando sono presenti due assegnazioni di ruolo per lo stesso utente Azure Active Directory con sezioni in conflitto di azioni o notact, le operazioni elencate in notacts da un ruolo potrebbero non essere effettive se sono anche elencate come azioni in un altro ruolo. Per altre informazioni su come Azure analizza le assegnazioni di ruolo, vedere in [che modo RBAC di Azure determina se un utente ha accesso a una risorsa](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)

- Per distribuire le risorse di calcolo all'interno di una VNet, è necessario disporre esplicitamente delle autorizzazioni per le azioni seguenti:
    - `Microsoft.Network/virtualNetworks/join/action` nella risorsa VNet.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` sulla risorsa della subnet.
    
    Per altre informazioni sul controllo degli accessi in base al ruolo di Azure con la rete, vedere i [ruoli predefiniti di rete](../role-based-access-control/built-in-roles.md#networking).

- A volte può essere necessaria fino a un'ora prima che le nuove assegnazioni di ruolo abbiano effetto sulle autorizzazioni memorizzate nella cache dello stack.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica sulla sicurezza aziendale](concept-enterprise-security.md)
- [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)
- [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
- [Operazioni del provider di risorse](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)