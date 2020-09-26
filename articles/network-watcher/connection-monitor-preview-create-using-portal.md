---
title: Anteprima di crea monitoraggio connessione-portale di Azure
titleSuffix: Azure Network Watcher
description: Informazioni su come creare un monitoraggio connessione (anteprima) usando il portale di Azure.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: b783a1434ebf6f8e1ad645d69f9f54a4c9c03e7f
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362921"
---
# <a name="create-a-connection-monitor-preview-using-the-azure-portal"></a>Creare un monitoraggio connessione (anteprima) utilizzando il portale di Azure

Informazioni su come creare un monitoraggio connessione (anteprima) per monitorare la comunicazione tra le risorse usando il portale di Azure. Supporta le distribuzioni ibride e cloud di Azure.

## <a name="before-you-begin"></a>Prima di iniziare 

Nei monitoraggi connessione creati in monitoraggio connessione (anteprima) è possibile aggiungere come origini sia i computer locali sia le VM di Azure. Questi monitoraggi di connessione possono inoltre monitorare la connettività agli endpoint. Gli endpoint possono trovarsi in Azure o in qualsiasi altro URL o indirizzo IP.

Il monitoraggio connessione (anteprima) include le seguenti entità:


* **Risorsa di monitoraggio della connessione** : una risorsa di Azure specifica dell'area. Tutte le entità seguenti sono proprietà di una risorsa di monitoraggio della connessione.
* **Endpoint** : un'origine o una destinazione che partecipa ai controlli di connettività. Esempi di endpoint includono 
    * Macchine virtuali di Azure
    * Azure reti virtuali
    * Subnet di Azure
    * Agenti locali
    * Subnet locali
    * Rete personalizzata locale che include più subnet
    * URL e indirizzi IP 
* **Configurazione di test** : una configurazione specifica del protocollo per un test. In base al protocollo scelto, è possibile definire la porta, le soglie, la frequenza di test e altri parametri.
* **Gruppo di test** : il gruppo che contiene gli endpoint di origine, gli endpoint di destinazione e le configurazioni di test. Un monitoraggio connessione può contenere più di un gruppo di test.
* **Test** : la combinazione di un endpoint di origine, un endpoint di destinazione e una configurazione di test. Un test è il livello più granulare in cui sono disponibili i dati di monitoraggio. I dati di monitoraggio includono la percentuale di controlli non riusciti e il tempo di round trip (RTT).

    ![Diagramma che mostra un monitoraggio della connessione, che definisce la relazione tra i gruppi di test e i test](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create"></a>Passaggi da creare

Per creare un monitoraggio connessione (anteprima) utilizzando il portale di Azure, attenersi alla procedura seguente:

1. Nella home page portale di Azure passare a **Network Watcher**.
1. Nella sezione **monitoraggio** a sinistra selezionare **monitoraggio connessione (anteprima)**.
1. Vengono visualizzati tutti i monitoraggi connessione creati in monitoraggio connessione (anteprima). Per visualizzare i monitoraggi connessioni creati nell'esperienza classica di monitoraggio connessione, passare alla scheda **monitoraggio connessione** .

    ![Screenshot che mostra i monitoraggi connessione creati in monitoraggio connessione (anteprima)](./media/connection-monitor-2-preview/cm-resource-view.png)   
    
1. Nell'angolo in alto a sinistra del dashboard di **monitoraggio connessione (anteprima)** selezionare **Crea**.

   ![Screenshot che mostra la scheda nozioni di base in Connection Monitor](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Nella scheda informazioni di **base** immettere le informazioni per il monitoraggio della connessione:
   * **Nome del monitoraggio della connessione** : aggiungere il nome del monitoraggio della connessione. Usare le regole di denominazione standard per le risorse di Azure.
   * **Sottoscrizione** : scegliere una sottoscrizione per il monitoraggio della connessione.
   * **Area** : scegliere un'area per il monitoraggio della connessione. È possibile selezionare solo le macchine virtuali di origine create in questa area.
   * **Configurazione dell'area di lavoro** : l'area di lavoro include i dati di monitoraggio. È possibile usare un'area di lavoro personalizzata o l'area di lavoro predefinita. 
       * Per utilizzare l'area di lavoro predefinita, selezionare la casella di controllo. 
       * Per scegliere un'area di lavoro personalizzata, deselezionare la casella di controllo. Quindi scegliere la sottoscrizione e l'area per l'area di lavoro personalizzata. 
1. Nella parte inferiore della scheda selezionare **Avanti: gruppi di test**.

   ![Screenshot che mostra la scheda Crea gruppo di test in monitoraggio connessione](./media/connection-monitor-2-preview/create-tg.png)

1. Aggiungere le origini, le destinazioni e le configurazioni di test nei gruppi di test. Per configurare i gruppi di test, vedere [creare gruppi di test in Connection Monitor](#create-test-groups-in-a-connection-monitor). 
1. Nella parte inferiore della scheda selezionare **Avanti: crea avvisi**.

   ![Screenshot che mostra il riquadro in cui si creano gli avvisi](./media/connection-monitor-2-preview/create-alert.png)

1. Per creare avvisi, vedere [creare avvisi nel monitoraggio della connessione](#create-alerts-in-connection-monitor)
1. Nella parte inferiore della scheda selezionare **Avanti: verifica + crea**.

  ![Screenshot del monitoraggio della connessione, che mostra la scheda verifica + crea](./media/connection-monitor-2-preview/review-create-cm.png)

1. Nella scheda **revisione e creazione** esaminare le informazioni di base e i gruppi di test prima di creare il monitoraggio della connessione. Se è necessario modificare il monitoraggio della connessione:
   * Per modificare i dettagli di base, selezionare l'icona della matita.
   * Per modificare un gruppo di test, selezionarlo.

   > [!NOTE] 
   > La scheda **Verifica + crea** Mostra il costo mensile durante la fase di anteprima di monitoraggio connessione. Attualmente la colonna **costo corrente** non mostra alcun addebito. Quando il monitoraggio della connessione diventa disponibile a livello generale, in questa colonna verrà visualizzato un addebito mensile. 
   > 
   > Anche nella fase di anteprima di monitoraggio connessione vengono applicati gli addebiti per l'inserimento Log Analytics.

1. Quando si è pronti per creare il monitoraggio della connessione, fare clic su **Crea**nella parte inferiore della scheda **Verifica + crea** .

Il monitoraggio connessione (anteprima) crea la risorsa di monitoraggio della connessione in background.

## <a name="create-test-groups-in-a-connection-monitor"></a>Creare gruppi di test in un monitoraggio della connessione

Ogni gruppo di test in un monitoraggio della connessione include origini e destinazioni che vengono testate sui parametri di rete. Sono testati per la percentuale di controlli che hanno esito negativo e il RTT sulle configurazioni di test.

Dal portale di Azure, per creare un gruppo di test in un monitoraggio della connessione, è necessario specificare i valori per i campi seguenti:

* **Disabilita gruppo di test** : è possibile selezionare questo campo per disabilitare il monitoraggio per tutte le origini e le destinazioni specificate dal gruppo di test. Questa selezione è deselezionata per impostazione predefinita.
* **Nome** : assegnare un nome al gruppo di test.
* **Origini** : è possibile specificare le VM di Azure e i computer locali come origini se gli agenti sono installati su di essi. Per installare un agente per l'origine, vedere [Install Monitoring Agents](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
   * Per scegliere gli agenti di Azure, selezionare la scheda **endpoint di Azure** . Qui vengono visualizzate solo le macchine virtuali associate all'area specificata al momento della creazione del monitoraggio connessione. Per impostazione predefinita, le macchine virtuali vengono raggruppate nella sottoscrizione a cui appartengono. Questi gruppi sono compressi. 
   
       È possibile eseguire il drill-down dal livello di sottoscrizione ad altri livelli nella gerarchia:

      **Sottoscrizione**  >  di **Gruppi**  >  di risorse **Reti virtuali**  >  **Subnet**  >  **VM con agenti**

      È anche possibile modificare il valore del campo **Group by** per avviare l'albero da qualsiasi altro livello. Se, ad esempio, si esegue il raggruppamento in base alla rete virtuale, vengono visualizzate le VM con agenti nella gerarchia **reti virtuali**  >  **subnet**  >  **VM con agenti**.
       Se si seleziona una VNET, una subnet o una singola macchina virtuale, l'ID risorsa corrispondente viene impostato come endpoint. Per impostazione predefinita, tutte le macchine virtuali nella VNET o nella subnet selezionata con Network Watcher estensione partecipano al monitoraggio. Per ridurre l'ambito, selezionare subnet/agenti specifici o modificare il valore della proprietà ambito. 

      ![Screenshot del monitoraggio della connessione, che mostra il pannello Aggiungi origini e la scheda agenti di Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Per scegliere gli agenti locali, selezionare la scheda **non-Azure Agents** . Per impostazione predefinita, gli agenti sono raggruppati in aree di lavoro in base all'area. Per tutte queste aree di lavoro è configurata la soluzione Monitoraggio prestazioni rete. 
   
       Se è necessario aggiungere Monitoraggio prestazioni rete all'area di lavoro, ottenerla da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Per informazioni su come aggiungere Monitoraggio prestazioni rete, vedere [monitoraggio delle soluzioni in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       Nella vista **Crea monitoraggio connessione** , nella scheda **nozioni di base** , viene selezionata l'area predefinita. Se si modifica l'area, è possibile scegliere agenti dalle aree di lavoro nella nuova area.  È possibile selezionare uno o più agenti o subnet. Nella visualizzazione subnet è possibile selezionare indirizzi IP specifici per il monitoraggio. L'aggiunta di più subnet creerà una rete locale personalizzata denominata "OnPremises_Network_1". È anche possibile modificare il valore del campo **Group by** in Raggruppa per subnet.

      ![Screenshot del monitoraggio della connessione, che mostra il pannello Aggiungi origini e la scheda agenti non Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)

   * È anche possibile scegliere gli endpoint usati di recente, usando la scheda **endpoint recente** 
   
   * Al termine della configurazione delle origini, selezionare **fine**. È comunque possibile modificare le proprietà di base come il nome dell'endpoint facendo clic sull'endpoint nella vista crea gruppo di test. 

* **Destinazioni** : è possibile monitorare la connettività alle VM di Azure, ai computer locali o a qualsiasi endpoint (un indirizzo IP pubblico, un URL o un nome di dominio completo) specificandone le destinazioni. In un singolo gruppo di test è possibile aggiungere VM di Azure, computer locale, URL di Office 365, URL di Dynamics 365 ed endpoint personalizzati.

    * Per scegliere macchine virtuali di Azure come destinazioni, selezionare la scheda **endpoint di Azure** . Per impostazione predefinita, le macchine virtuali di Azure sono raggruppate in una gerarchia di sottoscrizione che si trova nella stessa area selezionata nella vista **Crea monitoraggio connessione** , nella scheda **nozioni di base** . È possibile modificare l'area e scegliere macchine virtuali di Azure dall'area appena selezionata. Sarà quindi possibile eseguire il drill-down dal livello di sottoscrizione ad altri livelli nella gerarchia, proprio come gli endpoint di Azure di origine.
     È possibile selezionare reti virtuali, subnet o singole macchine virtuali, in modo analogo agli endpoint di Azure di origine. Se si seleziona una VNET, una subnet o una singola macchina virtuale, l'ID risorsa corrispondente viene impostato come endpoint. Per impostazione predefinita, tutte le macchine virtuali nella VNET o nella subnet selezionata con Network Watcher estensione partecipano al monitoraggio. Per ridurre l'ambito, selezionare subnet/agenti specifici o modificare il valore della proprietà ambito. 

       ![Screenshot del riquadro Aggiungi destinazioni, che mostra la scheda VM di Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Screenshot del riquadro Aggiungi destinazioni, che mostra il livello di sottoscrizione](./media/connection-monitor-2-preview/add-azure-dests2.png)
    
    * Per scegliere gli agenti non Azure come destinazioni, selezionare la scheda endpoint non Azure. Per impostazione predefinita, gli agenti sono raggruppati in aree di lavoro in base all'area. Per tutte queste aree di lavoro è configurata la soluzione Monitoraggio prestazioni rete. Se è necessario aggiungere Monitoraggio prestazioni rete all'area di lavoro, ottenerla da Azure Marketplace. Per informazioni su come aggiungere Monitoraggio prestazioni rete, vedere monitoraggio delle soluzioni in monitoraggio di Azure. Nella vista crea monitoraggio connessione, nella scheda nozioni di base, viene selezionata l'area predefinita. Se si modifica l'area, è possibile scegliere agenti dalle aree di lavoro nella nuova area. È possibile selezionare uno o più agenti o subnet. Nella visualizzazione subnet è possibile selezionare indirizzi IP specifici per il monitoraggio. L'aggiunta di più subnet creerà una rete locale personalizzata denominata "OnPremises_Network_1".  
    
     ![Screenshot del riquadro Aggiungi destinazioni non Azure](./media/connection-monitor-2-preview/add-non-azure-dest.png)
    
    * Per scegliere gli endpoint pubblici come destinazioni, selezionare la scheda **indirizzi esterni** . L'elenco degli endpoint include gli URL di test di Office 365 e gli URL di test di Dynamics 365, raggruppati in base al nome. Oltre a questi endpoint, è possibile scegliere un endpoint creato in altri gruppi di test nello stesso monitoraggio della connessione. 
    
        Per aggiungere un nuovo endpoint, nell'angolo in alto a destra selezionare **+ endpoint**. Specificare quindi un nome di endpoint e un URL, un indirizzo IP o un FQDN.

       ![Screenshot che mostra la posizione in cui aggiungere gli endpoint pubblici come destinazioni nel monitoraggio della connessione](./media/connection-monitor-2-preview/add-endpoints.png)

    * Per scegliere gli endpoint usati di recente, passare alla scheda **endpoint recenti**   .
    * Al termine della scelta delle destinazioni, selezionare **fine**. È comunque possibile modificare le proprietà di base come il nome dell'endpoint facendo clic sull'endpoint nella vista crea gruppo di test. 

* **Configurazioni di test** : è possibile associare una o più configurazioni di test in un gruppo di test. Creare una nuova configurazione di test utilizzando la scheda "nuova configurazione" oppure utilizzare una configurazione di test utilizzata negli altri gruppi di test nello stesso monitoraggio della connessione tramite la scheda "Scegli esistente".

    * **Nome** : nome della configurazione di test.
    * **Protocollo** : scegliere TCP, ICMP o http. Per impostare HTTP su HTTPS, selezionare **http** come protocollo e selezionare **443** come porta.
        * **Crea configurazione di test di rete** : questa casella di controllo viene visualizzata solo se si seleziona **http** nel campo **protocollo** . Selezionare questa casella per creare un'altra configurazione di test che usa le stesse origini e destinazioni specificate in altre parti della configurazione. La configurazione di test appena creata è denominata `<the name of your test configuration>_networkTestConfig` .
        * **Disabilitare traceroute** : questo campo si applica quando il protocollo è TCP o ICMP. Selezionare questa casella per arrestare le origini dall'individuazione della topologia e della RTT hop-by-hop.
    * **Porta di destinazione** : è possibile personalizzare questo campo con una porta di destinazione di propria scelta.
        * Ascolto sulla porta: questo campo si applica quando il protocollo è TCP. Selezionare questa casella per aprire la porta TCP scelta se non è già aperta. 
    * **Frequenza test** : utilizzare questo campo per scegliere la frequenza con cui le origini eseguiranno il ping delle destinazioni sul protocollo e sulla porta specificati. È possibile scegliere 30 secondi, 1 minuto, 5 minuti, 15 minuti o 30 minuti. Selezionare personalizzata per immettere la frequenza desiderata tra 30 secondi e 30 minuti. Le origini proveranno la connettività alle destinazioni in base al valore scelto.  Se ad esempio si seleziona 30 secondi, le origini verificheranno la connettività alla destinazione almeno una volta in un periodo di 30 secondi.
    * **Soglia di esito positivo** : è possibile impostare le soglie nei parametri di rete seguenti:
       * **Controlli non riusciti** : impostare la percentuale di controlli che possono avere esito negativo quando le origini controllano la connettività alle destinazioni usando i criteri specificati. Per il protocollo TCP o ICMP, la percentuale di controlli non riusciti può essere equivalente alla percentuale di perdita di pacchetti. Per il protocollo HTTP, questo campo rappresenta la percentuale di richieste HTTP che non hanno ricevuto risposta.
       * **Tempo di round trip** : impostare il RTT in millisecondi per il tempo necessario per la connessione alla destinazione tramite la configurazione di test.
    
       ![Screenshot che mostra dove configurare una configurazione di test in Connection Monitor](./media/connection-monitor-2-preview/add-test-config.png)
       
## <a name="create-alerts-in-connection-monitor"></a>Creazione di avvisi nel monitoraggio della connessione

È possibile configurare avvisi per i test che non riescono in base alle soglie impostate nelle configurazioni di test.   

Dal portale di Azure, per creare avvisi in un monitoraggio della connessione, è necessario specificare i valori per i campi seguenti: 

- Crea avviso: è possibile selezionare questo campo per creare un avviso sulle metriche in monitoraggio di Azure. Se si seleziona questa impostazione, gli altri campi vengono abilitati per la modifica. Verranno applicati addebiti aggiuntivi per l'avviso in base ai [prezzi per gli avvisi](https://azure.microsoft.com/pricing/details/monitor/) 

- Ambito: > della risorsa e dell'ambito > gerarchia. questa operazione è precompilata in base ai valori specificati nella scheda nozioni di base 

- Condition-> l'avviso viene creato nella metrica "risultato del test (anteprima)". Quando il risultato del test del monitoraggio della connessione non è riuscito, viene attivata la regola di avviso. 

- Gruppo di azione: è possibile scegliere di immettere l'indirizzo di posta elettronica direttamente oppure è possibile scegliere di creare avvisi tramite gruppi di azioni. Se si sceglie di immettere l'indirizzo di posta elettronica direttamente, viene creato un gruppo di azione con il nome NPM email ActionGroup e l'ID di posta elettronica viene aggiunto al gruppo di azioni. Se si sceglie di usare i gruppi di azioni, sarà necessario selezionare un gruppo di azioni creato in precedenza. Per informazioni su come creare un gruppo di azione, vedere qui. Una volta che l'avviso è stato creato correttamente, è possibile usare il collegamento Gestisci avvisi per gestire gli avvisi. 

- Nome regola di avviso: nome del monitoraggio connessione 

- Abilita regola al momento della creazione: la regola di avviso verrà abilitata in base alla condizione. Disabilitare questa operazione se si vuole creare la regola ma non abilitarla. 

    ![Screenshot che mostra la creazione di un avviso nel riquadro monitoraggio connessione](./media/connection-monitor-2-preview/create-alert-filled.png)

## <a name="scale-limits"></a> Limiti di scalabilità

I monitoraggi connessione presentano i limiti di scalabilità seguenti:

* Numero massimo di monitoraggi di connessione per ogni sottoscrizione per area: 100
* Numero massimo di gruppi di test per connessione: 20
* Numero massimo di origini e destinazioni per monitoraggio connessione: 100
* Numero massimo di configurazioni di test per ogni monitoraggio connessione: 2 tramite il portale di Azure

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [su come analizzare i dati di monitoraggio e impostare gli avvisi](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Informazioni [su come diagnosticare i problemi nella rete](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
