---
title: Come modificare, eliminare o gestire i gruppi di gestione - Azure | Microsoft Docs
description: Informazioni su come gestire e aggiornare la gerarchia dei gruppi di gestione.
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
ms.openlocfilehash: 975f572d9bd0f32825e6a618cd31bbc263885030
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="manage-your-resources-with-management-groups"></a>Gestire le risorse con i gruppi di gestione 
I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. È possibile modificare, eliminare e gestire questi contenitori per creare gerarchie utilizzabili con [Criteri di Azure](../azure-policy/azure-policy-introduction.md) e [Controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-what-is.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure ](management-groups-overview.md).

La funzionalità dei gruppi di gestione è disponibile come anteprima pubblica. Per iniziare a usare i gruppi di gestione, accedere al [portale di Azure](https://portal.azure.com). In alternativa, è possibile usare [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) o l'[API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) per gestire i gruppi di gestione.

Per apportare modifiche a un gruppo di gestione, è necessario disporre del ruolo Proprietario o Collaboratore per il gruppo di gestione. Per controllare le proprie autorizzazioni, selezionare il gruppo di gestione e quindi selezionare **IAM**. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Gestire accessi e autorizzazioni con il controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md).

## <a name="change-the-name-of-a-management-group"></a>Modificare il nome di un gruppo di gestione 
È possibile modificare il nome del gruppo di gestione tramite il portale, PowerShell o l'interfaccia della riga di comando di Azure.

### <a name="change-the-name-in-the-portal"></a>Modificare il nome nel portale

1. Accedere al [portale di Azure](https://portal.azure.com)
2. Selezionare **Tutti i servizi** > **Gruppi di gestione**  
3. Selezionare il gruppo di gestione da rinominare. 
4. Selezionare l'opzione **Rinomina gruppo** nella parte superiore della pagina. 

    ![Rinomina gruppo](media/management-groups/detail_action_small.png)
5. All'apertura del menu, immettere il nuovo nome che si vuole visualizzare.

    ![Rinomina gruppo](media/management-groups/rename_context.png) 
4. Selezionare **Salva**. 

### <a name="change-the-name-in-powershell"></a>Modificare il nome in PowerShell

Per aggiornare il nome visualizzato, usare use **Update-AzureRmManagementGroup**. Ad esempio, per modificare il nome di un gruppo di gestione da "Contoso" a "Gruppo Contoso" si esegue il comando seguente: 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Modificare il nome nell'interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure usare il comando di aggiornamento. 

```azure-cli
C:\> az account management-group update --group-name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>Eliminare un gruppo di gestione
Per eliminare un gruppo di gestione è necessario che siano soddisfatti i requisiti seguenti:
1. Nel gruppo di gestione non esistono gruppi di gestione o sottoscrizioni figlio. 
    - Per spostare una sottoscrizione da un gruppo di gestione, vedere [Spostare sottoscrizioni in un altro gruppo di gestione](#Move-subscriptions-in-the-hierarchy). 
    - Per spostare un gruppo di gestione in un altro gruppo di gestione, vedere [Spostare gruppi di gestione nella gerarchia](#Move-management-groups-in-the-hierarchy). 
2. Si dispone di autorizzazioni di scrittura e del ruolo Proprietario o Collaboratore per il gruppo di gestione. Per controllare le proprie autorizzazioni, selezionare il gruppo di gestione e quindi selezionare **IAM**. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Gestire accessi e autorizzazioni con il controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md).  

### <a name="delete-in-the-portal"></a>Eseguire l'eliminazione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com)
2. Selezionare **Tutti i servizi** > **Gruppi di gestione**  
3. Selezionare il gruppo di gestione da eliminare. 
    
    ![Elimina gruppo](media/management-groups/delete.png)
4. Selezionare **Elimina**. 
    - Se l'icona è disabilitata, posizionando il cursore del mouse sull'icona viene visualizzato il motivo. 
5. Si aprirà una finestra con la richiesta di confermare l'eliminazione del gruppo di gestione. 

    ![Elimina gruppo](media/management-groups/delete_confirm.png) 
6. Selezionare **Sì** 


### <a name="delete-in-powershell"></a>Eseguire l'eliminazione in PowerShell

Usare il comando **Remove-AzureRmManagementGroup** in PowerShell per eliminare gruppi di gestione. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Eseguire l'eliminazione nell'interfaccia della riga di comando di Azure
Nell'interfaccia della riga di comando di Azure usare il comando az account management-group delete. 

```azure-cli
C:\> az account management-group delete --group-name Contoso
```
---

## <a name="view-management-groups"></a>Visualizzare i gruppi di gestione
È possibile visualizzare tutti i gruppi di gestione su cui si ha un ruolo Controllo degli accessi in base al ruolo, diretto o ereditato.  

### <a name="view-in-the-portal"></a>Visualizzare nel portale
1. Accedere al [portale di Azure](https://portal.azure.com)
2. Selezionare **Tutti i servizi** > **Gruppi di gestione** 
3. Si apre la pagina della gerarchia dei gruppi di gestione, che mostra tutti i gruppi a cui si ha accesso. 
    ![Principale](media/management-groups/main.png)
4. Selezionare un gruppo di gestione per visualizzare i dettagli  

### <a name="view-in-powershell"></a>Visualizzare in PowerShell
Per recuperare tutti i gruppi, usare il comando Get-AzureRmManagementGroup.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
Per ottenere le informazioni su un singolo gruppo di gestione, usare il parametro - GroupName

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Visualizzare nell'interfaccia della riga di comando di Azure
Per recuperare tutti i gruppi, usare il comando list.  

```azure-cli
az account management-group list
```
Per ottenere le informazioni su un singolo gruppo di gestione, usare il comando show

```azurepowershell-interactive
az account management-group show --group-name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>Spostare sottoscrizioni nella gerarchia
Uno dei motivi per creare un gruppo di gestione è l'accorpamento delle sottoscrizioni. Solo i gruppi di gestione e le sottoscrizioni possono essere resi elementi figlio di un altro gruppo di gestione. Una sottoscrizione che viene spostata in un gruppo di gestione eredita tutti i criteri e le autorizzazioni di accesso utente dal gruppo di gestione padre. 

Per spostare una sottoscrizione sono necessarie due autorizzazioni: 
- Ruolo "Proprietario" sulla sottoscrizione figlio.
- Ruolo "Proprietario" o "Collaboratore" sul nuovo gruppo di gestione padre. 
- Ruolo "Proprietario" o "Collaboratore" sul vecchio gruppo di gestione padre.
Per controllare le proprie autorizzazioni, selezionare il gruppo di gestione e quindi selezionare **IAM**. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Gestire accessi e autorizzazioni con il controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-what-is.md). 

### <a name="move-subscriptions-in-the-portal"></a>Spostare sottoscrizioni nel portale

**Aggiungere una sottoscrizione esistente a un gruppo di gestione**
1. Accedere al [portale di Azure](https://portal.azure.com)
2. Selezionare **Tutti i servizi** > **Gruppi di gestione** 
3. Selezionare il gruppo di gestione che dovrà fungere da elemento padre.      
5. Nella parte superiore della pagina selezionare **Aggiungi esistente**. 
6. Nel menu visualizzato selezionare il **tipo di risorsa** dell'elemento che si vuole spostare, ovvero **Sottoscrizione**.
7. Selezionare nell'elenco la sottoscrizione con l'ID corretto. 

    ![Elemento figlio](media/management-groups/add_context_2.png)
8. Selezionare "Salva"

**Rimuovere una sottoscrizione da un gruppo di gestione**
1. Accedere al [portale di Azure](https://portal.azure.com)
2. Selezionare **Tutti i servizi** > **Gruppi di gestione** 
3. Selezionare il gruppo di gestione che attualmente funge da elemento padre.  
4. Selezionare i puntini di sospensione in fondo alla riga della sottoscrizione che si vuole spostare.

    ![Spostamento](media/management-groups/move_small.png)
5. Selezionare **Sposta**
6. Nel menu visualizzato selezionare il **Gruppo di gestione padre**.

    ![Spostamento](media/management-groups/move_small_context.png)
7. Selezionare **Salva**

### <a name="move-subscriptions-in-powershell"></a>Spostare sottoscrizioni in PowerShell
Per spostare una sottoscrizione in PowerShell, usare il comando Add-AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
Add-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

Per rimuovere il collegamento tra la sottoscrizione e il gruppo di gestione, usare il comando Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Spostare sottoscrizioni nell'interfaccia della riga di comando di Azure
Per spostare una sottoscrizione nell'interfaccia della riga di comando, usare il comando add. 

```azure-cli
C:\> az account management-group add --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

Per rimuovere la sottoscrizione dal gruppo di gestione, usare il comando remove.  

```azure-cli
C:\> az account management-group remove --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>Spostare gruppi di gestione nella gerarchia  
Quando si sposta un gruppo di gestione padre, tutte le risorse figlio, che includono gruppi di gestione, sottoscrizioni, gruppi di risorse e risorse, vengono spostate con l'elemento padre.   

### <a name="move-management-groups-in-the-portal"></a>Spostare gruppi di gestione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com)
2. Selezionare **Tutti i servizi** > **Gruppi di gestione** 
3. Selezionare il gruppo di gestione che dovrà fungere da elemento padre.      
5. Nella parte superiore della pagina selezionare **Aggiungi esistente**.
6. Nel menu visualizzato selezionare il **tipo di risorsa** dell'elemento che si vuole spostare, ovvero **Gruppo di gestione**.
7. Selezionare il gruppo di gestione con il nome e l'ID corretto.

    ![spostamento](media/management-groups/add_context.png)
8. Selezionare **Salva**

### <a name="move-management-groups-in-powershell"></a>Spostare gruppi di gestione in PowerShell
Usare il comando Update-AzureRmManagementGroup in PowerShell per spostare un gruppo di gestione in un gruppo diverso.  

```powershell
C:\> Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>Spostare gruppi di gestione nell'interfaccia della riga di comando di Azure
Usare il comando update per spostare un gruppo di gestione con l'interfaccia della riga di comando di Azure. 

```azure-cli
C:/> az account management-group udpate --group-name Contoso --parent-id "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni sui gruppi di gestione, vedere: 
- [Organizzare le risorse con i gruppi di gestione di Azure ](management-groups-overview.md)
- [Creare gruppi di gestione per organizzare le risorse di Azure](management-groups-create.md)
- [Installare il modulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Esaminare la specifica di dell'API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installare l'estensione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)