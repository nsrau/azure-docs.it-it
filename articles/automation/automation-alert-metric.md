---
title: Monitorare i runbook di automazione di Azure con avvisi di metrica
description: Questo articolo descrive come configurare un avviso della metrica in base allo stato di completamento di Runbook.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 8767687f0b72d3469bef570770ac81fa8300097f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88055930"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Monitorare i runbook con avvisi delle metriche

In questo articolo viene illustrato come creare un avviso di [metrica](../azure-monitor/platform/alerts-metric-overview.md) basato sullo stato di completamento di Runbook.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com)

## <a name="create-alert"></a>Creare un avviso

Gli avvisi consentono di definire una condizione per monitorare e un'azione da intraprendere quando viene soddisfatta tale condizione.

1. Avviare il servizio automazione di Azure nella portale di Azure facendo clic su **tutti i servizi**, quindi cercando e selezionando gli **account di automazione**.

2. Nell'elenco degli account di automazione selezionare l'account per cui si vuole creare un avviso. 

3. In **monitoraggio**selezionare **avvisi** e quindi selezionare **+ nuova regola di avviso**. L'ambito per la destinazione è già definito e associato all'account di automazione.

### <a name="configure-alert-criteria"></a>Configurare i criteri di avviso

1. Fare clic su **Seleziona condizione**. Selezionare **metrica** per il **tipo di segnale**e scegliere **Totale processi** dall'elenco.

2. La pagina **Configurare la logica dei segnali** viene usata per definire la logica che attiva l'avviso. Nel grafico della cronologia vengono visualizzate due dimensioni, **Nome del Runbook** e **Stato**. Le dimensioni sono proprietà diverse per una metrica che può essere usata per filtrare i risultati. Per il **Nome del Runbook**, selezionare il runbook che si desidera avvisare o lasciare vuoto per avvisare tutti i runbook. Per lo **Stato**, selezionare uno stato che si desidera monitorare dall'elenco a discesa. I valori del nome e dello stato del runbook che vengono visualizzati nell'elenco a discesa sono solo per i processi che sono stati eseguiti nella settimana precedente.

   Se si desidera ricevere un avviso per uno stato o un Runbook che non viene visualizzato nell'elenco a discesa, fare clic sull'opzione **Aggiungi valore personalizzato** accanto alla dimensione. Questa azione apre una finestra di dialogo che consente di specificare un valore personalizzato che non è stato emesso di recente per tale dimensione. Se si immette un valore che non esiste per una proprietà, l'avviso non verrà attivato.

   > [!NOTE]
   > Se non si specifica un nome per la dimensione del **nome Runbook** , se sono presenti manuali operativi che soddisfano i criteri di stato, che include manuali operativi di sistema nascosti, si riceverà un avviso.

    Ad esempio, per ricevere un avviso quando un Runbook restituisce uno stato _non riuscito_ , oltre a specificare il nome del Runbook, per la dimensione **stato** aggiungere il valore della dimensione personalizzata **non riuscito**.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Specificare il valore della dimensione personalizzata" border="false":::

3. In **Logica avvisi**, definire la condizione e la soglia per l'avviso. Viene visualizzata un'anteprima della condizione definita sotto.

4. In **valutato in base a**selezionare l'intervallo di tempo per la query e la frequenza con cui si desidera eseguire la query. Se, ad esempio, si sceglie **negli ultimi 5 minuti** per il **periodo**e **ogni minuto** per **frequenza**, l'avviso cerca il numero di manuali operativi che soddisfano i criteri negli ultimi 5 minuti. Questa query viene eseguita ogni minuto e, se i criteri di avviso definiti non vengono rilevati entro un intervallo di 5 minuti, l'avviso si risolve. Al termine, fare clic su **Fine**.

   ![Selezionare una risorsa per l'avviso](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>Definire l'azione da intraprendere

1. In **gruppo di azione**selezionare **specifica gruppo di azioni**. Un gruppo di azioni è un insieme di azioni che è possibile usare in più avvisi. È ad esempio possibile usare notifiche tramite posta elettronica, runbook, webhook e molto altro ancora. Per altre informazioni sui gruppi di azioni e sui passaggi per crearne uno che invia una notifica tramite posta elettronica, vedere [creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md).

### <a name="define-alert-details"></a>Definire i dettagli dell'avviso

1. In **Dettagli regola di avviso**assegnare all'avviso un nome descrittivo e una descrizione. Impostare la **Gravità** in base alla condizione di avviso. Esistono cinque livelli di gravità compresi tra 0 e 5. Gli avvisi sono considerati indipendenti in relazione alla gravità, è possibile abbinare il livello di gravità per abbinare la logica di business.

1. Per impostazione predefinita, le regole sono abilitate in fase di creazione, a meno che non **si selezioni l'** opzione **Abilita regola di avviso al momento della creazione**. Per gli avvisi creati in uno stato disabilitato, è possibile abilitarli in futuro quando si è pronti. Selezionare **Crea regola di avviso** per salvare le modifiche.

## <a name="receive-notification"></a>Ricevere una notifica

Quando vengono soddisfatti i criteri di avviso, il gruppo di azioni esegue l'azione definita. Nell'esempio di questo articolo, viene inviato un messaggio di posta elettronica. Nell'immagine seguente è riportato un esempio di un messaggio di posta elettronica che viene ricevuto dopo l'attivazione dell'avviso:

![Avviso di posta elettronica](./media/automation-alert-activity-log/alert-email.png)

Una volta che la metrica non è più al di fuori della soglia definita, l'avviso viene disattivato e il gruppo di azioni esegue l'azione definita. Se si seleziona un tipo di azione del messaggio di posta elettronica, viene inviato un messaggio di posta elettronica di risoluzione che informa che è stato risolto.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, vedere [Usare un avviso per attivare un runbook di Automazione di Azure](automation-create-alert-triggered-runbook.md).
