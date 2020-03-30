---
title: Gestire i ruoli nell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come accedere a un'area di lavoro di Azure Machine Learning usando il controllo degli accessi in base al ruolo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270095"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gestire l'accesso a un'area di lavoro di Azure Machine LearningManage access to an Azure Machine Learning workspace
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come gestire l'accesso a un'area di lavoro di Azure Machine Learning.In this article, you learn how to manage access to an Azure Machine Learning workspace. Il controllo degli accessi in base al ruolo viene usato per gestire l'accesso alle risorse di [Azure.Role-based access control (RBAC)](/azure/role-based-access-control/overview) is used to manage access to Azure resources. Agli utenti di Azure Active Directory vengono assegnati ruoli specifici, che concedono l'accesso alle risorse. Azure offre sia ruoli predefiniti che la possibilità di creare ruoli personalizzati.

## <a name="default-roles"></a>Ruoli predefiniti

Un'area di lavoro di Azure Machine Learning è una risorsa di Azure.An Azure Machine Learning workspace is an Azure resource. Come altre risorse di Azure, quando viene creata una nuova area di lavoro di Azure Machine Learning, viene fornito con tre ruoli predefiniti. È possibile aggiungere utenti all'area di lavoro e assegnarli a uno di questi ruoli predefiniti.

| Ruolo | Livello di accesso |
| --- | --- |
| **Lettore** | Azioni di sola lettura nell'area di lavoro. I lettori possono elencare e visualizzare le risorse in un'area di lavoro, ma non possono creare o aggiornare tali risorse. |
| **Collaboratore** | Visualizzare, creare, modificare o eliminare risorse (se applicabile) in un'area di lavoro. Ad esempio, i collaboratori possono creare un esperimento, creare o collegare un cluster di calcolo, inviare un'esecuzione e distribuire un servizio Web.For example, contributors can create an experiment, create or attach a compute cluster, submit a run, and deploy a web service. |
| **Proprietario** | Accesso completo all'area di lavoro, inclusa la possibilità di visualizzare, creare, modificare o eliminare risorse (se applicabile) in un'area di lavoro. Inoltre, è possibile modificare le assegnazioni di ruolo. |

> [!IMPORTANT]
> L'accesso ai ruoli può essere limitato a più livelli in Azure.Role access can be scoped to multiple levels in Azure. Ad esempio, un utente con accesso del proprietario a un'area di lavoro potrebbe non avere accesso del proprietario al gruppo di risorse che contiene l'area di lavoro. Per ulteriori informazioni, vedere [Funzionamento del controllo degli accessi in base al su più.](/azure/role-based-access-control/overview#how-rbac-works)

Per altre informazioni su ruoli predefiniti specifici, vedere Ruoli predefiniti per Azure.For more information on specific built-in roles, see [Built-in roles for Azure.](/azure/role-based-access-control/built-in-roles)

## <a name="manage-workspace-access"></a>Gestire l'accesso all'area di lavoro

Se si è proprietari di un'area di lavoro, è possibile aggiungere e rimuovere ruoli per l'area di lavoro. È inoltre possibile assegnare ruoli agli utenti. Utilizzare i collegamenti seguenti per scoprire come gestire l'accesso:
- [Interfaccia utente del portale di AzureAzure portal UI](/azure/role-based-access-control/role-assignments-portal)
- [Powershell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfaccia della riga di comando di AzureAzure](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Modelli di Gestione risorse di Azure](/azure/role-based-access-control/role-assignments-template)

Se è stata installata l'interfaccia della riga di comando di [Azure Machine Learning,](reference-azure-machine-learning-cli.md)è anche possibile usare un comando dell'interfaccia della riga di comando per assegnare ruoli agli utenti.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Il `user` campo è l'indirizzo di posta elettronica di un utente esistente nell'istanza di Azure Active Directory in cui si trova la sottoscrizione padre dell'area di lavoro. Di seguito è riportato un esempio di come utilizzare questo comando:Here is an example of how to use this command:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Creare un ruolo personalizzato

Se i ruoli predefiniti sono insufficienti, è possibile creare ruoli personalizzati. I ruoli personalizzati possono disporre di autorizzazioni per le risorse di lettura, scrittura, eliminazione e calcolo in tale area di lavoro. È possibile rendere il ruolo disponibile a un livello specifico dell'area di lavoro, a un livello di gruppo di risorse specifico o a un livello di sottoscrizione specifico.

> [!NOTE]
> Per creare ruoli personalizzati all'interno di tale risorsa, è necessario essere proprietari della risorsa a tale livello.

Per creare un ruolo personalizzato, creare innanzitutto un file JSON di definizione del ruolo che specifichi l'autorizzazione e l'ambito per il ruolo. Nell'esempio seguente viene definito un ruolo personalizzato denominato "Data Scientist" con ambito a un livello di area di lavoro specifico:The following example defines a custom role named "Data Scientist" scoped at a specific workspace level:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

È possibile `AssignableScopes` modificare il campo per impostare l'ambito di questo ruolo personalizzato a livello di sottoscrizione, a livello di gruppo di risorse o a un livello specifico dell'area di lavoro.

Questo ruolo personalizzato può eseguire tutte le operazioni nell'area di lavoro, ad eccezione delle azioni seguenti:This custom role can do everything in the workspace except for the following actions:

- Non è possibile creare o aggiornare una risorsa di calcolo.
- Non è possibile eliminare una risorsa di calcolo.
- Non può aggiungere, eliminare o modificare le assegnazioni di ruolo.
- Non può eliminare l'area di lavoro.

Per distribuire questo ruolo personalizzato, usare il comando dell'interfaccia della riga di comando di Azure seguente:To deploy this custom role, use the following Azure CLI command:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Dopo la distribuzione, questo ruolo diventa disponibile nell'area di lavoro specificata. A questo punto è possibile aggiungere e assegnare questo ruolo nel portale di Azure.Now you can add and assign this role in the Azure portal. In alternativa, è possibile assegnare questo `az ml workspace share` ruolo a un utente utilizzando il comando CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Per altre informazioni sui ruoli personalizzati, vedere Ruoli personalizzati per le risorse di Azure.For more information on custom roles, see [Custom roles for Azure resources.](/azure/role-based-access-control/custom-roles)

Per ulteriori informazioni sulle operazioni (azioni) utilizzabili con ruoli personalizzati, vedere [Operazioni del provider](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)di risorse .


## <a name="frequently-asked-questions"></a>Domande frequenti


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. Quali sono le autorizzazioni necessarie per eseguire varie azioni nel servizio Azure Machine Learning?

La tabella seguente è un riepilogo delle attività di Azure Machine Learning e le autorizzazioni necessarie per eseguirle nell'ambito minimo. Ad esempio, se un'attività può essere eseguita con un ambito dell'area di lavoro (colonna 4), anche tutti gli ambiti superiori con tale autorizzazione funzioneranno automaticamente. Tutti i percorsi in `Microsoft.MachineLearningServices/`questa tabella sono **percorsi relativi** a .

| Attività | Ambito a livello di sottoscrizioneSubscription-level scope | Ambito a livello di gruppo di risorse | Ambito a livello di area di lavoro |
|---|---|---|---|
| Creare una nuova area di lavoro | Facoltativo | Proprietario o collaboratore | N/D (diventa Proprietario o eredita il ruolo di ambito superiore dopo la creazione)N/D (becomes Owner or inherits higher scope role after creation) |
| Creare un nuovo cluster di calcoloCreate new compute cluster | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente:`workspaces/computes/write` |
| Creare una nuova macchina virtuale del blocco appuntiCreate new Notebook VM | Facoltativo | Proprietario o collaboratore | Non consentita |
| Creare una nuova istanza di calcoloCreate new compute instance | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente:`workspaces/computes/write` |
| Attività del piano dati, ad esempio l'invio di esecuzione, l'accesso ai dati, la distribuzione di modelli o pipeline di pubblicazioneData plane activity like submitting run, accessing data, deploying model or publishing pipeline | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente:`workspaces/*/write` <br/> Si noti che è necessario anche un archivio dati registrato nell'area di lavoro per consentire a MSI di accedere ai dati nell'account di archiviazione. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. Come si elencano tutti i ruoli personalizzati nella sottoscrizione?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. Come è possibile trovare la definizione di ruolo per un ruolo nella sottoscrizione?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente. Si `<role-name>` noti che deve essere nello stesso formato restituito dal comando precedente.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. Come si aggiorna una definizione di ruolo?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Si noti che è necessario disporre di autorizzazioni per l'intero ambito della nuova definizione di ruolo. Ad esempio, se questo nuovo ruolo ha un ambito in tre sottoscrizioni, è necessario disporre delle autorizzazioni per tutte e tre le sottoscrizioni. 

> [!NOTE]
> Gli aggiornamenti dei ruoli possono richiedere da 15 minuti a un'ora per essere applicati a tutte le assegnazioni di ruolo in tale ambito.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. È possibile definire un ruolo che impedisce l'aggiornamento dell'area di lavoro Edition? 

Sì, è possibile definire un ruolo che impedisce l'aggiornamento dell'area di lavoro Edition. Poiché l'aggiornamento dell'area di lavoro è una chiamata PATCH `"NotActions"` sull'oggetto dell'area di lavoro, eseguire questa operazione inserendo l'azione seguente nella matrice nella definizione JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. Quali autorizzazioni sono necessarie per eseguire le operazioni sulle quote in un'area di lavoro? 

Sono necessarie le autorizzazioni a livello di sottoscrizione per eseguire qualsiasi operazione correlata alla quota nell'area di lavoro. Ciò significa che l'impostazione della quota a livello di sottoscrizione o della quota a livello di area di lavoro per le risorse di calcolo gestite può avvenire solo se si dispone delle autorizzazioni di scrittura nell'ambito della sottoscrizione. 


## <a name="next-steps"></a>Passaggi successivi

- [Enterprise Security Package](concept-enterprise-security.md)
- [Esegui in modo sicuro esperimenti e inferenza/punteggio all'interno di una rete virtuale](how-to-enable-virtual-network.md)
- [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
- [Operazioni del provider di risorse](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
