---
title: Ottenere dati sulla conformità in Criteri di Azure
description: Le valutazioni e gli effetti di Criteri di Azure determinano la conformità. Informazioni su come ottenere informazioni dettagliate sulla conformità.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 3fa185e741f1b14bf3f2e7413945b70b1ea1baaa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970856"
---
# <a name="getting-compliance-data"></a>Ottenere dati sulla conformità

Uno dei maggiori vantaggi di Criteri di Azure è rappresentato dalle informazioni dettagliate e dai controlli che offre sulle risorse in una sottoscrizione o in un [gruppo di gestione](../../management-groups/overview.md) di sottoscrizioni. Questi controlli possono essere esercitati in molti modi diversi, ad esempio impedendo la creazione di risorse nel percorso errato, imponendo un uso dei tag coerente e comune o controllando che per le risorse esistenti ci siano configurazioni e impostazioni appropriate. In tutti i casi i dati vengono generati da Criteri per consentire di comprendere lo stato di conformità dell'ambiente in uso.

Esistono diversi modi per accedere alle informazioni sulla conformità generate dalle assegnazioni di criteri e iniziative:

- Tramite il [portale di Azure](#portal)
- Tramite script da [riga di comando](#command-line)

Prima di esaminare i metodi disponibili per creare report sulla conformità, è opportuno comprendere quando vengono aggiornate le informazioni sulla conformità e la frequenza e gli eventi che attivano un ciclo di valutazione.

> [!WARNING]
> Se lo stato di conformità viene segnalato come **Non registrato**, verificare che il provider di risorse **Microsoft.PolicyInsights** sia registrato e che l'utente abbia le autorizzazioni di controllo degli accessi in base al ruolo appropriate, come descritto [qui](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Trigger di valutazione

I risultati di un ciclo di valutazione completato si rispecchiano nel provider di risorse `Microsoft.PolicyInsights` attraverso le operazioni `PolicyStates` e `PolicyEvents`. Per altre informazioni sulle opzioni e le funzionalità dell'API REST di Policy Insights, vedere [Policy Insights](/rest/api/policy-insights/).

Le valutazioni delle iniziative e dei criteri assegnati sono il risultato di diversi eventi:

- Un criterio o un'iniziativa è stata appena assegnata a un ambito. In questo caso sono necessari circa 30 minuti prima che l'assegnazione venga applicata all'ambito definito. Dopo l'applicazione, ha inizio il ciclo di valutazione per le risorse in tale ambito sulla base del criterio o dell'iniziativa appena assegnata e, a seconda degli effetti usati dal criterio o dall'iniziativa, le risorse sono contrassegnate come conformi o non conformi. Un criterio o un'iniziativa estesa valutata rispetto a un ambito di risorse di grandi dimensioni può richiedere tempo, pertanto non è prevedibile quando verrà completato il ciclo di valutazione. Dopo il completamento, i risultati di conformità aggiornati sono disponibili nel portale e negli SDK.
- Un criterio o un'iniziativa già assegnata a un ambito viene aggiornata. Il ciclo di valutazione e la tempistica per questo scenario sono gli stessi di quelli per una nuova assegnazione a un ambito.
- Una risorsa viene distribuita a un ambito con un'assegnazione tramite Resource Manager, REST, l'interfaccia della riga di comando di Azure o Azure PowerShell. In questo scenario l'evento di effetto (Append, Audit, Deny, Deploy) e le informazioni sullo stato conforme per la singola risorsa diventano disponibili nel portale e negli SDK dopo circa 15 minuti. Questo evento non causa una valutazione di altre risorse.
- Ciclo di valutazione della conformità standard. Una volta ogni 24 ore, le assegnazioni vengono automaticamente rivalutate. Un criterio o un'iniziativa estesa valutata rispetto a un ambito di risorse di grandi dimensioni può richiedere tempo, pertanto non è prevedibile quando verrà completato il ciclo di valutazione. Dopo il completamento, i risultati di conformità aggiornati sono disponibili nel portale e negli SDK.

## <a name="how-compliance-works"></a>Funzionamento della conformità

In un'assegnazione, una risorsa risulta **Non conforme** se non segue le regole dei criteri o delle iniziative. La tabella seguente illustra il funzionamento dei diversi effetti dei criteri in base alla valutazione della condizione per lo stato di conformità risultante:

| Stato della risorsa | Effetto | Valutazione dei criteri | Stato di conformità |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True  | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nuovo | Audit, AuditIfNotExist\* | True  | Non conforme |
| Nuovo | Audit, AuditIfNotExist\* | False | Conforme |

\* Gli effetti Append, DeployIfNotExist e AuditIfNotExist richiedono che l'istruzione IF sia TRUE.
Richiedono inoltre che la condizione di esistenza sia FALSE per lo stato non conforme. Se è TRUE, la condizione IF attiva la valutazione della condizione di esistenza per le risorse correlate.

Ad esempio, si supponga di avere un gruppo di risorse, ContosoRG, con alcuni account di archiviazione (evidenziati in rosso) esposti su reti pubbliche.

![Account di archiviazione esposti su reti pubbliche](../media/getting-compliance-data/resource-group01.png)

In questo esempio, è necessario essere ben consapevoli dei rischi di sicurezza. Dopo avere creato un'assegnazione dei criteri, questa viene valutata per tutti gli account di archiviazione nel gruppo di risorse ContosoRG. Controlla i tre account di archiviazione non conformi, modificandone di conseguenza lo stato impostandolo su **Non conforme**.

![Account di archiviazione non conformi controllati](../media/getting-compliance-data/resource-group03.png)

Oltre a **Conforme** e **Non conforme**, i criteri e le risorse possono avere altri tre stati:

- **In conflitto**: sono presenti due o più criteri con regole in conflitto (ad esempio, due criteri che accodano lo stesso tag con valori diversi).
- **Non avviato**: il ciclo di valutazione per i criteri o la risorsa non è stato avviato.
- **Non registrato**: il provider di risorse di Criteri di Azure non è stato registrato o l'account connesso non è autorizzato a leggere i dati di conformità.

I criteri usano i campi **tipo** e **nome** nella definizione della regola dei criteri per determinare se una risorsa corrisponde. In caso affermativo, viene considerata applicabile e il suo stato viene impostato su **Conforme** o **Non conforme**. Se nella definizione della regola dei criteri la sola proprietà presente è **tipo** o **nome**, tutte le risorse sono considerate applicabili e vengono valutate.

La percentuale di conformità viene determinata dividendo le risorse **conformi** per il  _totale delle risorse_.
Per _totale delle risorse_ si intende la somma delle risorse **conformi**, **non conformi** e **in conflitto**. I valori di conformità complessiva corrispondono alla somma delle risorse distinte **conformi** divisa per la somma di tutte le risorse distinte. Nell'immagine seguente sono presenti 20 risorse distinte applicabili, di cui una sola **Non conforme**. La conformità complessiva delle risorse, pertanto, corrisponde a 19/20 ovvero al 95%.

![Esempio semplice di conformità](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portale

Il portale di Azure illustra un'esperienza grafica di visualizzazione e comprensione dello stato di conformità nell'ambiente in uso. Nella pagina **Criteri** l'opzione **Panoramica** fornisce i dettagli per gli ambiti disponibili sulla conformità dei criteri e delle iniziative. Oltre allo stato di conformità e al conteggio per assegnazione, contiene un grafico che mostra la conformità negli ultimi sette giorni. La pagina **Conformità** contiene buona parte di queste stesse informazioni (eccetto il grafico), ma fornisce altre opzioni di ordinamento e applicazione di filtri.

![Pagina Criteri - Conformità](../media/getting-compliance-data/compliance-page.png)

Dal momento che un criterio o un'iniziativa può essere assegnata a diversi ambiti, si noti che nella tabella sono riportati l'ambito per ogni assegnazione e il tipo di definizione assegnato a tale ambito. È anche indicato il numero di risorse non conformi e di criteri non conformi per ogni assegnazione. Quando si fa clic su un criterio o su un'iniziativa nella tabella, viene fornita un'analisi più approfondita della conformità per quella particolare assegnazione.

![Dettagli della conformità ai criteri](../media/getting-compliance-data/compliance-details.png)

L'elenco delle risorse nella scheda **Conformità risorsa** (il cui valore predefinito è **Non conforme** ma a cui può essere applicato un filtro) riflette lo stato di valutazione delle risorse esistenti per l'assegnazione corrente. Gli eventi (accodamento, controllo, negazione, distribuzione) attivati dalla richiesta di creazione di una risorsa sono visualizzati nella scheda **Eventi**.

![Eventi di conformità ai criteri](../media/getting-compliance-data/compliance-events.png)

Fare clic con il pulsante destro del mouse sulla riga dell'evento per cui si vogliono raccogliere maggiori dettagli e selezionare **Mostra log attività**. La pagina del log attività viene aperta e pre-filtrata con la ricerca contenente i dettagli per l'assegnazione e gli eventi. Il log attività offre un contesto aggiuntivo e altre informazioni su tali eventi.

![Log attività della conformità ai criteri](../media/getting-compliance-data/compliance-activitylog.png)

## <a name="command-line"></a>Riga di comando

Le stesse informazioni disponibili nel portale possono essere recuperate usando direttamente l'API REST (anche con [ARMClient](https://github.com/projectkudu/ARMClient)) o Azure PowerShell con l'API REST. Per tutti i dettagli sull'API REST, vedere le informazioni di riferimento su [Policy Insights](/rest/api/policy-insights/). Le pagine di riferimento sull'API REST includono un pulsante verde "Prova", che consente di provare ogni operazione direttamente nel browser.

Per usare gli esempi seguenti in Azure PowerShell, creare un token di autenticazione con questo codice di esempio. Sostituire quindi $restUri con la stringa desiderata negli esempi per recuperare un oggetto JSON che può quindi essere analizzato.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Riepilogare i risultati

Usando l'API REST, il riepilogo può essere eseguito tramite gruppo di gestione, sottoscrizione, gruppo di risorse, risorsa, iniziativa, criterio, assegnazione a livello di sottoscrizione o assegnazione a livello di gruppo di risorse. Di seguito è riportato un esempio di riepilogo a livello di sottoscrizione tramite il [riepilogo per sottoscrizione](/rest/api/policy-insights/policystates/summarizeforsubscription) di Policy Insights:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

L'output riepiloga la sottoscrizione. Nell'output di esempio seguente la conformità riepilogata è in corrispondenza di **value.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. La richiesta fornisce maggiori dettagli, tra cui ogni assegnazione che ha costituito i numeri non conformi e le informazioni sulla definizione per ogni assegnazione. Ogni oggetto criteri nella gerarchia fornisce un **queryResultsUri** che può essere usato per ottenere altri dettagli a quel livello.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Eseguire query per le risorse

Usando l'esempio precedente, **value.policyAssignments.policyDefinitions.results.queryResultsUri** ha fornito un Uri di esempio per ottenere tutte le risorse non conformi per una definizione di criteri specifici. Esaminando il valore **$filter**, IsCompliant è uguale (eq) a false, PolicyAssignmentId viene specificato per la definizione dei criteri e quindi PolicyDefinitionId stesso.
Il motivo per l'inclusione di PolicyAssignmentId nel filtro è che PolicyDefinitionId può essere presente in diverse assegnazioni di criteri o iniziative con una vasta gamma di ambiti. Specificando sia PolicyAssignmentId che PolicyDefinitionId, i risultati cercati possono essere espliciti. In precedenza, è stato usato **latest** per PolicyStates (l'unico valore consentito per **policyStatesSummaryResource** nell'operatore di riepilogo per sottoscrizione), che imposta automaticamente una finestra temporale **from** e **to** delle ultime 24 ore.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

La risposta di esempio riportata di seguito è stata troncata in modo da visualizzare solo una singola risorsa non conforme per ragioni di brevità (si noti che @odata.count è effettivamente pari a 15 e corrisponde al conteggio delle risorse non conformi dell'esempio precedente). La risposta dettagliata fornisce diversi dati che riguardano la risorsa, il criterio (o l'iniziativa) e l'assegnazione. Si noti che è anche possibile visualizzare quali parametri di assegnazione sono stati passati alla definizione dei criteri.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Visualizzare eventi

Quando si crea o si aggiorna una risorsa, viene generato un risultato di valutazione dei criteri. I risultati sono chiamati _eventi criteri_. Usare l'Uri seguente per visualizzare gli eventi criteri recenti associati alla sottoscrizione.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

I risultati saranno simili all'esempio seguente:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Per altre informazioni sull'esecuzione di query sugli eventi dei criteri, vedere l'articolo di riferimento [Policy Events](/rest/api/policy-insights/policyevents) (Eventi dei criteri).

### <a name="azure-powershell"></a>Azure PowerShell

Il modulo Azure PowerShell per Criteri non è disponibile in PowerShell Gallery come [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights). Grazie all'uso di PowerShellGet, è possibile installare il modulo usando `Install-Module -Name AzureRM.PolicyInsights` (assicurarsi di avere la versione più recente di [Azure PowerShell](/powershell/azure/install-azurerm-ps) installata):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

Il modulo ha tre cmdlet:

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Esempio: ottenere il riepilogo dello stato per i criteri più assegnati con il maggior numero di risorse non conformi.

```azurepowershell-interactive
PS> Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Esempio: ottenere il record di stato per la risorsa valutata più di recente (l'impostazione predefinita è per timestamp in ordine decrescente).

```azurepowershell-interactive
PS> Get-AzureRmPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Esempio: ottenere i dettagli per tutte le risorse di rete virtuale non conformi.

```azurepowershell-interactive
PS> Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Esempio: ottenere gli eventi correlati alle risorse di rete virtuale non conformi che si sono verificati dopo una data specifica.

```azurepowershell-interactive
PS> Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Il campo **PrincipalOid** può essere usato per ottenere un utente specifico con il cmdlet `Get-AzureRmADUser` di Azure PowerShell. Sostituire **{principalOid}** con la risposta ottenuta dall'esempio precedente.

```azurepowershell-interactive
PS> (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Se si dispone di un'area di lavoro di [Log Analytics](../../../log-analytics/log-analytics-overview.md) con la soluzione `AzureActivity` collegata alla sottoscrizione in uso, è anche possibile visualizzare i risultati di non conformità dal ciclo di valutazione usando semplici query Kusto e la tabella `AzureActivity`. Con i dettagli di non conformità in Log Analytics, questo vuol dire anche che è stato possibile configurare avvisi per controllare la non conformità per una risorsa, un gruppo di risorse o addirittura una soglia di elementi non conformi specifica, ad esempio più di 10 nelle ultime 24 ore.

![Conformità ai criteri con Log Analytics](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md)
- Vedere la [struttura delle definizioni dei criteri](../concepts/definition-structure.md)
- Vedere [Informazioni sugli effetti di Criteri](../concepts/effects.md)
- Informazioni su come [creare criteri a livello di programmazione](programmatically-create.md)
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md)
- Scoprire le caratteristiche di un gruppo di gestione con [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md)