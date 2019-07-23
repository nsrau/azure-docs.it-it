---
title: Eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure - Azure Lighthouse
description: Informazioni su come eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure, consentendo l'accesso e la gestione delle risorse tramite il proprio tenant.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1885a6220f14de234710b6980b5d3b6a6172bb7e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810855"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure

Questo articolo illustra come un provider di servizi può eseguire l'onboarding di un cliente nella gestione risorse delegate di Azure, consentendo l'accesso e la gestione delle risorse delegate (sottoscrizioni e/o gruppi di risorse) tramite il proprio tenant di Azure Active Directory ( Azure AD). Nonostante qui si faccia riferimento ai provider di servizi e ai clienti, le aziende che gestiscono più tenant possono usare lo stesso processo per consolidare l'esperienza di gestione.

È possibile ripetere questo processo se si gestiscono risorse per più clienti. Quando un utente autorizzato accede al tenant, tale utente può quindi essere autorizzato negli ambiti di tenancy dei clienti a eseguire operazioni di gestione senza dover accedere al tenant di ogni singolo cliente.

È possibile associare l'ID MPN (Microsoft Partner Network) alle sottoscrizioni caricate per tenere traccia dell'impatto sugli engagement dei clienti. Per altre informazioni, vedere [Collegare un ID partner agli account Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started).

> [!NOTE]
> È possibile eseguire l'onboarding automatico dei clienti quando acquistano un'offerta di servizi gestiti (pubblica o privata) pubblicata in Azure Marketplace. Per altre informazioni, vedere [Pubblicare offerte di servizi gestiti in Azure Marketplace](publish-managed-services-offers.md). È anche possibile usare il processo di onboarding descritto qui con un'offerta pubblicata in Azure Marketplace.

Il processo di onboarding richiede l'esecuzione di azioni sia dal tenant del provider di servizi che dal tenant del cliente. Tutti questi passaggi sono descritti in questo articolo.

> [!IMPORTANT]
> Non è attualmente possibile eseguire l'onboarding di una sottoscrizione (o di un gruppo di risorse all'interno di una sottoscrizione) per la gestione risorse delegate di Azure se la sottoscrizione usa Azure Databricks. Analogamente, se una sottoscrizione è stata registrata per l'onboarding con il provider di risorse **Microsoft.ManagedServices**, non sarà possibile creare un'area di lavoro di Databricks per tale sottoscrizione in questo momento.

## <a name="gather-tenant-and-subscription-details"></a>Raccogliere i dettagli del tenant e della sottoscrizione

Per eseguire l'onboarding del tenant di un cliente, è necessario che abbia una sottoscrizione di Azure attiva. Sarà necessario conoscere quanto segue:

- ID del tenant del provider di servizi, in cui si gestiranno le risorse del cliente
- ID del tenant del cliente, che avrà le risorse gestite dal provider di servizi
- ID di ogni specifica sottoscrizione nel tenant del cliente che verrà gestito dal provider di servizi o che contiene i gruppi di risorse che verranno gestiti dal provider di servizi

Se queste informazioni non sono già disponibili, è possibile recuperarle in uno dei modi seguenti.

### <a name="azure-portal"></a>Portale di Azure

L'ID del tenant può essere visualizzato passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**. Per selezionare e copiare l'ID tenant, cercare "Azure Active Directory" nel portale, quindi selezionare **Proprietà** e copiare il valore visualizzato nel campo **ID directory**. Per trovare l'ID di una sottoscrizione, cercare "Sottoscrizioni" e quindi selezionare l'ID sottoscrizione appropriato.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>Assicurarsi che la sottoscrizione del cliente sia registrata per l'onboarding

Ogni sottoscrizione deve essere autorizzata per l'onboarding registrando manualmente il provider di risorse **Microsoft.ManagedServices**. Il cliente può registrare una sottoscrizione seguendo i passaggi illustrati in [Provider e tipi di risorse di Azure](../../azure-resource-manager/resource-manager-supported-services.md).

Il cliente può verificare che la sottoscrizione sia pronta per l'onboarding in uno dei modi seguenti.

### <a name="azure-portal"></a>Portale di Azure

1. Nel portale di Azure selezionare la sottoscrizione.
1. Selezionare **Provider di risorse**.
1. Verificare che **Microsoft.ManagedServices** sia contrassegnato come **Registrato**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Dovrebbe restituire risultati simili ai seguenti:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show –namespace "Microsoft.ManagedServices" –-output table
```

Dovrebbe restituire risultati simili ai seguenti:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>Definire ruoli e autorizzazioni

Come provider di servizi, è possibile usare più offerte con un singolo cliente, per cui è necessario un accesso diverso per ambiti diversi.

Per semplificare la gestione, è consigliabile usare i gruppi di utenti di Azure AD per ogni ruolo, che consentono di aggiungere o rimuovere singoli utenti al gruppo invece di assegnare le autorizzazioni direttamente a tale utente. È anche possibile assegnare ruoli a un'entità servizio. Assicurarsi di seguire il principio dei privilegi minimi, in modo che gli utenti abbiano solo le autorizzazioni necessarie per completare il proprio lavoro, riducendo la possibilità di errori accidentali. Per altre informazioni, vedere [Procedure di sicurezza consigliate](../concepts/recommended-security-practices.md).

> [!NOTE]
> Le assegnazione di ruolo devono usare i [ruoli predefiniti](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) del controllo degli accessi in base al ruolo. Tutti i ruoli predefiniti sono attualmente supportati con la gestione risorse delegate di Azure tranne Proprietario e i ruoli predefiniti con l'autorizzazione [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions). Il ruolo predefinito Amministratore Accesso utenti è supportato per un uso limitato, come descritto di seguito. Non sono supportati neppure i ruoli personalizzati e i [ruoli di amministratore della sottoscrizione classica](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators).

Per definire le autorizzazioni, è necessario essere a conoscenza dei valori degli ID per ogni utente, gruppo di utenti o entità servizio a cui si vuole concedere l'accesso. È anche necessario l'ID di definizione del ruolo per ogni ruolo predefinito che si vuole assegnare. Se non sono già disponibili, è possibile recuperarli in uno dei modi seguenti.



### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list –-query "[?displayName == '<yourGroupName>'].objectId" –-output tsv

# To retrieve the objectId for an Azure AD user
az ad user show –-upn-or-object-id "<yourUPN>" –-query "objectId" –-output tsv

# To retrieve the objectId for an SPN
az ad sp list –-query "[?displayName == '<spDisplayName>'].objectId" –-output tsv

# To retrieve role definition IDs
az role definition list –-name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Creare un modello di Azure Resource Manager

Per eseguire l'onboarding del cliente, sarà necessario creare un modello di [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) che includa quanto segue:

|Campo  |Definizione  |
|---------|---------|
|**mspName**     |Nome del provider di servizi         |
|**mspOfferDescription**     |Breve descrizione dell'offerta (ad esempio, "Offerta di gestione di macchine virtuali Contoso")      |
|**managedByTenantId**     |ID del tenant.         |
|**authorizations**     |Valori **principalId** per gli utenti, i gruppi o i nomi dell'entità servizio del tenant, ognuno dei quali ha un **principalIdDisplayName** per consentire ai clienti di comprendere lo scopo dell'autorizzazione ed è associato a un valore **roleDefinitionId** predefinito per specificare il livello di accesso         |

Per eseguire l'onboarding della sottoscrizione di un cliente, usare il modello di Azure Resource Manager appropriato fornito nel [repository di esempi](https://github.com/Azure/Azure-Lighthouse-samples/), con un file di parametri corrispondente modificato in modo che corrisponda alla configurazione e che definisca le autorizzazioni. Sono disponibili modelli distinti a seconda che si stia eseguendo l'onboarding di un'intera sottoscrizione, di un gruppo di risorse o di più gruppi di risorse all'interno di una sottoscrizione. Viene anche fornito un modello che può essere usato per i clienti che hanno acquistato un'offerta di servizio gestito pubblicata in Azure Marketplace, se si preferisce eseguire l'onboarding delle sottoscrizioni in questo modo.

|**Onboarding di**  |**Usare questo modello di Azure Resource Manager**  |**E modificare questo file di parametri** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Più gruppi di risorse all'interno di una sottoscrizione   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Sottoscrizione (quando si usa un'offerta pubblicata in Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Il processo descritto qui richiede una distribuzione separata per ogni sottoscrizione di cui eseguire l'onboarding.
> 
> Le distribuzioni separate sono necessarie anche se si esegue l'onboarding di più gruppi di risorse all'interno di sottoscrizioni diverse. Tuttavia, l'onboarding di più gruppi di risorse all'interno di una singola sottoscrizione può essere eseguito in una sola distribuzione.

L'esempio seguente illustra un file **resourceProjection.parameters.json** modificato che verrà usato per eseguire l'onboarding di una sottoscrizione. I file di parametri dei gruppi di risorse (disponibili nella cartella [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management)) sono simili, ma includono anche un parametro**rgName** per identificare i gruppi di risorse specifici di cui eseguire l'onboarding.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
L'ultima autorizzazione nell'esempio precedente aggiunge un **principalId** con il ruolo Amministratore Accesso utenti (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Quando si assegna questo ruolo, è necessario includere la proprietà **delegatedRoleDefinitionIds** e uno o più ruoli predefiniti. L'utente creato in questa autorizzazione potrà assegnare questi ruoli predefiniti alle identità gestite. Si noti che nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente.

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuire i modelli di Azure Resource Manager

Dopo aver aggiornato il file dei parametri, il cliente deve distribuire il modello di gestione delle risorse nel tenant del cliente come distribuzione a livello di sottoscrizione. Per ogni sottoscrizione di cui si vuole eseguire l'onboarding nella gestione risorse delegate di Azure (o per ogni sottoscrizione che contiene i gruppi di risorse di cui si vuole eseguire l'onboarding) è necessaria una distribuzione separata.

> [!IMPORTANT]
> La distribuzione deve essere eseguita da un account non guest nel tenant del cliente con il [ruolo predefinito Proprietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) per la sottoscrizione di cui eseguire l'onboarding (o che contiene i gruppi di risorse di cui eseguire l'onboarding).

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Verificare che l'onboarding sia riuscito

Quando l'onboarding della sottoscrizione di un cliente è stato eseguito correttamente nella gestione risorse delegate di Azure, gli utenti nel tenant del provider di servizi potranno visualizzare la sottoscrizione e le relative risorse (se è stato concesso l'accesso tramite il processo precedente, singolarmente o come membro di un gruppo Azure AD con le autorizzazioni appropriate). Per esserne certi, verificare che la sottoscrizione venga visualizzata in uno dei modi seguenti.  

### <a name="azure-portal"></a>Portale di Azure

Nel tenant del provider di servizi:

1. Passare alla [pagina Clienti personali](view-manage-customers.md).
2. Selezionare **Clienti**.
3. Verificare che sia possibile visualizzare le sottoscrizioni con il nome dell'offerta specificato nel modello di Resource Manager.

Nel tenant del cliente:

1. Passare alla [pagina Provider di servizi](view-manage-service-providers.md).
2. Selezionare **Offerte del provider di servizi**.
3. Verificare che sia possibile visualizzare le sottoscrizioni con il nome dell'offerta specificato nel modello di Resource Manager.

> [!NOTE]
> Potrebbero essere necessari alcuni minuti dopo il completamento della distribuzione prima che gli aggiornamenti siano visibili nel portale di Azure.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
