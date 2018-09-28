---
title: Creare gruppi di gestione per organizzare le risorse di Azure
description: Informazioni su come creare gruppi di gestione di Azure per gestire più risorse.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2018
ms.author: rithorn
ms.openlocfilehash: c2d4317bcbf70a0cebf6ab1915968eeb9ef8b4c6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992627"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Creare gruppi di gestione per la gestione e l'organizzazione delle risorse

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica.

## <a name="create-a-management-group"></a>Creare un gruppo di gestione

È possibile creare il gruppo di gestione tramite il portale, PowerShell o l'interfaccia della riga di comando di Azure. Attualmente non è possibile usare i modelli di Resource Manager per creare gruppi di gestione.

### <a name="create-in-portal"></a>Creazione nel portale

1. Accedere al [portale di Azure](http://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gruppi di gestione**.

1. Nella pagina principale selezionare **Nuovo Gruppo di gestione.**

   ![Gruppo principale](./media/main.png)

1. Compilare il campo ID del gruppo di gestione.

   - L'**ID del gruppo di gestione** è l'identificatore univoco della directory usato per inviare i comandi per questo gruppo di gestione. Questo identificatore non è modificabile dopo la creazione, perché è usato all'interno dell'intero sistema Azure per identificare il gruppo.
   - Il nome visualizzato è il nome che viene visualizzato nel portale di Azure. Un nome visualizzato separato è un campo facoltativo al momento della creazione del gruppo di gestione e può essere modificato in qualsiasi momento.  

   ![Create](./media/create_context_menu.png)  

1. Selezionare **Salva**.

### <a name="create-in-powershell"></a>Creazione in PowerShell

All'interno di PowerShell è necessario usare i cmdlet Add-AzureRmManagementGroups:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso'
```

**GroupName** è un identificatore univoco creato. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere modificato in seguito.

Se si vuole visualizzare un nome diverso per il gruppo di gestione all'interno del portale di Azure, è necessario aggiungere il parametro **DisplayName** alla stringa. Se ad esempio si vuole creare un gruppo di gestione con GroupName Contoso e nome visualizzato "Contoso Group", usare il cmdlet seguente:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

Usare il parametro **ParentId** per creare questo gruppo di gestione in una gestione diversa.

### <a name="create-in-azure-cli"></a>Creazione nell'interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure usare il comando az account management-group create.

```azurecli-interactive
az account management-group create --group-name 'Contoso'
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di gestione, vedere:

- [Organizzare le risorse con i gruppi di gestione di Azure ](overview.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](manage.md)
- [Installare il modulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Esaminare la specifica di dell'API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installare l'estensione dell'interfaccia della riga di comando di Azure](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)