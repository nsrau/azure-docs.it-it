---
title: Creazione di un monitoraggio della connessione-portale di Azure
titleSuffix: Azure Network Watcher
description: Questo articolo descrive come creare un monitoraggio in Connection Monitor usando il portale di Azure.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: a6ba9c02b384c1b30977d962659983d600c7b2ac
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545721"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Creare un monitoraggio in Connection Monitor usando il portale di Azure

Informazioni su come usare il monitoraggio della connessione per monitorare la comunicazione tra le risorse. Questo articolo descrive come creare un monitoraggio usando il portale di Azure. Il monitoraggio della connessione supporta le distribuzioni ibride e cloud di Azure.


## <a name="before-you-begin"></a>Prima di iniziare 

Nei monitoraggi connessione creati tramite monitoraggio connessione è possibile aggiungere come origini sia computer locali che macchine virtuali di Azure. Questi monitoraggi di connessione possono inoltre monitorare la connettività agli endpoint. Gli endpoint possono trovarsi in Azure o in qualsiasi altro URL o IP.

Di seguito sono riportate alcune definizioni per iniziare:

* **Risorsa monitoraggio connessione**. Una risorsa di Azure specifica dell'area. Tutte le entità seguenti sono proprietà di una risorsa di monitoraggio della connessione.
* **Endpoint**. Origine o destinazione che partecipa ai controlli di connettività. Esempi di endpoint includono:
    * Macchine virtuali di Azure.
    * Reti virtuali di Azure.
    * Subnet di Azure.
    * Agenti locali.
    * Subnet locali.
    * Reti personalizzate locali che includono più subnet.
    * URL e indirizzi IP.
* **Configurazione di test**. Configurazione specifica del protocollo per un test. In base al protocollo scelto, è possibile definire la porta, le soglie, la frequenza di test e altri parametri.
* **Gruppo di test**. Il gruppo che contiene gli endpoint di origine, gli endpoint di destinazione e le configurazioni di test. Un monitoraggio connessione può contenere più di un gruppo di test.
* **Test**. Combinazione di un endpoint di origine, un endpoint di destinazione e una configurazione di test. Un test è il livello più granulare in cui sono disponibili i dati di monitoraggio. I dati di monitoraggio includono la percentuale di controlli non riusciti e il tempo di round trip (RTT).

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Diagramma che mostra un monitoraggio della connessione e definisce la relazione tra i gruppi di test e i test.":::


## <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione

Per creare un monitoraggio nel monitoraggio della connessione utilizzando la portale di Azure:

1. Nella home page portale di Azure passare a **Network Watcher**.
1. Nella sezione **monitoraggio** del riquadro sinistro selezionare **monitoraggio connessione**.

   Verranno visualizzati tutti i monitoraggi connessione creati in monitoraggio connessione. Per visualizzare i monitoraggi connessioni creati nel monitoraggio della connessione classica, passare alla scheda **monitoraggio connessione** .

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Screenshot che mostra i monitoraggi connessione creati in monitoraggio connessione.":::
   
    
1. Nell'angolo in alto a sinistra del dashboard di **monitoraggio della connessione** selezionare **Crea**.

   

1. Nella scheda informazioni di **base** immettere le informazioni per il monitoraggio della connessione: 
   * **Nome del monitoraggio della connessione**: immettere un nome per il monitoraggio della connessione. Usare le regole di denominazione standard per le risorse di Azure.
   * **Sottoscrizione**: selezionare una sottoscrizione per il monitoraggio della connessione.
   * **Area**: selezionare un'area per il monitoraggio della connessione. È possibile selezionare solo le macchine virtuali di origine create in questa area.
   * **Configurazione area di lavoro**: scegliere un'area di lavoro personalizzata o l'area di lavoro predefinita. L'area di lavoro include i dati di monitoraggio.
       * Per utilizzare l'area di lavoro predefinita, selezionare la casella di controllo. 
       * Per scegliere un'area di lavoro personalizzata, deselezionare la casella di controllo. Quindi selezionare la sottoscrizione e l'area per l'area di lavoro personalizzata. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Screenshot che mostra la scheda nozioni di base in Connection Monitor.":::
   
1. Nella parte inferiore della scheda selezionare **Avanti: gruppi di test**.

1. Aggiungere le origini, le destinazioni e le configurazioni di test nei gruppi di test. Per informazioni sulla configurazione dei gruppi di test, vedere [create test groups in Connection Monitor](#create-test-groups-in-a-connection-monitor). 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Screenshot che mostra la scheda gruppi di test in Connection Monitor.":::

1. Nella parte inferiore della scheda selezionare **Avanti: crea avvisi**. Per ulteriori informazioni sulla creazione di avvisi, vedere [create Alerts in Connection Monitor](#create-alerts-in-connection-monitor).

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Screenshot che mostra la scheda Crea avviso.":::

1. Nella parte inferiore della scheda selezionare **Avanti: verifica + crea**.

1. Nella scheda **revisione e creazione** esaminare le informazioni di base e i gruppi di test prima di creare il monitoraggio della connessione. Se è necessario modificare il monitoraggio della connessione, è possibile farlo tornando alle rispettive schede. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Screenshot che mostra la scheda verifica e crea in monitoraggio connessione.":::
   > [!NOTE] 
   > La scheda **Verifica e crea** Mostra il costo mensile durante la fase di monitoraggio della connessione. Attualmente la colonna **costo corrente/mese** non mostra alcun addebito. Quando il monitoraggio della connessione diventa disponibile a livello generale, in questa colonna verrà visualizzato un addebito mensile. 
   > 
   > Anche durante la fase di monitoraggio della connessione vengono applicati gli addebiti per l'inserimento Log Analytics.

1. Quando si è pronti per creare il monitoraggio della connessione, fare clic su **Crea** nella parte inferiore della scheda **Verifica + crea** .

Il monitoraggio connessione crea la risorsa di monitoraggio della connessione in background.

## <a name="create-test-groups-in-a-connection-monitor"></a>Creare gruppi di test in un monitoraggio della connessione

Ogni gruppo di test in un monitoraggio della connessione include origini e destinazioni che vengono testate sui parametri di rete. Sono testati per la percentuale di controlli che hanno esito negativo e il RTT sulle configurazioni di test.

Nel portale di Azure, per creare un gruppo di test in un monitoraggio della connessione, è necessario specificare i valori per i campi seguenti:

* **Disabilita gruppo di test**: è possibile selezionare questa casella di controllo per disabilitare il monitoraggio per tutte le origini e le destinazioni specificate dal gruppo di test. Questa selezione è deselezionata per impostazione predefinita.
* **Nome**: assegnare un nome al gruppo di test.
* **Origini**: è possibile specificare le VM di Azure e i computer locali come origini se gli agenti sono installati su di essi. Per informazioni sull'installazione di un agente per l'origine, vedere [Install Monitoring Agents](./connection-monitor-overview.md#install-monitoring-agents).
   * Per scegliere gli agenti di Azure, selezionare la scheda **endpoint di Azure** . Qui vengono visualizzate solo le macchine virtuali associate all'area specificata al momento della creazione del monitoraggio connessione. Per impostazione predefinita, le macchine virtuali vengono raggruppate nella sottoscrizione a cui appartengono. Questi gruppi sono compressi. 
   
       È possibile eseguire il drill-down dal livello di **sottoscrizione** ad altri livelli nella gerarchia:

      **Sottoscrizione**  >  di **Gruppo**  >  di risorse **VNET**  >  **Subnet**  >  di **VM con agenti**

      È anche possibile modificare il selettore **Group by** per avviare l'albero da qualsiasi altro livello. Ad esempio, se si esegue il raggruppamento in base alla rete virtuale, vengono visualizzate le VM con agenti nella gerarchia **VNET**  >  **subnet**  >  **VM con agenti**.

       Quando si seleziona una VNET, una subnet o una singola macchina virtuale, l'ID di risorsa corrispondente viene impostato come endpoint. Per impostazione predefinita, tutte le macchine virtuali nella VNET o nella subnet selezionata che hanno l'estensione Azure Network Watcher partecipano al monitoraggio. Per ridurre l'ambito, selezionare subnet o agenti specifici o modificare il valore della proprietà ambito. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Screenshot che mostra il riquadro Aggiungi origini e la scheda endpoint di Azure in monitoraggio connessione.":::

   * Per scegliere gli agenti locali, selezionare la scheda **endpoint non di Azure** . Per impostazione predefinita, gli agenti sono raggruppati in aree di lavoro in base all'area. Per tutte queste aree di lavoro è configurata la Monitoraggio prestazioni rete. 
   
       Se è necessario aggiungere Monitoraggio prestazioni rete all'area di lavoro, ottenerla da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Per informazioni su come aggiungere Monitoraggio prestazioni rete, vedere [monitoraggio delle soluzioni in monitoraggio di Azure](../azure-monitor/insights/solutions.md). 
   
       In **Crea monitoraggio connessione**, nella scheda **nozioni di base** , l'area predefinita è selezionata. Se si modifica l'area, è possibile scegliere agenti dalle aree di lavoro nella nuova area. È possibile selezionare uno o più agenti o subnet. Nella visualizzazione **subnet** è possibile selezionare indirizzi IP specifici per il monitoraggio. Se si aggiungono più subnet, verrà creata una rete locale personalizzata denominata **OnPremises_Network_1** . È anche possibile modificare il selettore **Group by** in Group by Agents.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Screenshot che mostra il riquadro Aggiungi origini e la scheda endpoint non di Azure in monitoraggio connessione.":::

   * Per scegliere gli endpoint usati di recente, è possibile usare la scheda **endpoint recente** 
   
   * Al termine della configurazione delle origini, fare clic su **fine** nella parte inferiore della scheda. È comunque possibile modificare le proprietà di base come il nome dell'endpoint selezionando l'endpoint nella visualizzazione del **gruppo crea test** . 

* **Destinazioni**: è possibile monitorare la connettività a una macchina virtuale di Azure, a un computer locale o a qualsiasi endpoint (un indirizzo IP pubblico, un URL o un nome di dominio completo) specificandone il valore come destinazione. In un singolo gruppo di test è possibile aggiungere macchine virtuali di Azure, computer locali, URL di Office 365, URL di Dynamics 365 ed endpoint personalizzati.

    * Per scegliere macchine virtuali di Azure come destinazioni, selezionare la scheda **endpoint di Azure** . Per impostazione predefinita, le macchine virtuali di Azure sono raggruppate in una gerarchia di sottoscrizione che si trova nell'area selezionata in **Crea monitoraggio connessione** nella scheda **nozioni di base** . È possibile modificare l'area e scegliere macchine virtuali di Azure dalla nuova area. È quindi possibile eseguire il drill-down dal livello di **sottoscrizione** ad altri livelli nella gerarchia, come è possibile quando si impostano gli endpoint di Azure di origine.

      È possibile selezionare reti virtuali, subnet o singole macchine virtuali, come è possibile quando si impostano gli endpoint di Azure di origine. Quando si seleziona una VNET, una subnet o una singola macchina virtuale, l'ID di risorsa corrispondente viene impostato come endpoint. Per impostazione predefinita, tutte le macchine virtuali nella VNET o nella subnet selezionata che hanno l'estensione Network Watcher partecipano al monitoraggio. Per ridurre l'ambito, selezionare subnet o agenti specifici o modificare il valore della proprietà ambito. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<screenshot che mostra il riquadro Aggiungi destinazioni e la scheda endpoint di Azure. >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<screenshot che mostra il riquadro Aggiungi destinazioni a livello di sottoscrizione. >":::
       
    
    * Per scegliere gli agenti non Azure come destinazioni, selezionare la scheda **endpoint non Azure** . Per impostazione predefinita, gli agenti sono raggruppati in aree di lavoro in base all'area. Tutte le aree di lavoro sono Monitoraggio prestazioni rete configurate. 
    
      Se è necessario aggiungere Monitoraggio prestazioni rete all'area di lavoro, ottenerla da Azure Marketplace. Per informazioni su come aggiungere Monitoraggio prestazioni rete, vedere [monitoraggio delle soluzioni in monitoraggio di Azure](../azure-monitor/insights/solutions.md). 

      In **Crea monitoraggio connessione**, nella scheda **nozioni di base**   , l'area predefinita è selezionata. Se si modifica l'area, è possibile scegliere agenti dalle aree di lavoro nella nuova area. È possibile selezionare uno o più agenti o subnet. Nella visualizzazione **subnet** è possibile selezionare indirizzi IP specifici per il monitoraggio. Se si aggiungono più subnet, verrà creata una rete locale personalizzata denominata **OnPremises_Network_1** .  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Screenshot che mostra il riquadro Aggiungi destinazioni e la scheda endpoint non di Azure.":::
    
    * Per scegliere gli endpoint pubblici come destinazioni, selezionare la scheda **indirizzi esterni** . L'elenco degli endpoint include gli URL di test di Office 365 e gli URL di test di Dynamics 365, raggruppati in base al nome. È anche possibile scegliere gli endpoint creati in altri gruppi di test nello stesso monitoraggio connessione. 
    
        Per aggiungere un endpoint, nell'angolo in alto a destra selezionare **Aggiungi endpoint**. Specificare quindi un nome di endpoint e un URL, un indirizzo IP o un FQDN.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Screenshot che mostra la posizione in cui aggiungere gli endpoint pubblici come destinazioni nel monitoraggio della connessione.":::

    * Per scegliere gli endpoint usati di recente, passare alla scheda **endpoint recente**   .
    * Al termine della scelta delle destinazioni, selezionare **fine**. È comunque possibile modificare le proprietà di base come il nome dell'endpoint selezionando l'endpoint nella visualizzazione del **gruppo crea test** . 

* **Configurazioni di test**: è possibile aggiungere una o più configurazioni di test a un gruppo di test. Creare una nuova configurazione di test usando la **nuova scheda configurazione** . In alternativa, aggiungere una configurazione di test da un altro gruppo di test nello stesso monitoraggio connessione dalla scheda **scegliere esistente** .

    * **Nome configurazione di test**: assegnare un nome alla configurazione di test.
    * **Protocollo**: selezionare **TCP**, **ICMP** o **http**. Per impostare HTTP su HTTPS, selezionare **http** come protocollo, quindi selezionare **443** come porta.
        * **Crea configurazione di test TCP**: questa casella di controllo viene visualizzata solo se si seleziona **http** nell'elenco di **protocolli** . Selezionare questa casella di controllo per creare un'altra configurazione di test che usi le stesse origini e destinazioni specificate altrove nella configurazione. La nuova configurazione di test è denominata **\<name of test configuration> _networkTestConfig**.
        * **Disabilitare traceroute**: questa casella di controllo si applica quando il protocollo è TCP o ICMP. Selezionare questa casella per arrestare le origini dall'individuazione della topologia e della RTT hop-by-hop.
    * **Porta di destinazione**: è possibile specificare una porta di destinazione di propria scelta.
        * **Ascolto sulla porta**: questa casella di controllo si applica quando il protocollo è TCP. Selezionare questa casella di controllo per aprire la porta TCP scelta se non è già aperta. 
    * **Frequenza test**: in questo elenco specificare la frequenza con cui le origini eseguiranno il ping delle destinazioni sul protocollo e sulla porta specificati. È possibile scegliere 30 secondi, 1 minuto, 5 minuti, 15 minuti o 30 minuti. Selezionare **personalizzata** per immettere un'altra frequenza compresa tra 30 secondi e 30 minuti. Le origini proveranno la connettività alle destinazioni in base al valore scelto. Se ad esempio si seleziona 30 secondi, le origini verificheranno la connettività alla destinazione almeno una volta in ogni 30 secondi.
    * **Soglia di esito positivo**: è possibile impostare le soglie nei parametri di rete seguenti:
       * **Controlli non riusciti**: impostare la percentuale di controlli che possono avere esito negativo quando le origini controllano la connettività alle destinazioni usando i criteri specificati. Per il protocollo TCP o ICMP, la percentuale di controlli non riusciti può essere equivalente alla percentuale di perdita di pacchetti. Per il protocollo HTTP, questo valore rappresenta la percentuale di richieste HTTP che non hanno ricevuto risposta.
       * **Tempo di round trip**: impostare il RTT, in millisecondi, per il tempo necessario per la connessione alla destinazione tramite la configurazione di test.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Screenshot che mostra dove configurare una configurazione di test in monitoraggio connessione.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Creazione di avvisi nel monitoraggio della connessione

È possibile configurare avvisi per i test che non riescono in base alle soglie impostate nelle configurazioni di test.

Nel portale di Azure, per creare avvisi per un monitoraggio della connessione, è necessario specificare i valori per questi campi: 

- **Crea avviso**: è possibile selezionare questa casella di controllo per creare un avviso sulle metriche in monitoraggio di Azure. Quando si seleziona questa casella di controllo, gli altri campi verranno abilitati per la modifica. Verranno applicati addebiti aggiuntivi per l'avviso, in base ai [prezzi per gli avvisi](https://azure.microsoft.com/pricing/details/monitor/). 

- **Ambito**  >  **Risorsa**  >  di **Gerarchia**: questi valori vengono compilati automaticamente in base ai valori specificati nella scheda **nozioni di base** .

- **Nome della condizione**: l'avviso viene creato sulla `Test Result(preview)` metrica. Quando il risultato del test di monitoraggio della connessione non è riuscito, viene attivata la regola di avviso. 

- **Nome gruppo di azioni**: è possibile immettere direttamente la posta elettronica oppure è possibile creare avvisi tramite gruppi di azioni. Se si immette direttamente il proprio indirizzo di posta elettronica, viene creato un gruppo di azione con il nome **NPM email ActionGroup** . L'ID di posta elettronica viene aggiunto al gruppo di azioni. Se si sceglie di usare i gruppi di azioni, è necessario selezionare un gruppo di azioni creato in precedenza. Per informazioni su come creare un gruppo di azione, vedere [creare gruppi di azioni nella portale di Azure](../azure-monitor/platform/action-groups.md). Dopo la creazione dell'avviso, è possibile [gestire gli avvisi](../azure-monitor/platform/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Nome regola di avviso**: il nome del monitoraggio connessione.

- **Abilita regola al momento della creazione**: selezionare questa casella di controllo per abilitare la regola di avviso in base alla condizione. Disabilitare questa casella di controllo se si desidera creare la regola senza abilitarla. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Screenshot che mostra la scheda Crea avviso in monitoraggio connessione.":::

## <a name="scale-limits"></a> Limiti di scalabilità

I monitoraggi connessione presentano questi limiti di scalabilità:

* Numero massimo di monitoraggi di connessione per ogni sottoscrizione per area: 100
* Numero massimo di gruppi di test per connessione: 20
* Numero massimo di origini e destinazioni per monitoraggio connessione: 100
* Numero massimo di configurazioni di test per ogni monitoraggio connessione: 2 tramite il portale di Azure

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come analizzare i dati di monitoraggio e impostare gli avvisi](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Informazioni [su come diagnosticare i problemi nella rete](./connection-monitor-overview.md#diagnose-issues-in-your-network).