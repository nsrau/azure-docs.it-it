---
title: Eseguire l'onboarding dei clienti in Azure Lighthouse
description: Informazioni su come caricare un cliente in Azure Lighthouse, consentendo l'accesso e la gestione delle risorse tramite il proprio tenant mediante la gestione delle risorse delegate di Azure.
ms.date: 05/26/2020
ms.topic: how-to
ms.openlocfilehash: cac40a835ff3227a31611b31655865d43fa378ab
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118876"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Eseguire l'onboarding dei clienti in Azure Lighthouse

Questo articolo illustra come un provider di servizi può caricare un cliente nel Faro di Azure. Quando si esegue questa operazione, è possibile accedere e gestire le risorse delegate del cliente (sottoscrizioni e/o gruppi di risorse) tramite il tenant di Azure Active Directory (Azure AD) usando la [gestione delle risorse delegata di Azure](../concepts/azure-delegated-resource-management.md).

È possibile ripetere questo processo se si gestiscono risorse per più clienti. Quindi, quando un utente autorizzato accede al tenant, tale utente può essere autorizzato negli ambiti di locazione dei clienti per eseguire operazioni di gestione, senza dover accedere a ogni singolo tenant del cliente.

Per tenere traccia dell'impatto sull'engagement dei clienti e ricevere il riconoscimento, associare l'ID MPN (Microsoft Partner Network) ad almeno un account utente che abbia accesso a ognuna delle sottoscrizioni di cui è stato eseguito l'onboarding. Si noti che sarà necessario eseguire questa associazione nel tenant del provider di servizi. Per semplicità, è consigliabile creare un account di entità servizio nel tenant associato all'ID MPN e concedergli l'accesso in lettura a tutti i clienti di cui è stato eseguito l'onboarding. Per altre informazioni, vedere [Collegare un ID partner agli account Azure](../../cost-management-billing/manage/link-partner-id.md). 

> [!NOTE]
> I clienti possono anche essere caricati in Azure Lighthouse quando acquistano un'offerta di servizi gestiti (pubblica o privata) pubblicata in Azure Marketplace. Per altre informazioni, vedere [Pubblicare offerte di servizi gestiti in Azure Marketplace](publish-managed-services-offers.md). È anche possibile usare il processo di onboarding descritto qui insieme a un'offerta pubblicata in Azure Marketplace.

Il processo di onboarding richiede l'esecuzione di azioni sia dal tenant del provider di servizi che dal tenant del cliente. Tutti questi passaggi sono descritti in questo articolo.

> [!TIP]
> Anche se si fa riferimento a provider di servizi e clienti in questo argomento, le [aziende che gestiscono più tenant](../concepts/enterprise.md) possono usare lo stesso processo per configurare il faro di Azure e consolidare la loro esperienza di gestione.

## <a name="gather-tenant-and-subscription-details"></a>Raccogliere i dettagli del tenant e della sottoscrizione

Per eseguire l'onboarding del tenant di un cliente, è necessario che abbia una sottoscrizione di Azure attiva. Sarà necessario conoscere quanto segue:

- ID del tenant del provider di servizi, in cui si gestiranno le risorse del cliente
- ID del tenant del cliente, che avrà le risorse gestite dal provider di servizi
- ID di ogni specifica sottoscrizione nel tenant del cliente che verrà gestito dal provider di servizi o che contiene i gruppi di risorse che verranno gestiti dal provider di servizi.

> [!NOTE]
> Anche se si vuole caricare solo uno o più gruppi di risorse all'interno di una sottoscrizione, la distribuzione deve essere eseguita a livello di sottoscrizione, quindi è necessario l'ID sottoscrizione.

Se i valori ID non sono già disponibili, è possibile recuperarli in uno dei modi seguenti. Assicurarsi di usare questi valori esatti nella distribuzione.

### <a name="azure-portal"></a>Portale di Azure

L'ID del tenant può essere visualizzato passando il puntatore sul nome dell'account nell'angolo in alto a destra del portale di Azure o selezionando **Cambia directory**. Per selezionare e copiare l'ID tenant, cercare "Azure Active Directory" nel portale, quindi selezionare **Proprietà** e copiare il valore visualizzato nel campo **ID directory**. Per trovare l'ID di una sottoscrizione nel tenant personalizzato, cercare "Sottoscrizioni" e quindi selezionare l'ID della sottoscrizione appropriato.

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

> [!NOTE]
> Quando si esegue l'onboarding di una sottoscrizione (o di uno o più gruppi di risorse all'interno di una sottoscrizione) tramite il processo descritto in questo articolo, il provider di risorse **Microsoft.ManagedServices** verrà registrato per tale sottoscrizione.

## <a name="define-roles-and-permissions"></a>Definire ruoli e autorizzazioni

Il provider di servizi può eseguire più attività per un singolo cliente, per cui deve avere un accesso diverso per ambiti diversi. È possibile definire tutte le autorizzazioni necessarie per assegnare [ruoli predefiniti Controllo degli accessi in base al ruolo](../../role-based-access-control/built-in-roles.md) agli utenti nel tenant.

Per semplificare la gestione, è consigliabile usare i gruppi di utenti di Azure AD per ogni ruolo, che consentono di aggiungere o rimuovere singoli utenti al gruppo invece di assegnare le autorizzazioni direttamente a tale utente. È anche possibile assegnare ruoli a un'entità servizio. Assicurarsi di seguire il principio dei privilegi minimi, in modo che gli utenti abbiano solo le autorizzazioni necessarie per completare il lavoro. Per consigli e informazioni sui ruoli supportati, vedere [Tenant, ruoli e utenti in scenari di Azure Lighthouse](../concepts/tenants-users-roles.md).

> [!IMPORTANT]
> Per aggiungere autorizzazioni per un gruppo di Azure AD, il **tipo di gruppo** deve essere **Sicurezza** e non **Office 365**. Questa opzione è selezionata quando viene creato il gruppo. Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Per definire le autorizzazioni, è necessario essere a conoscenza dei valori ID per ogni utente, gruppo di utenti o entità servizio nel tenant del provider di servizi a cui si vuole concedere l'accesso. È anche necessario l'ID di definizione del ruolo per ogni ruolo predefinito che si vuole assegnare. Se non sono già presenti, è possibile recuperarli eseguendo i comandi seguenti dall'interno del tenant del provider di servizi.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> È consigliabile assegnare il [ruolo di eliminazione dell'assegnazione della registrazione di servizi gestiti](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) durante l'onboarding di un cliente, in modo che gli utenti nel tenant possano [rimuovere l'accesso alla delega](remove-delegation.md) in un secondo momento se necessario. Se questo ruolo non viene assegnato, le risorse delegate possono essere rimosse solo da un utente che si trova nel tenant del cliente.

## <a name="create-an-azure-resource-manager-template"></a>Creare un modello di Azure Resource Manager

Per eseguire l'onboarding del cliente, sarà necessario creare un modello di [Azure Resource Manager](../../azure-resource-manager/index.yml) per l'offerta con le informazioni seguenti. I valori **mspOfferName** e **mspOfferDescription** saranno visibili al cliente quando si visualizzano i dettagli dell'offerta nella [pagina Provider di servizi](view-manage-service-providers.md) del portale di Azure.

|Campo  |Definizione  |
|---------|---------|
|**mspOfferName**     |Nome che descrive questa definizione. Questo valore viene visualizzato al cliente come titolo dell'offerta.         |
|**mspOfferDescription**     |Breve descrizione dell'offerta, ad esempio "Offerta di gestione di macchine virtuali Contoso".      |
|**managedByTenantId**     |ID tenant.          |
|**authorizations**     |Valori **principalId** per gli utenti, i gruppi o i nomi dell'entità servizio del tenant, ognuno dei quali ha un **principalIdDisplayName** per consentire ai clienti di comprendere lo scopo dell'autorizzazione ed è associato a un valore **roleDefinitionId** predefinito per specificare il livello di accesso.      |

Il processo di onboarding richiede un modello di Azure Resource Manager, disponibile nel [repository di esempi](https://github.com/Azure/Azure-Lighthouse-samples/), e un file di parametri corrispondente modificato in modo che corrisponda alla configurazione e definisca le autorizzazioni.

Il modello scelto dipenderà dal fatto che si esegua l'onboarding di un'intera sottoscrizione, di un gruppo di risorse o di più gruppi di risorse all'interno di una sottoscrizione. Viene anche fornito un modello che può essere usato per i clienti che hanno acquistato un'offerta di servizio gestito pubblicata in Azure Marketplace, se si preferisce eseguire l'onboarding delle sottoscrizioni in questo modo.

|Onboarding di  |Usare questo modello di Azure Resource Manager  |E modificare questo file dei parametri |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Più gruppi di risorse all'interno di una sottoscrizione   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Sottoscrizione (quando si usa un'offerta pubblicata in Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Il processo descritto di seguito richiede una distribuzione separata a livello di sottoscrizione per ogni sottoscrizione di cui si esegue l'onboarding, anche l'onboarding delle sottoscrizioni viene eseguito nello stesso tenant del cliente. Le distribuzioni separate sono necessarie anche se si esegue l'onboarding di più gruppi di risorse all'interno di sottoscrizioni diverse nello stesso tenant del cliente. Tuttavia, l'onboarding di più gruppi di risorse all'interno di una singola sottoscrizione può essere eseguito in una sola distribuzione a livello di sottoscrizione.
>
> Le distribuzioni separate sono necessarie anche se si applicano più offerte alla stessa sottoscrizione (o gruppi di risorse all'interno di una sottoscrizione). Ogni offerta applicata deve usare un diverso **mspOfferName**.

L'esempio seguente illustra un file **delegatedResourceManagement.parameters.json** modificato che potrà essere usato per eseguire l'onboarding di una sottoscrizione. I file di parametri dei gruppi di risorse (disponibili nella cartella [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management)) sono simili, ma includono anche un parametro**rgName** per identificare i gruppi di risorse specifici di cui eseguire l'onboarding.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
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

L'ultima autorizzazione nell'esempio precedente aggiunge un **principalId** con il ruolo Amministratore Accesso utenti (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Quando si assegna questo ruolo, è necessario includere la proprietà **delegatedRoleDefinitionIds** e uno o più ruoli predefiniti. L'utente creato in questa autorizzazione potrà assegnare questi ruoli predefiniti alle [identità gestite](../../active-directory/managed-identities-azure-resources/overview.md) nel tenant del cliente, operazione necessaria per [distribuire i criteri che possono essere corretti](deploy-policy-remediation.md).  L'utente è inoltre in grado di creare interventi di supporto.  Nessun'altra autorizzazione normalmente associata al ruolo Amministratore Accesso utenti verrà applicata a questo utente.

## <a name="deploy-the-azure-resource-manager-templates"></a>Distribuire i modelli di Azure Resource Manager

Dopo aver aggiornato il file dei parametri, un utente del tenant del cliente deve distribuire il modello di Azure Resource Manager nel proprio tenant come distribuzione a livello di sottoscrizione. È necessaria una distribuzione separata per ogni sottoscrizione di cui si vuole eseguire l'onboarding (o per ogni sottoscrizione che contiene i gruppi di risorse che si vuole caricare).

Trattandosi di una distribuzione a livello di sottoscrizione, non è possibile avviarla nel portale di Azure. È possibile eseguire la distribuzione tramite PowerShell o l'interfaccia della riga di comando di Azure, come illustrato di seguito.

> [!IMPORTANT]
> Questa distribuzione a livello di sottoscrizione deve essere eseguita da un account non guest nel tenant del cliente con il [ruolo predefinito Proprietario](../../role-based-access-control/built-in-roles.md#owner) per la sottoscrizione di cui eseguire l'onboarding (o che contiene i gruppi di risorse di cui eseguire l'onboarding). Per visualizzare tutti gli utenti che possono delegare la sottoscrizione, un utente nel tenant del cliente può selezionare la sottoscrizione nel portale di Azure, aprire **Controllo di accesso (IAM)** e [visualizzare tutti gli utenti con il ruolo Proprietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).
>
> Se la sottoscrizione è stata creata usando il [programma Cloud Solution Provider (CSP)](../concepts/cloud-solution-provider.md), tutti gli utenti con il ruolo [Agente amministratore](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) nel tenant del provider di servizi possono eseguire la distribuzione.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Verificare che l'onboarding sia riuscito

Quando la sottoscrizione di un cliente è stata caricata in Azure Lighthouse, gli utenti nel tenant del provider di servizi saranno in grado di visualizzare la sottoscrizione e le relative risorse (se ne è stato concesso l'accesso tramite il processo precedente, singolarmente o come membro di un gruppo di Azure AD con le autorizzazioni appropriate). Per esserne certi, verificare che la sottoscrizione venga visualizzata in uno dei modi seguenti.  

### <a name="azure-portal"></a>Portale di Azure

Nel tenant del provider di servizi:

1. Passare alla [pagina Clienti personali](view-manage-customers.md).
2. Selezionare **Clienti**.
3. Verificare che sia possibile visualizzare le sottoscrizioni con il nome dell'offerta specificato nel modello di Resource Manager.

> [!IMPORTANT]
> Per visualizzare la sottoscrizione delegata nei [miei clienti](view-manage-customers.md), è necessario che agli utenti nel tenant del provider di servizi sia stato concesso il ruolo [lettore](../../role-based-access-control/built-in-roles.md#reader) (o un altro ruolo predefinito che include l'accesso in lettura) al momento dell'onboarding della sottoscrizione.

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
- Informazioni su come [rimuovere l'accesso a una delega](remove-delegation.md) di cui in precedenza è stato eseguito l'onboarding.
