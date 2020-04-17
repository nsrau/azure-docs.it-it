---
title: Monitorare le modifiche di delega nel tenant di gestioneMonitor delegation changes in your managing tenant
description: Informazioni su come monitorare l'attività di delega dai tenant dei clienti al tenant di gestione.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a4593b34311eca34e4fb68926a3820899ab3f324
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458812"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorare le modifiche di delega nel tenant di gestioneMonitor delegation changes in your managing tenant

In qualità di provider di servizi, è consigliabile tenere presente quando le sottoscrizioni dei clienti o i gruppi di risorse vengono delegati al tenant tramite la [gestione delle risorse delegate](../concepts/azure-delegated-resource-management.md)di Azure o quando vengono rimosse le risorse delegate in precedenza.

Nel tenant di gestione, il [log attività](../../azure-monitor/platform/platform-logs-overview.md) di Azure tiene traccia dell'attività di delega a livello di tenant. Questa attività registrata include eventuali deleghe aggiunte o rimosse da tutti i tenant dei clienti.

In questo argomento vengono illustrate le autorizzazioni necessarie per monitorare l'attività di delega al tenant (in tutti i clienti) e le procedure consigliate per eseguire questa operazione. Include inoltre uno script di esempio che mostra un metodo per l'esecuzione di query e la creazione di report su questi dati.

> [!IMPORTANT]
> Tutti questi passaggi devono essere eseguiti nella gestione tenant, anziché in qualsiasi tenant del cliente.

## <a name="enable-access-to-tenant-level-data"></a>Abilitare l'accesso ai dati a livello di tenantEnable access to tenant-level data

Per accedere ai dati del log attività a livello di tenant, è necessario assegnare a un account il ruolo predefinito Lettore di [monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) nell'ambito radice (/). Questa assegnazione deve essere eseguita da un utente con il ruolo di amministratore globale con accesso con privilegi elevati aggiuntivo.

### <a name="elevate-access-for-a-global-administrator-account"></a>Elevare l'accesso per un account amministratore globale

Per assegnare un ruolo nell'ambito radice (/), è necessario disporre del ruolo amministratore globale con accesso con privilegi elevati. Questo accesso con privilegi elevati deve essere aggiunto solo quando è necessario effettuare l'assegnazione di ruolo, quindi rimuovere al termine.

Per istruzioni dettagliate sull'aggiunta e la rimozione dell'elevazione dei privilegi, vedere [Elevare l'accesso per gestire tutte le sottoscrizioni e i gruppi](../../role-based-access-control/elevate-access-global-admin.md)di gestione di Azure.For detailed instructions on adding and removing elevation, see Elevate access to manage all Azure subscriptions and management groups .

Dopo aver elevare l'accesso, l'account disterrà il ruolo di amministratore di accesso utente in Azure nell'ambito radice. Questa assegnazione di ruolo consente di visualizzare tutte le risorse e assegnare l'accesso in qualsiasi sottoscrizione o gruppo di gestione nella directory, nonché di effettuare assegnazioni di ruolo nell'ambito radice. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Creare un nuovo account dell'entità servizio per accedere ai dati a livello di tenantCreate a new service principal account to access tenant-level data

Dopo aver evlarmente elevato l'accesso, è possibile assegnare le autorizzazioni appropriate a un account in modo che possa eseguire query sui dati del log attività a livello di tenant. Questo account dovrà disporre del ruolo predefinito Lettore di [monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) assegnato nell'ambito radice del tenant di gestione.

> [!IMPORTANT]
> La concessione di un'assegnazione di ruolo nell'ambito radice significa che le stesse autorizzazioni verranno applicate a ogni risorsa nel tenant.

Poiché si tratta di un ampio livello di accesso, è consigliabile assegnare questo ruolo a un account dell'entità servizio, anziché a un singolo utente o a un gruppo. Inoltre, è consigliabile attenersi alle seguenti procedure consigliate:

- [Creare un nuovo account dell'entità servizio](../../active-directory/develop/howto-create-service-principal-portal.md) da utilizzare solo per questa funzione, anziché assegnare questo ruolo a un'entità servizio esistente usata per altre automazioni.
- Assicurarsi che questa entità servizio non abbia accesso alle risorse delegate dei clienti.
- [Usare un certificato per autenticarlo](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) e archiviarlo in modo sicuro in Archiviazione delle chiavi di Azure.Use a certificate to authenticate [and store it securely in Azure Key Vault](../../key-vault/general/best-practices.md).
- Limitare gli utenti che hanno accesso per agire per conto dell'entità servizio.

Utilizzare uno dei metodi seguenti per eseguire le assegnazioni dell'ambito radice.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Rimuovere l'accesso con privilegi elevati per l'account amministratore globale

Dopo aver creato l'account dell'entità servizio e aver assegnato il ruolo Lettore di monitoraggio nell'ambito radice, assicurarsi di [rimuovere l'accesso con privilegi elevati](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) per l'account amministratore globale, poiché questo livello di accesso non sarà più necessario.

## <a name="query-the-activity-log"></a>Interrogare il log attività

Dopo aver creato un nuovo account dell'entità servizio con accesso Lettore monitoraggio all'ambito radice del tenant di gestione, è possibile usarlo per eseguire query e creare report sull'attività di delega nel tenant. 

Questo script di [Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) può essere usato per eseguire query sugli ultimi 1 giorno di attività e report su eventuali deleghe aggiunte o rimosse (o tentativi non riusciti). Esegue una query sui dati del [log attività tenant,](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) quindi crea i valori seguenti per creare report sulle deleghe aggiunte o rimosse:

- **DelegatedResourceId:** ID della sottoscrizione delegata o del gruppo di risorse
- **CustomerTenantId**: L'ID tenant del cliente
- **CustomerSubscriptionId:** ID sottoscrizione delegata o contenente il gruppo di risorse delegato
- **CustomerDelegationStatus:** modifica dello stato della risorsa delegata (operazione non riuscita o non riuscita)
- **EventTimeStamp**: la data e l'ora in cui è stata registrata la modifica della delega

Quando si esegue una query su questi dati, tenere presente quanto segue:When querying this data, keep in mind:

- Se in una singola distribuzione vengono delegati più gruppi di risorse, verranno restituite voci separate per ogni gruppo di risorse.
- Le modifiche apportate a una delega precedente, ad esempio l'aggiornamento della struttura delle autorizzazioni, verranno registrate come delega aggiunta.
- Come indicato in precedenza, un account deve disporre del ruolo predefinito Lettore di monitoraggio nell'ambito radice (/) per accedere a questi dati a livello di tenant.
- È possibile utilizzare questi dati nei propri flussi di lavoro e report. Ad esempio, è possibile usare l'API agente di raccolta [dati HTTP (anteprima pubblica)](../../azure-monitor/platform/data-collector-api.md) per registrare i dati in Monitoraggio di Azure da un client DELL'API REST, quindi usare i [gruppi](../../azure-monitor/platform/action-groups.md) di azioni per creare notifiche o avvisi.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come eseguire l'onboarding dei clienti alla gestione delle risorse delegate di [Azure.](../concepts/azure-delegated-resource-management.md)
- Informazioni su [Monitoraggio di Azure](../../azure-monitor/index.yml) e sul log attività di [Azure.](../../azure-monitor/platform/platform-logs-overview.md)
