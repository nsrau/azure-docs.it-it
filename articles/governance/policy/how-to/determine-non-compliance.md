---
title: Determinare le cause di non conformità
description: Quando una risorsa non è conforme, i motivi possibili sono molti. Informazioni sulle possibili cause di non conformità.
ms.date: 05/20/2020
ms.topic: how-to
ms.openlocfilehash: 2b26357e9957259470049209913501cc024caeaa
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684237"
---
# <a name="determine-causes-of-non-compliance"></a>Determinare le cause di non conformità

Quando si determina che una risorsa di Azure non è conforme a una regola dei criteri, è utile comprendere a quale parte della regola la risorsa non è conforme. È anche importante conoscere quale modifica ha trasformato una risorsa precedentemente conforme in una risorsa non conforme. È possibile trovare queste informazioni in due modi:

> [!div class="checklist"]
> - [Dettagli di conformità](#compliance-details)
> - [Cronologia modifiche (anteprima)](#change-history)

## <a name="compliance-details"></a>Dettagli di conformità

Quando una risorsa non è conforme, è possibile trovare i dettagli di conformità della risorsa nella pagina **Conformità dei criteri**. Il riquadro dei dettagli di conformità include le informazioni seguenti:

- Dettagli della risorsa, ad esempio nome, tipo, posizione e ID risorsa
- Stato di conformità e timestamp dell'ultima valutazione per l'assegnazione dei criteri corrente
- Elenco dei _motivi_ per cui la risorsa non è conforme

> [!IMPORTANT]
> Poiché i dettagli di conformità per una risorsa _Non conforme_ indicano il valore corrente delle proprietà di tale risorsa, è necessario che l'utente disponga dell'operazione di **lettura** per la proprietà **type** della risorsa. Se ad esempio la risorsa _Non conforme_ è **Microsoft.Compute/virtualMachines**, l'utente deve disporre dell'operazione **Microsoft.Compute/virtualMachines/read**. Se l'utente non dispone dell'operazione necessaria, viene visualizzato un errore di accesso.

Per visualizzare i dettagli di conformità, attenersi alla procedura seguente:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nella pagina **Panoramica** o **Conformità** selezionare un criterio il cui **stato di conformità** sia _Non conforme_.

1. Nella scheda **Conformità risorsa** della pagina **Conformità dei criteri** fare clic con il pulsante destro del mouse o selezionare i puntini di sospensione di una risorsa il cui **stato di conformità** sia _Non conforme_. Quindi selezionare **Visualizzare i dettagli sulla conformità**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Opzione Visualizzare i dettagli sulla conformità" border="false":::

1. Il riquadro **Dettagli conformità** visualizza le informazioni della valutazione più recente della risorsa rispetto all'assegnazione dei criteri corrente. In questo esempio il valore del campo **Microsoft.Sql/servers/version** è _12.0_ mentre la definizione del criterio prevedeva _14.0_. Se la risorsa non è conforme per più motivi, in questo riquadro vengono elencati tutti i motivi.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Riquadro Dettagli conformità e motivi di non conformità" border="false":::

   Per una definizione dei criteri **auditIfNotExists** o **deployIfNotExists**, i dettagli includono la proprietà **details.type** e tutte le proprietà facoltative. Per un elenco, vedere [Proprietà di AuditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [Proprietà di DeployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Ultima risorsa valutata** collega a una risorsa correlata della sezione dei **dettagli** della definizione.

   Esempio parziale della definizione di **deployIfNotExists**:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Riquadro Dettagli conformità - *ifNotExists" border="false":::

> [!NOTE]
> Per proteggere i dati, quando il valore di una proprietà è _segreto_, il valore corrente visualizza gli asterischi.

Questi dettagli spiegano il motivo per cui una risorsa non è attualmente conforme, ma non indicano quando è stata apportata alla risorsa la modifica che l'ha resa non conforme. Per questo tipo di informazioni, vedere la sezione [Cronologia modifiche (anteprima)](#change-history) più avanti.

### <a name="compliance-reasons"></a>Motivi di conformità

Nella matrice seguente viene eseguito il mapping di ogni possibile _motivo_ relativamente alla [condizione ](../concepts/definition-structure.md#conditions) responsabile nella definizione dei criteri:

|Motivo | Condizione |
|-|-|
|Il valore corrente deve contenere il valore di destinazione come chiave. |containsKey o **not** notContainsKey |
|Il valore corrente deve contenere il valore di destinazione. |contains o **not** notContains |
|Il valore corrente deve essere uguale al valore di destinazione. |equals o **not** notEquals |
|Il valore corrente deve essere minore del valore di destinazione. |less o **not** greaterOrEquals |
|Il valore corrente deve essere maggiore o uguale al valore di destinazione. |greaterOrEquals o **not** less |
|Il valore corrente deve essere maggiore del valore di destinazione. |greater o **not** lessOrEquals |
|Il valore corrente deve essere minore o uguale al valore di destinazione. |lessOrEquals o **not** greater |
|Il valore corrente deve esistere. |esiste |
|Il valore corrente deve essere nel valore di destinazione. |in o **not** notIn |
|Il valore corrente deve essere uguale al valore di destinazione. |like o **not** notLike |
|Il valore corrente deve corrispondere al valore di destinazione con distinzione tra maiuscole/minuscole. |match o **not** notMatch |
|Il valore corrente deve corrispondere al valore di destinazione senza distinzione tra maiuscole/minuscole. |matchInsensitively o **not** notMatchInsensitively |
|Il valore corrente non deve contenere il valore di destinazione come chiave. |notContainsKey o **not** containsKey|
|Il valore corrente non deve contenere il valore di destinazione. |notContains o **not** contains |
|Il valore corrente non deve essere uguale al valore di destinazione. |notEquals o **not** equals |
|Il valore corrente non deve esistere. |**not** exists  |
|Il valore corrente non deve essere nel valore di destinazione. |notIn o **not** in |
|Il valore corrente non deve essere uguale al valore di destinazione. |notLike o **not** like |
|Il valore corrente non deve corrispondere al valore di destinazione con distinzione tra maiuscole/minuscole. |notMatch o **not** match |
|Il valore corrente non deve corrispondere al valore di destinazione senza distinzione tra maiuscole/minuscole. |notMatchInsensitively o **not** matchInsensitively |
|Non esistono risorse correlate corrispondenti ai dettagli dell'effetto nella definizione dei criteri. |Non esiste una risorsa del tipo definito in **then.details.type** e relativa alla risorsa definita nella parte **if** della regola dei criteri. |

## <a name="compliance-details-for-guest-configuration"></a>Dettagli di conformità per la configurazione guest

Per i criteri _auditIfNotExists_ nella categoria _Configurazione guest_, è possibile che vengano valutate più impostazioni nella macchina virtuale e che sia necessario visualizzare i dettagli per ogni impostazione. Ad esempio, se si esegue il controllo di un elenco di criteri password e solo per uno di essi lo stato è _Non conforme_, è necessario stabilire quali criteri password specifici non sono conformi e il motivo della non conformità.

Potrebbe non essere possibile accedere direttamente alla macchina virtuale. È tuttavia necessario segnalare il motivo per cui lo stato della macchina virtuale è _Non conforme_.

### <a name="azure-portal"></a>Portale di Azure

Per iniziare, seguire la stessa procedura descritta nella sezione precedente per visualizzare i dettagli di conformità dei criteri.

Nel riquadro Dettagli conformità fare clic sul collegamento **Ultima risorsa valutata**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Visualizzare i dettagli della definizione di auditIfNotExists" border="false":::

Nella pagina **Assegnazione guest** vengono visualizzati tutti i dettagli di conformità disponibili. Ogni riga della vista rappresenta una valutazione eseguita all'interno del computer. Nella colonna **Motivo** viene visualizzata una frase che descrive il motivo per cui l'assegnazione guest è _Non conforme_. Se ad esempio si controllano i criteri password, nella colonna **Motivo** verrà visualizzato il testo che include il valore corrente per ogni impostazione.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Visualizzare i dettagli di conformità" border="false":::

### <a name="azure-powershell"></a>Azure PowerShell

È anche possibile visualizzare i dettagli di conformità da Azure PowerShell. Assicurarsi prima di tutto che il modulo Configurazione guest sia installato.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

È possibile visualizzare lo stato corrente di tutte le assegnazioni guest per una macchina virtuale usando il comando seguente:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Per visualizzare solo la frase del _motivo_ che descrive la ragione per cui la macchina virtuale è _Non conforme_, restituire solo la proprietà figlio Reason.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

È anche possibile generare una cronologia di conformità per le assegnazioni guest nell'ambito del computer. L'output di questo comando include i dettagli di ogni report per la macchina virtuale.

> [!NOTE]
> L'output può restituire un volume elevato di dati. È consigliabile archiviare l'output in una variabile.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Per semplificare questa visualizzazione, usare il parametro **ShowChanged**. L'output di questo comando include solo i report che hanno riportato una modifica dello stato di conformità.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"/>Cronologia modifiche (anteprima)

La nuova **anteprima pubblica** include gli ultimi 14 giorni di cronologia delle modifiche per tutte le risorse di Azure che supportano l'[eliminazione in modalità completa](../../../azure-resource-manager/templates/complete-mode-deletion.md). La cronologia modifiche fornisce informazioni dettagliate su quando è stata rilevata una modifica e offre un _diff visivo_ per ogni modifica. Viene attivato un rilevamento delle modifiche ogni volta che vengono aggiunte, rimosse o modificate le proprietà di Resource Manager.

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nella pagina **Panoramica** o **Conformità** selezionare un criterio con uno **stato di conformità** qualsiasi.

1. Nella scheda **Conformità risorsa** della pagina **Conformità dei criteri** selezionare una risorsa.

1. Selezionare la scheda **Cronologia modifiche (anteprima)** nella pagina **Conformità risorsa**. Verrà visualizzato un elenco delle eventuali modifiche rilevate.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Scheda Cronologia modifiche di Criteri di Azure nella pagina Conformità risorsa" border="false":::

1. Selezionare una delle modifiche rilevate. Verrà visualizzato il _diff visivo_ per la risorsa nella pagina **Cronologia modifiche**.

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Diff visivo in Cronologia modifiche di Criteri di Azure nella pagina Cronologia modifiche" border="false":::

Il _diff visivo_ facilita l'identificazione delle modifiche di una risorsa. Le modifiche rilevate potrebbero non essere correlate allo stato di conformità corrente della risorsa.

I dati della cronologia delle modifiche vengono forniti da [Azure Resource Graph](../../resource-graph/overview.md). Per eseguire una query su queste informazioni all'esterno del portale di Azure, vedere [Ottenere le modifiche delle risorse](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [ottenere dati sulla conformità](get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
