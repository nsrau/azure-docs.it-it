---
title: Monitorare i runbook di automazione di Azure con avvisi di metrica
description: Questo articolo illustra il monitoraggio dei runbook di automazione di Azure basato sulle metriche
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7932d057a348957d369ba325044055ac8dfe3428
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58170191"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitoraggio dei runbook con avvisi di metrica

In questo articolo sono fornite informazioni su come creare gli avvisi sullo stato di completamento dei runbook.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com

## <a name="create-alert"></a>Creare un avviso

Gli avvisi consentono di definire una condizione per monitorare e un'azione da intraprendere quando viene soddisfatta tale condizione.

Nel portale di Azure passare all'account di Automazione. In **Monitoraggio** selezionare **Avvisi** e fare clic su **+ Nuova regola di avviso**. L'ambito per la destinazione è già definito per l'account di Automazione.

### <a name="configure-alert-criteria"></a>Configurare i criteri di avviso

1. Fare clic su **+ Aggiungi criteri**. Selezionare **Metriche** per il **Tipo di segnale**e scegliere **Processi totali** dalla tabella.

2. La pagina **Configurare la logica dei segnali** viene usata per definire la logica che attiva l'avviso. Nel grafico della cronologia vengono visualizzate due dimensioni, **Nome del Runbook** e **Stato**. Le dimensioni sono proprietà diverse per una metrica che può essere usata per filtrare i risultati. Per il **Nome del Runbook**, selezionare il runbook che si desidera avvisare o lasciare vuoto per avvisare tutti i runbook. Per lo **Stato**, selezionare uno stato che si desidera monitorare dall'elenco a discesa. I valori del nome e dello stato del runbook che vengono visualizzati nell'elenco a discesa sono solo per i processi che sono stati eseguiti nella settimana precedente.

   Se si vuole attivare un avviso su uno stato o un runbook che non è visualizzato nell'elenco a discesa, fare clic su **\+** accanto alla dimensione. Verrà visualizzata una finestra di dialogo che consente di immettere un valore personalizzato, che non è stato generato di recente per la specifica dimensione. Se si immette un valore che non esiste per una proprietà, l'avviso non verrà attivato.

   > [!NOTE]
   > Se non si applica un nome per il **RunbookName** della dimensione, se sono presenti tutti i runbook che soddisfano i criteri di stato, che include runbook di sistema nascosta, si riceverà un avviso.

3. In **Logica avvisi**, definire la condizione e la soglia per l'avviso. Viene visualizzata un'anteprima della condizione definita sotto.

4. In **Valutati in base a** selezionare l'intervallo di tempo per la query e la frequenza con cui tale query deve essere eseguita. Ad esempio, se si sceglie **Negli ultimi 5 minuti** per **Periodo** e **Ogni 1 minuto** per **Frequenza**, l'avviso è simile al numero di runbook che soddisfano i criteri negli ultimi 5 minuti. Questa query viene eseguita ogni minuto e, se i criteri di avviso definiti non vengono rilevati entro un intervallo di 5 minuti, l'avviso si risolve. Al termine, fare clic su **Fine**.

   ![Selezionare una risorsa per l'avviso](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definire i dettagli dell'avviso

1. In **2. Definire i dettagli dell'avviso** assegnare all'avviso un nome descrittivo e una descrizione. Impostare la **Gravità** in base alla condizione di avviso. Esistono cinque livelli di gravità compresi tra 0 e 5. Gli avvisi sono considerati indipendenti in relazione alla gravità, è possibile abbinare il livello di gravità per abbinare la logica di business.

1. Nella parte inferiore della sezione è presente un pulsante che consente di abilitare la regola al termine dell'operazione. Per impostazione predefinita le regole sono abilitate al momento della creazione. Se si seleziona No, è possibile creare l'avviso e quest'ultimo viene creato in uno stato **Disabilitato**. Dalla pagina **Regole** in Monitoraggio di Azure è possibile selezionare l'avviso e fare clic su **Abilita** per abilitarlo quando si è pronti.

### <a name="define-the-action-to-take"></a>Definire l'azione da intraprendere

1. In **3. Definire il gruppo di azioni** fare clic su **+ Nuovo gruppo di azioni**. Un gruppo di azioni è un insieme di azioni che è possibile usare in più avvisi. È ad esempio possibile usare notifiche tramite posta elettronica, runbook, webhook e molto altro ancora. Per informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../azure-monitor/platform/action-groups.md)

1. Nella casella **Nome gruppo di azione** assegnare un nome descrittivo e un nome breve. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.

1. Nella sezione **Azioni** sotto **TIPO DI AZIONE**, selezionare **Messaggio di posta elettronica/SMS/Push/Voce**.

1. Nella pagina **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce) assegnare un nome. Selezionare la casella di controllo **Posta elettronica** e immettere un indirizzo di posta elettronica valido da usare.

   ![Configurare il gruppo di azioni di posta elettronica](./media/automation-alert-activity-log/add-action-group.png)

1. Fare clic su **OK** nella pagina **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce) per chiuderla e fare clic su **OK** per chiudere la pagina **Aggiungi gruppo di azioni**. Il nome specificato in questa pagina viene salvato come **NOME DELL'AZIONE**.

1. Al termine, fare clic su **Salva**. Questa azione crea la regola che avvisa l'utente quando un runbook è completato con un determinato stato.

> [!NOTE]
> Quando si aggiunge un indirizzo di posta elettronica a un gruppo di azioni, viene inviato un messaggio di posta elettronica di notifica che indica che l'indirizzo è stato aggiunto a un gruppo di azioni.

## <a name="notification"></a>Notifica

Quando vengono soddisfatti i criteri di avviso, il gruppo di azioni esegue l'azione definita. Nell'esempio di questo articolo, viene inviato un messaggio di posta elettronica. Nell'immagine seguente è riportato un esempio di un messaggio di posta elettronica che viene ricevuto dopo l'attivazione dell'avviso:

![Avviso di posta elettronica](./media/automation-alert-activity-log/alert-email.png)

Una volta che la metrica non è più al di fuori della soglia definita, l'avviso viene disattivato e il gruppo di azioni esegue l'azione definita. Se si seleziona un tipo di azione del messaggio di posta elettronica, viene inviato un messaggio di posta elettronica di risoluzione che informa che è stato risolto.

## <a name="next-steps"></a>Passaggi successivi

Continuare l'articolo seguente per informazioni su altri metodi con cui è possibile integrare gli avvisi nell'Account di automazione.

> [!div class="nextstepaction"]
> [Usare un avviso per attivare un runbook di Automazione di Azure](automation-create-alert-triggered-runbook.md)
