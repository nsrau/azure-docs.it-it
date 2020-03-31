---
title: Monitoraggio connessione (anteprima) Documenti Microsoft
description: Informazioni su come usare Connection Monitor (anteprima) per monitorare le comunicazioni di rete in un ambiente distribuito.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 8f3a6f002fbebe215699c9b97a6dce63177c446f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599327"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitoraggio della connettività di rete con Monitoraggio connessione (anteprima)Network Connectivity Monitoring with Connection Monitor (Preview)

Connection Monitor (Preview) provides unified end-to-end connection monitoring in Azure Network Watcher. La funzionalità Monitoraggio connessione (anteprima) supporta le distribuzioni di distribuzioni di distribuzioni di applicazioni ibride e cloud di Azure.The Connection Monitor (Preview) feature supports hybrid and Azure cloud deployments. Network Watcher fornisce strumenti per monitorare, diagnosticare e visualizzare le metriche correlate alla connettività per le distribuzioni di Azure.Network Watcher provides tools to monitor, diagnose, and view connectivity-related metrics for your Azure deployments.

Di seguito sono riportati alcuni casi d'uso per Connection Monitor (anteprima):

- La macchina virtuale del server Web front-end comunica con una macchina virtuale del server di database in un'applicazione a più livelli. Si desidera verificare la connettività di rete tra le due macchine virtuali.
- Si desidera che le macchine virtuali nell'area Stati Uniti orientali eserivano le macchine virtuali nell'area Stati Uniti centrale e si desidera confrontare le latenze di rete tra aree.
- Si dispone di più uffici locali a Seattle, Washington e ad Ashburn, Virginia. I siti di Office si connettono agli URL di Office 365. Per gli utenti degli URL di Office 365, confrontare le latenze tra Seattle e Ashburn.
- L'applicazione ibrida richiede la connettività a un endpoint di Archiviazione di Azure.Your hybrid application needs connectivity to an Azure Storage endpoint. Il sito locale e l'applicazione azure si connettono allo stesso endpoint di Archiviazione di Azure.Your on-premises site and your Azure application connect to the same Azure Storage endpoint. Si desidera confrontare le latenze del sito locale con le latenze dell'applicazione Azure.
- Si vuole verificare la connettività tra le configurazioni locali e le macchine virtuali di Azure che ospitano l'applicazione cloud.

Nella fase di anteprima, Connection Monitor combina il meglio di due funzionalità: la funzionalità Network Watcher [Connection Monitor](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) e la funzionalità [Monitoraggio connettività](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) servizio di Network Performance Monitor (NPM).

Di seguito sono riportati alcuni vantaggi di Monitoraggio connessione (anteprima):

* Esperienza unificata e intuitiva per le esigenze di monitoraggio ibrido e di Azure
* Monitoraggio della connettività tra aree e tra aree di lavoro tra aree di lavoroCross-region, cross-workspace connectivity monitoring
* Frequenze di sondaggio più elevate e migliore visibilità sulle prestazioni di rete
* Avvisi più rapidi per le distribuzioni ibride
* Supporto per i controlli di connettività basati su HTTP, TCP e ICMP 
* Metriche e supporto di Log Analytics per le configurazioni di test di Azure e non AzureMetrics and Log Analytics support for both Azure and non-Azure test setups

![Diagramma che illustra l'interazione di Monitoraggio connessione con macchine virtuali di Azure, host non Di Azure, endpoint e percorsi di archiviazione dei dati](./media/connection-monitor-2-preview/hero-graphic.png)

Per iniziare a utilizzare Connection Monitor (anteprima) per il monitoraggio, attenersi alla seguente procedura: 

1. Installare gli agenti di monitoraggio.
1. Abilitare Network Watcher per l'abbonamento.
1. Creare un monitor di connessione.
1. Impostare l'analisi dei dati e gli avvisi.
1. Diagnosticare i problemi nella rete.

Nelle sezioni seguenti vengono fornite informazioni dettagliate per questi passaggi.

## <a name="install-monitoring-agents"></a>Installare gli agenti di monitoraggio

Connection Monitor si basa su file eseguibili leggeri per eseguire i controlli di connettività.  Supporta i controlli di connettività sia dagli ambienti Azure che dagli ambienti locali. Il file eseguibile usato dipende dal fatto che la macchina virtuale sia ospitata in Azure o in locale.

### <a name="agents-for-azure-virtual-machines"></a>Agenti per le macchine virtuali di AzureAgents for Azure virtual machines

Per fare in modo che Connection Monitor riconosca le macchine virtuali di Azure come origini di monitoraggio, installare l'estensione della macchina virtuale Network Watcher Agent in esse. Questa estensione è nota anche come *estensione di Network Watcher*. Le macchine virtuali di Azure richiedono che l'estensione attivi il monitoraggio end-to-end e altre funzionalità avanzate. 

È possibile installare l'estensione Network Watcher quando si [crea una macchina virtuale.](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm) È inoltre possibile installare, configurare e risolvere [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) i problemi relativi a windows per windows e windows separatamente. [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)

Le regole per un gruppo di sicurezza di rete o un firewall possono bloccare la comunicazione tra l'origine e la destinazione. Connection Monitor rileva questo problema e lo visualizza come messaggio diagnostico nella topologia. Per abilitare il monitoraggio della connessione, verificare che le regole del gruppo di sicurezza di rete e del firewall consentano pacchetti su TCP o ICMP tra l'origine e la destinazione.

### <a name="agents-for-on-premises-machines"></a>Agenti per computer locali

Per fare in modo che Connection Monitor riconosca i computer locali come origini per il monitoraggio, installare l'agente di Log Analytics nei computer. Abilitare quindi la soluzione Monitoraggio prestazioni rete. Questi agenti sono collegati alle aree di lavoro di Log Analytics, pertanto è necessario configurare l'ID dell'area di lavoro e la chiave primaria prima che gli agenti possano avviare il monitoraggio.

Per installare l'agente di Log Analytics per computer Windows, vedere Estensione macchina virtuale di Monitor di [Azure per Windows.](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

Se il percorso include firewall o appliance virtuali di rete , assicurarsi che la destinazione sia raggiungibile.

## <a name="enable-network-watcher-on-your-subscription"></a>Abilitare Network Watcher per l'abbonamentoEnable Network Watcher on your subscription

Tutte le sottoscrizioni che dispongono di una rete virtuale sono abilitate con Network Watcher.All subscriptions that have a virtual network are enabled with Network Watcher. Quando si crea una rete virtuale nella sottoscrizione, Network Watcher viene abilitato automaticamente nell'area e nella sottoscrizione della rete virtuale. Questa abilitazione automatica non influisce sulle risorse né comporta addebiti. Assicurarsi che Network Watcher non sia disabilitato in modo esplicito nella sottoscrizione. 

Per ulteriori informazioni, consultate [Abilitare Network Watcher.](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione 

Connection Monitor monitora le comunicazioni a intervalli regolari. Ti informa dei cambiamenti di raggiungibilità e latenza. È inoltre possibile controllare la topologia di rete corrente e cronologica tra gli agenti di origine e gli endpoint di destinazione.

Le origini possono essere macchine virtuali di Azure o macchine locali con un agente di monitoraggio installato. Gli endpoint di destinazione possono essere URL di Office 365, URL Dynamics 365, URL personalizzati, ID risorsa VM di Azure, IPv4, IPv6, FQDN o qualsiasi nome di dominio.

### <a name="access-connection-monitor-preview"></a>Access Connection Monitor (anteprima)

1. Nella home page del portale di Azure passare a **Network Watcher**.
1. A sinistra, nella sezione **Monitoraggio,** selezionare **Connection Monitor (anteprima)**.
1. Vengono visualizzati tutti i monitoraggi delle connessioni creati in Connection Monitor (anteprima). Per visualizzare i monitoraggi delle connessioni creati nell'esperienza classica di Connection Monitor, passare alla scheda **Monitoraggio connessione.**

    ![Screenshot che mostra i monitoraggi delle connessioni creati in Connection Monitor (anteprima)Screenshot, that and connection monitors that were created in Connection Monitor (Preview)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione

Nei monitoraggi delle connessioni creati in Monitoraggio connessione (anteprima) è possibile aggiungere sia macchine locali che macchine virtuali di Azure come origini. Questi monitoraggi delle connessioni possono anche monitorare la connettività agli endpoint. Gli endpoint possono trovarsi in Azure o in qualsiasi altro URL o IP.

Connection Monitor (anteprima) include le entità seguenti:Connection Monitor (Preview) includes the following entities:

* **Risorsa di monitoraggio connessione:** una risorsa di Azure specifica dell'area. Tutte le entità seguenti sono proprietà di una risorsa di monitoraggio connessione.
* **Endpoint:** origine o destinazione che partecipa ai controlli di connettività. Esempi di endpoint includono macchine virtuali di Azure, agenti locali, URL e indirizzi IP.
* **Configurazione di** test: una configurazione specifica del protocollo per un test. In base al protocollo scelto, è possibile definire la porta, le soglie, la frequenza di test e altri parametri.
* **Gruppo di test:** gruppo che contiene endpoint di origine, endpoint di destinazione e configurazioni di test. Un monitor di connessione può contenere più di un gruppo di test.
* **Test:** combinazione di un endpoint di origine, un endpoint di destinazione e una configurazione di test. Un test è il livello più granulare in cui sono disponibili i dati di monitoraggio. I dati di monitoraggio includono la percentuale di controlli non riusciti e il tempo di andata e ritorno (RTT).

 ![Diagramma che mostra un monitoraggio della connessione, definizione della relazione tra gruppi di test e test](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Creare un monitoraggio della connessione dal portale di AzureCreate a connection monitor from the Azure portal

Per creare un monitoraggio della connessione dal portale di Azure, eseguire la procedura seguente:To create a connection monitor from the Azure portal, follow these steps:

1. Nel dashboard **Monitoraggio connessione (anteprima)** selezionare **Crea**nell'angolo superiore sinistro.
1. Nella scheda **Nozioni di base,** immettere le informazioni per il monitor di connessione:
   * **Nome monitor di connessione:** aggiungere il nome del monitor di connessione. Usare le regole di denominazione standard per le risorse di Azure.Use the standard naming rules for Azure resources.
   * **Abbonamento:** scegliere un abbonamento per il monitoraggio della connessione.
   * **Regione:** scegliere un'area per il monitor di connessione. È possibile selezionare solo le macchine virtuali di origine create in quest'area.
   * **Configurazione dell'area di lavoro:** l'area di lavoro contiene i dati di monitoraggio. È possibile utilizzare un'area di lavoro personalizzata o l'area di lavoro predefinita. 
       * Per utilizzare l'area di lavoro predefinita, selezionare la casella di controllo. 
       * Per scegliere un'area di lavoro personalizzata, deselezionare la casella di controllo. Scegliere quindi la sottoscrizione e l'area geografica per l'area di lavoro personalizzata. 
1. Nella parte inferiore della scheda selezionare **Successivo: Gruppi di test**.

   ![Screenshot della scheda Nozioni di base in Connection Monitor](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Nella scheda **Gruppi di test** selezionare il gruppo **Test.** Per configurare i gruppi di test, vedere Creare gruppi di [test in Monitoraggio connessioni](#create-test-groups-in-a-connection-monitor). 
1. Nella parte inferiore della scheda, selezionare **Avanti: Revisione : crea** per rivedere il monitor di connessione.

   ![Screenshot della scheda Gruppi di test e del riquadro in cui si aggiungono i dettagli del gruppo di test](./media/connection-monitor-2-preview/create-tg.png)

1. Esaminare **Review + create** le informazioni di base e i gruppi di test prima di creare il monitoraggio della connessione. Se è necessario modificare il monitor di connessione:
   * Per modificare i dettagli di base, selezionare l'icona a matita.
   * Per modificare un gruppo di test, selezionarlo.

   > [!NOTE] 
   > La scheda **Revisione e creazione** mostra il costo mensile durante la fase di anteprima di Connection Monitor. Attualmente, la colonna **COST CORRENTE** non mostra alcun addebito. Quando Connection Monitor diventa generalmente disponibile, questa colonna mostrerà un addebito mensile. 
   > 
   > Anche nella fase di anteprima di Monitoraggio connessione, vengono applicati i costi di inserimento di Log Analytics.Even in the Connection Monitor preview stage, Log Analytics ingestion charges apply.

1. Quando si è pronti per creare il monitor di connessione, nella parte inferiore della scheda **Revisione e creazione** selezionare **Crea**.

   ![Screenshot di Connection Monitor, che mostra la scheda Revisione e creazione](./media/connection-monitor-2-preview/review-create-cm.png)

Connection Monitor (Preview) crea la risorsa monitor di connessione in background.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Creare un monitoraggio della connessione tramite ARMClientCreate a connection monitor by using ARMClient

Utilizzare il codice seguente per creare un monitoraggio della connessione tramite ARMClient.Use the following code to create a connection monitor by using ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premises agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

Ecco il comando di distribuzione:Here's the deployment command:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Creare gruppi di test in un monitoraggio della connessioneCreate test groups in a connection monitor

Ogni gruppo di test in un monitoraggio della connessione include origini e destinazioni che vengono testate sui parametri di rete. Vengono testati per la percentuale di controlli che hanno esito negativo e la RTT sulle configurazioni di test.

Dal portale di Azure, per creare un gruppo di test in un monitoraggio della connessione, specificare i valori per i campi seguenti:From the Azure portal, to create a test group in a connection monitor, you specify values for the following fields:

* **Disabilita gruppo** di test: è possibile selezionare questo campo per disabilitare il monitoraggio per tutte le origini e le destinazioni specificate dal gruppo di test. Questa selezione è deselezionata per impostazione predefinita.
* **Nome:** assegnare un nome al gruppo di test.
* **Origini:** è possibile specificare sia macchine virtuali di Azure che computer locali come origini se gli agenti sono installati in esse. Per installare un agente per l'origine, vedere [Installare gli agenti](#install-monitoring-agents)di monitoraggio .
   * Per scegliere gli agenti di Azure, selezionare la scheda Agenti di **Azure.To** choose Azure agents, select the Azure Agents tab. Qui vengono visualizzate solo le macchine virtuali associate all'area specificata al momento della creazione del monitoraggio della connessione. Per impostazione predefinita, le macchine virtuali vengono raggruppate nella sottoscrizione a cui appartengono. Questi gruppi sono compressi. 
   
       È possibile eseguire il drill-down dal livello di sottoscrizione ad altri livelli della gerarchia:You can drill down from the Subscription level to other levels in the hierarchy:

      **Gruppi** > **di risorse di** > sottoscrizione VNETS Subnetvm con gli agentiSubscription Resource groups**VNETs** > **Subnets** > **VMs with agents**

      È inoltre possibile modificare il valore del campo **Raggruppa per** per avviare l'albero da qualsiasi altro livello. Ad esempio, se si raggruppa in base alla rete virtuale, vengono visualizzate le macchine virtuali con agenti che dispongono di agenti nelle**macchine virtuali** **VNETs** > **Subnets** > della gerarchia con agenti .

      ![Screenshot di Connection Monitor con il riquadro Aggiungi origini e la scheda Agenti di Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Per scegliere gli agenti locali, selezionare la scheda Agenti non di Azure.To choose on-premises agents, select the **Non-Azure Agents** tab. Per impostazione predefinita, gli agenti vengono raggruppati in aree di lavoro per area di lavoro. In tutte queste aree di lavoro è configurata la soluzione Monitoraggio prestazioni rete. 
   
       Se è necessario aggiungere Monitoraggio prestazioni rete all'area di lavoro, ottenerlo da [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) Per informazioni su come aggiungere Monitoraggio prestazioni rete, vedere [Monitoraggio di soluzioni in Monitoraggio di Azure.For](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)information about how to add Network Performance Monitor, see Monitoring solutions in Azure Monitor . 
   
       Nella visualizzazione **Crea monitoraggio connessione,** nella scheda **Nozioni di base,** l'area predefinita è selezionata. Se si modifica l'area, è possibile scegliere gli agenti dalle aree di lavoro nella nuova area. È inoltre possibile modificare il valore del campo **Raggruppa per** per raggruppare in base alle subnet.

      ![Screenshot di Connection Monitor con il riquadro Aggiungi origini e la scheda Agenti non Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Per esaminare gli agenti di Azure e non Azure selezionati, passare alla scheda **Revisione.To** review the Azure and non-Azure agents that you selected, go to the Review tab.

      ![Screenshot di Connection Monitor con il pannello Aggiungi origini e la scheda Revisione](./media/connection-monitor-2-preview/review-sources.png)

   * Al termine della configurazione delle sorgenti, nella parte inferiore del pannello **Aggiungi origini** selezionare **Fatto**.

* **Destinazioni:** è possibile monitorare la connettività alle macchine virtuali di Azure o a qualsiasi endpoint (ip pubblico, URL o FQDN) specificandole come destinazioni. In un singolo gruppo di test è possibile aggiungere macchine virtuali di Azure, URL di Office 365, URL Dynamics 365 ed endpoint personalizzati.

    * Per scegliere Macchine virtuali di Azure come destinazioni, selezionare la scheda Macchine virtuali di **Azure.To** choose Azure VMs as destinations, select the Azure VMs tab. Per impostazione predefinita, le macchine virtuali di Azure sono raggruppate in una gerarchia di sottoscrizioni che si trova nella stessa area selezionata nella visualizzazione **Crea monitoraggio connessione** nella scheda **Nozioni di base.** È possibile modificare l'area e scegliere Macchine virtuali di Azure dall'area appena selezionata. È quindi possibile eseguire il drill-down dal livello di sottoscrizione ad altri livelli della gerarchia, ad esempio il livello di azure Agents.Then you can drill down from Subscription level to other levels in the hierarchy, like the Azure Agents level.

       ![Screenshot del riquadro Aggiungi destinazioni con la scheda Macchine virtuali di Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Screenshot del riquadro Aggiungi destinazioni con il livello di sottoscrizione](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Per scegliere gli endpoint come destinazioni, selezionare la scheda **Endpoint.To choose endpoints** as destinations, select the Endpoints tab. L'elenco degli endpoint include gli URL di test di Office 365 e gli URL di test di Dynamics 365, raggruppati per nome. Oltre a questi endpoint, è possibile scegliere un endpoint creato in altri gruppi di test nello stesso monitoraggio delle connessioni. 
    
        Per aggiungere un nuovo punto finale, nell'angolo in alto a destra, selezionare **Endpoint**. Specificare quindi un nome e un URL dell'endpoint, un indirizzo IP o un nome di dominio completo.

       ![Screenshot che mostra dove aggiungere gli endpoint come destinazioni in Connection Monitor](./media/connection-monitor-2-preview/add-endpoints.png)

    * Per esaminare le macchine virtuali di Azure e gli endpoint scelti, selezionare la scheda **Revisione.To** review the Azure VMs and endpoints that you chose, select the Review tab.
    * Al termine della scelta delle destinazioni, selezionare **Fatto**.

* **Configurazioni di** test: è possibile associare configurazioni di test in un gruppo di test. Il portale di Azure consente una sola configurazione di test per ogni gruppo di test, ma è possibile usare ARMClient per aggiungerne altre.

    * **Nome:** assegnare un nome alla configurazione di test.
    * **Protocollo:** scegliere TCP, ICMP o HTTP. Per modificare HTTP in HTTPS, selezionare **HTTP** come protocollo e selezionare **443** come porta.
        * **Crea configurazione test** di rete: questa casella di controllo viene visualizzata solo se si seleziona **HTTP** nel campo **Protocollo.** Selezionare questa casella per creare un'altra configurazione di test che utilizza le stesse origini e destinazioni specificate altrove nella configurazione. La configurazione di test `<the name of your test configuration>_networkTestConfig`appena creata è denominata .
        * **Disabilita traceroute:** questo campo si applica ai gruppi di test il cui protocollo è TCP o ICMP. Selezionare questa casella per impedire alle origini di individuare la topologia e l'RTT hop-by-hop.
    * **Porta di destinazione:** è possibile personalizzare questo campo con una porta di destinazione di propria scelta.
    * **Frequenza test:** utilizzare questo campo per scegliere la frequenza con cui le origini verranno visualizzate nelle destinazioni nel protocollo e nella porta specificati. È possibile scegliere 30 secondi, 1 minuto, 5 minuti, 15 minuti o 30 minuti. Le origini testeranno la connettività alle destinazioni in base al valore scelto.  Ad esempio, se si seleziona 30 secondi, le origini controlleranno la connettività alla destinazione almeno una volta in un periodo di 30 secondi.
    * **Soglia riuscita:** è possibile impostare soglie sui seguenti parametri di rete:
       * **Controlli non riusciti:** impostare la percentuale di controlli che possono avere esito negativo quando le origini controllano la connettività alle destinazioni utilizzando i criteri specificati. Per il protocollo TCP o ICMP, la percentuale di controlli non riusciti può essere equiparata alla percentuale di perdita di pacchetti. Per il protocollo HTTP, questo campo rappresenta la percentuale di richieste HTTP che non hanno ricevuto risposta.
       * **Tempo di andata** e ritorno: impostare il RTT in millisecondi per il tempo che le origini possono richiedere per connettersi alla destinazione tramite la configurazione di test.
    
       ![Screenshot che mostra dove configurare una configurazione di test in Connection Monitor](./media/connection-monitor-2-preview/add-test-config.png)

Tutte le origini, le destinazioni e le configurazioni di test aggiunte a un gruppo di test vengono suddivise in singoli test. Ecco un esempio di come le origini e le destinazioni sono suddivise:

* Gruppo di test: TG1
* Fonti: 3 (A, B, C)
* Destinazioni: 2 (D, E)
* Configurazioni di test: 2 (Config 1, Config 2)
* Totale test creati: 12

| Numero di prova | Source (Sorgente) | Destination | Configurazione di test |
| --- | --- | --- | --- |
| 1 | Una  | D | Configurazione 1 |
| 2 | Una  | D | Configurazione 2 |
| 3 | Una  | E | Configurazione 1 |
| 4 | Una  | E | Configurazione 2 |
| 5 | b | D | Configurazione 1 |
| 6 | b | D | Configurazione 2 |
| 7 | b | E | Configurazione 1 |
| 8 | b | E | Configurazione 2 |
| 9 | C | D | Configurazione 1 |
| 10 | C | D | Configurazione 2 |
| 11 | C | E | Configurazione 1 |
| 12 | C | E | Configurazione 2 |

### <a name="scale-limits"></a> Limiti di scalabilità

I monitoraggi delle connessioni hanno i seguenti limiti di scala:

* Numero massimo di monitoraggi di connessione per sottoscrizione per area: 100
* Numero massimo di gruppi di test per monitor di connessione: 20
* Numero massimo di origini e destinazioni per monitor di connessione: 100
* Numero massimo di configurazioni di test per monitor di connessione: 
    * 20 tramite ARMClient
    * 2 tramite il portale di Azure

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analizzare i dati di monitoraggio e impostare gli avvisiAnalyze monitoring data and set alerts

Dopo aver creato un monitoraggio della connessione, le origini controllano la connettività alle destinazioni in base alla configurazione di test.

### <a name="checks-in-a-test"></a>Controlli in un test

In base al protocollo scelto nella configurazione di test, Connection Monitor (Preview) esegue una serie di controlli per la coppia origine-destinazione. I controlli vengono eseguiti in base alla frequenza di test scelta.

Se si utilizza HTTP, il servizio calcola il numero di risposte HTTP che hanno restituito un codice di risposta. Il risultato determina la percentuale di controlli non riusciti. Per calcolare RTT, il servizio misura il tempo tra una chiamata HTTP e la risposta.

Se si utilizza TCP o ICMP, il servizio calcola la percentuale di perdita di pacchetti per determinare la percentuale di controlli non riusciti. Per calcolare RTT, il servizio misura il tempo necessario per ricevere il riconoscimento (ACK) per i pacchetti inviati. Se sono stati abilitati i dati traceroute per i test di rete, è possibile visualizzare la perdita hop per hop e la latenza per la rete locale.

### <a name="states-of-a-test"></a>Stati di prova

In base ai dati restituiti dai controlli, i test possono avere i seguenti stati:

* **Pass:** valori effettivi per la percentuale di controlli non riusciti e RTT rientrano nelle soglie specificate.
* **Fail:** i valori effettivi per la percentuale di controlli non riusciti o RTT hanno superato le soglie specificate. Se non viene specificata alcuna soglia, un test raggiunge lo stato Non superato quando la percentuale di controlli non riusciti è 100.
* **Avviso:** non è stato specificato alcun criterio per la percentuale di controlli non riusciti. In assenza di criteri specificati, Connection Monitor (Anteprima) assegna automaticamente una soglia. Quando tale soglia viene superata, lo stato del test diventa Avviso.

### <a name="data-collection-analysis-and-alerts"></a>Raccolta, analisi e avvisi dei dati

I dati raccolti da Connection Monitor (anteprima) vengono archiviati nell'area di lavoro di Log Analytics.The data that Connection Monitor (Preview) collects is stored in the Log Analytics workspace. Questa area di lavoro viene configurata al momento della creazione del monitoraggio della connessione. 

I dati di monitoraggio sono disponibili anche in Metriche di Monitoraggio di Azure.Monitoring data is also available in Azure Monitor Metrics. È possibile usare Log Analytics per mantenere i dati di monitoraggio per tutto il tempo che si desidera. Monitoraggio di Azure archivia le metriche solo per 30 giorni per impostazione predefinita. 

È possibile [impostare avvisi basati su metriche sui dati.](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)

#### <a name="monitoring-dashboards"></a>Monitoraggio dei dashboard

Nei dashboard di monitoraggio viene visualizzato un elenco dei monitoraggi delle connessioni a cui è possibile accedere per le sottoscrizioni, le aree, gli indicatori di data e ora, le origini e i tipi di destinazione.

Quando si passa a Connection Monitor (anteprima) da Network Watcher, è possibile visualizzare i dati:

* **Monitoraggio connessione:** elenco di tutti i monitoraggi delle connessioni creati per le sottoscrizioni, le aree, gli indicatori di data e ora, le origini e i tipi di destinazione. Questa vista è quella predefinita.
* **Gruppi di test:** elenco di tutti i gruppi di test creati per le sottoscrizioni, le aree, gli indicatori di data e ora, le origini e i tipi di destinazione. Questi gruppi di test non vengono filtrati in base ai monitoraggi delle connessioni.
* **Test:** elenco di tutti i test eseguiti per sottoscrizioni, aree, timestamp, origini e tipi di destinazione. Questi test non vengono filtrati in base a monitoraggi connessioni o gruppi di test.

Nell'immagine seguente, le tre visualizzazioni dati sono indicate dalla freccia 1.

Nel dashboard è possibile espandere ogni monitoraggio della connessione per visualizzarne i gruppi di test. È quindi possibile espandere ogni gruppo di test per visualizzare i test eseguiti in esso. 

È possibile filtrare un elenco in base a:

* **Filtri di primo livello:** scegliere sottoscrizioni, aree geografiche, origini timestamp e tipi di destinazione. Vedere la casella 2 nell'immagine seguente.
* **Filtri basati sullo stato:** filtro in base allo stato del monitoraggio della connessione, del gruppo di test o del test. Vedere la freccia 3 nell'immagine seguente.
* **Filtri personalizzati:** scegliere **Seleziona tutto** per eseguire una ricerca generica. Per eseguire la ricerca in base a un'entità specifica, selezionare dall'elenco a discesa. Vedere la freccia 4 nell'immagine seguente.

![Screenshot che mostra come filtrare le visualizzazioni di monitoraggi connessioni, gruppi di test e test in Monitoraggio connessione (anteprima)](./media/connection-monitor-2-preview/cm-view.png)

Ad esempio, per esaminare tutti i test in Connection Monitor (anteprima) in cui l'IP di origine è 10.192.64.56:For example, to look at all tests in Connection Monitor (Preview) where the source IP is 10.192.64.56:
1. Modificare la visualizzazione in **Test**.
1. Nel campo di ricerca digitare *10.192.64.56*
1. Nell'elenco a discesa selezionare **Origini**.

Per visualizzare solo i test non superati in Connection Monitor (anteprima) in cui l'IP di origine è 10.192.64.56:
1. Modificare la visualizzazione in **Test**.
1. Per il filtro basato sullo stato, selezionare **Fail**.
1. Nel campo di ricerca digitare *10.192.64.56*
1. Nell'elenco a discesa selezionare **Origini**.

Per visualizzare solo i test non superati in Connection Monitor (anteprima) in cui la destinazione è outlook.office365.com:
1. Impostare la visualizzazione su **Test**.
1. Per il filtro basato sullo stato, selezionare **Fail**.
1. Nel campo di ricerca, immettere *outlook.office365.com*
1. Nell'elenco a discesa selezionare **Destinazioni**.

   ![Screenshot che mostra una visualizzazione filtrata per mostrare solo i test non riusciti per la destinazione Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Per visualizzare le tendenze in RTT e la percentuale di controlli non riusciti per un monitoraggio della connessione:
1. Selezionare il monitor di connessione che si desidera analizzare. Per impostazione predefinita, i dati di monitoraggio sono organizzati per gruppo di test.

   ![Screenshot che mostra le metriche per un monitoraggio della connessione, visualizzate in base al gruppo di test](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Scegliere il gruppo di test che si desidera analizzare.

   ![Schermata che mostra dove selezionare un gruppo di test](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Vengono visualizzati i primi cinque test non riusciti del gruppo di test, in base all'RTT o alla percentuale di controlli non superati. Per ogni test vengono visualizzate le teRT E le linee di tendenza per la percentuale di controlli non riusciti.
1. Selezionare un test dall'elenco o scegliere un altro test da analizzare. Per l'intervallo di tempo e la percentuale di controlli non riusciti, vengono visualizzati i valori soglia ed effettivo. Per RTT vengono visualizzati i valori per soglia, media, minimo e massimo.

   ![Screenshot che mostra i risultati di un test per RTT e la percentuale di controlli non riusciti](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Modificare l'intervallo di tempo per visualizzare più dati.
1. Modificare la visualizzazione per visualizzare origini, destinazioni o configurazioni di test. 
1. Scegliere un'origine basata su test non superati ed esaminare i primi cinque test non superati. Ad esempio, scegliere **Visualizza per** > **origini** e **Visualizza per** > **destinazioni** per analizzare i test pertinenti nel monitor di connessione.

   ![Screenshot che mostra le metriche delle prestazioni per i primi cinque test non riusciti](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Per visualizzare le tendenze in RTT e la percentuale di controlli non riusciti per un gruppo di test:

1. Selezionare il gruppo di test che si desidera analizzare. 

    Per impostazione predefinita, i dati di monitoraggio sono organizzati in base a origini, destinazioni e configurazioni di test (test). Successivamente, è possibile modificare la visualizzazione da gruppi di test a origini, destinazioni o configurazioni di test. Scegliere quindi un'entità per analizzare i primi cinque test non superati. Ad esempio, modificare la visualizzazione in origini e destinazioni per analizzare i test pertinenti nel monitor di connessione selezionato.
1. Scegliere il test che si desidera analizzare.

   ![Schermata che mostra dove selezionare un test](./media/connection-monitor-2-preview/tg-drill.png)

    Per l'intervallo di tempo e per la percentuale di controlli non riusciti, vengono visualizzati i valori di soglia e i valori effettivi. Per RTT, vengono visualizzati i valori per soglia, media, minimo e massimo. Vengono inoltre visualizzati avvisi generati per il test selezionato.
1. Modificare l'intervallo di tempo per visualizzare più dati.

Per visualizzare le tendenze in RTT e la percentuale di controlli non superati per un test:
1. Selezionare l'origine, la destinazione e la configurazione di test che si desidera analizzare.

    Per l'intervallo di tempo e per la percentuale di controlli non riusciti, vengono visualizzati i valori di soglia e i valori effettivi. Per RTT, vengono visualizzati i valori per soglia, media, minimo e massimo. Vengono inoltre visualizzati avvisi generati per il test selezionato.

   ![Screenshot che mostra le metriche per un test](./media/connection-monitor-2-preview/test-drill.png)

1. Per visualizzare la topologia di rete, selezionare **Topologia**.

   ![Schermata della scheda Topologia di rete](./media/connection-monitor-2-preview/test-topo.png)

1. Per visualizzare i problemi identificati, nella topologia selezionare un hop nel percorso. Questi hop sono risorse di Azure. Questa funzionalità non è attualmente disponibile per le reti locali.

   ![Screenshot che mostra un collegamento hop selezionato nella scheda Topologia](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Log queries in Log Analytics

Usare Log Analytics per creare visualizzazioni personalizzate dei dati di monitoraggio. Tutti i dati visualizzati dall'interfaccia utente provengono da Log Analytics.All data that the UI displays is from Log Analytics. È possibile analizzare in modo interattivo i dati nel repository. Correlare i dati di Integrità agente o altre soluzioni basate su Log Analytics.Correlate the data from Agent Health or other solutions that are based in Log Analytics. Esportare i dati in Excel o Power BI o creare un collegamento condivisibile.

#### <a name="metrics-in-azure-monitor"></a>Metriche in Monitoraggio di Azure

Nei monitoraggi di connessione creati prima dell'esperienza di Connection Monitor (anteprima): sono disponibili tutte e quattro le metriche: % probe non riusciti, AverageRoundtripMs, ChecksFailedPercent (anteprima) e RoundTripTimeMs (anteprima). Nei monitoraggi delle connessioni creati nell'esperienza di Connection Monitor (anteprima), i dati sono disponibili solo per le metriche contrassegnate con *(Anteprima).*

![Screenshot che mostra le metriche in Connection Monitor (anteprima)Screenshot:](./media/connection-monitor-2-preview/monitor-metrics.png)

Quando si usano le metriche, impostare il tipo di risorsa come Microsoft.Network/networkWatchers/connectionMonitorsWhen you use metrics, set the resource type as Microsoft.Network/networkWatchers/connectionMonitors

| Metrica | Nome visualizzato | Unità | Tipo di aggregazione | Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % di probe non riusciti | Percentuale | Media | Percentuale di probe di monitoraggio della connettività non riuscita. | Nessuna dimensione |
| AverageRoundtripMs | Tempo di andata e ritorno (tempo di andata e ritorno) (tempo di andata e ritorno) (ms) | Millisecondi | Media | RTT di rete media per i probe di monitoraggio della connettività inviati tra l'origine e la destinazione. |             Nessuna dimensione |
| ChecksFailedPercent (anteprima)ChecksFailedPercent (Preview) | % Controlli non riusciti (anteprima)% Checks Failed (Preview) | Percentuale | Media | Percentuale di controlli non superati per un test. | ConnectionMonitorResourceId <br>IndirizzoOrigine <br>SourceName <br>IdOrigine <br>SourceType <br>Protocollo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>NomeGruppoTestGroupName <br>NomeConfigurazioneTestConfigurationName <br>Region |
| RoundTripTimeMs (anteprima)RoundTripTimeMs (Preview) | Tempo di andata e ritorno (ms) (anteprima)Round-trip Time (ms) (Preview) | Millisecondi | Media | RTT per i controlli inviati tra l'origine e la destinazione. Questo valore non è mediato. | ConnectionMonitorResourceId <br>IndirizzoOrigine <br>SourceName <br>IdOrigine <br>SourceType <br>Protocollo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>NomeGruppoTestGroupName <br>NomeConfigurazioneTestConfigurationName <br>Region |

#### <a name="metric-alerts-in-azure-monitor"></a>Avvisi di metrica in Monitoraggio di AzureMetri alerts in Azure Monitor

Per creare un avviso in Monitoraggio di Azure:To create an alert in Azure Monitor:

1. Scegliere la risorsa di monitoraggio connessione creata in Connection Monitor (anteprima).
1. Assicurarsi che **Metric** venga visualizzato come tipo di segnale per il monitor di connessione.
1. In **Aggiungi condizione**, per Nome **segnale**, selezionare **ChecksFailedPercent(Preview)** o **RoundTripTimeMs(Preview)**.
1. Per **Tipo di segnale**, scegliere **Metriche**. Ad esempio, selezionare **ChecksFailedPercent(Preview)**.
1. Vengono elencate tutte le dimensioni per la metrica. Scegliere il nome della quota e il valore di quota. Ad esempio, selezionare Indirizzo di **origine** e quindi immettere l'indirizzo IP di qualsiasi origine nel monitor di connessione.
1. In **Logica di avviso**immettere i dettagli seguenti:In Alert Logic , fill in the following details:
   * **Tipo di condizione**: **Statico**.
   * **Condizione** e **soglia**.
   * **Granularità di aggregazione e frequenza di valutazione:** Connection Monitor (anteprima) aggiorna i dati ogni minuto.
1. In **Azioni**scegliere il gruppo di azioni.
1. Fornire i dettagli dell'avviso.
1. Creare la regola di avviso.

   ![Screenshot showing the Create rule area in Azure Monitor; "Indirizzo di origine" e "Nome endpoint di origine" evidenziati](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnosticare i problemi nella rete

Connection Monitor (Preview) consente di diagnosticare i problemi nel monitor di connessione e nella rete. I problemi nella rete ibrida vengono rilevati dagli agenti di Log Analytics installati in precedenza. I problemi in Azure vengono rilevati dall'estensione Network Watcher.Issues in Azure are detected by the Network Watcher extension. 

È possibile visualizzare i problemi nella rete di Azure nella topologia di rete.

Per le reti le cui origini sono macchine virtuali locali, è possibile rilevare i problemi seguenti:For networks whose sources are on-premises VMs, the following issues can be detected:

* Timeout della richiesta.
* Endpoint non risolto dal DNS: temporaneo o permanente. URL non valido.
* Nessun host trovato.
* Origine impossibile connettersi alla destinazione. Obiettivo non raggiungibile tramite ICMP.
* Problemi relativi ai certificati: 
    * Certificato client necessario per autenticare l'agente. 
    * L'elenco di rilocazione dei certificati non è accessibile. 
    * Il nome host dell'endpoint non corrisponde al soggetto o al nome alternativo del soggetto del certificato. 
    * Certificato radice mancante nell'archivio Autorità di certificazione attendibili del computer locale dell'origine. 
    * Il certificato SSL è scaduto, non valido, revocato o incompatibile.

Per le reti le cui origini sono macchine virtuali di Azure, è possibile rilevare i problemi seguenti:For networks whose sources are Azure VMs, the following issues can be detected:

* Problemi dell'agente:
    * L'agente si è fermato.
    * Risoluzione DNS non riuscita.
    * Nessun'applicazione o listener in ascolto sulla porta di destinazione.
    * Impossibile aprire l'socket.
* Problemi relativi allo stato della macchina virtuale:VM state issues: 
    * Avvio in corso
    * Stopping
    * Arrestato
    * Deallocazione
    * Deallocato
    * Riavviare
    * Non assegnato
* Voce della tabella ARP è mancante.
* Il traffico è stato bloccato a causa di problemi del firewall locale o delle regole del gruppo di sicurezza di rete.
* Problemi relativi al gateway di rete virtuale: 
    * Percorsi mancanti.
    * Il tunnel tra due gateway è disconnesso o mancante.
    * La seconda porta non è stata trovata dal tunnel.
    * Non sono state trovate informazioni di peering.
* Percorso mancante in Microsoft Edge.
* Traffico interrotto a causa di route di sistema o UDR.
* BGP non è abilitato nella connessione gateway.
* Il probe DIP è inattivo nel servizio di bilanciamento del carico.
