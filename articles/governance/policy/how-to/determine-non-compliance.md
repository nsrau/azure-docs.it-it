---
title: Determinare le cause di non conformità
description: Quando una risorsa non è conforme, esistono molti motivi possibili. Scopri cosa ha causato la mancata conformità.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: 213797a1eafda10d5e97fbc60fc143de975fe221
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873132"
---
# <a name="determine-causes-of-non-compliance"></a>Determinare le cause di non conformità

Quando una risorsa di Azure viene determinata come non conforme a una regola dei criteri, è utile comprendere la parte della regola con la quale la risorsa non è conforme. È inoltre utile comprendere quale modifica ha modificato una risorsa conforme in precedenza per renderla non conforme. Sono disponibili due modi per trovare queste informazioni:

> [!div class="checklist"]
> - [Dettagli conformità](#compliance-details)
> - [Cronologia modifiche (anteprima)](#change-history)

## <a name="compliance-details"></a>Dettagli conformità

Quando una risorsa non è conforme, i dettagli di conformità per tale risorsa sono disponibili nella pagina **conformità criteri** . Il riquadro dettagli conformità include le informazioni seguenti:

- Dettagli delle risorse, ad esempio nome, tipo, posizione e ID risorsa
- Stato di conformità e timestamp dell'ultima valutazione per l'assegnazione di criteri corrente
- Elenco di _motivi_ per la mancata conformità delle risorse

> [!IMPORTANT]
> Poiché i dettagli di conformità per una risorsa _non conforme_ mostrano il valore corrente delle proprietà di tale risorsa, l'utente deve avere un'operazione di **lettura** per il **tipo** di risorsa. Ad esempio, se la risorsa _non conforme_ è **Microsoft. Compute/virtualMachines** , l'utente deve disporre dell'operazione **Microsoft. Compute/virtualMachines/Read** . Se l'utente non ha l'operazione necessaria, viene visualizzato un errore di accesso.

Per visualizzare i dettagli di conformità, attenersi alla procedura seguente:

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nella pagina **Panoramica** o **conformità** Selezionare un criterio in uno stato di **conformità** _non conforme_.

1. Nella scheda **conformità risorse** della pagina **conformità criteri** fare clic con il pulsante destro del mouse o selezionare i puntini di sospensione di una risorsa in uno **stato di conformità** _non conforme_. Quindi selezionare **Visualizza i dettagli di conformità**.

   ![Opzione Visualizza dettagli conformità](../media/determine-non-compliance/view-compliance-details.png)

1. Il riquadro **Dettagli conformità** Visualizza le informazioni dalla valutazione più recente della risorsa all'assegnazione di criteri corrente. In questo esempio, il campo **Microsoft. SQL/Servers/Version** è _12,0_ mentre è prevista la definizione del criterio _14,0_. Se la risorsa non è conforme per diversi motivi, ciascuna di esse viene elencata in questo riquadro.

   ![Riquadro dei dettagli di conformità e motivi per la mancata conformità](../media/determine-non-compliance/compliance-details-pane.png)

   Per una definizione dei criteri **auditIfNotExists** o **deployIfNotExists** , i dettagli includono la proprietà **Details. Type** e tutte le proprietà facoltative. Per un elenco, vedere [Proprietà auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [Proprietà deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). L' **ultima risorsa valutata** è una risorsa correlata dalla sezione dei **Dettagli** della definizione.

   Esempio di definizione di **deployIfNotExists** parziale:

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

   ![Riquadro dei dettagli di conformità-* ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Per proteggere i dati, quando il valore di una proprietà è un _segreto_ , il valore corrente Visualizza gli asterischi.

Questi dettagli spiegano il motivo per cui una risorsa non è attualmente conforme, ma non Mostra quando è stata apportata la modifica alla risorsa che lo ha causato come non conforme. Per informazioni, vedere la [cronologia delle modifiche (anteprima)](#change-history) riportata di seguito.

### <a name="compliance-reasons"></a>Motivi di conformità

Nella matrice seguente viene eseguito il mapping di ogni _motivo_ possibile alla [condizione](../concepts/definition-structure.md#conditions) responsabile nella definizione dei criteri:

|Motivo | Condizione |
|-|-|
|Il valore corrente deve contenere il valore di destinazione come chiave. |containsKey o **not** notContainsKey |
|Il valore corrente deve contenere il valore di destinazione. |contiene o **non** notContains |
|Il valore corrente deve essere uguale al valore di destinazione. |uguale a o **non** notEquals |
|Il valore corrente deve essere minore del valore di destinazione. |minore o **non** greaterOrEquals |
|Il valore corrente deve essere maggiore o uguale al valore di destinazione. |greaterOrEquals o **meno** |
|Il valore corrente deve essere maggiore del valore di destinazione. |maggiore o **non** lessOrEquals |
|Il valore corrente deve essere minore o uguale al valore di destinazione. |lessOrEquals o **non** superiore |
|Il valore corrente deve esistere. |exists |
|Il valore corrente deve essere nel valore di destinazione. |in o **non** notIn |
|Il valore corrente deve essere simile al valore di destinazione. |like o **not** notLike |
|Il valore corrente deve corrispondere al valore di destinazione con distinzione tra maiuscole e minuscole. |corrisponde o **non** notMatch |
|Il valore corrente non deve essere soggetto a distinzione tra maiuscole e minuscole. |matchInsensitively o **not** notMatchInsensitively |
|Il valore corrente non deve contenere il valore di destinazione come chiave. |notContainsKey o **not** ContainsKey|
|Il valore corrente non deve contenere il valore di destinazione. |notContains o **not** Contains |
|Il valore corrente non deve essere uguale al valore di destinazione. |notEquals o **not** Equals |
|Il valore corrente non deve esistere. |**non** esiste  |
|Il valore corrente non deve essere nel valore di destinazione. |notIn o **not** in |
|Il valore corrente non deve essere simile al valore di destinazione. |notLike o **No** like |
|Il valore corrente non deve essere soggetto a distinzione tra maiuscole e minuscole. |notMatch o **not** match |
|Il valore corrente non deve far distinzione tra maiuscole e minuscole con il valore di destinazione. |notMatchInsensitively o **not** matchInsensitively |
|Nessuna risorsa correlata corrisponde ai dettagli degli effetti nella definizione dei criteri. |Una risorsa del tipo definito in **then. Details. Type** e relativa alla risorsa definita nella parte **if** della regola dei criteri non esiste. |

## <a name="compliance-details-for-guest-configuration"></a>Dettagli di conformità per la configurazione Guest

Per i criteri _auditIfNotExists_ nella categoria _configurazione Guest_ , è possibile che vengano valutate più impostazioni nella macchina virtuale e che sia necessario visualizzare i dettagli per ogni impostazione. Se, ad esempio, si esegue il controllo per un elenco di criteri password e solo uno di essi ha lo stato _non conforme_, è necessario stabilire quali criteri password specifici non sono conformi e perché.

Inoltre, potrebbe non essere possibile accedere direttamente alla macchina virtuale, ma è necessario segnalare il motivo per cui la macchina virtuale _non è conforme_.

### <a name="azure-portal"></a>Portale di Azure

Per iniziare, seguire la stessa procedura descritta nella sezione precedente per visualizzare i dettagli di conformità dei criteri.

Nella visualizzazione riquadro **Dettagli conformità** fare clic sul collegamento **ultima risorsa valutata**.

   ![Visualizza i dettagli della definizione auditIfNotExists](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

Nella pagina **assegnazione Guest** vengono visualizzati tutti i dettagli di conformità disponibili. Ogni riga della vista rappresenta una valutazione eseguita all'interno del computer. Nella colonna **reason** viene visualizzata una frase che descrive il motivo per cui l'assegnazione Guest è _non conforme_ . Se, ad esempio, si controllano i criteri password, nella colonna **motivo** verrà visualizzato testo con il valore corrente per ogni impostazione.

![Visualizza i dettagli di conformità](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

È anche possibile visualizzare i dettagli di conformità da Azure PowerShell. Assicurarsi prima di tutto che sia installato il modulo di configurazione Guest.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

È possibile visualizzare lo stato corrente di tutte le assegnazioni Guest per una macchina virtuale usando il comando seguente:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Per visualizzare solo la frase del _motivo_ che descrive il motivo per cui la macchina virtuale _non è conforme_, restituire solo il motivo della proprietà figlio.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

È anche possibile generare una cronologia di conformità per le assegnazioni Guest nell'ambito del computer. L'output di questo comando include i dettagli di ogni report per la macchina virtuale.

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

Per semplificare questa visualizzazione, usare il parametro **ShowChanged** . L'output di questo comando include solo i report che hanno seguito una modifica dello stato di conformità.

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

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>cronologia modifiche (anteprima)

Come parte di una nuova versione di **anteprima pubblica**, gli ultimi 14 giorni di cronologia delle modifiche sono disponibili per tutte le risorse di Azure che supportano l' [eliminazione in modalità completa](../../../azure-resource-manager/complete-mode-deletion.md). La cronologia modifiche fornisce informazioni dettagliate su quando è stata rilevata una modifica e offre un _diff visivo_ per ogni modifica. Il rilevamento delle modifiche viene attivato quando vengono aggiunte, rimosse o modificate le proprietà del Gestione risorse.

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nella pagina **Panoramica** o **conformità** Selezionare un criterio in uno stato di **conformità**.

1. Nella scheda **conformità risorse** della pagina **conformità criteri** Selezionare una risorsa.

1. Selezionare la scheda **Cronologia modifiche (anteprima)** nella pagina **Conformità risorsa**. Verrà visualizzato un elenco delle eventuali modifiche rilevate.

   ![Scheda cronologia modifiche criteri di Azure nella pagina conformità risorse](../media/determine-non-compliance/change-history-tab.png)

1. Selezionare una delle modifiche rilevate. Le _differenze visive_ per la risorsa vengono visualizzate nella pagina della **cronologia delle modifiche** .

   ![Differenze visive della cronologia modifiche dei criteri di Azure nella pagina Cronologia modifiche](../media/determine-non-compliance/change-history-visual-diff.png)

Il _diff visivo_ facilita l'identificazione delle modifiche di una risorsa. Le modifiche rilevate potrebbero non essere correlate allo stato di conformità corrente della risorsa.

I dati della cronologia delle modifiche vengono forniti da [Azure Resource Graph](../../resource-graph/overview.md). Per eseguire una query su queste informazioni all'esterno del portale di Azure, vedere [ottenere le modifiche alle risorse](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [creare criteri a livello di codice](programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](get-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
