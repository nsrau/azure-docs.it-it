---
title: Esportare risorse di Criteri di Azure
description: Informazioni su come esportare le risorse di criteri di Azure in GitHub, ad esempio le definizioni dei criteri e le assegnazioni dei criteri.
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 923b063244f6f47def1c3e6a63d6e4d6b3b88083
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919566"
---
# <a name="export-azure-policy-resources"></a>Esportare risorse di Criteri di Azure

Questo articolo fornisce informazioni su come esportare le risorse di criteri di Azure esistenti. L'esportazione delle risorse è utile e consigliata per il backup, ma è anche un passaggio importante del percorso con la governance del cloud e la gestione dei [criteri come codice](../concepts/policy-as-code.md). Le risorse di criteri di Azure possono essere esportate tramite [portale di Azure](#export-with-azure-portal), l'interfaccia della riga di comando di [Azure](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)e ognuno degli SDK supportati.

## <a name="export-with-azure-portal"></a>Esporta con portale di Azure

Per esportare una definizione di criteri dal portale di Azure, seguire questa procedura:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Selezionare **Definizioni** a sinistra nella pagina Criteri di Azure.

1. Usare il pulsante **Esporta definizioni** oppure selezionare i puntini di sospensione nella riga di una definizione di criteri, quindi selezionare **Esporta definizione**.

1. Selezionare il pulsante **Accedi con GitHub**. Se non è ancora stata eseguita l'autenticazione con GitHub per autorizzare Criteri di Azure a esportare la risorsa, verificare di quale accesso ha bisogno l'[azione GitHub](https://github.com/features/actions) nella nuova finestra visualizzata e selezionare **Autorizza AzureGitHubActions** per continuare con il processo di esportazione. Al termine, la nuova finestra si chiude automaticamente.

1. Nella scheda **Informazioni di base** impostare le opzioni seguenti, quindi selezionare la scheda **Criteri** o il pulsante **Avanti: Criteri** nella parte inferiore della pagina.

   - **Filtro per repository**: impostare su _Repository personali_ per visualizzare solo i repository di cui si è proprietari o su _Tutti i repository_ per visualizzare tutti i repository a cui è stato concesso l'accesso all'azione GitHub.
   - **Repository**: impostare sul repository in cui si vogliono esportare le risorse di Criteri di Azure.
   - **Ramo**: impostare il ramo nel repository. L'uso di un ramo diverso da quello predefinito è un modo efficace per convalidare gli aggiornamenti prima di eseguire ulteriori unioni nel codice sorgente.
   - **Directory**: _cartella di livello radice_ in cui esportare le risorse di Criteri di Azure. Le sottocartelle di questa directory vengono create in base alle risorse esportate.

1. Nella scheda **Criteri** impostare l'ambito per la ricerca selezionando i puntini di sospensione e scegliendo una combinazione di gruppi di gestione, sottoscrizioni o gruppi di risorse.
   
1. Usare il pulsante **Aggiungi definizione/i dei criteri** per cercare nell'ambito gli oggetti da esportare. Nella finestra che si aprirà a lato selezionare ogni oggetto da esportare. Filtrare la selezione in base alla casella di ricerca o al tipo. Dopo aver selezionato tutti gli oggetti da esportare, usare il pulsante **Aggiungi** in fondo alla pagina.

1. Per ogni oggetto selezionato selezionare le opzioni di esportazione desiderate, ad esempio _Solo definizione_ o _Definizione e assegnazione/i_, per una definizione di criteri. Selezionare quindi la scheda **Rivedi ed esporta** o il pulsante **Avanti: Rivedi ed esporta** nella parte inferiore della pagina.

   > [!NOTE]
   > Se si sceglie l'opzione _Definizione e assegnazione/i_, vengono esportate solo le assegnazioni dei criteri all'interno dell'ambito impostato dal filtro al momento dell'aggiunta della definizione di criteri.

1. Nella scheda **Rivedi ed esporta** controllare che i dettagli corrispondano e quindi usare il pulsante **Esporta** nella parte inferiore della pagina.

1. Controllare il repository GitHub, il ramo e la _cartella di livello radice_ per verificare che le risorse selezionate siano state esportate nel controllo del codice sorgente.

Le risorse di Criteri di Azure vengono esportate nella struttura seguente all'interno del repository GitHub selezionato e della _cartella di livello radice_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Esportare con interfaccia della riga di comando di Azure

Le definizioni, le iniziative e le assegnazioni di criteri di Azure possono essere esportate come JSON con la [CLI di Azure](/cli/azure/install-azure-cli). Ognuno di questi comandi usa un parametro **Name** per specificare l'oggetto per il quale ottenere il JSON. La proprietà **Name** è spesso un _GUID_ e non è il **DisplayName** dell'oggetto.

- Definizione- [AZ Policy Definition Show](/cli/azure/policy/definition#az_policy_definition_show)
- Iniziativa- [AZ Policy set-Definition Show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Assegnazione- [AZ Policy Assignment Show](/cli/azure/policy/assignment#az_policy_assignment_show)

Di seguito è riportato un esempio di recupero di JSON per una definizione di criteri con il **nome** _VirtualMachineStorage_:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Esporta con Azure PowerShell

Le definizioni, le iniziative e le assegnazioni di criteri di Azure possono essere esportate come JSON con [Azure PowerShell](/powershell/azure/). Ognuno di questi cmdlet usa un parametro **Name** per specificare l'oggetto per il quale ottenere il JSON. La proprietà **Name** è spesso un _GUID_ e non è il **DisplayName** dell'oggetto.

- Definizione- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Initiative- [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Assegnazione- [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Di seguito è riportato un esempio di recupero di JSON per una definizione di criteri con il **nome** _VirtualMachineStorage_:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
