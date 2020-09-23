---
title: Ottenere i dati di conformità ai criteri
description: Le valutazioni e gli effetti di Criteri di Azure determinano la conformità. Informazioni su come ottenere informazioni dettagliate sulle risorse di Azure.
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2ab75bdab0dcf910da91eb60b5f0cf23892d6c51
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895430"
---
# <a name="get-compliance-data-of-azure-resources"></a>Ottenere i dati di conformità delle risorse di Azure

Uno dei maggiori vantaggi di Criteri di Azure è rappresentato dalle informazioni dettagliate e dai controlli che offre sulle risorse in una sottoscrizione o in un [gruppo di gestione](../../management-groups/overview.md) di sottoscrizioni. Questi controlli possono essere esercitati in molti modi diversi, ad esempio impedendo la creazione di risorse nel percorso errato, imponendo un uso dei tag coerente e comune o controllando che per le risorse esistenti ci siano configurazioni e impostazioni appropriate. In tutti i casi i dati vengono generati da Criteri di Azure per consentire di comprendere lo stato di conformità dell'ambiente in uso.

Esistono diversi modi per accedere alle informazioni sulla conformità generate dalle assegnazioni di criteri e iniziative:

- Usare il [portale di Azure](#portal)
- Tramite script da [riga di comando](#command-line)

Prima di esaminare i metodi disponibili per creare report sulla conformità, è opportuno comprendere quando vengono aggiornate le informazioni sulla conformità e la frequenza e gli eventi che attivano un ciclo di valutazione.

> [!WARNING]
> Se lo stato di conformità viene segnalato come **Non registrato**, verificare che il provider di risorse **Microsoft.PolicyInsights** sia registrato e che l'utente abbia le autorizzazioni di controllo degli accessi in base al ruolo appropriate, come descritto in [Controllo degli accessi in base al ruolo in Criteri di Azure](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Trigger di valutazione

I risultati di un ciclo di valutazione completato sono disponibili nel provider di risorse `Microsoft.PolicyInsights` attraverso le operazioni `PolicyStates` e `PolicyEvents`. Per altre informazioni sulle operazioni dell'API REST di Azure Policy Insights, vedere [Azure Policy Insights](/rest/api/policy-insights/).

Le valutazioni delle iniziative e dei criteri assegnati sono il risultato di diversi eventi:

- Un criterio o un'iniziativa è stata appena assegnata a un ambito. L'applicazione dell'assegnazione all'ambito definito richiede circa 30 minuti. Una volta applicato, il ciclo di valutazione inizia per le risorse all'interno di tale ambito rispetto al criterio o all'iniziativa appena assegnata e a seconda degli effetti usati dal criterio o dall'iniziativa, le risorse sono contrassegnate come conformi, non conformi o esenti. La valutazione di un criterio o un'iniziativa estesa rispetto a un ambito di risorse di grandi dimensioni può richiedere tempo. Di conseguenza, non esiste un tempo predefinito di completamento di un ciclo di valutazione. Dopo il completamento, i risultati di conformità aggiornati sono disponibili nel portale e negli SDK.

- Un criterio o un'iniziativa già assegnata a un ambito viene aggiornata. Il ciclo di valutazione e la tempistica per questo scenario sono gli stessi di quelli per una nuova assegnazione a un ambito.

- Una risorsa viene distribuita o aggiornata all'interno di un ambito con un'assegnazione tramite Azure Resource Manager, l'API REST o un SDK supportato. In questo scenario l'evento di effetto (Append, Audit, Deny, Deploy) e le informazioni sullo stato conforme per la singola risorsa diventano disponibili nel portale e negli SDK dopo circa 15 minuti. Questo evento non causa una valutazione di altre risorse.

- Viene creata, aggiornata o eliminata un' [esenzione dei criteri](../concepts/exemption-structure.md) . In questo scenario, l'assegnazione corrispondente viene valutata per l'ambito di esenzione definito.

- Ciclo di valutazione della conformità standard. Le assegnazioni vengono automaticamente rivalutate ogni 24 ore. Un criterio o un'iniziativa estesa di molte risorse può richiedere tempo, pertanto non è prevedibile quando verrà completato il ciclo di valutazione. Dopo il completamento, i risultati di conformità aggiornati sono disponibili nel portale e negli SDK.

- Il provider di risorse [configurazione guest](../concepts/guest-configuration.md) viene aggiornato con i dettagli relativi alla conformità da una risorsa gestita.

- Analisi su richiesta

### <a name="on-demand-evaluation-scan"></a>Analisi di valutazione su richiesta

È possibile avviare un'analisi di valutazione per una sottoscrizione o un gruppo di risorse con l'interfaccia della riga di comando di Azure, Azure PowerShell o una chiamata all'API REST. Questa analisi è un processo asincrono.

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Analisi di valutazione su richiesta-interfaccia della riga di comando di Azure

L'analisi di conformità viene avviata con il comando [AZ Policy state trigger-Scan](/cli/azure/policy/state#az-policy-state-trigger-scan) .

Per impostazione predefinita, `az policy state trigger-scan` avvia una valutazione per tutte le risorse nella sottoscrizione corrente. Per avviare una valutazione in un gruppo di risorse specifico, usare il parametro **Resource-Group** . Nell'esempio seguente viene avviata un'analisi di conformità nella sottoscrizione corrente per il gruppo di risorse _MyRG_:

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

È possibile scegliere di non attendere il completamento del processo asincrono prima di continuare con il parametro **No-wait** .

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Analisi della valutazione su richiesta - Azure PowerShell

L'analisi di conformità viene avviata con il cmdlet [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan).

Per impostazione predefinita, `Start-AzPolicyComplianceScan` avvia una valutazione per tutte le risorse nella sottoscrizione corrente. Per avviare una valutazione in un gruppo di risorse specifico, usare il parametro **ResourceGroupName**. Nell'esempio seguente viene avviata un'analisi di conformità nella sottoscrizione corrente per il gruppo di risorse _MyRG_:

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

È possibile fare in modo che PowerShell attenda il completamento della chiamata asincrona prima di fornire l'output dei risultati o che venga eseguito in background come [processo](/powershell/module/microsoft.powershell.core/about/about_jobs). Per usare un processo di PowerShell per eseguire l'analisi della conformità in background, usare il parametro **AsJob** e impostare il valore su un oggetto, come `$job` nell'esempio seguente:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

È possibile controllare lo stato del processo controllando l'oggetto `$job`. Il processo è di tipo `Microsoft.Azure.Commands.Common.AzureLongRunningJob`. Usare `Get-Member` sull'oggetto `$job` per visualizzare le proprietà e i metodi disponibili.

Durante l'esecuzione dell'analisi di conformità, il controllo dell'oggetto `$job` restituisce i risultati seguenti:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Al termine dell'analisi di conformità, la proprietà **Stato** viene modificata in _Completato_.

#### <a name="on-demand-evaluation-scan---rest"></a>Analisi di valutazione su richiesta - REST

Poiché si tratta di un processo asincrono, l'endpoint REST per avviare l'analisi non attende finché l'analisi non è stata completata per rispondere. Invece, fornisce un URI per eseguire una query dello stato della valutazione richiesta.

In ogni URI dell'API REST vengono usate variabili che è necessario sostituire con i propri valori:

- `{YourRG}`: sostituire con il nome del gruppo di risorse
- `{subscriptionId}`: sostituire con l'ID sottoscrizione

L'analisi supporta la valutazione delle risorse in una sottoscrizione o in un gruppo di risorse. Avviare un'analisi per ambito con un comando **POST** dell'API REST usando le strutture URI seguenti:

- Subscription

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Resource group

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

La chiamata restituisce uno stato **202-Accepted**. L'intestazione della risposta include una proprietà **Posizione** con il formato seguente:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` viene generato in modo statico per l'ambito richiesto. Se un ambito sta già eseguendo un'analisi su richiesta, non viene avviata una nuova analisi. Al contrario, alla nuova richiesta viene fornito lo stesso URI `{ResourceContainerGUID}` di **posizione** per lo stato. Un comando **GET** dell'API REST per l'URI di **Posizione** restituisce un **202-accepted** mentre è in corso la valutazione. Quando l'analisi di valutazione è stata completata, restituisce uno stato **200 OK**. Il corpo di un'analisi completata è una risposta JSON con lo stato:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Funzionamento della conformità

In un'assegnazione, una risorsa **non è conforme** se non segue le regole di criteri o di iniziativa e non è _esente_. La tabella seguente illustra il funzionamento dei diversi effetti dei criteri in base alla valutazione della condizione per lo stato di conformità risultante:

| Stato della risorsa | Effetto | Valutazione dei criteri | Stato di conformità |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nuovo | Audit, AuditIfNotExist\* | True | Non conforme |
| Nuovo | Audit, AuditIfNotExist\* | False | Conforme |

\* Per gli effetti Modify, Append, DeployIfNotExist e AuditIfNotExist è necessario che l'istruzione IF sia TRUE. Richiedono inoltre che la condizione di esistenza sia FALSE per lo stato non conforme. Se è TRUE, la condizione IF attiva la valutazione della condizione di esistenza per le risorse correlate.

Ad esempio, si supponga di avere un gruppo di risorse, ContosoRG, con alcuni account di archiviazione (evidenziati in rosso) esposti su reti pubbliche.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagramma degli account di archiviazione esposti alle reti pubbliche nel gruppo di risorse contoso R G." border="false":::
   Diagramma che mostra le immagini per cinque account di archiviazione nel gruppo di risorse contoso R G.  Gli account di archiviazione uno e tre sono blu, mentre gli account di archiviazione due, quattro e cinque sono rossi.
:::image-end:::

In questo esempio, è necessario essere ben consapevoli dei rischi di sicurezza. Ora che è stata creata un'assegnazione di criteri, questa viene valutata per tutti gli account di archiviazione inclusi e non esenti nel gruppo di risorse ContosoRG. Controlla i tre account di archiviazione non conformi, modificandone di conseguenza lo stato impostandolo su **Non conforme**.

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagramma della conformità dell'account di archiviazione nel gruppo di risorse contoso R G." border="false":::
   Diagramma che mostra le immagini per cinque account di archiviazione nel gruppo di risorse contoso R G. Gli account di archiviazione uno e tre ora hanno segni di spunta verdi sotto di essi, mentre gli account di archiviazione due, quattro e cinque ora hanno segni di avviso rossi sotto di essi.
:::image-end:::

Oltre ai criteri **conformi** e **non conformi**, i criteri e le risorse hanno altri quattro stati:

- **Esenzione**: la risorsa è nell'ambito di un'assegnazione, ma ha un' [esenzione definita](../concepts/exemption-structure.md).
- In **conflitto**: sono presenti due o più definizioni di criteri con regole in conflitto. Ad esempio, due definizioni aggiungono lo stesso tag con valori diversi.
- **Non avviato**: il ciclo di valutazione per i criteri o la risorsa non è stato avviato.
- **Non registrato**: il provider di risorse di Criteri di Azure non è stato registrato o l'account connesso non è autorizzato a leggere i dati di conformità.

Criteri di Azure usa i campi **tipo** e **nome** nella definizione per determinare se una risorsa corrisponde. Quando la risorsa corrisponde, viene considerata applicabile e presenta uno stato **conforme**, **non conforme**o **esentato**. Se il **tipo** o il **nome** è l'unica proprietà nella definizione, tutte le risorse incluse e non esenti vengono considerate applicabili e vengono valutate.

La percentuale di conformità viene determinata dividendo le risorse **conformi** ed **esenti** dalle _risorse totali_. _Il totale delle risorse_ è definito come la somma delle risorse **conformi**, **non conformi**, **esentate**e in **conflitto** . I numeri di conformità generali sono la somma delle risorse distinte **conformi** o **esentate** divise per la somma di tutte le risorse distinte. Nell'immagine seguente sono presenti 20 risorse distinte applicabili, di cui una sola **Non conforme**.
La conformità complessiva delle risorse è pari al 95% (19 su 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Screenshot dei dettagli di conformità dei criteri dalla pagina conformità." border="false":::

> [!NOTE]
> La conformità alle normative in criteri di Azure è una funzionalità in anteprima. Le proprietà di conformità da SDK e pagine del portale sono diverse per le iniziative abilitate. Per ulteriori informazioni, vedere [conformità normativa](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portale

Il portale di Azure illustra un'esperienza grafica di visualizzazione e comprensione dello stato di conformità nell'ambiente in uso. Nella pagina **Criteri** l'opzione **Panoramica** fornisce i dettagli per gli ambiti disponibili sulla conformità dei criteri e delle iniziative. Oltre allo stato di conformità e al conteggio per assegnazione, contiene un grafico che mostra la conformità negli ultimi sette giorni. La pagina **Conformità** contiene buona parte di queste stesse informazioni (eccetto il grafico), ma fornisce altre opzioni di ordinamento e applicazione di filtri.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Screenshot della pagina conformità, opzioni di filtro e dettagli." border="false":::

Dal momento che un criterio o un'iniziativa può essere assegnata a diversi ambiti, la tabella include l'ambito di ogni assegnazione e il tipo di definizione assegnato. È anche indicato il numero di risorse non conformi e di criteri non conformi per ogni assegnazione. La selezione di un criterio o di un'iniziativa nella tabella consente di esaminare in modo più approfondito la conformità per quella particolare assegnazione.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Screenshot della pagina dei dettagli di conformità, compresi i conteggi e i dettagli conformi alle risorse." border="false":::

L'elenco delle risorse nella scheda **Conformità risorsa** mostra lo stato di valutazione delle risorse esistenti per l'assegnazione corrente. Il valore predefinito della scheda è **Non conforme**, ma è possibile applicare un filtro.
Gli eventi (Append, Audit, Deny, Deploy) attivati dalla richiesta di creazione di una risorsa sono visualizzati nella scheda **Eventi**.

> [!NOTE]
> Per i criteri del motore del servizio Azure Kubernetes, la risorsa mostrata è il gruppo di risorse.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Screenshot della scheda eventi nella pagina Dettagli conformità." border="false":::

Per le risorse della [Modalità del provider di risorse](../concepts/definition-structure.md#resource-provider-modes), nella scheda **Conformità risorse** selezionare la risorsa o fare clic con il pulsante destro del mouse sulla riga e selezionare **Visualizza dettagli conformità** per aprire i dettagli di conformità dei componenti. Questa pagina include anche le schede per visualizzare i criteri assegnati a questa risorsa, eventi, eventi del componente e cronologia delle modifiche.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Screenshot della scheda conformità componenti e dei dettagli di conformità per un'assegnazione in modalità provider di risorse." border="false":::

Tornare alla pagina di conformità delle risorse e fare clic con il pulsante destro del mouse sulla riga dell'evento per cui si vogliono raccogliere maggiori dettagli, quindi selezionare **Mostra log attività**. La pagina del log attività viene aperta e pre-filtrata con la ricerca contenente i dettagli per l'assegnazione e gli eventi. Il log attività offre un contesto aggiuntivo e altre informazioni su tali eventi.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Screenshot del log attività per le attività e le valutazioni di criteri di Azure." border="false":::

### <a name="understand-non-compliance"></a>Comprendere la mancata conformità

Quando una risorsa **non è conforme**, i motivi possibili sono molti. Per determinare il motivo per cui una risorsa **non è conforme** o per trovare la modifica responsabile, vedere [Determinare la non conformità](./determine-non-compliance.md).

## <a name="command-line"></a>Riga di comando

Le stesse informazioni disponibili nel portale possono essere recuperate con l'API REST (incluso con [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell e l'interfaccia della riga di comando di Azure. Per tutti i dettagli sull'API REST, vedere le informazioni di riferimento su [Azure Policy Insights](/rest/api/policy-insights/). Le pagine di riferimento sull'API REST includono un pulsante verde "Prova", che consente di provare ogni operazione direttamente nel browser.

Usare ARMClient o uno strumento simile per gestire l'autenticazione in Azure per gli esempi di API REST.

### <a name="summarize-results"></a>Riepilogare i risultati

L'API REST consente di eseguire il riepilogo per contenitore, definizione o assegnazione. Di seguito è riportato un esempio di riepilogo a livello di sottoscrizione tramite il [riepilogo per sottoscrizione](/rest/api/policy-insights/policystates/summarizeforsubscription) di Azure Policy Insights:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
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
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Eseguire query per le risorse

Nell'esempio precedente, **value.policyAssignments.policyDefinitions.results.queryResultsUri** fornisce un URI di esempio per tutte le risorse non conformi per una definizione di criteri specifica. Esaminando il valore **$Filter** , ComplianceState è uguale (EQ) a' conforme ', viene specificato PolicyAssignmentId per la definizione dei criteri e quindi il PolicyDefinitionId stesso. Il motivo dell'inclusione di PolicyAssignmentId nel filtro è che PolicyDefinitionId potrebbe essere presente in diverse assegnazioni di criteri o iniziative con ambiti diversi. Specificando sia PolicyAssignmentId che PolicyDefinitionId, è possibile indicare in modo esplicito i risultati cercati. In precedenza per PolicyStates è stato usato **latest**, che imposta automaticamente una finestra temporale **from** e **to** delle ultime 24 ore.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Per brevità, la risposta di esempio seguente è stata tagliata in una singola risorsa non conforme. La risposta dettagliata contiene diversi dati che riguardano la risorsa, il criterio (o l'iniziativa) e l'assegnazione. Si noti che è anche possibile visualizzare quali parametri di assegnazione sono stati passati alla definizione dei criteri.

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
        "ComplianceState": "NonCompliant",
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
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
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

Per altre informazioni sull'esecuzione di query sugli eventi dei criteri, vedere l'articolo di riferimento [Azure Policy Events](/rest/api/policy-insights/policyevents) (Eventi di Criteri di Azure).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il gruppo di comandi dell' [interfaccia](/cli/azure/what-is-azure-cli) della riga di comando di Azure per criteri di Azure copre la maggior parte delle operazioni disponibili in REST o Azure PowerShell. Per l'elenco completo dei comandi disponibili, vedere interfaccia della riga di comando di [Azure-Panoramica di criteri di Azure](/cli/azure/policy).

Esempio: ottenere il riepilogo dello stato per i criteri più assegnati con il maggior numero di risorse non conformi.

```azurecli-interactive
az policy state summarize --top 1
```

La parte superiore della risposta ha un aspetto simile a questo esempio:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Esempio: ottenere il record di stato per la risorsa valutata più di recente (l'impostazione predefinita è per timestamp in ordine decrescente).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Esempio: ottenere i dettagli per tutte le risorse di rete virtuale non conformi.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Esempio: ottenere gli eventi correlati alle risorse di rete virtuale non conformi che si sono verificati dopo una data specifica.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

Il modulo Azure PowerShell per Criteri di Azure è disponibile in PowerShell Gallery come [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Grazie all'uso di PowerShellGet, è possibile installare il modulo usando `Install-Module -Name Az.PolicyInsights` (assicurarsi di avere la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps) installata):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Il modulo include i cmdlet seguenti:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Esempio: ottenere il riepilogo dello stato per i criteri più assegnati con il maggior numero di risorse non conformi.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Esempio: ottenere il record di stato per la risorsa valutata più di recente (l'impostazione predefinita è per timestamp in ordine decrescente).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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

Il campo **PrincipalOid** può essere usato per ottenere un utente specifico con il cmdlet `Get-AzADUser` di Azure PowerShell. Sostituire **{principalOid}** con la risposta ottenuta dall'esempio precedente.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

Se si dispone di un'[area di lavoro Log Analytics](../../../azure-monitor/log-query/log-query-overview.md) con `AzureActivity` dalla [soluzione di Analisi log attività](../../../azure-monitor/platform/activity-log.md) collegata alla sottoscrizione in uso, è anche possibile visualizzare i risultati di non conformità dal ciclo di valutazione usando semplici query Kusto e la tabella `AzureActivity`. Con i dettagli dei log di Monitoraggio di Azure è possibile configurare gli avvisi in modo da individuare le risorse non conformi.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Screenshot dei log di monitoraggio di Azure che illustrano le azioni dei criteri di Azure nella tabella AzureActivity." border="false":::

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
