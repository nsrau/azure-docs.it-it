---
title: Creare gruppi di gestione per organizzare le risorse di Azure | Microsoft Docs
description: "Informazioni su come creare gruppi di gestione di Azure per gestire più risorse."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2018
ms.author: rithorn
ms.openlocfilehash: 7c5aeca5afe8921ab39040e9afc2921b1711c447
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Creare gruppi di gestione per la gestione e l'organizzazione delle risorse
I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../azure-policy/azure-policy-introduction.md) e [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-what-is.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](management-groups-overview.md). 

La funzionalità dei gruppi di gestione è disponibile come anteprima pubblica. Per iniziare a usare i gruppi di gestione, accedere al [portale di Azure](https://portal.azure.com). In alternativa, è possibile usare [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) o [l'API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) per creare gruppi di gestione.   

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica.  

## <a name="how-to-create-a-management-group"></a>Come creare un gruppo di gestione
È possibile creare il gruppo di gestione tramite il portale, PowerShell o l'interfaccia della riga di comando di Azure.

### <a name="create-in-portal"></a>Creazione nel portale

1. Accedere al [portale di Azure](http://portal.azure.com).
2. Selezionare **Tutti i servizi** > **Gruppi di gestione**.
3. Nella pagina principale selezionare **Nuovo Gruppo di gestione.** 

    ![Creare un gruppo](media/management-groups/create_main.png) 
4.  Compilare il campo ID del gruppo di gestione. 
    - L'**ID del gruppo di gestione** è l'identificatore univoco della directory usato per inviare i comandi per questo gruppo di gestione. Questo identificatore non è modificabile dopo la creazione, perché è usato all'interno dell'intero sistema Azure per identificare il gruppo. 
    - Il nome visualizzato è il nome che viene visualizzato nel portale di Azure. Un nome visualizzato separato è un campo facoltativo al momento della creazione del gruppo di gestione e può essere modificato in qualsiasi momento.  

    ![Create](media/management-groups/create_context_menu.png)  
5.  Selezionare **Salva**


### <a name="create-in-powershell"></a>Creazione in PowerShell
All'interno di PowerShell si usano i cmdlet Add-AzureRmManagementGroups.   

```azurepowershell-interactive
C:\> Add-AzureRmManagementGroup -GroupName Contoso 
```
**GroupName** è un identificatore univoco creato. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere modificato in seguito.

Se si vuole visualizzare un nome diverso per il gruppo di gestione all'interno del portale di Azure, è necessario aggiungere il parametro **DisplayName** alla stringa. Se ad esempio si vuole creare un gruppo di gestione con GroupName Contoso e nome visualizzato "Contoso Group", usare il cmdlet seguente: 

```azurepowershell-interactive
C:\> Add-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Usare il parametro **ParentId** per creare questo gruppo di gestione in una gestione diversa.  

### <a name="create-in-azure-cli"></a>Creazione nell'interfaccia della riga di comando di Azure
Nell'interfaccia della riga di comando di Azure usare il comando az account management-group create. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni sui gruppi di gestione, vedere: 
- [Organizzare le risorse con i gruppi di gestione di Azure ](management-groups-overview.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](management-groups-manage.md)
- [Installare il modulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Esaminare la specifica di dell'API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installare l'estensione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
