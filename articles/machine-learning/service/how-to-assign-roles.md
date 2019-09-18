---
title: Gestire i ruoli in un'area di lavoro Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come accedere a un'area di lavoro di Azure Machine Learning usando il controllo degli accessi in base al ruolo (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 22ce9ea44dde6da4d1194463fe266ed00c5a3f96
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067714"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gestire l'accesso a un'area di lavoro Azure Machine Learning

Questo articolo illustra come gestire l'accesso a un'area di lavoro Azure Machine Learning. Il [controllo degli accessi in base al ruolo (RBAC)](/azure/role-based-access-control/overview) viene usato per gestire l'accesso alle risorse di Azure. Agli utenti nella Azure Active Directory vengono assegnati ruoli specifici che consentono di accedere alle risorse. Azure fornisce ruoli predefiniti e la possibilità di creare ruoli personalizzati.

## <a name="default-roles"></a>Ruoli predefiniti

Un'area di lavoro Azure Machine Learning è una risorsa di Azure. Analogamente ad altre risorse di Azure, quando viene creata una nuova area di lavoro Azure Machine Learning, viene fornita con tre ruoli predefiniti. È possibile aggiungere utenti all'area di lavoro e assegnarli a uno di questi ruoli predefiniti.

| Role | Livello di accesso |
| --- | --- |
| **Lettore** | Azioni di sola lettura nell'area di lavoro. I lettori possono elencare e visualizzare gli asset in un'area di lavoro, ma non possono creare o aggiornare tali asset. |
| **Collaboratore** | Consente di visualizzare, creare, modificare o eliminare risorse, ove applicabile, in un'area di lavoro. I collaboratori possono ad esempio creare un esperimento, creare o alleghire un cluster di calcolo, inviare un'esecuzione e distribuire un servizio Web. |
| **Proprietario** | Accesso completo all'area di lavoro, inclusa la possibilità di visualizzare, creare, modificare o eliminare le risorse (ove applicabile) in un'area di lavoro. Inoltre, è possibile modificare le assegnazioni di ruolo. |

> [!IMPORTANT]
> L'accesso ai ruoli può essere limitato a più livelli in Azure. Ad esempio, un utente con accesso proprietario a un'area di lavoro potrebbe non avere accesso proprietario al gruppo di risorse che contiene l'area di lavoro. Per ulteriori informazioni [, vedere funzionamento](/azure/role-based-access-control/overview#how-rbac-works)del controllo degli accessi in base al ruolo.

Per altre informazioni sui ruoli predefiniti specifici, vedere [ruoli predefiniti per Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gestisci l'accesso all'area di lavoro

Se si è proprietari di un'area di lavoro, è possibile aggiungere e rimuovere ruoli per l'area di lavoro. È anche possibile assegnare ruoli agli utenti. Usare i collegamenti seguenti per scoprire come gestire l'accesso:
- [Interfaccia utente di portale di Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfaccia della riga di comando di Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelli di Gestione risorse di Azure](/azure/role-based-access-control/role-assignments-template)

Se è stata installata l' [interfaccia](reference-azure-machine-learning-cli.md)della riga di comando di Azure Machine Learning, è anche possibile usare un comando dell'interfaccia della riga di comando per assegnare ruoli agli utenti.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Il `user` campo è l'indirizzo di posta elettronica di un utente esistente nell'istanza di Azure Active Directory in cui risiede la sottoscrizione padre dell'area di lavoro. Di seguito è riportato un esempio di come usare questo comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Crea ruolo personalizzato

Se i ruoli predefiniti non sono sufficienti, è possibile creare ruoli personalizzati. I ruoli personalizzati potrebbero avere autorizzazioni di risorse di lettura, scrittura, eliminazione e calcolo in tale area di lavoro. È possibile rendere il ruolo disponibile a livello di area di lavoro specifico, a livello di gruppo di risorse specifico o a un livello di sottoscrizione specifico.

> [!NOTE]
> Per creare ruoli personalizzati all'interno di tale risorsa, è necessario essere un proprietario della risorsa a tale livello.

Per creare un ruolo personalizzato, creare prima di tutto un file JSON di definizione del ruolo che specifichi l'autorizzazione e l'ambito per il ruolo. Nell'esempio seguente viene definito un ruolo personalizzato denominato "Data Scientist" con ambito a un livello specifico dell'area di lavoro:

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

È possibile modificare il `AssignableScopes` campo per impostare l'ambito di questo ruolo personalizzato a livello di sottoscrizione, a livello di gruppo di risorse o a livello di area di lavoro specifico.

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

Per altre informazioni sui ruoli personalizzati, vedere [ruoli personalizzati per le risorse di Azure](/azure/role-based-access-control/custom-roles).

Per ulteriori informazioni sulle operazioni (azioni) utilizzabili con i ruoli personalizzati, vedere [operazioni del provider di risorse](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della sicurezza aziendale](concept-enterprise-security.md)
- [Eseguire in modo sicuro gli esperimenti e l'inferenza e il Punteggio all'interno di una rete virtuale](how-to-enable-virtual-network.md)
- [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
- [Operazioni del provider di risorse](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)