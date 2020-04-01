---
title: Determinare le cause di non conformità
description: Quando una risorsa non è conforme, esistono molti motivi possibili. Scopri cosa ha causato la non conformità.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "79264635"
---
# <a name="determine-causes-of-non-compliance"></a>Determinare le cause di non conformità

Quando una risorsa di Azure viene determinata come non conforme a una regola dei criteri, è utile comprendere la parte della regola con cui la risorsa non è conforme. È anche utile comprendere quale modifica ha modificato una risorsa conforme in precedenza per renderla non conforme. Esistono due modi per trovare queste informazioni:

> [!div class="checklist"]
> - [Dettagli di conformità](#compliance-details)
> - [Cronologia modifiche (anteprima)](#change-history)

## <a name="compliance-details"></a>Dettagli di conformità

Quando una risorsa non è conforme, i dettagli di conformità per tale risorsa sono disponibili nella pagina **Conformità criteri.** Il riquadro dei dettagli di conformità include le informazioni seguenti:The compliance details pane includes the following information:

- Dettagli risorsa, ad esempio nome, tipo, posizione e ID risorsa
- Stato di conformità e timestamp dell'ultima valutazione per l'assegnazione dei criteri corrente
- Elenco dei _motivi_ della non conformità della risorsa

> [!IMPORTANT]
> Poiché i dettagli di conformità per una risorsa _non conforme_ mostrano il valore corrente delle proprietà in tale risorsa, l'utente deve disporre dell'operazione di **lettura** per il **tipo** di risorsa. Ad esempio, se la risorsa _non conforme_ è **Microsoft.Compute/virtualMachines,** l'utente deve disporre dell'operazione **Microsoft.Compute/virtualMachines/read.** Se l'utente non dispone dell'operazione necessaria, viene visualizzato un errore di accesso.

Per visualizzare i dettagli di conformità, attenersi alla seguente procedura:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nella pagina **Panoramica** o **Conformità** selezionare un criterio in uno stato di **conformità** _non conforme._

1. Nella scheda **Conformità risorse** della pagina **Conformità criteri** fare clic con il pulsante destro del mouse o selezionare i lipsia di una risorsa in uno stato di **conformità** _non conforme._ Selezionare **quindi Visualizza dettagli conformità**.

   ![Opzione Visualizza dettagli conformità](../media/determine-non-compliance/view-compliance-details.png)

1. Nel riquadro **dei dettagli Conformità** vengono visualizzate le informazioni relative all'ultima valutazione della risorsa e all'assegnazione dei criteri corrente. In questo esempio, il campo **Microsoft.Sql/servers/version** è stato trovato a _12.0_ mentre la definizione dei criteri prevista _14.0_. Se la risorsa non è conforme per più motivi, ognuno è elencato in questo riquadro.

   ![Riquadro dei dettagli di conformità e motivi di non conformità](../media/determine-non-compliance/compliance-details-pane.png)

   Per una definizione dei criteri **auditIfNotExists** o **deployIfNotExists,** i dettagli includono la proprietà **details.type** ed eventuali proprietà facoltative. Per un elenco, vedere [proprietà auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [proprietà deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Ultima risorsa valutata** è una risorsa correlata dalla sezione **dei dettagli** della definizione.

   Esempio di definizione deployIfNotExists parziale di esempio:Example partial **deployIfNotExists** definition:

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

   ![Riquadro dei dettagli di conformità - ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Per proteggere i dati, quando il valore di una proprietà è un _segreto,_ il valore corrente visualizza asterischi.

Questi dettagli spiegano perché una risorsa è attualmente non conforme, ma non mostrano quando è stata apportata la modifica alla risorsa che ha causato la sua non conformità. Per queste informazioni, vedere [Cronologia delle modifiche (anteprima)](#change-history) di seguito.

### <a name="compliance-reasons"></a>Motivi di conformità

La matrice seguente esegue il mapping di ogni possibile _motivo_ alla condizione responsabile nella definizione dei criteri:The following matrix maps each possible reason to the responsible [condition](../concepts/definition-structure.md#conditions) in the policy definition:

|Motivo | Condizione |
|-|-|
|Il valore corrente deve contenere il valore di destinazione come chiave. |containsKey o **nonContainsKey** |
|Il valore corrente deve contenere il valore di destinazione. |contiene o **menocontiene** |
|Il valore corrente deve essere uguale al valore di destinazione. |è uguale o **non** uguali |
|Il valore corrente deve essere minore del valore di destinazione. |meno o **non** maggioreOrUguale |
|Il valore corrente deve essere maggiore o uguale al valore di destinazione. |greaterOrEquals o **non** meno |
|Il valore corrente deve essere maggiore del valore di destinazione. |maggiore **not** o menoOUguale |
|Il valore corrente deve essere minore o uguale al valore di destinazione. |lessOrEquals o **non** maggiore |
|Il valore corrente deve esistere. |esiste |
|Il valore corrente deve essere nel valore di destinazione. |in o **non** in |
|Il valore corrente deve essere simile al valore di destinazione. |come o **noCome** |
|Il valore corrente deve corrispondere tra maiuscole e minuscole al valore di destinazione. |corrispondenza o **non** corrispondente |
|Il valore corrente deve corrispondere senza distinzione tra maiuscole e minuscole al valore di destinazione. |matchInsensitively **not** o notMatchInsensitively |
|Il valore corrente non deve contenere il valore di destinazione come chiave. |notContainsKey o **non** containsKey|
|Il valore corrente non deve contenere il valore di destinazione. |notContiene o **non** contiene |
|Il valore corrente non deve essere uguale al valore di destinazione. |notEquals o **non** uguale a |
|Il valore corrente non deve esistere. |**non** esiste  |
|Il valore corrente non deve essere compreso nel valore di destinazione. |notIn o **non** in |
|Il valore corrente non deve essere simile al valore di destinazione. |nonCome o **non** piace |
|Il valore corrente non deve corrispondere tra maiuscole e minuscole al valore di destinazione. |notCorrisponde o **non** corrisponde |
|Il valore corrente non deve corrispondere tra maiuscole e minuscole al valore di destinazione. |notMatchInsensitively o **non** matchInsensitively |
|Nessuna risorsa correlata corrisponde ai dettagli dell'effetto nella definizione dei criteri. |Risorsa del tipo definito in **then.details.type** e correlata alla risorsa definita nella parte **se** la regola dei criteri non esiste. |

## <a name="compliance-details-for-guest-configuration"></a>Dettagli di conformità per la configurazione guest

Per i criteri _auditIfNotExists_ nella categoria _Configurazione guest,_ potrebbero essere previste più impostazioni all'interno della macchina virtuale e sarà necessario visualizzare i dettagli per impostazione. Ad esempio, se si esegue il controllo di un elenco di criteri password e solo uno di essi ha lo stato _Non conforme,_ è necessario sapere quali criteri password specifici non sono conformi e perché.

È anche possibile che non si abbia accesso direttamente alla macchina virtuale, ma è necessario creare report sul motivo per cui la macchina virtuale non è _conforme._

### <a name="azure-portal"></a>Portale di Azure

Iniziare seguendo gli stessi passaggi nella sezione precedente per visualizzare i dettagli di conformità dei criteri.

Nella visualizzazione del riquadro **dei dettagli Conformità** fare clic sul collegamento Ultima risorsa **valutata**.

   ![Visualizzare i dettagli della definizione auditIfNotExists](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

Nella pagina **Assegnazione ospite** vengono visualizzati tutti i dettagli di conformità disponibili. Ogni riga nella visualizzazione rappresenta una valutazione eseguita all'interno del computer. Nella colonna **Motivo** viene visualizzata una frase che descrive il motivo per cui l'assegnazione guest è _non conforme._ Ad esempio, se si stanno controllando i criteri password, nella colonna **Motivo** verrà visualizzato il testo che include il valore corrente per ogni impostazione.

![Visualizzare i dettagli di conformità](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

È anche possibile visualizzare i dettagli di conformità da Azure PowerShell.You can also view compliance details from Azure PowerShell. Prima di tutto, assicurarsi di avere installato il modulo Configurazione ospite.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

È possibile visualizzare lo stato corrente di tutte le assegnazioni guest per una macchina virtuale usando il comando seguente:You can view the current status of all Guest Assignments for a VM using the following command:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Per visualizzare solo la frase motivo che descrive il _motivo_ per cui la macchina virtuale non è _conforme,_ restituire solo la proprietà figlio Reason.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

È inoltre possibile generare una cronologia di conformità per le assegnazioni guest nell'ambito della macchina. L'output di questo comando include i dettagli di ogni report per la macchina virtuale.

> [!NOTE]
> L'output può restituire un grande volume di dati. Si consiglia di archiviare l'output in una variabile.

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

Per semplificare questa visualizzazione, utilizzare il parametro **ShowChanged.** L'output di questo comando include solo i report che hanno seguito una modifica dello stato di conformità.

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

Come parte di una nuova **anteprima pubblica**, gli ultimi 14 giorni della cronologia delle modifiche sono disponibili per tutte le risorse di Azure che supportano [l'eliminazione completa](../../../azure-resource-manager/templates/complete-mode-deletion.md)della modalità . La cronologia modifiche fornisce informazioni dettagliate su quando è stata rilevata una modifica e offre un _diff visivo_ per ogni modifica. Un rilevamento delle modifiche viene attivato quando le proprietà di Gestione risorse vengono aggiunte, rimosse o modificate.

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nella pagina **Panoramica** o **Conformità** selezionare un criterio in qualsiasi stato di **conformità.**

1. Nella scheda **Conformità risorse** della pagina **Conformità criteri** selezionare una risorsa.

1. Selezionare la scheda **Cronologia modifiche (anteprima)** nella pagina **Conformità risorsa**. Verrà visualizzato un elenco delle eventuali modifiche rilevate.

   ![Scheda Cronologia modifiche criteri di Azure nella pagina Conformità delle risorseAzure Policy Change History tab on Resource Compliance page](../media/determine-non-compliance/change-history-tab.png)

1. Selezionare una delle modifiche rilevate. Il _diff visivo_ per la risorsa viene presentato nella pagina **Cronologia modifiche.**

   ![Cronologia modifiche criteri di Azure Differenza visiva nella pagina Cronologia modificheAzure Policy Change History On Visual Diff on Change history page](../media/determine-non-compliance/change-history-visual-diff.png)

Il _diff visivo_ facilita l'identificazione delle modifiche di una risorsa. Le modifiche rilevate potrebbero non essere correlate allo stato di conformità corrente della risorsa.

I dati della cronologia delle modifiche vengono forniti da [Azure Resource Graph](../../resource-graph/overview.md). Per eseguire una query su queste informazioni all'esterno del portale di Azure, vedere [Ottenere le modifiche alle risorse.](../../resource-graph/how-to/get-resource-changes.md)

## <a name="next-steps"></a>Passaggi successivi

- Esaminare esempi in [Esempi di criteri di Azure](../samples/index.md).Review examples at Azure Policy samples .
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Comprendere come creare criteri a livello di [codice.](programmatically-create.md)
- Scopri come ottenere i dati di [conformità](get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi.](remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).
