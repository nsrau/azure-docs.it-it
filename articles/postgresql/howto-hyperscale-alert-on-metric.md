---
title: Configurare gli avvisi-scalabilità iperscala (CITUS)-database di Azure per PostgreSQL
description: Questo articolo descrive come configurare e accedere agli avvisi delle metriche per database di Azure per PostgreSQL-iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: 73705434aef3ee438c02fbfd6502d30e7620b695
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026455"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Usare il portale di Azure per configurare gli avvisi sulle metriche per database di Azure per PostgreSQL-iperscalabilità (CITUS)

Questo articolo mostra come configurare avvisi di Database di Azure per PostgreSQL usando il portale di Azure. È possibile ricevere un avviso in base alle [metriche di monitoraggio](concepts-hyperscale-monitoring.md) per i servizi di Azure.

Verrà impostato un avviso da attivare quando il valore di una metrica specificata supera una soglia. L'avviso viene attivato quando la condizione viene soddisfatta per la prima volta e continua a essere attivata in seguito.

È possibile configurare un avviso affinché esegua le azioni seguenti al momento dell'attivazione:
* Inviare notifiche tramite posta elettronica all'amministratore e ai coamministratori del servizio.
* Inviare un messaggio di posta elettronica ad altri indirizzi specificati.
* Chiamare un webhook.

È possibile configurare regole di avviso e ottenere informazioni su di esse tramite:
* [Azure portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Interfaccia della riga di comando di Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST di Monitoraggio di Azure](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creare una regola di avviso in base a una metrica dal portale di Azure
1. Nel [portale di Azure](https://portal.azure.com/) selezionare il server di Database di Azure per PostgreSQL da monitorare.

2. Nella sezione **Monitoraggio** della barra laterale selezionare **Avvisi** come illustrato di seguito:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="Selezionare le regole di avviso":::

3. Selezionare **nuova regola di avviso** (+ icona).

4. Verrà aperta la pagina **Crea regola** come illustrato di seguito. Specificare le informazioni necessarie:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Form Aggiungi avviso per la metrica":::

5. Nella sezione **condizione** selezionare **Aggiungi**.

6. Selezionare una metrica dall'elenco dei segnali per i quali ricevere un avviso. In questo esempio selezionare "Storage percent".
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Screenshot mostra la pagina Configura logica del segnale in cui è possibile visualizzare diversi segnali.":::

7. Configurare la logica di avviso:

    * **Operatore** (es. "Maggiore di")
    * **Valore soglia** (ad esempio 85%)
    * Quantità di tempo di **granularità di aggregazione** che deve essere soddisfatta prima che l'avviso venga attivato (ad esempio, "Negli ultimi 30 minuti"
    * e **la frequenza di valutazione** (ad esempio "1 minuto")
   
   Al termine, selezionare **Chiudi**.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Screenshot mostra il riquadro in cui è possibile configurare la logica di avviso.":::

8. Nella sezione **Gruppi di azioni** selezionare **Crea nuovo** per creare un nuovo gruppo di azioni per ricevere le notifiche relative all'avviso.

9. Compilare il modulo "Aggiungi gruppo di azioni" specificando nome, nome breve, sottoscrizione e gruppo di risorse.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="Screenshot mostra il form Aggiungi gruppo di azioni in cui è possibile immettere i valori descritti.":::

10. Configurare un tipo di azione **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce).
    
    Scegliere "email Azure Resource Manager role" per inviare notifiche a proprietari, collaboratori e lettori della sottoscrizione.
   
    Al termine, fare clic su **OK**.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Screenshot che mostra il riquadro posta elettronica/S/S/push/voce.":::

11. Specificare un nome per la regola di avviso, una descrizione e il livello di gravità.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Screenshot che mostra il riquadro Dettagli avviso."::: 

12. Selezionare **Crea regola di avviso** per creare l'avviso.

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

### <a name="managing-alerts"></a>Gestione degli avvisi

Dopo aver creato un avviso, è possibile selezionarlo ed eseguire le azioni seguenti:

* Visualizzare un grafico che mostra la soglia della metrica e i valori effettivi del giorno precedente in relazione all'avviso.
* Scegliere l'opzione **Modifica** o **Elimina** per la regola di avviso.
* Scegliere l'opzione **Disabilita** o **Abilita** per l'avviso per interrompere temporaneamente o riprendere la ricezione delle notifiche.

## <a name="suggested-alerts"></a>Avvisi consigliati

### <a name="disk-space"></a>Spazio su disco

Il monitoraggio e gli avvisi sono importanti per tutti i gruppi di server di produzione iperscala (CITUS). Per il corretto funzionamento del database PostgreSQL sottostante è necessario lo spazio libero su disco. Se il disco si riempie, il nodo del server di database passerà offline e rifiuterà l'avvio fino a quando non sarà disponibile spazio. A questo punto, è necessaria una richiesta di supporto tecnico Microsoft per risolvere il problema.

Si consiglia di impostare gli avvisi di spazio su disco in ogni nodo di ogni gruppo di server, anche per l'utilizzo non di produzione. Gli avvisi di utilizzo dello spazio su disco forniscono l'avviso di anticipo necessario per intervenire e garantire l'integrità dei nodi. Per ottenere risultati ottimali, provare una serie di avvisi al 75%, 85% e 95% di utilizzo. Le percentuali da scegliere dipendono dalla velocità di inserimento dei dati, perché l'inserimento rapido dei dati riempie il disco più velocemente.

Quando il disco si avvicina al limite di spazio, provare queste tecniche per ottenere maggiore spazio libero:

* Esaminare i criteri di conservazione dei dati. Spostare i dati meno recenti nell'archiviazione a freddo, se possibile.
* Prendere in considerazione l' [aggiunta di nodi](howto-hyperscale-scale-grow.md#add-worker-nodes) al gruppo di server e il ribilanciamento delle partizioni. Il ribilanciamento distribuisce i dati tra più computer.
* Si consiglia di [aumentare la capacità](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) dei nodi del ruolo di lavoro. Ogni thread di lavoro può avere fino a 2 TiB di archiviazione. Tuttavia, l'aggiunta di nodi deve essere tentata prima di ridimensionare i nodi perché l'aggiunta di nodi viene completata più velocemente.

### <a name="cpu-usage"></a>Utilizzo della CPU

Il monitoraggio dell'utilizzo della CPU è utile per stabilire una linea di base per le prestazioni. Ad esempio, è possibile notare che l'utilizzo della CPU è in genere pari al 40-60%. Se l'utilizzo della CPU inizia improvvisamente al passaggio del 95%, è possibile riconoscere un'anomalia. L'utilizzo della CPU può riflettere la crescita organica, ma potrebbe anche rivelare una query randagia. Quando si crea un avviso CPU, impostare una granularità di aggregazione lunga per rilevare aumenti prolungati e ignorare i picchi momentanei.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [configurazione dei webhook negli avvisi](../azure-monitor/platform/alerts-webhooks.md).
* Leggere una [panoramica della raccolta di metriche](../azure-monitor/platform/data-platform.md) per verificare che il servizio sia disponibile e reattivo.
