---
title: Determinare le cause di non conformità
description: Se una risorsa non conforme, esistono numerose cause. Informazioni su come stabilire la causa la non conformità.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 8a593e92d7f24885c35043b874528e881d2e021e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276100"
---
# <a name="determine-causes-of-non-compliance"></a>Determinare le cause di non conformità

Quando una risorsa di Azure viene determinata come non conforme a una regola di criteri, è utile comprendere quale parte della regola della risorsa non è conforme. È anche utile comprendere quali modifiche modificato una risorsa prima era conforme per renderlo non conformi. Esistono due modi per trovare queste informazioni:

> [!div class="checklist"]
> - [Dettagli conformità](#compliance-details)
> - [Cronologia modifiche (anteprima)](#change-history-preview)

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

   Per un **auditIfNotExists** oppure **deployIfNotExists** definizione dei criteri, i dettagli includono il **details.type** proprietà e le eventuali proprietà facoltative. Per un elenco, vedere [proprietà auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [deployIfNotExists proprietà](../concepts/effects.md#deployifnotexists-properties). **Ultima valutazione resource** è delle risorse correlata di **dettagli** sezione della definizione.

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

|Motivo | Condizione |
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

## <a name="change-history-preview"></a>Cronologia modifiche (anteprima)

Come parte di una nuova **versione di anteprima pubblica**, gli ultimi 14 giorni di modifica della cronologia è disponibile per tutte le risorse di Azure che supportano [completare l'eliminazione di modalità](../../../azure-resource-manager/complete-mode-deletion.md). La cronologia modifiche fornisce informazioni dettagliate su quando è stata rilevata una modifica e offre un _diff visivo_ per ogni modifica. Un rilevamento delle modifiche viene attivato quando le proprietà di Resource Manager vengano aggiunto, rimosso o modificate.

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

1. Nel **Overview** o **conformità** pagina, selezionare un criterio in qualsiasi **stato di conformità**.

1. Sotto il **conformità risorsa** scheda della finestra di **conformità ai criteri** pagina, selezionare una risorsa.

1. Selezionare la scheda **Cronologia modifiche (anteprima)** nella pagina **Conformità risorsa**. Verrà visualizzato un elenco delle eventuali modifiche rilevate.

   ![Scheda Cronologia modifiche dei criteri nella pagina relativa alla conformità risorsa](../media/determine-non-compliance/change-history-tab.png)

1. Selezionare una delle modifiche rilevate. Il _diff visual_ per la risorsa viene presentata nella **cronologia delle modifiche** pagina.

   ![Diff Visual cronologia modifiche di criteri nella pagina cronologia modifiche](../media/determine-non-compliance/change-history-visual-diff.png)

Il _diff visivo_ facilita l'identificazione delle modifiche di una risorsa. Sono state rilevate le modifiche potrebbero non essere correlate per lo stato di conformità corrente della risorsa.

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md)
- Vedere la [struttura delle definizioni dei criteri](../concepts/definition-structure.md)
- Vedere [Informazioni sugli effetti di Criteri](../concepts/effects.md)
- Informazioni su come [creare criteri a livello di programmazione](programmatically-create.md)
- Informazioni su come [ottenere dati sulla conformità](getting-compliance-data.md)
- Informazioni su come [correggere le risorse non conformi](remediate-resources.md)
- Scoprire le caratteristiche di un gruppo di gestione con [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md)