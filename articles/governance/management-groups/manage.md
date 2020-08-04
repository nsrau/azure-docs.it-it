---
title: Come lavorare con i gruppi di gestione - Governance di Azure
description: Informazioni su come visualizzare, gestire, aggiornare ed eliminare la gerarchia dei gruppi di gestione.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: c5a0269935daedb3be478cc27d5ecaf87f3c97f7
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535009"
---
# <a name="manage-your-resources-with-management-groups"></a>Gestire le risorse con i gruppi di gestione

Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni. Le sottoscrizioni sono organizzate in contenitori chiamati "gruppi di gestione" a cui vengono applicate le condizioni di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione.

I gruppi di gestione offrono gestione di livello aziendale su larga scala, indipendentemente dal tipo di sottoscrizioni che si posseggono. Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](./overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> I token utente di Azure Resource Manager e la cache del gruppo di gestione durano 30 minuti prima essere aggiornati in modo forzato. Dopo aver eseguito un'azione come lo spostamento di un gruppo di gestione o una sottoscrizione, possono essere necessari fino a 30 minuti per la visualizzazione. Per visualizzare gli aggiornamenti prima che sia necessario aggiornare il token aggiornando il browser, eseguendo l'accesso e la disconnessione o richiedendo un nuovo token.  

## <a name="change-the-name-of-a-management-group"></a>Modificare il nome di un gruppo di gestione

È possibile modificare il nome del gruppo di gestione tramite il portale, PowerShell o l'interfaccia della riga di comando di Azure.

### <a name="change-the-name-in-the-portal"></a>Modificare il nome nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gruppi di gestione**.

1. Selezionare il gruppo di gestione da rinominare.

1. Selezionare **Dettagli**.

1. Selezionare l'opzione **Rinomina gruppo** nella parte superiore della pagina.

   :::image type="content" source="./media/detail_action_small.png" alt-text="Opzione Rinomina gruppo nella pagina Gruppo di gestione" border="false":::

1. All'apertura del menu, immettere il nuovo nome che si vuole visualizzare.

   :::image type="content" source="./media/rename_context.png" alt-text="Riquadro Rinomina gruppo per rinominare il gruppo di gestione" border="false":::

1. Selezionare **Salva**.

### <a name="change-the-name-in-powershell"></a>Modificare il nome in PowerShell

Per aggiornare il nome visualizzato, usare use **Update-AzManagementGroup**. Ad esempio, per modificare il nome di visualizzazione di un gruppo di gestione da "Contoso" a "Gruppo Contoso" si esegue il comando seguente:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Modificare il nome nell'interfaccia della riga di comando di Azure

Per l'interfaccia della riga di comando di Azure usare il comando di aggiornamento.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Eliminare un gruppo di gestione

Per eliminare un gruppo di gestione è necessario che siano soddisfatti i requisiti seguenti:

1. Nel gruppo di gestione non esistono gruppi di gestione o sottoscrizioni figlio.

   - Per spostare una sottoscrizione o un gruppo di gestione in un altro gruppo di gestione, vedere [Spostare gruppi di gestione e sottoscrizioni nella gerarchia](#moving-management-groups-and-subscriptions).

1. Sono necessarie autorizzazioni di scrittura per il gruppo di gestione ("Proprietario" o "Collaboratore" o "Collaboratore del gruppo di gestione"). Per controllare le proprie autorizzazioni, selezionare il gruppo di gestione e quindi selezionare **IAM**. Per ulteriori informazioni sui ruoli di Azure, vedere  
   [Gestire accessi e autorizzazioni con il controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md).

### <a name="delete-in-the-portal"></a>Eseguire l'eliminazione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gruppi di gestione**.

1. Selezionare il gruppo di gestione da eliminare.

1. Selezionare **Dettagli**.

1. Selezionare **Elimina**

   :::image type="content" source="./media/delete.png" alt-text="Opzione Elimina gruppo" border="false":::

   > [!TIP]
   > Se l'icona è disabilitata, posizionando il cursore del mouse sull'icona viene visualizzato il motivo.

1. Si aprirà una finestra con la richiesta di confermare l'eliminazione del gruppo di gestione.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Finestra di dialogo di conferma dell'eliminazione del gruppo" border="false":::

1. Selezionare **Sì**.

### <a name="delete-in-powershell"></a>Eseguire l'eliminazione in PowerShell

Usare il comando **Remove-AzManagementGroup** in PowerShell per eliminare gruppi di gestione.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Eseguire l'eliminazione nell'interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure usare il comando az account management-group delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Visualizzare i gruppi di gestione

È possibile visualizzare qualsiasi gruppo di gestione di cui si ha un ruolo di Azure diretto o ereditato.  

### <a name="view-in-the-portal"></a>Visualizzare nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gruppi di gestione**.

1. Viene caricata la pagina della gerarchia del gruppo di gestione. Questa è la pagina in cui è possibile esplorare tutti i gruppi di gestione e le sottoscrizioni a cui si ha accesso. Quando si seleziona il nome del gruppo si scende di un livello nella gerarchia. Le funzioni di spostamento sono le stesse di Esplora file.

1. Per visualizzare i dettagli del gruppo di gestione, selezionare il collegamento **(dettagli)** accanto al titolo del gruppo di gestione. Se questo collegamento non è disponibile, non si hanno le autorizzazioni per visualizzare il gruppo di gestione.

   :::image type="content" source="./media/main.png" alt-text="Principale" border="false":::

### <a name="view-in-powershell"></a>Visualizzare in PowerShell

Per recuperare tutti i gruppi, usare il comando Get-AzManagementGroup. Vedere i moduli [AZ.Resources](/powershell/module/az.resources/Get-AzManagementGroup) per l'elenco completo dei comandi Ottieni PowerShell per il gruppo di gestione.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Per ottenere le informazioni su un singolo gruppo di gestione, usare il parametro - GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Per restituire un gruppo di gestione specifico e tutti i livelli della gerarchia al suo interno, usare i parametri **-Expand** e **-Recurse**.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Visualizzare nell'interfaccia della riga di comando di Azure

Per recuperare tutti i gruppi, usare il comando list.  

```azurecli-interactive
az account management-group list
```

Per ottenere le informazioni su un singolo gruppo di gestione, usare il comando show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Per restituire un gruppo di gestione specifico e tutti i livelli della gerarchia al suo interno, usare i parametri **-Expand** e **-Recurse**.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Spostamento di gruppi di gestione e sottoscrizioni   

Uno dei motivi per creare un gruppo di gestione è l'accorpamento delle sottoscrizioni. Solo i gruppi di gestione e le sottoscrizioni possono essere resi elementi figlio di un altro gruppo di gestione. Una sottoscrizione che viene spostata in un gruppo di gestione eredita tutti i criteri e le autorizzazioni di accesso utente dal gruppo di gestione padre

Quando si sposta un gruppo di gestione o una sottoscrizione in modo da essere un elemento figlio di un altro gruppo di gestione, è necessario che siano soddisfatte tre regole.

Per eseguire l'azione di spostamento, è necessario avere: 

- Autorizzazioni di scrittura per il gruppo di gestione e l'assegnazione di ruolo nel gruppo di gestione o nella sottoscrizione figlio.
  - **Proprietario** dell'esempio di ruolo predefinito
- Accesso in scrittura del gruppo di gestione nel gruppo di gestione padre di destinazione.
  - Esempio di ruolo predefinito: **Proprietario**, **Collaboratore**, **Collaboratore gruppo di gestione**
- Accesso in scrittura del gruppo di gestione nel gruppo di gestione padre esistente.
  - Esempio di ruolo predefinito: **Proprietario**, **Collaboratore**, **Collaboratore gruppo di gestione**

**Eccezione**: se il gruppo di gestione padre di destinazione o esistente è il gruppo di gestione radice, i requisiti delle autorizzazioni non sono applicabili. Poiché il gruppo di gestione radice è il punto di destinazione predefinito per tutti i nuovi gruppi di gestione e le sottoscrizioni, non è necessario avere le autorizzazioni per spostare un elemento.

Se il ruolo Proprietario nella sottoscrizione viene ereditato dal gruppo di gestione corrente, le destinazioni di spostamento sono limitate. È possibile spostare la sottoscrizione solo in un altro gruppo di gestione in cui si ha il ruolo Proprietario. Non è possibile spostarla in un gruppo di gestione in cui si ha il ruolo Collaboratore perché si perderebbe la proprietà della sottoscrizione. Se il ruolo Proprietario per la sottoscrizione è stato assegnato direttamente (non ereditato dal gruppo di gestione), è possibile eseguire lo spostamento in qualsiasi gruppo di gestione in cui si ha il ruolo Collaboratore.

Per controllare le proprie autorizzazioni nel portale di Azure, selezionare il gruppo di gestione e quindi selezionare **IAM**. Per altre informazioni sui ruoli di Azure, vedere [gestire l'accesso e le autorizzazioni con RBAC](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Spostare sottoscrizioni 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Aggiungere una sottoscrizione esistente a un gruppo di gestione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gruppi di gestione**.

1. Selezionare il gruppo di gestione che dovrà fungere da elemento padre.

1. Nella parte superiore della pagina selezionare **Aggiungi sottoscrizione**.

1. Selezionare nell'elenco la sottoscrizione con l'ID corretto.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Sottoscrizioni disponibili da aggiungere a un gruppo di gestione" border="false":::

1. Selezionare "Salva".

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Rimuovere una sottoscrizione da un gruppo di gestione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gruppi di gestione**.

1. Selezionare il gruppo di gestione che attualmente funge da elemento padre.  

1. Selezionare i puntini di sospensione in fondo alla riga della sottoscrizione che si vuole spostare.

   :::image type="content" source="./media/move_small.png" alt-text="Opzione Sposta in un gruppo di gestione" border="false":::

1. Selezionare **Sposta**.

1. Nel menu visualizzato selezionare il **Gruppo di gestione padre**.

   :::image type="content" source="./media/move_small_context.png" alt-text="Riquadro Sposta per modificare il gruppo padre" border="false":::

1. Selezionare **Salva**.

### <a name="move-subscriptions-in-powershell"></a>Spostare sottoscrizioni in PowerShell

Per spostare una sottoscrizione in PowerShell, usare il comando New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Per rimuovere il collegamento tra la sottoscrizione e il gruppo di gestione, usare il comando Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Spostare sottoscrizioni nell'interfaccia della riga di comando di Azure

Per spostare una sottoscrizione nell'interfaccia della riga di comando, usare il comando add.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Per rimuovere la sottoscrizione dal gruppo di gestione, usare il comando remove.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>Spostare i gruppi di gestione 

### <a name="move-management-groups-in-the-portal"></a>Spostare gruppi di gestione nel portale

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** > **Gruppi di gestione**.

1. Selezionare il gruppo di gestione che dovrà fungere da elemento padre.

1. Nella parte superiore della pagina selezionare **Aggiungi gruppo di gestione**.

1. Nel menu visualizzato, selezionare se usare un gruppo di gestione nuovo o esistente.

   - Selezionando la prima opzione verrà creato un nuovo gruppo di gestione.
   - Selezionando la seconda, verrà visualizzato un elenco a discesa di tutti i gruppi di gestione a cui è possibile spostare questo gruppo di gestione.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Spostare un gruppo di gestione in un gruppo nuovo o esistente" border="false":::

1. Selezionare **Salva**.

### <a name="move-management-groups-in-powershell"></a>Spostare gruppi di gestione in PowerShell

Usare il comando Update-AzManagementGroup in PowerShell per spostare un gruppo di gestione in un gruppo diverso.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Spostare gruppi di gestione nell'interfaccia della riga di comando di Azure

Usare il comando update per spostare un gruppo di gestione con l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Controllare i gruppi di gestione con i log attività

I gruppi di gestione sono supportati all'interno del [log attività di Azure](../../azure-monitor/platform/platform-logs-overview.md). È possibile eseguire una query di tutti gli eventi che si verificano per un gruppo di gestione nella stessa posizione centrale delle altre risorse di Azure. È ad esempio possibile vedere tutte le modifiche delle assegnazioni di ruoli o di criteri apportate a uno specifico gruppo di gestione.

:::image type="content" source="./media/al-mg.png" alt-text="Log attività con i gruppi di gestione" border="false":::

Quando si esegue una query sui gruppi di gestione all'esterno del portale di Azure, l'ambito di destinazione per tali gruppi sarà simile a **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Riferimento a gruppi di gestione da altri provider di risorse

Quando si fa riferimento a gruppi di gestione di altre azioni del provider di risorse, usare il percorso seguente come ambito. Questo percorso viene usato quando si usano PowerShell, l'interfaccia della riga di comando di Azure e le API REST.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Un esempio di uso di questo percorso è l'assegnazione di una nuova assegnazione di ruolo a un gruppo di gestione in PowerShell:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Lo stesso percorso dell'ambito viene usato quando si recupera una definizione di criteri in un gruppo di gestione.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di gestione, vedere:

- [Creare gruppi di gestione per organizzare le risorse di Azure](./create.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](./manage.md)
- [Gruppi di gestione nel modulo Resources di Azure PowerShell](/powershell/module/az.resources#resources)
- [Gruppi di gestione nell'API REST](/rest/api/resources/managementgroups)
- [Gruppi di gestione nell'interfaccia della riga di comando di Azure](/cli/azure/account/management-group)
