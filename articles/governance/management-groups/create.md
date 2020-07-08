---
title: Creare gruppi di gestione per organizzare le risorse-governance di Azure
description: Informazioni su come creare gruppi di gestione di Azure per gestire più risorse tramite il portale, Azure PowerShell e l'interfaccia della riga di comando di Azure.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 6fe7f2ea74c697701084321e068042b36acbdb11
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85971136"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Creare gruppi di gestione per la gestione e l'organizzazione delle risorse

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per ulteriori informazioni, vedere [configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Creare un gruppo di gestione

Qualsiasi utente Azure AD nel tenant può creare un gruppo di gestione senza l'autorizzazione di scrittura del gruppo di gestione assegnato a tale utente. Questo nuovo gruppo di gestione sarà figlio del gruppo di gestione radice e al Creatore verrà assegnata un'assegnazione di ruolo "proprietario". Il servizio del gruppo di gestione consente questa possibilità, in modo che le assegnazioni di ruolo non siano necessarie a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare di trovare gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è possibile creare i gruppi di gestione iniziali alla radice  
livello.

È possibile creare il gruppo di gestione usando il portale, un [modello di Azure Resource Manager](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group), PowerShell o l'interfaccia della riga di comando di Azure.

### <a name="create-in-portal"></a>Creazione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **tutti i servizi**  >  **gestione e governance**.

1. Selezionare **gruppi di gestione**.

1. Selezionare **+ Aggiungi gruppo di gestione**.

   :::image type="content" source="./media/main.png" alt-text="Pagina per l'utilizzo di gruppi di gestione" border="false":::

1. Compilare il campo ID del gruppo di gestione.

   - L'**ID del gruppo di gestione** è l'identificatore univoco della directory usato per inviare i comandi per questo gruppo di gestione. Questo identificatore non è modificabile dopo la creazione, perché è usato all'interno dell'intero sistema Azure per identificare il gruppo. Il [gruppo di gestione radice](overview.md#root-management-group-for-each-directory) viene creato automaticamente con un ID che corrisponde all'ID Azure Active Directory. Per tutti gli altri gruppi di gestione, assegnare un ID univoco.
   - Il nome visualizzato è il nome che viene visualizzato nel portale di Azure. Un nome visualizzato separato è un campo facoltativo durante la creazione del gruppo di gestione e può essere modificato in qualsiasi  
     al tempo.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Riquadro delle opzioni per la creazione di un nuovo gruppo di gestione" border="false":::

1. Selezionare **Salva**.

### <a name="create-in-powershell"></a>Creazione in PowerShell

Per PowerShell, usare il cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) per creare un nuovo gruppo di gestione.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** è un identificatore univoco creato. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere modificato in un secondo momento.

Se si desidera che il gruppo di gestione visualizzi un nome diverso all'interno del portale di Azure, aggiungere il parametro **DisplayName** . Ad esempio, per creare un gruppo di gestione con GroupName di Contoso e il nome visualizzato "contoso Group", usare il cmdlet seguente:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Negli esempi precedenti, il nuovo gruppo di gestione viene creato nel gruppo di gestione radice. Per specificare un gruppo di gestione diverso come padre, usare il parametro **parentID** .

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Creazione nell'interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure, usare il comando [AZ Account Management-Group create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) per creare un nuovo gruppo di gestione.

```azurecli-interactive
az account management-group create --name Contoso
```

Il **nome** è un identificatore univoco da creare. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere modificato in un secondo momento.

Se si desidera che il gruppo di gestione visualizzi un nome diverso all'interno del portale di Azure, aggiungere il parametro **Display-Name** . Ad esempio, per creare un gruppo di gestione con GroupName di Contoso e il nome visualizzato "contoso Group", utilizzare il comando seguente:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Negli esempi precedenti, il nuovo gruppo di gestione viene creato nel gruppo di gestione radice. Per specificare un gruppo di gestione diverso come padre, usare il parametro **Parent** e specificare il nome del gruppo padre.

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
