---
title: Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure
description: Informazioni su come usare il portale di Azure o l'interfaccia della riga di comando per creare, visualizzare e gestire regole di avviso per le metriche.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 6ac31818a68aa2b4624410b423b7559b84275777
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409372"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure

Gli avvisi delle metriche in Monitoraggio di Azure consentono di ricevere una notifica quando una delle metriche supera una soglia. Gli avvisi delle metriche funzionano su una gamma di metriche di piattaforme multidimensionali, personalizzate, standard e personalizzate di Application Insights. In questo articolo verrà illustrato come creare, visualizzare e gestire le regole di avviso per le metriche tramite il portale di Azure e l'interfaccia della riga di comando di Azure. È anche possibile creare regole di avviso per le metriche usando modelli di Azure Resource Manager, come descritto in [un articolo distinto](monitoring-enable-alerts-using-template.md).

Altre informazioni sul funzionamento degli avvisi delle metriche sono disponibili nella [panoramica degli avvisi delle metriche](alert-metric-overview.md).

## <a name="create-with-azure-portal"></a>Creare con il portale di Azure

La procedura seguente descrive come creare una regola di avviso per la metrica nel portale di Azure:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Monitoraggio**. Il pannello Monitoraggio consolida tutte le impostazioni e i dati di monitoraggio in una vista.

2. Fare clic su **Avvisi** e su **+Nuova regola di avviso**.

    > [!TIP]
    > Anche la maggior parte dei pannelli di risorse include la voce **Avvisi** nel menu delle risorse, in **Monitoraggio**: è possibile creare avvisi anche qui.

3. Fare clic su **Selezionare la destinazione** e, nel riquadro del contesto caricato, selezionare una risorsa di destinazione che si intende modificare. Usare gli elenchi a discesa **Sottoscrizione** e **Tipo di risorsa** per trovare la risorsa da monitorare. È anche possibile usare la barra di ricerca per trovare la risorsa.

4. Se la risorsa selezionata include metriche su cui creare avvisi, **Segnali disponibili** nella parte inferiore destra includerà le metriche. È possibile visualizzare l'elenco completo dei tipi di risorse supportati per gli avvisi delle metriche in questo [articolo](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)

5. Dopo aver selezionato una risorsa di destinazione, fare clic su **Aggiungi criteri**

6. Verrà visualizzato un elenco dei segnali supportati per la risorsa. Selezionare la metrica su cui creare un avviso.

7. Verrà visualizzato un grafico per la metrica per le ultime 6 ore. Definire **Periodo**, **Frequenza**, **Operatore** e **Soglia** per determinare la logica che verrà valutata dalla regola di avviso per la metrica.

8. Usando il grafico della metrica, è possibile determinare una soglia ragionevole.

9. Facoltativamente, se la metrica include dimensioni, verrà visualizzata la tabella Dimensioni. Selezionare uno o più valori per ogni dimensione. L'avviso della metrica valuterà la condizione per tutte le combinazioni di valori selezionate. [Altre informazioni sul funzionamento degli avvisi sulle metriche multidimensionali](alert-metric-overview.md). È anche possibile scegliere **Seleziona\*** per qualsiasi dimensione. **Seleziona \*** ridimensiona dinamicamente la selezione a tutti i valori correnti e futuri per una dimensione.

10. Fare clic su **Fine**

11. Facoltativamente, aggiungere altri criteri per monitorare una regola di avviso complessa

12. Compilare **Dettagli avviso** come **Nome regola di avviso**, **Descrizione** e **Gravità**

13. Aggiungere un gruppo di azioni per l'avviso selezionando un gruppo di azioni esistente o creandone uno nuovo.

14. Fare clic su **Fine** per salvare la regola di avviso per la metrica.

> [!NOTE]
> Le regole di avviso per le metriche create mediante il portale vengono generate nello stesso gruppo di risorse della risorsa di destinazione.

## <a name="view-and-manage-with-azure-portal"></a>Visualizzare e gestire con il portale di Azure

È possibile visualizzare e gestire le regole di avviso per le metriche tramite il pannello Gestisci le regole in Avvisi. La procedura riportata di seguito spiega come visualizzare le regole di avviso per le metriche e modificare una di esse.

1. Nel portale di Azure passare a **Monitoraggio**

2. Fare clic su **Avvisi** e **Gestisci le regole**

3. Nel pannello **Gestisci le regole** è possibile visualizzare tutte le regole di avviso tra le sottoscrizioni. È possibile filtrare ulteriormente le regole usando **Gruppo di risorse**, **Tipo di risorsa** e **Risorsa**. Per visualizzare solo gli avvisi delle metriche, selezionare **Tipi di segnali** come Metriche.

    > [!TIP]
    > Nel pannello **Gestisci le regole** è possibile selezionare più regole di avviso e abilitarle/disabilitarle. Questa operazione potrebbe essere utile quando è necessario sottoporre a manutenzione alcune risorse di destinazione

4. Fare clic sul nome della regola di avviso per le metriche da modificare

5. In Modifica regola fare clic sui **criteri di avviso** da modificare. È possibile modificare la metrica, la soglia e altri campi in base alle esigenze

    > [!NOTE]
    > Non è possibile modificare **Risorsa di destinazione** e **Nome regola di avviso** dopo aver creato l'avviso per le metriche.

6. Fare clic su **Fine** per salvare le modifiche.

## <a name="with-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

Le sezioni precedenti descrivono come creare, visualizzare e gestire le regole di avviso per le metriche tramite il portale di Azure. Questa sezione descrive come eseguire la stessa operazione usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) multipiattaforma. [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview?view=azure-cli-latest) è il metodo più rapido per iniziare a usare l'interfaccia della riga di comando di Azure. In questo articolo si userà Cloud Shell.

1. Passare al portale di Azure e fare clic su **Cloud Shell**.

2. Al prompt è possibile usare i comandi con l'opzione ``--help`` per altre informazioni sul comando e su come usarlo. Ad esempio, il comando seguente mostra l'elenco di comandi disponibili per la creazione, la visualizzazione e la gestione degli avvisi delle metriche

    ```azurecli
    az monitor metrics alert --help
    ```

3. È possibile creare una regola di avviso delle metriche semplice che monitora se il valore medio di CPU percentuale in una VM è maggiore di 70

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90"
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
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} -enabled false
    ```

7. È possibile eliminare una regola di avviso per le metriche con il comando seguente.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} -enabled false
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Creare avvisi delle metriche mediante modelli di Azure Resource Manager](monitoring-enable-alerts-using-template.md).
- [Comprendere il funzionamento degli avvisi delle metriche](alert-metric-overview.md).
- [Understand the web hook schema for metric alerts](monitoring-near-real-time-metric-alerts.md#payload-schema) (Comprendere lo schema webhook per gli avvisi delle metriche)
