---
title: Configurare gli avvisi-scalabilità iperscala (CITUS)-database di Azure per PostgreSQL
description: Questo articolo descrive come configurare e accedere agli avvisi delle metriche per database di Azure per PostgreSQL-iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a830f344023967f07ab809d67833f99e4e2958
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977608"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Usare il portale di Azure per configurare gli avvisi sulle metriche per database di Azure per PostgreSQL-iperscalabilità (CITUS)

Questo articolo mostra come configurare avvisi di Database di Azure per PostgreSQL usando il portale di Azure. È possibile ricevere un avviso in base alle metriche di monitoraggio per i servizi di Azure.

Verrà impostato un avviso da attivare quando il valore di una metrica specificata supera una soglia. L'avviso viene attivato quando la condizione viene soddisfatta per la prima volta e continua a essere attivata in seguito.

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:
* Inviare notifiche tramite posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook.

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:
* [Azure portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [interfaccia della riga di comando di Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creare una regola di avviso in base a una metrica dal portale di Azure
1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di Database di Azure per PostgreSQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Avvisi** come illustrato di seguito:

   ![Selezionare le regole di avviso](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Selezionare **nuova regola di avviso** (+ icona).

4. Verrà aperta la pagina **Crea regola** come illustrato di seguito. Specificare le informazioni necessarie:

   ![Form Aggiungi avviso per la metrica](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. Nella sezione **condizione** selezionare **Aggiungi**.

6. Selezionare una metrica dall'elenco dei segnali per i quali ricevere un avviso. In questo esempio selezionare "Storage percent".
   
   ![Selezionare una metrica](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Configurare la logica di avviso:

    * **Operatore** (es. "Maggiore di")
    * **Valore soglia** (ad esempio 85%)
    * Quantità di tempo di **granularità di aggregazione** che deve essere soddisfatta prima che l'avviso venga attivato (ad esempio, "Negli ultimi 30 minuti"
    * e **la frequenza di valutazione** (ad esempio "1 minuto")
   
   Al termine, selezionare **Chiudi**.

   ![Selezionare una metrica](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. Nella sezione **Gruppi di azioni** selezionare **Crea nuovo** per creare un nuovo gruppo di azioni per ricevere le notifiche relative all'avviso.

9. Compilare il modulo "Aggiungi gruppo di azioni" specificando nome, nome breve, sottoscrizione e gruppo di risorse.

    ![Gruppo di azioni](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Configurare un tipo di azione **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce).
    
    Scegliere "email Azure Resource Manager role" per inviare notifiche a proprietari, collaboratori e lettori della sottoscrizione.
   
    Al termine, fare clic su **OK**.

    ![Gruppo di azioni](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Specificare un nome per la regola di avviso, una descrizione e il livello di gravità.

    ![Gruppo di azioni](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Selezionare **Crea regola di avviso** per creare l'avviso.

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="manage-your-alerts"></a>Gestire gli avvisi

Dopo aver creato un avviso, è possibile selezionarlo ed eseguire le azioni seguenti:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [configurazione dei webhook negli avvisi](../azure-monitor/platform/alerts-webhooks.md).
* Leggere una [panoramica della raccolta di metriche](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
