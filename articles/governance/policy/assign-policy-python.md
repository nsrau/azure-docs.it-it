---
title: 'Avvio rapido: Nuova assegnazione di criteri con Python'
description: In questo argomento di avvio rapido viene usato Python per creare un'assegnazione di Criteri di Azure per identificare le risorse non conformi.
ms.date: 08/10/2020
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: a160b9bc389bc0c902f9644887aa478f80822e60
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88134617"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Avvio rapido: Creare un'assegnazione di criteri per identificare le risorse non conformi con Python

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. In questo avvio rapido si creerà un'assegnazione di criteri per identificare le macchine virtuali che non usano dischi gestiti. Al termine, si identificheranno le macchine virtuali _non conformi_.

La libreria di Python viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida illustra come usare la libreria di Python per creare un'assegnazione di criteri.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Aggiungere la libreria di Criteri

Per abilitare l'uso di Python con Criteri di Azure, è necessario aggiungere la libreria. Questa libreria funziona ovunque sia possibile usare Python, sia con [bash in Windows 10](/windows/wsl/install-win10) o con installazione locale.

1. Controllare che sia installata la versione di Python più recente o almeno la versione **3.8**. Se non è ancora installata, scaricarla dal sito [Python.org](https://www.python.org/downloads/).

1. Controllare che sia installata l'interfaccia della riga di comando di Azure più recente o almeno la versione **2.5.1**. Se non è ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > L'interfaccia della riga di comando di Azure è necessaria per consentire a Python di usare **l'autenticazione basata sull'interfaccia della riga di comando** negli esempi seguenti. Per informazioni su altre opzioni, vedere [Eseguire l'autenticazione tramite le librerie di gestione di Azure per Python](/azure/developer/python/azure-sdk-authenticate).

1. Eseguire l'autenticazione tramite l'interfaccia della riga di comando di Azure.

   ```azurecli
   az login
   ```

1. Nell'ambiente Python preferito installare le librerie necessarie per Azure Resource Graph:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se Python è installato per tutti gli utenti, questi comandi devono essere eseguiti da una console con privilegi elevati.

1. Verificare che le librerie siano state installate. `azure-mgmt-policyinsights` deve essere **0.5.0** o versione successiva, `azure-mgmt-resource` deve essere **9.0.0** o versione successiva e `azure-cli-core` deve essere **2.5.0** o versione successiva.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questo avvio rapido i crea un'assegnazione di criteri e si assegna la definizione **Controlla macchine virtuali che non usano dischi gestiti** (`06a78e20-9358-41c9-923c-fb736d382a4d`). Questa definizione di criteri identifica le risorse che non rispettano le condizioni in essa impostate.

Eseguire questo codice per creare un nuova assegnazione di criteri:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

I comandi precedenti usano le informazioni seguenti:

Dettagli dell'assegnazione:
- **display_name** - Nome visualizzato per l'assegnazione di criteri. In questo caso viene usato _controllare le macchine virtuali senza assegnazione di dischi gestiti_.
- **policy_definition_id** – Percorso di definizione di criteri in base al quale si crea l'assegnazione. In questo caso si tratta dell'ID della definizione di criteri _Controllare le macchine virtuali che non usano i dischi gestiti_. In questo esempio la definizione di criteri è predefinita e il percorso non include informazioni sul gruppo di gestione o sulla sottoscrizione.
- **scope** - Ambito che determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri. Può spaziare da un gruppo di gestione a una singola risorsa. Assicurarsi di sostituire `{scope}` con uno dei criteri seguenti:
  - Gruppo di gestione: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Sottoscrizione: `/subscriptions/{subscriptionId}`
  - Gruppo di risorse: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Risorsa: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description** - Spiegazione più dettagliata delle operazioni eseguite dal criterio o dei motivi per cui è assegnato a questo ambito.

Creazione dell'assegnazione:

- Scope (Ambito): questo ambito determina la posizione in cui viene salvata l'assegnazione di criteri. L'ambito configurato nei dettagli dell'assegnazione deve esistere all'interno di questo ambito.
- Name: nome effettivo dell'assegnazione. Per questo esempio è stato usato il nome _audit-vm-manageddisks_.
- Policy assignment (Assegnazione criteri): oggetto **PolicyAssignment** di Python creato nel passaggio precedente.

A questo punto si è pronti per identificare le risorse non conformi e comprendere così lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare risorse non conformi

Usare le informazioni seguenti per identificare le risorse non conformi all'assegnazione di criteri creata. Eseguire il codice seguente:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

Sostituire `{subscriptionId}` con la sottoscrizione per cui si vogliono visualizzare i risultati della conformità per l'assegnazione di criteri. Per un elenco di altri ambiti e modi per riepilogare i dati, vedere [Metodi per lo stato dei criteri](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

I risultati saranno simili all'esempio seguente:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

I risultati corrispondono a quanto visualizzato nella scheda **Conformità risorsa** di un'assegnazione di criteri nella vista del portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire il comando seguente per rimuovere l'assegnazione creata:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

Sostituire `{scope}` con lo stesso ambito usato per creare l'assegnazione di criteri.

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stata assegnata una definizione dei criteri per identificare le risorse non conformi nell'ambiente Azure.

Per altre informazioni sull'assegnazione di definizioni di criteri per convalidare la conformità delle nuove risorse, continuare con l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Creazione e gestione dei criteri](./tutorials/create-and-manage.md)