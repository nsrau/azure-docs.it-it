---
title: Come creare avvisi per Gestione aggiornamenti di automazione di Azure
description: Questo articolo descrive come configurare gli avvisi di Azure per notificare lo stato delle valutazioni o delle distribuzioni degli aggiornamenti.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 1a46b5bf6c4be4953e6cde9972aa143be71406a4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977008"
---
# <a name="how-to-create-alerts-for-update-management"></a>Come creare avvisi per Gestione aggiornamenti

Gli avvisi in Azure notificano in modo proattivo i risultati dei processi Runbook, dei problemi di integrità del servizio o di altri scenari correlati all'account di automazione. Automazione di Azure non include regole di avviso preconfigurate, ma è possibile crearne di personalizzate in base ai dati generati. Questo articolo fornisce indicazioni sulla creazione di regole di avviso usando le metriche incluse con Gestione aggiornamenti.

## <a name="available-metrics"></a>Metriche disponibili

Automazione di Azure crea due metriche di piattaforma distinte correlate ai Gestione aggiornamenti che vengono raccolte e trasmesse a monitoraggio di Azure. Queste metriche sono disponibili per l'analisi usando [Esplora metriche](../../azure-monitor/platform/metrics-charts.md) e per gli avvisi usando una [regola di avviso](../../azure-monitor/platform/alerts-metric.md)per le metriche.

Le due metriche emesse sono:

* Total Update Deployment Machine Runs (Esecuzioni totali della distribuzione di aggiornamenti del computer)
* Total Update Deployment Runs (Esecuzioni totali della distribuzione di aggiornamenti)

Quando vengono usati per gli avvisi, entrambe le metriche supportano le dimensioni che contengono informazioni aggiuntive per semplificare l'ambito dell'avviso a uno specifico dettaglio di distribuzione degli aggiornamenti. Nella tabella seguente vengono illustrati i dettagli relativi alla metrica e alle dimensioni disponibili durante la configurazione di un avviso.

|Nome segnale|Dimensioni|Descrizione
|---|---|---|
|`Total Update Deployment Runs`|- Nome della distribuzione di aggiornamenti<br>- Stato | Avvisi sullo stato generale di una distribuzione di aggiornamenti.|
|`Total Update Deployment Machine Runs`|- Nome della distribuzione di aggiornamenti</br>- Stato</br>- Computer di destinazione</br>- ID di esecuzione della distribuzione di aggiornamenti    |Avvisi sullo stato di una distribuzione di aggiornamenti destinata a computer specifici.|

## <a name="create-alert"></a>Creare un avviso

Attenersi alla procedura seguente per configurare gli avvisi per indicare lo stato di una distribuzione degli aggiornamenti. Se non si ha familiarità con gli avvisi di Azure, vedere [Panoramica degli avvisi di Azure](../../azure-monitor/platform/alerts-overview.md).

1. Nell'account di automazione selezionare **avvisi** sotto **monitoraggio**e quindi selezionare **nuova regola di avviso**.

2. Nella pagina **Crea regola di avviso** l'account di automazione è già selezionato come risorsa. Se si desidera modificarlo, selezionare **Modifica risorsa**.

3. Nella pagina selezionare una risorsa scegliere account di **automazione** dall'elenco a discesa **Filtra per tipo di risorsa** .

4. Selezionare l'account di automazione che si vuole usare e quindi fare clic su **fine**.

5. Selezionare **Aggiungi condizione** per scegliere il segnale appropriato per il requisito.

6. Per una dimensione, selezionare un valore valido dall'elenco. Se il valore desiderato non è presente nell'elenco, selezionare **\+** accanto alla dimensione e digitare il nome personalizzato. Selezionare quindi il valore da cercare. Se si desidera selezionare tutti i valori per una dimensione, selezionare il **pulsante \* Seleziona** . Se non si sceglie un valore per la dimensione, Gestione aggiornamenti ignorerà la dimensione.

    ![Configurare la logica dei segnali](./media/update-mgmt-manage-updates-for-vm/signal-logic.png)

7. In **logica avvisi**, immettere i valori nei campi **aggregazione temporale** e **soglia** , quindi selezionare **fine**.

8. Nella pagina successiva immettere un nome e una descrizione per l'avviso.

9. Impostare il campo **Gravità** su **Informazioni (gravità 2)** per un'esecuzione riuscita o su **Informazioni (gravità 1)** per un'esecuzione non riuscita.

    ![Screenshot mostra la sezione Definisci dettagli avviso con i campi relativi a nome, descrizione e gravità della regola di avviso evidenziati.](./media/update-mgmt-manage-updates-for-vm/define-alert-details.png)

10. Selezionare **Sì** per abilitare la regola di avviso.

## <a name="configure-action-groups-for-your-alerts"></a>Configurare gruppi di azioni per gli avvisi

Dopo aver configurato gli avvisi, è possibile configurare un gruppo di azioni da usare in più avvisi. Le azioni possono includere notifiche tramite posta elettronica, manuali operativi, webhook e molto altro ancora. Per altre informazioni sui gruppi di azioni, vedere [Creare e gestire gruppi di azioni](../../azure-monitor/platform/action-groups.md).

1. Selezionare un avviso e quindi selezionare **Crea nuovo** in **Gruppi di azioni**.

2. Immettere un nome completo e un nome breve per il gruppo di azioni. Gestione aggiornamenti usa il nome breve quando invia notifiche usando il gruppo specificato.

3. In **Azioni** immettere un nome che specifichi l'azione, ad esempio **Notifica via posta elettronica**.

4. Per **Tipo azione** selezionare il tipo appropriato, ad esempio **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce).

5. Selezionare **Modifica dettagli**.

6. Compilare il riquadro per il tipo di azione. Ad esempio, se si usa **posta elettronica/SMS/push/voce**, immettere un nome di azione, selezionare la casella di controllo **posta elettronica** , immettere un indirizzo di posta elettronica valido e quindi fare clic su **OK**.

    ![Configurare un gruppo di azioni di posta elettronica](./media/update-mgmt-manage-updates-for-vm/configure-email-action-group.png)

7. Nel riquadro Aggiungi gruppo di azioni selezionare **OK**.

8. Per i messaggi di posta elettronica di avviso è possibile personalizzare l'oggetto. Selezionare **Personalizza azioni** in **Crea regola** e quindi selezionare **Oggetto messaggio di posta elettronica**.

9. Al termine, selezionare **Crea regola di avviso**.

## <a name="next-steps"></a>Passaggi successivi

