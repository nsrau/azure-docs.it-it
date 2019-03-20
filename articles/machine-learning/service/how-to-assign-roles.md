---
title: Gestire i ruoli in un'area di lavoro di Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Informazioni su come accedere a un'area di lavoro del servizio Azure Machine Learning usando il controllo di accesso basato sui ruoli (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: b40edf705ba61713f4b695dd55a6a20028936c82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993809"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gestire l'accesso a un'area di lavoro di Azure Machine Learning

In questo articolo descrive come gestire l'accesso a un'area di lavoro di Azure Machine Learning. [Controllo di accesso basato sui ruoli (RBAC)](/azure/role-based-access-control/overview) viene usato per gestire l'accesso alle risorse di Azure. Gli utenti in Azure Active Directory vengono assegnati ruoli specifici, che concedono l'accesso alle risorse. Azure fornisce ruoli predefiniti e la possibilità di creare ruoli personalizzati.

## <a name="default-roles"></a>Ruoli predefiniti

Un'area di lavoro di Azure Machine Learning è una risorsa di Azure. Quali altre risorse di Azure quando viene creata una nuova area di Azure Machine Learning, include tre ruoli predefiniti. È possibile aggiungere utenti all'area di lavoro e assegnarli a uno di questi ruoli predefiniti.

| Ruolo | Livello di accesso |
| --- | --- |
| **Lettore** | Azioni di sola lettura nell'area di lavoro. I lettori possono elencare e visualizzare gli asset in un'area di lavoro, ma Impossibile creare o aggiornare questi asset. |
| **Collaboratore** | Visualizzare, creare, modificare o eliminare (dove applicabile) gli asset in un'area di lavoro. Ad esempio, i collaboratori possono creare un esperimento creare o collegare un cluster di calcolo, invia un'esecuzione e distribuire un servizio web. |
| **Proprietario** | Accesso all'area di lavoro, inclusa la possibilità di visualizzare, creare, modificare o eliminare (dove applicabile) completo gli asset in un'area di lavoro. Inoltre, è possibile modificare le assegnazioni di ruolo. |

> [!IMPORTANT]
> Accesso del ruolo può essere limitato a più livelli in Azure. Ad esempio, un utente con accesso come proprietario a un gruppo di lavoro potrebbe non avere accesso come proprietario al gruppo di risorse che contiene il gruppo di lavoro. Per altre informazioni, vedere [funziona come RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Per altre informazioni sui ruoli predefiniti specifici, vedere [ruoli predefiniti per Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gestire l'accesso dell'area di lavoro

Se si ha un proprietario di un'area di lavoro, è possibile aggiungere e rimuovere i ruoli per l'area di lavoro. È anche possibile assegnare ruoli agli utenti. Usare i collegamenti seguenti per informazioni su come gestire l'accesso:
- [Portale di Azure dell'interfaccia utente](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfaccia della riga di comando di Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelli di Gestione risorse di Azure](/azure/role-based-access-control/role-assignments-template)

Se è stato installato il [della riga di comando di Azure Machine Learning](reference-azure-machine-learning-cli.md), è anche possibile usare una riga di comando per assegnare ruoli agli utenti.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Il `user` campo è l'indirizzo di posta elettronica di un utente esistente nell'istanza di Azure Active Directory in cui si trova nella sottoscrizione padre dell'area di lavoro. Di seguito è riportato un esempio di come usare questo comando:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Crea ruolo personalizzato

Se i ruoli predefiniti non sono sufficienti, è possibile creare ruoli personalizzati. Ruoli personalizzati è necessario leggere, scrivere, eliminare e le autorizzazioni delle risorse nell'area di lavoro di calcolo. È possibile rendere disponibile il ruolo a un livello specifico dell'area di lavoro, il livello di un gruppo di risorse specifico o un livello di abbonamento specifico.

> [!NOTE]
> È necessario essere un proprietario della risorsa a tale livello per creare ruoli personalizzati all'interno di tale risorsa.

Per creare un ruolo personalizzato, creare innanzitutto un file JSON di definizione di ruolo che specifica l'autorizzazione e l'ambito per il ruolo. L'esempio seguente definisce un ruolo personalizzato denominato "Data Scientist" con ambito a un livello di area di lavoro specifici:

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

È possibile modificare il `AssignableScopes` campo per impostare l'ambito di questo ruolo personalizzato a livello di sottoscrizione, il livello di gruppo di risorse o un livello di area di lavoro specifica.

Questo ruolo personalizzato è possibile eseguire tutti gli elementi nell'area di lavoro eccetto le azioni seguenti:

- Impossibile creare o aggiornare una risorsa di calcolo.
- Non è possibile eliminare una risorsa di calcolo.
- È possibile aggiungere, eliminare o modificare le assegnazioni di ruolo.
- Non è possibile eliminare l'area di lavoro.

Per distribuire questo ruolo personalizzato, usare il comando di Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Dopo la distribuzione, questo ruolo diventa disponibile nell'area di lavoro specificato. A questo punto è possibile aggiungere e assegnare questo ruolo nel portale di Azure. In alternativa, è possibile assegnare questo ruolo a un utente usando il `az ml workspace share` riga di comando:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Per altre informazioni, vedere [ruoli personalizzati per le risorse di Azure](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della sicurezza aziendale](concept-enterprise-security.md)
- [In modo sicuro eseguire esperimenti e l'inferenza all'interno di una rete virtuale](how-to-enable-virtual-network.md)
- [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
