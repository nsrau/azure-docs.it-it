---
title: 'Avvio rapido: Creare un gruppo di gestione con Azure PowerShell'
description: In questo argomento di avvio rapido si usa Azure PowerShell per creare un gruppo di gestione con cui organizzare le risorse in una gerarchia.
ms.date: 08/31/2020
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74e4f0769bbf4deafefd27e166e0dae2c2e019a3
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236949"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>Avvio rapido: Creare un gruppo di gestione con Azure PowerShell

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per altre informazioni, vedere [Configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Prima di iniziare verificare che sia installata la versione più recente di Azure PowerShell. Per informazioni dettagliate, vedere [Installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps).

- Se la [protezione della gerarchia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) non è abilitata, qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza avere autorizzazioni di scrittura assegnate per tale gruppo di gestione. Questo nuovo gruppo di gestione diventa un elemento figlio del gruppo di gestione radice o del [gruppo di gestione predefinito](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e all'autore viene assegnato il ruolo "Proprietario". Il servizio del gruppo di gestione offre questa possibilità per evitare che siano necessarie assegnazioni di ruolo a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare il problema di reperire gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è consentita la creazione dei gruppi di gestione iniziali al livello radice.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Creazione in Azure PowerShell

Per PowerShell, usare il cmdlet [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) per creare un nuovo gruppo di gestione. In questo esempio il valore di **GroupName** del gruppo di gestione è _Contoso_.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** è un identificatore univoco creato. Questo ID viene usato da altri comandi per fare riferimento a questo gruppo e non può essere cambiato in seguito.

Se si vuole visualizzare un nome diverso per il gruppo di gestione all'interno del portale di Azure, aggiungere il parametro **DisplayName**. Ad esempio, per creare un gruppo di gestione con GroupName Contoso e nome visualizzato "Contoso Group", usare il cmdlet seguente:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Negli esempi precedenti il nuovo gruppo di gestione viene creato nel gruppo di gestione radice. Per specificare un gruppo di gestione diverso come elemento padre, usare il parametro **ParentId**.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere il gruppo di gestione creato in precedenza, usare il cmdlet [Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup):

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un gruppo di gestione per organizzare la gerarchia delle risorse. Il gruppo di gestione può contenere sottoscrizioni o altri gruppi di gestione.

Per altre informazioni sui gruppi di gestione e su come gestire la gerarchia delle risorse, continuare con:

> [!div class="nextstepaction"]
> [Gestire le risorse con i gruppi di gestione](./manage.md)