---
title: Creare gruppi di gestione per organizzare le risorse - Governance di AzureCreate management groups to organize resources - Azure Governance
description: Informazioni su come creare gruppi di gestione di Azure per gestire più risorse tramite il portale, Azure PowerShell e l'interfaccia della riga di comando di Azure.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 34815089367512c4aa54f148c118a669625d0ea3
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381597"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Creare gruppi di gestione per la gestione e l'organizzazione delle risorse

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per ulteriori informazioni, vedere [configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Creare un gruppo di gestione

Qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza l'autorizzazione di scrittura del gruppo di gestione assegnata a tale utente. Questo nuovo gruppo di gestione sarà un elemento figlio del gruppo di gestione radice e al creatore verrà assegnata un'assegnazione di ruolo "Proprietario". Il servizio del gruppo di gestione consente questa capacità in modo che le assegnazioni di ruolo non siano necessarie a livello di radice. Nessun utente ha accesso al gruppo di gestione radice al momento della creazione. Per evitare l'ostacolo di trovare gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è possibile creare i gruppi di gestione iniziali nella radiceTo avoid the hurle hurthe hurthe from a finding the Azure AD Global Admins to start using management groups, we allow the creation of the initial management groups at the root  
Livello.

È possibile creare il gruppo di gestione usando il portale, un modello di Resource Manager, PowerShell o l'interfaccia della riga di comando di Azure.You can create the management group by using the portal, a [Resource Manager template,](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group)PowerShell, or Azure CLI.

### <a name="create-in-portal"></a>Creazione nel portale

1. Accedere al portale di [Azure](https://portal.azure.com).

1. Selezionare **Gestione tutti i servizi** > **e governance**.

1. Selezionare **Gestione costi e Fatturazione**

1. Nella pagina Gestione costi - Fatturazione - Gruppi di gestione selezionare **Gruppi di gestione**

1. Selezionare **: Aggiungi gruppo di gestione**.

   :::image type="content" source="./media/main.png" alt-text="Pagina per l'utilizzo dei gruppi di gestione" border="false":::

1. Compilare il campo ID del gruppo di gestione.

   - L'**ID del gruppo di gestione** è l'identificatore univoco della directory usato per inviare i comandi per questo gruppo di gestione. Questo identificatore non è modificabile dopo la creazione, perché è usato all'interno dell'intero sistema Azure per identificare il gruppo. Il gruppo di gestione radice viene creato automaticamente con un ID con ID Azure Active Directory.The [root management group](overview.md#root-management-group-for-each-directory) is automatically created with an ID that is the Azure Active Directory ID. Per tutti gli altri gruppi di gestione, assegnare un ID univoco.
   - Il nome visualizzato è il nome che viene visualizzato nel portale di Azure. Un nome visualizzato separato è un campo facoltativo durante la creazione del gruppo di gestione e può essere modificato in qualsiasi  
     al tempo.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Riquadro Opzioni per la creazione di un nuovo gruppo di gestione" border="false":::

1. Selezionare **Salva**.

### <a name="create-in-powershell"></a>Creazione in PowerShell

Per PowerShell, utilizzare il cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) per creare un nuovo gruppo di gestione.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** è un identificatore univoco creato. Questo ID viene utilizzato da altri comandi per fare riferimento a questo gruppo e non può essere modificato in un secondo momento.

Se si vuole che il gruppo di gestione mostri un nome diverso all'interno del portale di Azure, aggiungere il parametro **DisplayName.If** you want the management group to show a different name within the Azure portal, add the DisplayName parameter. Ad esempio, per creare un gruppo di gestione con GroupName di Contoso e il nome visualizzato "Contoso Group", utilizzare il seguente cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Negli esempi precedenti, il nuovo gruppo di gestione viene creato nel gruppo di gestione radice. Per specificare un gruppo di gestione diverso come padre, usare il parametro **ParentId.To** specify a different management group as the parent, use the ParentId parameter.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Creazione nell'interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, usare il comando [az account management-group create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) per creare un nuovo gruppo di gestione.

```azurecli-interactive
az account management-group create --name Contoso
```

Il **nome** è un identificatore univoco in fase di creazione. Questo ID viene utilizzato da altri comandi per fare riferimento a questo gruppo e non può essere modificato in un secondo momento.

Se si vuole che il gruppo di gestione mostri un nome diverso all'interno del portale di Azure, aggiungere il parametro **display-name.** Ad esempio, per creare un gruppo di gestione con GroupName di Contoso e il nome visualizzato "Contoso Group", utilizzare il comando seguente:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Negli esempi precedenti, il nuovo gruppo di gestione viene creato nel gruppo di gestione radice. Per specificare un gruppo di gestione diverso come padre, utilizzare il parametro **padre** e specificare il nome del gruppo padre.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di gestione, vedere:

- [Creare gruppi di gestione per organizzare le risorse di Azure](./create.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](./manage.md)
- [Gruppi di gestione nel modulo Resources di Azure PowerShell](/powershell/module/az.resources#resources)
- [Gruppi di gestione nell'API REST](/rest/api/resources/managementgroups)
- [Gruppi di gestione nell'interfaccia della riga di comando di Azure](/cli/azure/account/management-group)
