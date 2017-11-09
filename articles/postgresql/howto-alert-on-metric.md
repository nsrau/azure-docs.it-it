---
title: Configurare avvisi per le metriche per Database di Azure per PostgreSQL nel portale di Azure | Microsoft Docs
description: Questo articolo descrive come configurare avvisi per le metriche per Database di Azure per PostgreSQL e accedere a tali avvisi nel portale di Azure.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 3a09be8131b57381eb470027a134109c116467ed
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Usare il portale di Azure per configurare avvisi per le metriche per Database di Azure per PostgreSQL 

Questo articolo mostra come configurare avvisi di Database di Azure per PostgreSQL usando il portale di Azure. È possibile ricevere un avviso in base alle metriche di monitoraggio per i servizi di Azure.

L'avviso viene attivato quando il valore di una metrica specifica supera una soglia assegnata. L'avviso viene attivato sia quando la condizione viene inizialmente soddisfatta che successivamente, quando tale condizione non è più soddisfatta. 

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:
* Inviare notifiche di posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook.

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:
* [Portale di Azure](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Interfaccia della riga di comando (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creare una regola di avviso in base a una metrica dal portale di Azure
1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di Database di Azure per PostgreSQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Regole di avviso**, come illustrato:

   ![Selezionare le regole di avviso](./media/howto-alert-on-metric/1-alert-rules.png)

3. Selezionare **Aggiungi avviso per la metrica** (icona +). 

4. Verrà aperta la pagina **Aggiungi regola**, come illustrato di seguito.  Specificare le informazioni necessarie:

   ![Form Aggiungi avviso per la metrica](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Impostazione | Description  |
   |---------|---------|
   | Nome | Specificare un nome per la regola di avviso. Questo valore viene inviato nel messaggio di posta elettronica di notifica di avviso. |
   | Descrizione | Fornire una breve descrizione per la regola di avviso. Questo valore viene inviato nel messaggio di posta elettronica di notifica di avviso. |
   | Avviso per | Per questo tipo di avviso, scegliere **Metriche**. |
   | Sottoscrizione | Questo campo viene pre-popolato con la sottoscrizione che ospita Database di Azure per PostgreSQL. |
   | Gruppo di risorse | Questo campo viene pre-popolato con il gruppo di risorse di Database di Azure per PostgreSQL. |
   | Risorsa | Questo campo viene pre-popolato con il nome dell'istanza di Database di Azure per PostgreSQL. |
   | Metrica | Selezionare la metrica per la quale si vuole generare un avviso. Ad esempio, **Percentuale di archiviazione**. |
   | Condizione | Scegliere la condizione per il confronto con la metrica. Ad esempio, **Maggiore di**. |
   | Soglia | Valore di soglia per la metrica, ad esempio 85 (valore percentuale). |
   | Periodo | Periodo di tempo per il quale la regola della metrica deve essere soddisfatta prima dell'attivazione dell'avviso. Ad esempio, **Negli ultimi 30 minuti**. |

   In base all'esempio, l'avviso cerca i casi in cui la percentuale di archiviazione è superiore all'85% in un periodo di 30 minuti. L'avviso viene attivato quando la percentuale media di archiviazione rimane al di sopra dell'85% per 30 minuti. Dopo la prima attivazione, l'avviso viene attivato di nuovo quando la percentuale media di archiviazione rimane al di sotto dell'85% per più di 30 minuti.

5. Scegliere il metodo di notifica per la regola di avviso. 

   Selezionare l'opzione **Invia messaggio di posta elettronica a proprietari, collaboratori e lettori** se si vuole che gli amministratori e i coamministratori della sottoscrizione ricevano un messaggio di posta elettronica quando si attiva l'avviso.

   Per aggiungere altri indirizzi di posta elettronica ai quali inviare una notifica quando viene attivato l'avviso, usare il campo **Indirizzi di posta elettronica aggiuntivi dell'amministratore**. Separare gli indirizzi di posta elettronica con punti e virgola: *email@contoso.com;email2@contoso.com*

   Facoltativamente, specificare un URI valido nel campo **Webhook** se lo si vuole chiamare quando viene attivato l'avviso.

6. Fare clic su **OK** per creare l'avviso.

   Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

## <a name="manage-your-alerts"></a>Gestire gli avvisi
Dopo aver creato un avviso, è possibile selezionarlo ed eseguire queste azioni:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [configurazione dei webhook negli avvisi](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Leggere una [panoramica della raccolta di metriche](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
