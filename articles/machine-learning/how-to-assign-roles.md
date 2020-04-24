---
title: Gestire i ruoli nell'area di lavoro
titleSuffix: Azure Machine Learning
description: Informazioni su come accedere a un'area di lavoro di Azure Machine Learning usando il controllo degli accessi in base al ruolo (RBAC).
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
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gestire l'accesso a un'area di lavoro Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come gestire l'accesso a un'area di lavoro Azure Machine Learning. Il [controllo degli accessi in base al ruolo (RBAC)](/azure/role-based-access-control/overview) viene usato per gestire l'accesso alle risorse di Azure. Agli utenti nella Azure Active Directory vengono assegnati ruoli specifici che consentono di accedere alle risorse. Azure fornisce ruoli predefiniti e la possibilità di creare ruoli personalizzati.

## <a name="default-roles"></a>Ruoli predefiniti

Un'area di lavoro Azure Machine Learning è una risorsa di Azure. Analogamente ad altre risorse di Azure, quando viene creata una nuova area di lavoro Azure Machine Learning, viene fornita con tre ruoli predefiniti. È possibile aggiungere utenti all'area di lavoro e assegnarli a uno di questi ruoli predefiniti.

| Ruolo | Livello di accesso |
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
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Modelli di Gestione risorse di Azure](/azure/role-based-access-control/role-assignments-template)

Se è stata installata l' [interfaccia](reference-azure-machine-learning-cli.md)della riga di comando di Azure Machine Learning, è anche possibile usare un comando dell'interfaccia della riga di comando per assegnare ruoli agli utenti.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Il `user` campo è l'indirizzo di posta elettronica di un utente esistente nell'istanza di Azure Active Directory in cui risiede la sottoscrizione padre dell'area di lavoro. Di seguito è riportato un esempio di come usare questo comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Creare un ruolo personalizzato

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


## <a name="frequently-asked-questions"></a>Domande frequenti


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. Quali sono le autorizzazioni necessarie per eseguire varie azioni nel servizio Azure Machine Learning?

La tabella seguente è un riepilogo delle attività Azure Machine Learning e delle autorizzazioni necessarie per eseguirle nel minor ambito. Se, ad esempio, un'attività può essere eseguita con un ambito dell'area di lavoro (colonna 4), anche tutti gli ambiti più elevati con tale autorizzazione funzioneranno automaticamente. Tutti i percorsi in questa tabella sono **percorsi relativi** a `Microsoft.MachineLearningServices/`.

| Attività | Ambito a livello di sottoscrizione | Ambito a livello di gruppo di risorse | Ambito a livello di area di lavoro |
|---|---|---|---|
| Creare una nuova area di lavoro | Facoltativo | Proprietario o collaboratore | N/d (diventa proprietario o eredita un ruolo con ambito superiore dopo la creazione) |
| Crea nuovo cluster di calcolo | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente:`workspaces/computes/write` |
| Creare una nuova macchina virtuale notebook | Facoltativo | Proprietario o collaboratore | Non consentita |
| Crea nuova istanza di calcolo | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente:`workspaces/computes/write` |
| Attività del piano dati come l'invio dell'esecuzione, l'accesso ai dati, la distribuzione del modello o della pipeline di pubblicazione | Facoltativo | Facoltativo | Proprietario, collaboratore o ruolo personalizzato che consente:`workspaces/*/write` <br/> Si noti che è necessario anche un archivio dati registrato nell'area di lavoro per consentire l'accesso di MSI ai dati nell'account di archiviazione. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. Ricerca per categorie elencare tutti i ruoli personalizzati nella sottoscrizione?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. Ricerca per categorie trovare la definizione di ruolo per un ruolo nella sottoscrizione?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente. Si noti `<role-name>` che deve essere nello stesso formato restituito dal comando precedente.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. Ricerca per categorie aggiornare una definizione di ruolo?

Nell'interfaccia della riga di comando di Azure eseguire il comando seguente.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Si noti che è necessario disporre delle autorizzazioni per l'intero ambito della nuova definizione di ruolo. Se, ad esempio, il nuovo ruolo dispone di un ambito in tre sottoscrizioni, è necessario disporre delle autorizzazioni per tutte e tre le sottoscrizioni. 

> [!NOTE]
> Gli aggiornamenti dei ruoli possono richiedere da 15 minuti a un'ora per essere applicati a tutte le assegnazioni di ruolo in tale ambito.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. È possibile definire un ruolo che impedisce l'aggiornamento dell'edizione dell'area di lavoro? 

Sì, è possibile definire un ruolo che impedisce l'aggiornamento dell'edizione dell'area di lavoro. Poiché l'aggiornamento dell'area di lavoro è una chiamata PATCH nell'oggetto dell'area di lavoro, è possibile eseguire questa operazione `"NotActions"` inserendo l'azione seguente nella matrice nella definizione JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. Quali autorizzazioni sono necessarie per eseguire operazioni di quota in un'area di lavoro? 

È necessario disporre delle autorizzazioni a livello di sottoscrizione per eseguire qualsiasi operazione relativa alla quota nell'area di lavoro. Ciò significa che l'impostazione della quota a livello di sottoscrizione o della quota a livello di area di lavoro per le risorse di calcolo gestite può verificarsi solo se si dispone di autorizzazioni di scrittura nell'ambito della sottoscrizione. 


## <a name="next-steps"></a>Passaggi successivi

- [Enterprise Security Package](concept-enterprise-security.md)
- [Eseguire in modo sicuro gli esperimenti e l'inferenza e il Punteggio all'interno di una rete virtuale](how-to-enable-virtual-network.md)
- [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
- [Operazioni del provider di risorse](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
