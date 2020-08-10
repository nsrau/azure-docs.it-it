---
title: Creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure
description: Informazioni su come usare portale di Azure o l'interfaccia della riga di comando per creare, visualizzare e gestire le regole di avviso della metrica.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 08/10/2020
ms.subservice: alerts
ms.openlocfilehash: 8afe62173dc0e90783c494fa51994e9ebac506f3
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042735"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure

Gli avvisi delle metriche in monitoraggio di Azure consentono di ricevere una notifica quando una metrica supera una soglia. Gli avvisi delle metriche funzionano su una gamma di metriche di piattaforme multidimensionali, personalizzate, standard e personalizzate di Application Insights. In questo articolo verrà descritto come creare, visualizzare e gestire le regole di avviso delle metriche tramite portale di Azure e l'interfaccia della riga di comando di Azure. È anche possibile creare regole di avviso per la metrica usando modelli di Azure Resource Manager, descritti in [un articolo separato](alerts-metric-create-templates.md).

Altre informazioni sul funzionamento degli avvisi delle metriche sono disponibili nella [panoramica degli avvisi delle metriche](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Creare con il portale di Azure

La procedura seguente descrive come creare una regola di avviso per la metrica nel portale di Azure:

1. In [portale di Azure](https://portal.azure.com)fare clic su **monitoraggio**. Il pannello Monitoraggio consolida tutte le impostazioni e i dati di monitoraggio in una vista.

2. Fare clic su **Avvisi** e su **+Nuova regola di avviso**.

    > [!TIP]
    > Anche la maggior parte dei pannelli di risorse include la voce **Avvisi** nel menu delle risorse, in **Monitoraggio**: è possibile creare avvisi anche qui.

3. Fare clic su **Selezionare la destinazione** e, nel riquadro del contesto caricato, selezionare una risorsa di destinazione su cui si vuole impostare una regola di avviso. Usare gli elenchi a discesa **Sottoscrizione** e **Tipo di risorsa** per trovare la risorsa da monitorare. È anche possibile usare la barra di ricerca per trovare la risorsa.

4. Se la risorsa selezionata include metriche su cui creare avvisi, **Segnali disponibili** nella parte inferiore destra includerà le metriche. L'elenco completo dei tipi di risorse supportati per gli avvisi delle metriche è disponibile in questo [articolo](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Dopo aver selezionato una risorsa di destinazione, fare clic su **Aggiungi condizione**.

6. Verrà visualizzato un elenco dei segnali supportati per la risorsa. Selezionare la metrica su cui creare un avviso.

7. Viene visualizzato un grafico per la metrica per le ultime sei ore. Utilizzare l'elenco a discesa **periodo grafico** per selezionare la cronologia più lunga per la metrica.

8. Se la metrica ha dimensioni, viene visualizzata una tabella delle dimensioni. Selezionare uno o più valori per ogni dimensione.
    - I valori della dimensione visualizzati sono basati sui dati delle metriche degli ultimi tre giorni.
    - Se il valore della dimensione che si sta cercando non è visualizzato, fare clic su "+" per aggiungere un valore personalizzato.
    - È anche possibile scegliere **Seleziona\*** per qualsiasi dimensione. **Seleziona \*** ridimensiona dinamicamente la selezione a tutti i valori correnti e futuri per una dimensione.

    La regola di avviso metrica valuterà la condizione per tutte le combinazioni di valori selezionati. [Altre informazioni sul funzionamento degli avvisi sulle metriche multidimensionali](alerts-metric-overview.md).

9. Selezionare il tipo di **soglia** , l' **operatore**e il **tipo di aggregazione**. In questo modo verrà determinata la logica che verrà valutata dalla regola di avviso della metrica.
    - Se si usa una soglia **statica** , continuare a definire un **valore soglia**. Il grafico delle metriche può essere utile per determinare quale sia una soglia ragionevole.
    - Se si usa una soglia **dinamica** , continuare a definire la **sensibilità della soglia**. Il grafico delle metriche mostrerà le soglie calcolate in base ai dati recenti. [Altre informazioni sulle opzioni di sensibilità e i tipi di condizione delle soglie dinamiche](alerts-dynamic-thresholds.md).

10. Facoltativamente, perfezionare la condizione modificando la granularità e la **frequenza di valutazione dell'** **aggregazione** . 

11. Fare clic su **Fine**.

12. Facoltativamente, aggiungere altri criteri per monitorare una regola di avviso complessa. Attualmente gli utenti possono ricevere regole di avviso con criteri di soglia dinamica come singolo criterio.

13. Specificare **i dettagli dell'avviso** , ad esempio il nome, la **Descrizione**e la **gravità**della **regola di avviso**.

14. Aggiungere un gruppo di azioni per l'avviso selezionando un gruppo di azioni esistente o creandone uno nuovo.

15. Fare clic su **Fine** per salvare la regola di avviso per la metrica.

> [!NOTE]
> Le regole di avviso per le metriche create mediante il portale vengono generate nello stesso gruppo di risorse della risorsa di destinazione.

## <a name="view-and-manage-with-azure-portal"></a>Visualizzare e gestire con il portale di Azure

È possibile visualizzare e gestire le regole di avviso per le metriche tramite il pannello Gestisci le regole in Avvisi. La procedura riportata di seguito spiega come visualizzare le regole di avviso per le metriche e modificare una di esse.

1. Nel portale di Azure passare a **Monitoraggio**

2. Fare clic su **Avvisi** e **Gestisci le regole**

3. Nel pannello **Gestisci le regole** è possibile visualizzare tutte le regole di avviso tra le sottoscrizioni. È possibile filtrare ulteriormente le regole usando il **gruppo di risorse**, il tipo di **risorsa**e la **risorsa**. Per visualizzare solo gli avvisi delle metriche, selezionare **Tipi di segnali** come Metriche.

    > [!TIP]
    > Nel pannello **Gestisci le regole** è possibile selezionare più regole di avviso e abilitarle/disabilitarle. Questa operazione potrebbe essere utile quando è necessario sottoporre a manutenzione alcune risorse di destinazione

4. Fare clic sul nome della regola di avviso per le metriche da modificare

5. In Modifica regola fare clic sui **criteri di avviso** da modificare. È possibile modificare la metrica, la condizione di soglia e altri campi in base alle esigenze

    > [!NOTE]
    > Non è possibile modificare **Risorsa di destinazione** e **Nome regola di avviso** dopo aver creato l'avviso per le metriche.

6. Fare clic su **Fine** per salvare le modifiche.


## <a name="with-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Nelle sezioni precedenti è stato descritto come creare, visualizzare e gestire le regole di avviso delle metriche utilizzando portale di Azure. Questa sezione descrive come eseguire la stessa operazione usando l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) multipiattaforma. [Azure Cloud Shell](../../cloud-shell/overview.md?view=azure-cli-latest) è il metodo più rapido per iniziare a usare l'interfaccia della riga di comando di Azure. Per questo articolo verrà usato Cloud Shell.

1. Passare a portale di Azure, fare clic su **cloud Shell**.

2. Al prompt è possibile usare i comandi con l'opzione ``--help`` per altre informazioni sul comando e su come usarlo. Il comando seguente, ad esempio, Mostra l'elenco dei comandi disponibili per la creazione, la visualizzazione e la gestione degli avvisi delle metriche

    ```azurecli
    az monitor metrics alert --help
    ```

3. È possibile creare una regola di avviso delle metriche semplice che monitora se il valore medio di Percentuale CPU in una macchina virtuale è maggiore di 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. È possibile visualizzare tutti gli avvisi delle metriche in un gruppo di risorse usando il comando seguente

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. È possibile visualizzare i dettagli di una particolare regola di avviso delle metriche con il nome o l'ID di risorsa della regola.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. È possibile disabilitare una regola di avviso per le metriche con il comando seguente.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. È possibile eliminare una regola di avviso per le metriche con il comando seguente.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="with-powershell"></a>Con PowerShell

Per le regole di avviso delle metriche sono disponibili cmdlet di PowerShell dedicati:

- [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2): crea una nuova regola di avviso per la metrica o ne aggiorna una esistente.
- [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2): ottiene una o più regole di avviso per la metrica.
- [Remove-AzMetricAlertRuleV2](/powershell/module/az.monitor/remove-azmetricalertrulev2): rimuove una regola di avviso per la metrica.

## <a name="next-steps"></a>Passaggi successivi

- [Creare avvisi delle metriche mediante modelli di Azure Resource Manager](./alerts-metric-create-templates.md).
- [Comprendere il funzionamento degli avvisi delle metriche](alerts-metric-overview.md).
- [Comprendere il funzionamento degli avvisi delle metriche con il tipo di condizione delle soglie dinamiche](alerts-dynamic-thresholds.md).
- [Understand the web hook schema for metric alerts](./alerts-metric-near-real-time.md#payload-schema) (Comprendere lo schema webhook per gli avvisi delle metriche)

