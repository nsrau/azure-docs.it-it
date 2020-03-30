---
title: Configurare avvisi - Hyperscale (Citus) - Database di Azure per PostgreSQLConfigure alerts - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Questo articolo descrive come configurare e accedere agli avvisi di metrica per il database di Azure per PostgreSQL - Hyperscale (Citus)This article describes how to configure and access metric alerts for Azure Database for PostgreSQL - Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: 80c061f72ce827df8f8354a5881c032c6f874fe1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063134"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Usare il portale di Azure per configurare avvisi sulle metriche per Il database di Azure per PostgreSQL - Hyperscale (Citus)Use the Azure portal to set up alerts on metrics for Azure Database for PostgreSQL - Hyperscale (Citus)

Questo articolo mostra come configurare avvisi di Database di Azure per PostgreSQL usando il portale di Azure. È possibile ricevere un avviso basato sulle [metriche](concepts-hyperscale-monitoring.md) di monitoraggio per i servizi di Azure.You can receive an alert based on monitoring metrics for your Azure services.

Verrà impostato un avviso da attivare quando il valore di una metrica specificata supera una soglia. L'avviso viene attivato quando la condizione viene soddisfatta per la prima volta e continua ad attivarsi in un secondo momento.

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:
* Inviare notifiche tramite posta elettronica all'amministratore del servizio e ai coamministratori.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook.

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:
* [Portale di Azure](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfaccia della riga di comando di AzureAzure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creare una regola di avviso in base a una metrica dal portale di Azure
1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di Database di Azure per PostgreSQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Avvisi** come illustrato di seguito:

   ![Selezionare le regole di avviso](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Selezionare **Nuova regola di avviso** (icona a z).

4. Verrà aperta la pagina **Crea regola** come illustrato di seguito. Specificare le informazioni necessarie:

   ![Form Aggiungi avviso per la metrica](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. Nella sezione **Condizione** selezionare **Aggiungi**.

6. Selezionare una metrica dall'elenco dei segnali per i quali ricevere un avviso. In questo esempio selezionare "Storage percent".
   
   ![Selezionare una metrica](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Configurare la logica di avviso:Configure the alert logic:

    * **Operatore** (ad es. "Maggiore di")
    * **Valore soglia** (ad es. 85 per cento)
    * La granularità di **aggregazione** del tempo in cui la regola metrica deve essere soddisfatta prima che l'avviso venga generato (ad es. "Negli ultimi 30 minuti")
    * e **Frequenza di valutazione** (ad es. "1 minuto")
   
   Al termine, selezionare **Chiudi**.

   ![Selezionare una metrica](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. Nella sezione **Gruppi di azioni** selezionare **Crea nuovo** per creare un nuovo gruppo di azioni per ricevere le notifiche relative all'avviso.

9. Compilare il modulo "Aggiungi gruppo di azioni" specificando nome, nome breve, sottoscrizione e gruppo di risorse.

    ![Gruppo di azioni](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Configurare un tipo di azione **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce).
    
    Scegliere "Email Azure Resource Manager Role" per inviare notifiche ai proprietari, ai collaboratori e ai lettori della sottoscrizione.
   
    Al termine, fare clic su **OK**.

    ![Gruppo di azioni](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Specificare un nome per la regola di avviso, una descrizione e il livello di gravità.

    ![Gruppo di azioni](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Selezionare **Crea regola di avviso** per creare l'avviso.

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

### <a name="managing-alerts"></a>Gestione degli avvisi

Dopo aver creato un avviso, è possibile selezionarlo ed eseguire le azioni seguenti:Once you've created an alert, you can select it and do the following actions:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.

## <a name="suggested-alerts"></a>Avvisi suggeriti

### <a name="disk-space"></a>Spazio su disco

Il monitoraggio e gli avvisi sono importanti per ogni gruppo di server Dischiene (Citus) di produzione. Il database PostgreSQL sottostante richiede spazio libero su disco per funzionare correttamente. Se il disco diventa pieno, il nodo del server di database passerà alla modalità offline e si rifiuterà di avviare fino a quando non sarà disponibile spazio. A questo punto, richiede una richiesta di supporto Microsoft per risolvere la situazione.

È consigliabile impostare avvisi di spazio su disco in ogni nodo di ogni gruppo di server, anche per l'utilizzo non di produzione. Gli avvisi sull'utilizzo dello spazio su disco forniscono l'avviso anticipato necessario per intervenire e mantenere integri i nodi. Per ottenere risultati ottimali, prova una serie di avvisi con un utilizzo del 75%, 85% e 95%. Le percentuali da scegliere dipendono dalla velocità di inserimento dei dati, poiché l'inserimento di dati veloce riempie il disco più velocemente.

Man mano che il disco si avvicina al limite di spazio, provare queste tecniche per ottenere più spazio libero:As the disk approaches its space limit, try these techniques to get more free space:

* Esaminare i criteri di conservazione dei dati. Spostare i dati meno recenti in cold storage, se possibile.
* Valutare la possibilità [di aggiungere nodi](howto-hyperscale-scaling.md#add-worker-nodes) al gruppo di server e di ribilanciare le partizioni. Il ribilanciamento distribuisce i dati su più computer.
* Valutare la possibilità di [aumentare la capacità](howto-hyperscale-scaling.md#increase-or-decrease-vcores-on-nodes) dei nodi di lavoro. Ogni lavoratore può avere fino a 2 TiB di stoccaggio. Tuttavia, l'aggiunta di nodi deve essere tentata prima di ridimensionare i nodi perché l'aggiunta di nodi viene completata più velocemente.

### <a name="cpu-usage"></a>Utilizzo di CPU

Il monitoraggio dell'utilizzo della CPU è utile per stabilire una linea di base per le prestazioni. Ad esempio, è possibile notare che l'utilizzo della CPU è in genere intorno 40-60%. Se l'utilizzo della CPU inizia improvvisamente a circa 95%, è possibile riconoscere un'anomalia. L'utilizzo della CPU può riflettere la crescita organica, ma può anche rivelare una query vagante. Quando si crea un avviso della CPU, impostare una granularità di aggregazione lunga per rilevare aumenti prolungati e ignorare i picchi momentanei.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [configurazione dei webhook negli avvisi](../azure-monitor/platform/alerts-webhooks.md).
* Leggere una [panoramica della raccolta di metriche](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) per verificare che il servizio sia disponibile e reattivo.
