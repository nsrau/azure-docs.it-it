---
title: Configurare gli avvisi-portale di Azure-database di Azure per PostgreSQL-server flessibile
description: Questo articolo descrive come configurare e accedere agli avvisi relativi alle metriche per database di Azure per PostgreSQL-server flessibili dal portale di Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d3d5ced5860c14e9e4d522c42ffd0bc71341a9c4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938665"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---flexible-server"></a>Usare il portale di Azure per configurare gli avvisi relativi alle metriche per database di Azure per PostgreSQL-server flessibili

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questo articolo mostra come configurare avvisi di Database di Azure per PostgreSQL usando il portale di Azure. È possibile ricevere un avviso in base alle metriche di monitoraggio per i servizi di Azure.

L'avviso viene attivato quando il valore di una metrica specifica supera una soglia assegnata. L'avviso viene attivato sia quando la condizione viene inizialmente soddisfatta che successivamente, quando tale condizione non è più soddisfatta.

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:

* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook.

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:

* [Azure portal](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#create-with-azure-portal)
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric#with-azure-cli)
* [API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creare una regola di avviso in base a una metrica dal portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di Database di Azure per PostgreSQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Avvisi** come illustrato di seguito:

   :::image type="content" source="./media/howto-alert-on-metrics/2-alert-rules.png" alt-text="Selezionare le regole di avviso":::

3. Selezionare **Aggiungi avviso per la metrica** (icona +).

4. Verrà aperta la pagina **Crea regola** come illustrato di seguito. Specificare le informazioni necessarie:

   :::image type="content" source="./media/howto-alert-on-metrics/4-add-rule-form.png" alt-text="Form Aggiungi avviso per la metrica":::

5. Nella sezione **Condizione** selezionare **Aggiungi condizione**.

6. Selezionare una metrica dall'elenco dei segnali per i quali ricevere un avviso. In questo esempio selezionare "Storage percent".

   :::image type="content" source="./media/howto-alert-on-metrics/6-configure-signal-logic.png" alt-text="Selezionare una metrica":::

7. Configurare la logica degli avvisi specificando i valori nei campi **Condizione** (ad esempio "Maggiore di"), **Soglia** (ad esempio l'85%), **Aggregazione temporale**, **Periodo** di tempo in cui è necessario soddisfare la regola della metrica prima che venga attivato l'avviso (ad esempio "Over the Last 30 minutes" e **Frequency**.

   Al termine, selezionare **Chiudi**.

   :::image type="content" source="./media/howto-alert-on-metrics/7-set-threshold-time.png" alt-text="Imposta soglia":::

8. Nella sezione **Gruppi di azioni** selezionare **Crea nuovo** per creare un nuovo gruppo di azioni per ricevere le notifiche relative all'avviso.

9. Compilare il modulo "Aggiungi gruppo di azioni" specificando nome, nome breve, sottoscrizione e gruppo di risorse.

10. Configurare un tipo di azione **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce).

    1. Scegliere "Invia un messaggio di posta elettronica al ruolo di Azure Resource Manager" per consentire a proprietari di sottoscrizioni, collaboratori e lettori di ricevere le notifiche.

    2. Facoltativamente, specificare un URI valido nel campo **Webhook** se lo si vuole chiamare quando viene attivato l'avviso.

    3. Al termine, fare clic su **OK**.

    :::image type="content" source="./media/howto-alert-on-metrics/10-action-group-type.png" alt-text="Gruppo di azioni":::

11. Specificare un nome per la regola di avviso, una descrizione e il livello di gravità.

    :::image type="content" source="./media/howto-alert-on-metrics/11-name-description-severity.png" alt-text="Specifica, Descrizione nome e gravità"::: 

12. Selezionare **Crea regola di avviso** per creare l'avviso.

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="manage-your-alerts"></a>Gestire gli avvisi

Dopo aver creato un avviso, è possibile selezionarlo ed eseguire queste azioni:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [configurazione dei webhook negli avvisi](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-webhooks).
* Leggere una [panoramica della raccolta di metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-how-to-customize-monitoring) per verificare che il servizio sia disponibile e reattivo.
