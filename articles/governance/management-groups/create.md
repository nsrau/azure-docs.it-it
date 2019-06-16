---
title: Creare gruppi di gestione per organizzare le risorse di Azure | Governance di Azure
description: Informazioni su come creare gruppi di gestione di Azure per gestire più risorse tramite il portale, Azure PowerShell e l'interfaccia della riga di comando di Azure.
author: rthorn17
manager: rithorn
ms.service: governance
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 9e0a864019c2940ba7b5188ea43e9bbae484178d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241989"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Creare gruppi di gestione per la gestione e l'organizzazione delle risorse

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>Creare un gruppo di gestione

È possibile creare il gruppo di gestione tramite il portale, PowerShell o l'interfaccia della riga di comando di Azure. Attualmente non è possibile usare i modelli di Resource Manager per creare gruppi di gestione.

### <a name="create-in-portal"></a>Creazione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gruppi di gestione**.

1. Nella pagina principale selezionare **Nuovo Gruppo di gestione.**

   ![Pagina per l'uso di gruppi di gestione](./media/main.png)

1. Compilare il campo ID del gruppo di gestione.

   - L'**ID del gruppo di gestione** è l'identificatore univoco della directory usato per inviare i comandi per questo gruppo di gestione. Questo identificatore non è modificabile dopo la creazione, perché è usato all'interno dell'intero sistema Azure per identificare il gruppo. Il [gruppo di gestione radice](index.md#root-management-group-for-each-directory) viene creato automaticamente con un ID che è l'ID di Azure Active Directory. Per tutti gli altri gruppi di gestione, assegnare un ID univoco.
   - Il nome visualizzato è il nome che viene visualizzato nel portale di Azure. Un nome visualizzato separato è un campo facoltativo al momento della creazione del gruppo di gestione e può essere modificato in qualsiasi momento.  

   ![Riquadro delle opzioni per la creazione di un nuovo gruppo di gestione](./media/create_context_menu.png)  

1. Selezionare **Salva**.

### <a name="create-in-powershell"></a>Creazione in PowerShell

Per PowerShell, usare il [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet per creare un nuovo gruppo di gestione.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** è un identificatore univoco creato. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere modificato in un secondo momento.

Se si desidera che il gruppo di gestione per mostrare un nome diverso nel portale di Azure, aggiungere il **DisplayName** parametro. Ad esempio, per creare un gruppo di gestione con GroupName Contoso e il nome visualizzato "Contoso Group", usare il cmdlet seguente:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Negli esempi precedenti, viene creato il nuovo gruppo di gestione nel gruppo di gestione radice. Per specificare un gruppo di gestione differente come elemento padre, usare il **ParentId** parametro.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Creazione nell'interfaccia della riga di comando di Azure

Riga di comando di Azure, usare il [az account management-group create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) comando per creare un nuovo gruppo di gestione.

```azurecli-interactive
az account management-group create --name Contoso
```

Il **nome** è un identificatore univoco viene creato. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere modificato in un secondo momento.

Se si desidera che il gruppo di gestione per mostrare un nome diverso nel portale di Azure, aggiungere il **display-name** parametro. Ad esempio, per creare un gruppo di gestione con GroupName Contoso e il nome visualizzato "Contoso Group", usare il comando seguente:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Negli esempi precedenti, viene creato il nuovo gruppo di gestione nel gruppo di gestione radice. Per specificare un gruppo di gestione differente come elemento padre, usare il **padre** parametro e specificare il nome del gruppo padre.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di gestione, vedere:

- [Creare gruppi di gestione per organizzare le risorse di Azure](create.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](manage.md)
- [Gruppi di gestione nel modulo Resources di Azure PowerShell](/powershell/module/az.resources#resources)
- [Gruppi di gestione nell'API REST](/rest/api/resources/managementgroups)
- [Gruppi di gestione nell'interfaccia della riga di comando di Azure](/cli/azure/account/management-group)