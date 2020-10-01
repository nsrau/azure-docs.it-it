---
title: Esportare le risorse di criteri di Azure
description: Informazioni su come esportare le risorse di criteri di Azure in GitHub, ad esempio le definizioni dei criteri e le assegnazioni dei criteri.
ms.date: 09/30/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 17f0322390cd2cd89ba182a9e3c7c95843cc135e
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604431"
---
# <a name="export-azure-policy-resources"></a>Esportare le risorse di criteri di Azure

Questo articolo fornisce informazioni su come esportare le risorse di criteri di Azure esistenti. L'esportazione delle risorse è utile e consigliata per il backup, ma è anche un passaggio importante del percorso con la governance del cloud e la gestione dei [criteri come codice](../concepts/policy-as-code.md). Le risorse di criteri di Azure possono essere esportate tramite [portale di Azure](#export-with-azure-portal), l'interfaccia della riga di comando di [Azure](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)e ognuno degli SDK supportati.

## <a name="export-with-azure-portal"></a>Esporta con portale di Azure

Per esportare una definizione dei criteri da portale di Azure, attenersi alla procedura seguente:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Selezionare **definizioni** sul lato sinistro della pagina Criteri di Azure.

1. Usare il pulsante **Esporta definizioni** oppure selezionare i puntini di sospensione nella riga di una definizione dei criteri e quindi selezionare **Esporta definizione**.

1. Selezionare il pulsante **Accedi con GitHub** . Se non è ancora stata eseguita l'autenticazione con GitHub per autorizzare i criteri di Azure all'esportazione della risorsa, verificare le esigenze di accesso alle [azioni GitHub](https://github.com/features/actions) nella nuova finestra visualizzata e selezionare **autorizzare AzureGitHubActions** per continuare con il processo di esportazione. Una volta completata l'operazione, la nuova finestra viene chiusa autonomamente.

1. Nella scheda **nozioni di base** impostare le opzioni seguenti, quindi selezionare la scheda **criteri** o **Avanti: pulsante criteri** nella parte inferiore della pagina.

   - **Filtro repository**: impostare su repository _personali_ per visualizzare solo i repository di cui si è proprietari o _tutti_ i repository per visualizzare tutti i repository a cui è stato concesso l'accesso all'azione github.
   - **Repository**: impostare sul repository in cui si vogliono esportare le risorse di criteri di Azure.
   - **Branch**: impostare il ramo nel repository. L'uso di un ramo diverso da quello predefinito è un modo efficace per convalidare gli aggiornamenti prima di unirli ulteriormente nel codice sorgente.
   - **Directory**: _cartella a livello radice_ in cui esportare le risorse di criteri di Azure. Le sottocartelle in questa directory vengono create in base alle risorse esportate.

1. Nella scheda **criteri** impostare l'ambito per la ricerca selezionando i puntini di sospensione e selezionando una combinazione di gruppi di gestione, sottoscrizioni o gruppi di risorse.
   
1. Usare il pulsante **Aggiungi definizioni criteri** per cercare nell'ambito gli oggetti da esportare. Nella finestra laterale visualizzata selezionare ogni oggetto da esportare. Filtrare la selezione in base alla casella di ricerca o al tipo. Dopo aver selezionato tutti gli oggetti da esportare, usare il pulsante **Aggiungi** nella parte inferiore della pagina.

1. Per ogni oggetto selezionato selezionare le opzioni di esportazione desiderate, ad esempio la _definizione o la definizione_ _e le assegnazioni_ per una definizione di criteri. Selezionare quindi la scheda **Verifica + Esporta** o **Avanti: pulsante Verifica + Esporta** nella parte inferiore della pagina.

   > [!NOTE]
   > Se si sceglie la definizione dell'opzione _e le assegnazioni_ , verranno esportate solo le assegnazioni dei criteri all'interno dell'ambito impostato dal filtro al momento dell'aggiunta della definizione dei criteri.

1. Nella scheda verifica **+ Esporta** , controllare la corrispondenza dei dettagli e quindi usare il pulsante **Esporta** nella parte inferiore della pagina.

1. Verificare la cartella del repository GitHub, del ramo e del _livello radice_ per verificare che le risorse selezionate siano ora esportate nel controllo del codice sorgente.

Le risorse di criteri di Azure vengono esportate nella struttura seguente all'interno del repository GitHub e della _cartella del livello radice_selezionati:

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

- Definizione- [AZ Policy Definition Show](/cli/azure/policy/definition#az-policy-definition-show)
- Iniziativa- [AZ Policy set-Definition Show](/cli/azure/policy/set-definition#az-policy-set-definition-show)
- Assegnazione- [AZ Policy Assignment Show](/cli/azure/policy/assignment#az-policy-assignment-show)

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
Get-AzPolicyDefinition --Name 'VirtualMachineStorage'
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
