---
title: Determinare le cause di non conformità
description: Se una risorsa non conforme, esistono numerose cause. Informazioni su come stabilire la causa la non conformità.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 2f856e9c42b26d4e286493e2eb5d019a8cff6c23
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868680"
---
# <a name="determine-causes-of-non-compliance"></a>Determinare le cause di non conformità

Quando una risorsa di Azure viene determinata come non conforme a una regola di criteri, è utile comprendere quale parte della regola della risorsa non è conforme. È anche utile comprendere quali modifiche modificato una risorsa prima era conforme per renderlo non conformi. Esistono due modi per trovare queste informazioni:

> [!div class="checklist"]
> - [Dettagli di conformità](#compliance-details)
> - [Cronologia delle modifiche (anteprima)](#change-history-preview)

## <a name="compliance-details"></a>Dettagli conformità

Quando si una risorsa è non conformi, i dettagli di conformità per quella risorsa sono disponibili i **conformità ai criteri** pagina. Il riquadro dei dettagli di conformità include le informazioni seguenti:

- Dettagli risorsa, ad esempio nome, tipo, posizione e ID risorsa
- Stato di conformità e il timestamp dell'ultima valutazione per l'assegnazione di criteri corrente
- Un elenco delle _motivi_ per le risorse non conformi

> [!IMPORTANT]
> Come i dettagli di conformità per un _Non conformi_ risorse Mostra il valore corrente della proprietà su tale risorsa, l'utente deve disporre **leggere** operazione per il **tipo** di risorsa. Ad esempio, se il _Non conformi_ risorsa è **COMPUTE/virtualmachines** quindi l'utente deve avere il **Microsoft.Compute/virtualMachines/read** operazione. Se l'utente non ha l'operazione necessaria, viene visualizzato un errore di accesso.

Per visualizzare i dettagli di conformità, seguire questa procedura:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nel **Overview** o **conformità** pagina, selezionare un criterio in un **lo stato di conformità** vale a dire _Non conforme_.

1. Sotto il **conformità risorsa** scheda della finestra di **la conformità ai criteri** pagina, pulsante destro del mouse o selezionare i puntini di sospensione di una risorsa in un **lo stato di conformità** vale a dire  _Non conforme_. Quindi selezionare **visualizzare i dettagli di conformità**.

   ![Opzione di dettagli conformità visualizzazione](../media/determine-non-compliance/view-compliance-details.png)

1. Il **dettagli di conformità** riquadro vengono visualizzate informazioni rispetto alla valutazione più recente della risorsa per l'assegnazione di criteri corrente. In questo esempio, il campo **Microsoft.Sql/servers/version** risulta _12.0_ mentre la definizione di criteri previsto _14.0_. Se la risorsa non conforme per diversi motivi, ognuno è elencato in questo riquadro.

   ![Riquadro dei dettagli di conformità e i motivi per la mancata conformità](../media/determine-non-compliance/compliance-details-pane.png)

   Per un **auditIfNotExists** oppure **deployIfNotExists** definizione dei criteri, i dettagli includono il **details.type** proprietà e le eventuali proprietà facoltative. Per un elenco, vedere [proprietà auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [deployIfNotExists proprietà](../concepts/effects.md#deployifnotexists-properties). **Ultima valutazione resource** è una risorsa correlata dal **dettagli** sezione della definizione.

   Esempio parziale **deployIfNotExists** definizione:

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

   ![Riquadro dei dettagli di conformità - * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Per proteggere i dati, quando un valore della proprietà è un _segreto_ il valore corrente visualizza degli asterischi.

Questi dettagli di spiegano il motivo per cui una risorsa è attualmente non conformi, ma non vengono visualizzati quando sono state modificate per la risorsa che ha causato lo rende non conformi. Per ulteriori informazioni, vedere [cronologia revisioni (anteprima)](#change-history-preview) sotto.

### <a name="compliance-reasons"></a>Motivi di conformità

La matrice seguente esegue il mapping di ogni possibile _motivo_ per il responsabile [condizione](../concepts/definition-structure.md#conditions) nella definizione dei criteri:

|`Reason` | Condizione |
|-|-|
|Il valore corrente deve contenere il valore di destinazione come chiave. |containsKey oppure **non** notContainsKey |
|Il valore corrente deve contenere il valore di destinazione. |contiene oppure **non** notContains |
|Il valore corrente deve essere uguale al valore di destinazione. |è uguale a o **non** notEquals |
|Il valore corrente deve esistere. |exists |
|Il valore corrente deve essere nel valore di destinazione. |in o **non** notIn |
|Il valore corrente deve essere uguale al valore di destinazione. |ad esempio oppure **non** notLike |
|Il valore corrente deve corrispondere al valore di destinazione con distinzione tra maiuscole/minuscole. |corrispondenza o **non** notMatch |
|Il valore corrente deve corrispondere al valore di destinazione senza distinzione tra maiuscole/minuscole. |matchInsensitively oppure **non** notMatchInsensitively |
|Il valore corrente non deve contenere il valore di destinazione come chiave. |notContainsKey oppure **non** containsKey|
|Il valore corrente non deve contenere il valore di destinazione. |notContains oppure **non** contiene |
|Il valore corrente non deve essere uguale al valore di destinazione. |notEquals oppure **non** è uguale a |
|Il valore corrente non deve esistere. |**non** esiste  |
|Il valore corrente non deve essere nel valore di destinazione. |notIn oppure **non** in |
|Il valore corrente non deve essere uguale al valore di destinazione. |notLike oppure **non** , ad esempio |
|Il valore corrente non deve corrispondere al valore di destinazione con distinzione tra maiuscole/minuscole. |notMatch oppure **non** corrispondono |
|Il valore corrente non deve corrispondere al valore di destinazione senza distinzione tra maiuscole/minuscole. |notMatchInsensitively oppure **non** matchInsensitively |
|Non esiste alcuna risorsa correlata corrispondente ai dettagli dell'effetto nella definizione dei criteri. |Una risorsa del tipo definito **then.details.type** correlata alla risorsa definita nel **se** parte della regola dei criteri non esiste. |

## <a name="compliance-details-for-guest-configuration"></a>Dettagli di conformità per la configurazione di Guest

Per _audit_ i criteri nel _Guest configurazione_ categoria, potrebbero esserci più impostazioni valutate all'interno della macchina virtuale e sarà necessario visualizzare i dettagli per ogni impostazione. Ad esempio, se si esegue il controllo per un elenco delle applicazioni installate e lo stato di assegnazione viene _Non conformi_, è necessario sapere quali applicazioni sono mancanti.

Inoltre non si dispone dell'accesso per accedere direttamente alla macchina virtuale ma è necessario eseguire segnalazioni su perché la macchina virtuale viene _Non conformi_. È ad esempio, potrebbe essere controllare che le macchine virtuali vengono aggiunti al dominio corretto e includono l'appartenenza al dominio corrente nei dettagli del report.

### <a name="azure-portal"></a>Portale di Azure

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nel **Overview** o **conformità** pagina, selezionare un'assegnazione di criteri per le iniziative che contiene una definizione di criteri di configurazione di Guest che _Non conforme_.

1. Selezionare un _audit_ dei criteri nell'iniziativa Ecco _Non conforme_.

   ![Visualizzare i dettagli di definizione di controllo](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Nel **conformità risorsa** scheda, viene fornite le informazioni seguenti:

   - **Nome** -il nome delle assegnazioni di configurazione di Guest.
   - **Risorsa padre** -la macchina virtuale in un _Non conforme_ dello stato per l'assegnazione del Guest configurazione selezionata.
   - **Tipo di risorsa** - il _guestConfigurationAssignments_ nome completo.
   - **Ultima valutazione** : l'ultima volta il servizio di configurazione Guest riceve una notifica sullo stato della macchina virtuale di destinazione i criteri di Azure.

   ![Visualizzare i dettagli sulla conformità.](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Selezionare il nome dell'assegnazione di configurazione Guest nel **Name** colonna per aprire il **conformità risorsa** pagina.

1. Selezionare il **risorsa vista** nella parte superiore della pagina per aprire il **Guest assegnazione** pagina.

Il **Guest assegnazione** pagina vengono visualizzati tutti i dettagli di conformità disponibili. Ogni riga nella visualizzazione rappresenta una versione di valutazione è stata eseguita all'interno della macchina virtuale. Nel **motivo** colonna, una frase che descrive il motivo per cui l'assegnazione del Guest _Non conforme_ viene visualizzato. Ad esempio, se si sta controllando che le macchine virtuali devono essere unite in join a un dominio, il **motivo** colonna potrebbe visualizzare il testo tra cui l'appartenenza al dominio corrente.

![Visualizzare i dettagli sulla conformità.](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

È anche possibile visualizzare i dettagli di conformità da Azure PowerShell. In primo luogo, assicurarsi di avere installato il modulo di configurazione di Guest.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

È possibile visualizzare lo stato corrente di tutte le assegnazioni di Guest per una macchina virtuale usando il comando seguente:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Per visualizzare solo le _motivo_ frase che descrive il motivo per cui la macchina virtuale sia _Non conforme_, restituire solo la proprietà figlio di motivo.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

È anche possibile inviare una cronologia di conformità per le assegnazioni di Guest nell'ambito per la macchina virtuale. L'output da questo comando include i dettagli di ogni report per la macchina virtuale.

> [!NOTE]
> L'output potrebbe restituire una quantità notevole di dati. È consigliabile archiviare l'output in una variabile.

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

Per semplificare questa visualizzazione, usare il **ShowChanged** parametro. L'output da questo comando include solo i report di seguito una modifica nello stato di conformità.

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

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Cronologia delle modifiche (anteprima)

Come parte di una nuova **versione di anteprima pubblica**, gli ultimi 14 giorni di cronologia delle modifiche sono disponibili per tutte le risorse di Azure che supportano [completare l'eliminazione di modalità](../../../azure-resource-manager/complete-mode-deletion.md). La cronologia modifiche fornisce informazioni dettagliate su quando è stata rilevata una modifica e offre un _diff visivo_ per ogni modifica. Un rilevamento delle modifiche viene attivato quando le proprietà di Resource Manager vengano aggiunto, rimosso o modificate.

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nel **Overview** o **conformità** pagina, selezionare un criterio in qualsiasi **stato di conformità**.

1. Sotto il **conformità risorsa** scheda della finestra di **conformità ai criteri** pagina, selezionare una risorsa.

1. Selezionare la scheda **Cronologia modifiche (anteprima)** nella pagina **Conformità risorsa**. Verrà visualizzato un elenco delle eventuali modifiche rilevate.

   ![Scheda Cronologia modifiche dei criteri nella pagina relativa alla conformità risorsa](../media/determine-non-compliance/change-history-tab.png)

1. Selezionare una delle modifiche rilevate. Il _diff visual_ per la risorsa viene presentata nella **cronologia delle modifiche** pagina.

   ![Diff Visual cronologia modifiche di criteri nella pagina cronologia modifiche](../media/determine-non-compliance/change-history-visual-diff.png)

Il _diff visivo_ facilita l'identificazione delle modifiche di una risorsa. Sono state rilevate le modifiche potrebbero non essere correlate per lo stato di conformità corrente della risorsa.

Dati della cronologia delle modifiche viene forniti da [Graph di Azure Resource](../../resource-graph/overview.md). Per eseguire query su queste informazioni all'esterno del portale di Azure, vedere [ottenere le modifiche alle risorse](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi nella [esempi di criteri di Azure](../samples/index.md).
- Vedere [Struttura delle definizioni di criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Comprendere come [a livello di codice, creare criteri](programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](getting-compliance-data.md).
- Informazioni su come [monitora e aggiorna le risorse non conformi](remediate-resources.md).
- Esaminare un gruppo di gestione riguarda [organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).