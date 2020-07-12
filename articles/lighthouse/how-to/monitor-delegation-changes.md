---
title: Monitorare le modifiche della delega nel tenant di gestione
description: Informazioni su come monitorare l'attività di delega dai tenant del cliente al tenant di gestione.
ms.date: 07/10/2020
ms.topic: how-to
ms.openlocfilehash: 63b19f56538f060a158fd665a9bef3bf43a9d087
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252284"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Monitorare le modifiche della delega nel tenant di gestione

Come provider di servizi, è opportuno tenere presente quando le sottoscrizioni dei clienti o i gruppi di risorse vengono delegati al tenant tramite [Azure Lighthouse](../overview.md)o quando le risorse delegate in precedenza vengono rimosse.

Nel tenant di gestione, il [log attività di Azure](../../azure-monitor/platform/platform-logs-overview.md) tiene traccia dell'attività di delega a livello di tenant. Questa attività registrata include tutte le deleghe aggiunte o rimosse da tutti i tenant del cliente.

In questo argomento vengono illustrate le autorizzazioni necessarie per monitorare l'attività di delega al tenant (in tutti i clienti) e le procedure consigliate per eseguire questa operazione. Include anche uno script di esempio che mostra un metodo per l'esecuzione di query e la creazione di report su questi dati.

> [!IMPORTANT]
> Tutti questi passaggi devono essere eseguiti nel tenant di gestione, anziché in tutti i tenant del cliente.

## <a name="enable-access-to-tenant-level-data"></a>Abilitare l'accesso ai dati a livello di tenant

Per accedere ai dati del log attività a livello di tenant, a un account deve essere assegnato il ruolo predefinito [lettore monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) nell'ambito radice (/). Questa assegnazione deve essere eseguita da un utente che dispone del ruolo di amministratore globale con accesso con privilegi elevati.

### <a name="elevate-access-for-a-global-administrator-account"></a>Elevare l'accesso per un account amministratore globale

Per assegnare un ruolo all'ambito radice (/), sarà necessario disporre del ruolo di amministratore globale con accesso con privilegi elevati. Questo accesso con privilegi elevati deve essere aggiunto solo quando è necessario eseguire l'assegnazione di ruolo, quindi rimuovere al termine dell'operazione.

Per istruzioni dettagliate sull'aggiunta e la rimozione dell'elevazione, vedere [elevare l'accesso per gestire tutti i gruppi di gestione e le sottoscrizioni di Azure](../../role-based-access-control/elevate-access-global-admin.md).

Dopo aver innalzato di livello l'accesso, l'account avrà il ruolo di amministratore accesso utenti in Azure nell'ambito radice. Questa assegnazione di ruolo consente di visualizzare tutte le risorse e di assegnare l'accesso in qualsiasi sottoscrizione o gruppo di gestione nella directory, nonché di effettuare assegnazioni di ruolo nell'ambito radice. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Creare un nuovo account dell'entità servizio per accedere ai dati a livello di tenant

Una volta che l'accesso è stato elevato, è possibile assegnare le autorizzazioni appropriate a un account in modo che sia in grado di eseguire query sui dati del log attività a livello di tenant. Questo account deve avere il ruolo predefinito [lettore di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) assegnato all'ambito radice del tenant di gestione.

> [!IMPORTANT]
> La concessione di un'assegnazione di ruolo nell'ambito radice significa che le stesse autorizzazioni verranno applicate a tutte le risorse nel tenant.

Poiché si tratta di un ampio livello di accesso, è consigliabile assegnare questo ruolo a un account dell'entità servizio, anziché a un singolo utente o a un gruppo. È inoltre consigliabile eseguire le procedure consigliate seguenti:

- [Creare un nuovo account dell'entità servizio](../../active-directory/develop/howto-create-service-principal-portal.md) da usare solo per questa funzione, invece di assegnare questo ruolo a un'entità servizio esistente usata per l'automazione.
- Assicurarsi che questa entità servizio non disponga dell'accesso alle risorse dei clienti Delegate.
- [Usare un certificato per autenticare](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in) e [archiviarlo in modo sicuro in Azure Key Vault](../../key-vault/general/best-practices.md).
- Limitare gli utenti che dispongono dell'accesso per agire per conto dell'entità servizio.

Usare uno dei metodi seguenti per effettuare le assegnazioni dell'ambito radice.

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

Dopo aver creato l'account dell'entità servizio e assegnato il ruolo di lettore di monitoraggio nell'ambito radice, assicurarsi di [rimuovere l'accesso con privilegi elevati](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) per l'account amministratore globale, in quanto questo livello di accesso non sarà più necessario.

## <a name="query-the-activity-log"></a>Eseguire una query sul log attività

Dopo aver creato un nuovo account dell'entità servizio con il monitoraggio dell'accesso del lettore all'ambito radice del tenant di gestione, è possibile usarlo per eseguire query e creare report sulle attività di delega nel tenant. 

[Questo script di Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) può essere usato per eseguire una query dell'ultimo giorno di attività e per i report sulle deleghe aggiunte o rimosse o sui tentativi non riusciti. Esegue una query sui dati del [log attività del tenant](/rest/api/monitor/TenantActivityLogs/List) , quindi costruisce i valori seguenti per generare report sulle deleghe aggiunte o rimosse:

- **DelegatedResourceId**: ID della sottoscrizione o del gruppo di risorse delegato
- **CustomerTenantId**: ID tenant del cliente
- **CustomerSubscriptionId**: ID sottoscrizione delegato o che contiene il gruppo di risorse delegato
- **CustomerDelegationStatus**: modifica dello stato per la risorsa delegata (SUCCEEDED o Failed)
- **EventTimeStamp**: data e ora in cui è stata registrata la modifica della delega

Quando si eseguono query su questi dati, tenere presente quanto segue:

- Se più gruppi di risorse vengono delegati in una singola distribuzione, verranno restituite voci separate per ogni gruppo di risorse.
- Le modifiche apportate a una delega precedente, ad esempio l'aggiornamento della struttura delle autorizzazioni, verranno registrate come delega aggiuntiva.
- Come indicato in precedenza, un account deve avere il ruolo predefinito lettore monitoraggio nell'ambito radice (/) per accedere ai dati a livello di tenant.
- È possibile usare questi dati nei flussi di lavoro e nella creazione di report personalizzati. Ad esempio, è possibile usare l' [API dell'agente di raccolta dati http (anteprima pubblica)](../../azure-monitor/platform/data-collector-api.md) per registrare i dati in monitoraggio di Azure da un client dell'API REST, quindi usare i [gruppi di azioni](../../azure-monitor/platform/action-groups.md) per creare notifiche o avvisi.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come caricare i clienti nel [Faro di Azure](../concepts/azure-delegated-resource-management.md).
- Informazioni su [monitoraggio di Azure](../../azure-monitor/index.yml) e [log attività di Azure](../../azure-monitor/platform/platform-logs-overview.md).
