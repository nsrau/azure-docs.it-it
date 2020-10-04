---
title: Configurare gli avvisi-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come configurare e accedere agli avvisi delle metriche per database di Azure per PostgreSQL-server singolo dal portale di Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: fba5c868a146529a981e23cd88b413f2eb441896
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708935"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Usare il portale di Azure per configurare gli avvisi sulle metriche per database di Azure per PostgreSQL-server singolo

Questo articolo mostra come configurare avvisi di Database di Azure per PostgreSQL usando il portale di Azure. È possibile ricevere un avviso in base alle metriche di monitoraggio per i servizi di Azure.

L'avviso viene attivato quando il valore di una metrica specifica supera una soglia assegnata. L'avviso viene attivato sia quando la condizione viene inizialmente soddisfatta che successivamente, quando tale condizione non è più soddisfatta. 

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:
* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook.

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:
* [Azure portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfaccia della riga di comando di Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creare una regola di avviso in base a una metrica dal portale di Azure
1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di Database di Azure per PostgreSQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Avvisi** come illustrato di seguito:

   :::image type="content" source="./media/howto-alert-on-metric/2-alert-rules.png" alt-text="Selezionare le regole di avviso":::

3. Selezionare **Aggiungi avviso per la metrica** (icona +).

4. Verrà aperta la pagina **Crea regola** come illustrato di seguito. Specificare le informazioni necessarie:

   :::image type="content" source="./media/howto-alert-on-metric/4-add-rule-form.png" alt-text="Selezionare le regole di avviso":::

5. Nella sezione **Condizione** selezionare **Aggiungi condizione**.

6. Selezionare una metrica dall'elenco dei segnali per i quali ricevere un avviso. In questo esempio selezionare "Storage percent".
   
   :::image type="content" source="./media/howto-alert-on-metric/6-configure-signal-logic.png" alt-text="Selezionare le regole di avviso" e **Frequency**.
   
   Al termine, selezionare **Chiudi**.

   :::image type="content" source="./media/howto-alert-on-metric/7-set-threshold-time.png" alt-text="Selezionare le regole di avviso" per consentire a proprietari di sottoscrizioni, collaboratori e lettori di ricevere le notifiche.
   
    Facoltativamente, specificare un URI valido nel campo **Webhook** se lo si vuole chiamare quando viene attivato l'avviso.

    Al termine, fare clic su **OK**.

    :::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Selezionare le regole di avviso":::

11. Specificare un nome per la regola di avviso, una descrizione e il livello di gravità.

    :::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Selezionare le regole di avviso"::: 

12. Selezionare **Crea regola di avviso** per creare l'avviso.

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="manage-your-alerts"></a>Gestire gli avvisi
Dopo aver creato un avviso, è possibile selezionarlo ed eseguire queste azioni:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [configurazione dei webhook negli avvisi](../azure-monitor/platform/alerts-webhooks.md).
* Leggere una [panoramica della raccolta di metriche](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
