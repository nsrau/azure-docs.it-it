---
title: Configurare gli avvisi delle metriche-portale di Azure-database di Azure per MySQL
description: Questo articolo descrive come configurare avvisi per le metriche per Database di Azure per MySQL e accedere a tali avvisi nel portale di Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 81003be986524b94e9c4487cd0d8606540445d16
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542083"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Usare il portale di Azure per configurare avvisi per le metriche per Database di Azure per MySQL 

Questo articolo mostra come configurare avvisi di Database di Azure per MySQL usando il portale di Azure. È possibile ricevere un avviso in base alle metriche di monitoraggio per i servizi di Azure.

L'avviso viene attivato quando il valore di una metrica specifica supera una soglia assegnata. L'avviso viene attivato sia quando la condizione viene inizialmente soddisfatta che successivamente, quando tale condizione non è più soddisfatta. 

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:
* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:
* [Azure portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfaccia della riga di comando di Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST di Monitoraggio di Azure](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creare una regola di avviso in base a una metrica dal portale di Azure
1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di Database di Azure per MySQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Avvisi** come illustrato di seguito:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="Selezionare le regole di avviso":::

3. Selezionare **Aggiungi avviso per la metrica** (icona +).

4. Verrà aperta la pagina **Crea regola** come illustrato di seguito. Specificare le informazioni necessarie:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Form Aggiungi avviso per la metrica":::

5. Nella sezione **Condizione** selezionare **Aggiungi condizione**.

6. Selezionare una metrica dall'elenco dei segnali per i quali ricevere un avviso. In questo esempio selezionare "Storage percent".
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Selezionare una metrica":::

7. Configurare la logica degli avvisi specificando i valori nei campi **Condizione** (ad esempio "Maggiore di"), **Soglia** (ad esempio l'85%), **Aggregazione temporale** , **Periodo** di tempo in cui è necessario soddisfare la regola della metrica prima che venga attivato l'avviso (ad esempio "Over the Last 30 minutes" e **Frequency**.
   
   Al termine, selezionare **Chiudi**.

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Selezionare la metrica 2":::

8. Nella sezione **Gruppi di azioni** selezionare **Crea nuovo** per creare un nuovo gruppo di azioni per ricevere le notifiche relative all'avviso.

9. Compilare il modulo "Aggiungi gruppo di azioni" specificando nome, nome breve, sottoscrizione e gruppo di risorse.

10. Configurare un tipo di azione **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce).
    
    Scegliere "Invia un messaggio di posta elettronica al ruolo di Azure Resource Manager" per consentire a proprietari di sottoscrizioni, collaboratori e lettori di ricevere le notifiche.
   
    Facoltativamente, specificare un URI valido nel campo **Webhook** se lo si vuole chiamare quando viene attivato l'avviso.

    Al termine, fare clic su **OK**.

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Gruppo di azioni":::

11. Specificare un nome per la regola di avviso, una descrizione e il livello di gravità.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Gruppo di azioni 2"::: 

12. Selezionare **Crea regola di avviso** per creare l'avviso.

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="manage-your-alerts"></a>Gestire gli avvisi
Dopo aver creato un avviso, è possibile selezionarlo ed eseguire queste azioni:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [configurazione dei webhook negli avvisi](../azure-monitor/platform/alerts-webhooks.md).
* Leggere una [panoramica della raccolta di metriche](../azure-monitor/platform/data-platform.md) per verificare che il servizio sia disponibile e reattivo.