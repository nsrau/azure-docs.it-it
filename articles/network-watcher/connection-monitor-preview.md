---
title: Monitoraggio connessione (anteprima) | Microsoft Docs
description: Informazioni su come utilizzare monitoraggio connessione (anteprima) per monitorare le comunicazioni di rete in un ambiente distribuito.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 52d33e7292ebe7b27eede2b89aa605780f826392
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737615"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Monitoraggio della connettività di rete con monitoraggio connessione (anteprima)

Monitoraggio connessione (anteprima) fornisce il monitoraggio della connessione end-to-end unificato in Azure Network Watcher. La funzionalità di monitoraggio connessione (anteprima) supporta le distribuzioni ibride e cloud di Azure. Network Watcher offre strumenti per monitorare, diagnosticare e visualizzare le metriche relative alla connettività per le distribuzioni di Azure.

Di seguito sono riportati alcuni casi d'uso per monitoraggio connessione (anteprima):

- La VM del server Web front-end comunica con una macchina virtuale del server di database in un'applicazione multilivello. Si vuole controllare la connettività di rete tra le due VM.
- Si vuole che le macchine virtuali nell'area Stati Uniti orientali per il ping delle macchine virtuali nell'area Stati Uniti centrali e si desideri confrontare le latenze di rete tra aree.
- Sono disponibili più siti di Office locali a Seattle, Washington e in Ashburn, Virginia. I siti di Office si connettono agli URL di Office 365. Per gli utenti degli URL di Office 365, confrontare le latenze tra Seattle e Ashburn.
- Per l'applicazione ibrida è necessaria la connettività a un endpoint di archiviazione di Azure. Il sito locale e l'applicazione Azure si connettono allo stesso endpoint di archiviazione di Azure. Si desidera confrontare le latenze del sito locale con le latenze dell'applicazione Azure.
- Si vuole controllare la connettività tra le installazioni locali e le macchine virtuali di Azure che ospitano l'applicazione cloud.

Nella fase di anteprima, il monitoraggio della connessione combina il meglio di due funzionalità: la funzionalità di monitoraggio della [connessione](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) Network Watcher e la funzionalità di [monitoraggio della connettività del servizio](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) di monitoraggio prestazioni rete (NPM).

Di seguito sono riportati alcuni vantaggi di monitoraggio connessione (anteprima):

* Esperienza unificata e intuitiva per le esigenze di Azure e di monitoraggio ibrido
* Monitoraggio della connettività tra aree di lavoro
* Frequenze di probe più elevate e visibilità migliore sulle prestazioni della rete
* Avvisi più veloci per le distribuzioni ibride
* Supporto per i controlli di connettività basati su HTTP, TCP e ICMP 
* Metriche e supporto Log Analytics per le configurazioni di test di Azure e non Azure

![Diagramma che illustra il modo in cui il monitoraggio delle connessioni interagisce con le VM di Azure, gli host non Azure, gli endpoint e le posizioni di archiviazione dei dati](./media/connection-monitor-2-preview/hero-graphic.png)

Per iniziare a utilizzare monitoraggio connessione (anteprima) per il monitoraggio, attenersi alla seguente procedura: 

1. Installare gli agenti di monitoraggio.
1. Abilitare Network Watcher sulla sottoscrizione.
1. Creazione di un monitoraggio della connessione.
1. Configurare gli avvisi e l'analisi dei dati.
1. Diagnosticare i problemi nella rete.

Le sezioni seguenti forniscono informazioni dettagliate su questi passaggi.

## <a name="install-monitoring-agents"></a>Installare gli agenti di monitoraggio

Il monitoraggio della connessione si basa sui file eseguibili leggeri per eseguire i controlli di connettività.  Supporta i controlli di connettività da ambienti Azure e ambienti locali. Il file eseguibile usato varia a seconda che la macchina virtuale sia ospitata in Azure o in locale.

### <a name="agents-for-azure-virtual-machines"></a>Agenti per macchine virtuali di Azure

Per fare in modo che il monitoraggio delle connessioni riconosca le VM di Azure come origini di monitoraggio, installare l'estensione della macchina virtuale di Network Watcher Agent. Questa estensione è nota anche come *estensione Network Watcher*. Le macchine virtuali di Azure richiedono l'estensione per attivare il monitoraggio end-to-end e altre funzionalità avanzate. 

È possibile installare l'estensione Network Watcher quando si [Crea una macchina virtuale](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm). È anche possibile installare, configurare e risolvere i problemi relativi all'estensione Network Watcher per [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows).

Le regole per un gruppo di sicurezza di rete (NSG) o un firewall possono bloccare la comunicazione tra l'origine e la destinazione. Il monitoraggio della connessione rileva questo problema e lo Mostra come un messaggio di diagnostica nella topologia. Per abilitare il monitoraggio della connessione, assicurarsi che le regole NSG e firewall consentano i pacchetti su TCP o ICMP tra l'origine e la destinazione.

### <a name="agents-for-on-premises-machines"></a>Agenti per computer locali

Per fare in modo che il monitoraggio della connessione riconosca i computer locali come origini per il monitoraggio, installare l'agente Log Analytics nei computer. Abilitare quindi la soluzione Monitoraggio prestazioni rete. Questi agenti sono collegati alle aree di lavoro Log Analytics, quindi è necessario configurare l'ID dell'area di lavoro e la chiave primaria prima che gli agenti possano avviare il monitoraggio.

Per installare l'agente di Log Analytics per i computer Windows, vedere [estensione macchina virtuale di monitoraggio di Azure per Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows).

Se il percorso include firewall o appliance virtuali di rete (appliance virtuali), assicurarsi che la destinazione sia raggiungibile.

## <a name="enable-network-watcher-on-your-subscription"></a>Abilitare Network Watcher sulla sottoscrizione

Tutte le sottoscrizioni con una rete virtuale sono abilitate con Network Watcher. Quando si crea una rete virtuale nella sottoscrizione, Network Watcher viene abilitata automaticamente nell'area e nella sottoscrizione della rete virtuale. Questa abilitazione automatica non influisce sulle risorse o è soggetta a un addebito. Verificare che Network Watcher non sia disabilitato in modo esplicito nella sottoscrizione. 

Per ulteriori informazioni, vedere [Enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione 

Il monitoraggio della connessione monitora la comunicazione a intervalli regolari. Indica le modifiche apportate alla raggiungibilità e alla latenza. È anche possibile controllare la topologia di rete corrente e cronologica tra gli agenti di origine e gli endpoint di destinazione.

Le origini possono essere macchine virtuali di Azure o computer locali in cui è installato un agente di monitoraggio. Gli endpoint di destinazione possono essere URL di Office 365, URL di Dynamics 365, URL personalizzati, ID di risorsa di VM di Azure, IPv4, IPv6, FQDN o qualsiasi nome di dominio.

### <a name="access-connection-monitor-preview"></a>Monitoraggio connessione di accesso (anteprima)

1. Nella home page portale di Azure passare a **Network Watcher**.
1. Nella sezione **monitoraggio** a sinistra selezionare **monitoraggio connessione (anteprima)**.
1. Vengono visualizzati tutti i monitoraggi connessione creati in monitoraggio connessione (anteprima). Per visualizzare i monitoraggi connessioni creati nell'esperienza classica di monitoraggio connessione, passare alla scheda **monitoraggio connessione** .

    ![Screenshot che mostra i monitoraggi connessione creati in monitoraggio connessione (anteprima)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>Creare un monitoraggio della connessione

Nei monitoraggi connessione creati in monitoraggio connessione (anteprima) è possibile aggiungere come origini sia i computer locali sia le VM di Azure. Questi monitoraggi di connessione possono inoltre monitorare la connettività agli endpoint. Gli endpoint possono trovarsi in Azure o in qualsiasi altro URL o indirizzo IP.

Il monitoraggio connessione (anteprima) include le seguenti entità:

* **Risorsa di monitoraggio della connessione** : una risorsa di Azure specifica dell'area. Tutte le entità seguenti sono proprietà di una risorsa di monitoraggio della connessione.
* **Endpoint** : un'origine o una destinazione che partecipa ai controlli di connettività. Esempi di endpoint includono macchine virtuali di Azure, agenti locali, URL e indirizzi IP.
* **Configurazione di test** : una configurazione specifica del protocollo per un test. In base al protocollo scelto, è possibile definire la porta, le soglie, la frequenza di test e altri parametri.
* **Gruppo di test** : il gruppo che contiene gli endpoint di origine, gli endpoint di destinazione e le configurazioni di test. Un monitoraggio connessione può contenere più di un gruppo di test.
* **Test** : la combinazione di un endpoint di origine, un endpoint di destinazione e una configurazione di test. Un test è il livello più granulare in cui sono disponibili i dati di monitoraggio. I dati di monitoraggio includono la percentuale di controlli non riusciti e il tempo di round trip (RTT).

 ![Diagramma che mostra un monitoraggio della connessione, che definisce la relazione tra i gruppi di test e i test](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Creazione di un monitoraggio della connessione dal portale di Azure

Per creare un monitoraggio della connessione dalla portale di Azure, attenersi alla procedura seguente:

1. Nell'angolo in alto a sinistra del dashboard di **monitoraggio connessione (anteprima)** selezionare **Crea**.
1. Nella scheda informazioni di **base** immettere le informazioni per il monitoraggio della connessione:
   * **Nome del monitoraggio della connessione** : aggiungere il nome del monitoraggio della connessione. Usare le regole di denominazione standard per le risorse di Azure.
   * **Sottoscrizione** : scegliere una sottoscrizione per il monitoraggio della connessione.
   * **Area** : scegliere un'area per il monitoraggio della connessione. È possibile selezionare solo le macchine virtuali di origine create in questa area.
   * **Configurazione dell'area di lavoro** : l'area di lavoro include i dati di monitoraggio. È possibile usare un'area di lavoro personalizzata o l'area di lavoro predefinita. 
       * Per utilizzare l'area di lavoro predefinita, selezionare la casella di controllo. 
       * Per scegliere un'area di lavoro personalizzata, deselezionare la casella di controllo. Quindi scegliere la sottoscrizione e l'area per l'area di lavoro personalizzata. 
1. Nella parte inferiore della scheda selezionare **Avanti: gruppi di test**.

   ![Screenshot che mostra la scheda nozioni di base in Connection Monitor](./media/connection-monitor-2-preview/create-cm-basics.png)

1. Nella scheda **gruppi di test** selezionare **+ gruppo di test**. Per configurare i gruppi di test, vedere [creare gruppi di test in Connection Monitor](#create-test-groups-in-a-connection-monitor). 
1. Nella parte inferiore della scheda selezionare **Avanti: verifica + crea** per esaminare il monitoraggio della connessione.

   ![Screenshot che mostra la scheda gruppi di test e il riquadro in cui si aggiungono i dettagli del gruppo di test](./media/connection-monitor-2-preview/create-tg.png)

1. Nella scheda **revisione e creazione** esaminare le informazioni di base e i gruppi di test prima di creare il monitoraggio della connessione. Se è necessario modificare il monitoraggio della connessione:
   * Per modificare i dettagli di base, selezionare l'icona della matita.
   * Per modificare un gruppo di test, selezionarlo.

   > [!NOTE] 
   > La scheda **Verifica + crea** Mostra il costo mensile durante la fase di anteprima di monitoraggio connessione. Attualmente la colonna **costo corrente** non mostra alcun addebito. Quando il monitoraggio della connessione diventa disponibile a livello generale, in questa colonna verrà visualizzato un addebito mensile. 
   > 
   > Anche nella fase di anteprima di monitoraggio connessione vengono applicati gli addebiti per l'inserimento Log Analytics.

1. Quando si è pronti per creare il monitoraggio della connessione, fare clic su **Crea**nella parte inferiore della scheda **Verifica + crea** .

   ![Screenshot del monitoraggio della connessione, che mostra la scheda verifica + crea](./media/connection-monitor-2-preview/review-create-cm.png)

Il monitoraggio connessione (anteprima) crea la risorsa di monitoraggio della connessione in background.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>Creazione di un monitoraggio della connessione tramite ARMClient

Usare il codice seguente per creare un monitoraggio della connessione tramite ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

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

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Ecco il comando di distribuzione:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>Creare gruppi di test in un monitoraggio della connessione

Ogni gruppo di test in un monitoraggio della connessione include origini e destinazioni che vengono testate sui parametri di rete. Sono testati per la percentuale di controlli che hanno esito negativo e il RTT sulle configurazioni di test.

Dal portale di Azure, per creare un gruppo di test in un monitoraggio della connessione, è necessario specificare i valori per i campi seguenti:

* **Disabilita gruppo di test** : è possibile selezionare questo campo per disabilitare il monitoraggio per tutte le origini e le destinazioni specificate dal gruppo di test. Questa selezione è deselezionata per impostazione predefinita.
* **Nome** : assegnare un nome al gruppo di test.
* **Origini** : è possibile specificare le VM di Azure e i computer locali come origini se gli agenti sono installati su di essi. Per installare un agente per l'origine, vedere [Install Monitoring Agents](#install-monitoring-agents).
   * Per scegliere gli agenti di Azure, selezionare la scheda **agenti di Azure** . Qui vengono visualizzate solo le macchine virtuali associate all'area specificata al momento della creazione del monitoraggio connessione. Per impostazione predefinita, le macchine virtuali vengono raggruppate nella sottoscrizione a cui appartengono. Questi gruppi sono compressi. 
   
       È possibile eseguire il drill-down dal livello di sottoscrizione ad altri livelli nella gerarchia:

      **Sottoscrizione**  >  di **Gruppi**  >  di risorse **Reti virtuali**  >  **Subnet**  >  **VM con agenti**

      È anche possibile modificare il valore del campo **Group by** per avviare l'albero da qualsiasi altro livello. Se, ad esempio, si esegue il raggruppamento in base alla rete virtuale, vengono visualizzate le VM con agenti nella gerarchia **reti virtuali**  >  **subnet**  >  **VM con agenti**.

      ![Screenshot del monitoraggio della connessione, che mostra il pannello Aggiungi origini e la scheda agenti di Azure](./media/connection-monitor-2-preview/add-azure-sources.png)

   * Per scegliere gli agenti locali, selezionare la scheda **non-Azure Agents** . Per impostazione predefinita, gli agenti sono raggruppati in aree di lavoro in base all'area. Per tutte queste aree di lavoro è configurata la soluzione Monitoraggio prestazioni rete. 
   
       Se è necessario aggiungere Monitoraggio prestazioni rete all'area di lavoro, ottenerla da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Per informazioni su come aggiungere Monitoraggio prestazioni rete, vedere [monitoraggio delle soluzioni in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solutions). 
   
       Nella vista **Crea monitoraggio connessione** , nella scheda **nozioni di base** , viene selezionata l'area predefinita. Se si modifica l'area, è possibile scegliere agenti dalle aree di lavoro nella nuova area. È anche possibile modificare il valore del campo **Group by** in Raggruppa per subnet.

      ![Screenshot del monitoraggio della connessione, che mostra il pannello Aggiungi origini e la scheda agenti non Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * Per esaminare gli agenti Azure e non Azure selezionati, passare alla scheda **Verifica** .

      ![Screenshot del monitoraggio della connessione, che mostra il pannello Aggiungi origini e la scheda Verifica](./media/connection-monitor-2-preview/review-sources.png)

   * Al termine della configurazione delle origini, nella parte inferiore del pannello **Aggiungi origini** selezionare **fine**.

* **Destinazioni** : è possibile monitorare la connettività alle macchine virtuali di Azure o a qualsiasi endpoint (un indirizzo IP pubblico, un URL o un nome di dominio completo) specificandone le destinazioni. In un singolo gruppo di test è possibile aggiungere macchine virtuali di Azure, URL di Office 365, URL di Dynamics 365 ed endpoint personalizzati.

    * Per scegliere macchine virtuali di Azure come destinazioni, selezionare la scheda **VM di Azure** . Per impostazione predefinita, le macchine virtuali di Azure sono raggruppate in una gerarchia di sottoscrizione che si trova nella stessa area selezionata nella vista **Crea monitoraggio connessione** , nella scheda **nozioni di base** . È possibile modificare l'area e scegliere macchine virtuali di Azure dall'area appena selezionata. Sarà quindi possibile eseguire il drill-down dal livello di sottoscrizione ad altri livelli nella gerarchia, ad esempio il livello degli agenti di Azure.

       ![Screenshot del riquadro Aggiungi destinazioni, che mostra la scheda VM di Azure](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![Screenshot del riquadro Aggiungi destinazioni, che mostra il livello di sottoscrizione](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * Per scegliere gli endpoint come destinazioni, selezionare la scheda **endpoint** . L'elenco degli endpoint include gli URL di test di Office 365 e gli URL di test di Dynamics 365, raggruppati in base al nome. Oltre a questi endpoint, è possibile scegliere un endpoint creato in altri gruppi di test nello stesso monitoraggio della connessione. 
    
        Per aggiungere un nuovo endpoint, nell'angolo in alto a destra selezionare **+ endpoint**. Specificare quindi un nome di endpoint e un URL, un indirizzo IP o un FQDN.

       ![Screenshot che mostra la posizione in cui aggiungere gli endpoint come destinazioni nel monitoraggio della connessione](./media/connection-monitor-2-preview/add-endpoints.png)

    * Per esaminare le macchine virtuali e gli endpoint di Azure scelti, selezionare la scheda **Verifica** .
    * Al termine della scelta delle destinazioni, selezionare **fine**.

* **Configurazioni di test** : è possibile associare configurazioni di test in un gruppo di test. Il portale di Azure consente solo una configurazione di test per gruppo di test, ma è possibile usare ARMClient per aggiungerne altri.

    * **Nome** : nome della configurazione di test.
    * **Protocollo** : scegliere TCP, ICMP o http. Per impostare HTTP su HTTPS, selezionare **http** come protocollo e selezionare **443** come porta.
        * **Crea configurazione di test di rete** : questa casella di controllo viene visualizzata solo se si seleziona **http** nel campo **protocollo** . Selezionare questa casella per creare un'altra configurazione di test che usa le stesse origini e destinazioni specificate in altre parti della configurazione. La configurazione di test appena creata è denominata `<the name of your test configuration>_networkTestConfig` .
        * **Disabilitare traceroute** : questo campo si applica ai gruppi di test il cui protocollo è TCP o ICMP. Selezionare questa casella per arrestare le origini dall'individuazione della topologia e della RTT hop-by-hop.
    * **Porta di destinazione** : è possibile personalizzare questo campo con una porta di destinazione di propria scelta.
    * **Frequenza test** : utilizzare questo campo per scegliere la frequenza con cui le origini eseguiranno il ping delle destinazioni sul protocollo e sulla porta specificati. È possibile scegliere 30 secondi, 1 minuto, 5 minuti, 15 minuti o 30 minuti. Le origini proveranno la connettività alle destinazioni in base al valore scelto.  Se ad esempio si seleziona 30 secondi, le origini verificheranno la connettività alla destinazione almeno una volta in un periodo di 30 secondi.
    * **Soglia di esito positivo** : è possibile impostare le soglie nei parametri di rete seguenti:
       * **Controlli non riusciti** : impostare la percentuale di controlli che possono avere esito negativo quando le origini controllano la connettività alle destinazioni usando i criteri specificati. Per il protocollo TCP o ICMP, la percentuale di controlli non riusciti può essere equivalente alla percentuale di perdita di pacchetti. Per il protocollo HTTP, questo campo rappresenta la percentuale di richieste HTTP che non hanno ricevuto risposta.
       * **Tempo di round trip** : impostare il RTT in millisecondi per il tempo necessario per la connessione alla destinazione tramite la configurazione di test.
    
       ![Screenshot che mostra dove configurare una configurazione di test in Connection Monitor](./media/connection-monitor-2-preview/add-test-config.png)

Tutte le origini, le destinazioni e le configurazioni di test aggiunte a un gruppo di test vengono suddivise in singoli test. Ecco un esempio di come vengono suddivise le origini e le destinazioni:

* Gruppo di test: TG1
* Origini: 3 (A, B, C)
* Destinazioni: 2 (D, E)
* Configurazioni di test: 2 (config 1, config 2)
* Totale test creati: 12

| Numero di test | Source (Sorgente) | Destination | Configurazione di test |
| --- | --- | --- | --- |
| 1 | Una | D | Configurazione 1 |
| 2 | Una | D | Configurazione 2 |
| 3 | Una | E | Configurazione 1 |
| 4 | Una | E | Configurazione 2 |
| 5 | b | D | Configurazione 1 |
| 6 | b | D | Configurazione 2 |
| 7 | b | E | Configurazione 1 |
| 8 | b | E | Configurazione 2 |
| 9 | C | D | Configurazione 1 |
| 10 | C | D | Configurazione 2 |
| 11 | C | E | Configurazione 1 |
| 12 | C | E | Configurazione 2 |

### <a name="scale-limits"></a>Limiti di scalabilità

I monitoraggi connessione presentano i limiti di scalabilità seguenti:

* Numero massimo di monitoraggi di connessione per ogni sottoscrizione per area: 100
* Numero massimo di gruppi di test per connessione: 20
* Numero massimo di origini e destinazioni per monitoraggio connessione: 100
* Numero massimo di configurazioni di test per ogni monitoraggio della connessione: 
    * 20 tramite ARMClient
    * 2 tramite il portale di Azure

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analizzare i dati di monitoraggio e impostare gli avvisi

Dopo aver creato un monitoraggio della connessione, le origini controllano la connettività alle destinazioni in base alla configurazione di test.

### <a name="checks-in-a-test"></a>Verifica in un test

In base al protocollo scelto nella configurazione di test, monitoraggio connessione (anteprima) esegue una serie di controlli per la coppia di origine-destinazione. I controlli vengono eseguiti in base alla frequenza di test scelta.

Se si usa HTTP, il servizio calcola il numero di risposte HTTP che hanno restituito un codice di risposta. Il risultato determina la percentuale di controlli non riusciti. Per calcolare RTT, il servizio misura il tempo tra una chiamata HTTP e la risposta.

Se si usa TCP o ICMP, il servizio calcola la percentuale di perdita dei pacchetti per determinare la percentuale di verifiche non riuscite. Per calcolare RTT, il servizio misura il tempo impiegato per ricevere il riconoscimento (ACK) per i pacchetti inviati. Se sono stati abilitati i dati traceroute per i test di rete, è possibile visualizzare la perdita e la latenza hop-by-hop per la rete locale.

### <a name="states-of-a-test"></a>Stati di un test

In base ai dati restituiti dai controlli, i test possono avere gli Stati seguenti:

* **Pass** : i valori effettivi per la percentuale di controlli non riusciti e RTT sono compresi entro le soglie specificate.
* **Errore** : i valori effettivi per la percentuale di controlli non riusciti o RTT hanno superato le soglie specificate. Se non viene specificata alcuna soglia, un test raggiunge lo stato di errore quando la percentuale di verifiche non riuscite è 100.
* **Avviso** : non è stato specificato alcun criterio per la percentuale di controlli non riusciti. In assenza di criteri specificati, monitoraggio connessione (anteprima) assegna automaticamente una soglia. Quando viene superata tale soglia, lo stato del test passa ad avviso.

### <a name="data-collection-analysis-and-alerts"></a>Raccolta dati, analisi e avvisi

I dati raccolti da monitoraggio connessione (anteprima) vengono archiviati nell'area di lavoro Log Analytics. Questa area di lavoro viene configurata durante la creazione del monitoraggio connessione. 

I dati di monitoraggio sono disponibili anche nelle metriche di monitoraggio di Azure. È possibile utilizzare Log Analytics per proteggere i dati di monitoraggio fino a quando si desidera. Monitoraggio di Azure archivia le metriche solo per 30 giorni per impostazione predefinita. 

È possibile [impostare avvisi basati sulle metriche per i dati](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Dashboard di monitoraggio

Nei dashboard di monitoraggio viene visualizzato un elenco dei monitoraggi connessione a cui è possibile accedere per sottoscrizioni, aree, timestamp, origini e tipi di destinazione.

Quando si passa a monitoraggio connessione (anteprima) da Network Watcher, è possibile visualizzare i dati in base a:

* **Connection Monitor** : elenco di tutti i monitoraggi connessioni creati per sottoscrizioni, aree, timestamp, origini e tipi di destinazione. Questa vista è quella predefinita.
* **Gruppi di test** : elenco di tutti i gruppi di test creati per le sottoscrizioni, le aree, i timestamp, le origini e i tipi di destinazione. Questi gruppi di test non vengono filtrati in base ai monitoraggi connessione.
* **Test** : elenco di tutti i test eseguiti per sottoscrizioni, aree, timestamp, origini e tipi di destinazione. Questi test non vengono filtrati in base ai monitoraggi connessione o ai gruppi di test.

Nella figura seguente, le tre visualizzazioni dati sono indicate dalla freccia 1.

Nel dashboard è possibile espandere ogni monitoraggio della connessione per visualizzare i gruppi di test. È quindi possibile espandere ogni gruppo di test per visualizzare i test in esecuzione. 

È possibile filtrare un elenco in base a:

* **Filtri di primo livello** : scegliere le sottoscrizioni, le aree, le origini dei timestamp e i tipi di destinazione. Vedere la casella 2 nell'immagine seguente.
* **Filtri basati sullo stato** : filtrare in base allo stato del monitoraggio della connessione, del gruppo di test o del test. Vedere Arrow 3 nell'immagine seguente.
* **Filtri personalizzati** : scegliere **Seleziona tutto** per eseguire una ricerca generica. Per eseguire la ricerca in base a un'entità specifica, selezionare nell'elenco a discesa. Vedere la freccia 4 nell'immagine seguente.

![Screenshot che illustra come filtrare le visualizzazioni di monitoraggi connessione, gruppi di test e test in Connection Monitor (anteprima)](./media/connection-monitor-2-preview/cm-view.png)

Ad esempio, per esaminare tutti i test in Connection Monitor (anteprima) in cui l'indirizzo IP di origine è 10.192.64.56:
1. Modificare la visualizzazione in **test**.
1. Nel campo di ricerca digitare *10.192.64.56*
1. Nell'elenco a discesa selezionare **origini**.

Per visualizzare solo i test non superati in monitoraggio connessione (anteprima) in cui l'indirizzo IP di origine è 10.192.64.56:
1. Modificare la visualizzazione in **test**.
1. Per il filtro basato sullo stato, selezionare **esito negativo**.
1. Nel campo di ricerca digitare *10.192.64.56*
1. Nell'elenco a discesa selezionare **origini**.

Per visualizzare solo i test non superati in monitoraggio connessione (anteprima) in cui la destinazione è outlook.office365.com:
1. Modificare la visualizzazione da **testare**.
1. Per il filtro basato sullo stato, selezionare **esito negativo**.
1. Nel campo di ricerca immettere *Outlook.office365.com*
1. Nell'elenco a discesa selezionare **Destinations (destinazioni**).

   ![Screenshot che mostra una visualizzazione filtrata per mostrare solo i test non superati per la destinazione Outlook.Office365.com](./media/connection-monitor-2-preview/tests-view.png)

Per visualizzare le tendenze in RTT e la percentuale di verifiche non riuscite per un monitoraggio della connessione:
1. Selezionare il monitoraggio della connessione che si desidera analizzare. Per impostazione predefinita, i dati di monitoraggio sono organizzati in base al gruppo di test.

   ![Screenshot che mostra le metriche per un monitoraggio della connessione, visualizzate dal gruppo di test](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. Scegliere il gruppo di test che si desidera analizzare.

   ![Screenshot che mostra dove selezionare un gruppo di test](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    Vengono visualizzati i primi cinque test non riusciti del gruppo di test, in base al RTT o alla percentuale di verifiche non riuscite. Per ogni test vengono visualizzate le linee RTT e tendenza per la percentuale di verifiche non riuscite.
1. Selezionare un test nell'elenco o scegliere un altro test da analizzare. Per l'intervallo di tempo e la percentuale di controlli non riusciti, vengono visualizzati i valori di soglia e effettivi. Per RTT vengono visualizzati i valori relativi a soglia, media, minimo e massimo.

   ![Screenshot che mostra i risultati di un test per il RTT e la percentuale di controlli non riusciti](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. Modificare l'intervallo di tempo per visualizzare altri dati.
1. Modificare la visualizzazione per visualizzare origini, destinazioni o configurazioni di test. 
1. Scegliere un'origine in base ai test non superati ed esaminare i primi cinque test non superati. Scegliere, ad esempio, **Visualizza**per  >  **origini** e **Visualizza**per  >  **destinazioni** per esaminare i test rilevanti nel monitoraggio della connessione.

   ![Screenshot che mostra le metriche delle prestazioni per i primi cinque test non superati](./media/connection-monitor-2-preview/cm-drill-select-source.png)

Per visualizzare le tendenze in RTT e la percentuale di verifiche non riuscite per un gruppo di test:

1. Selezionare il gruppo di test che si desidera analizzare. 

    Per impostazione predefinita, i dati di monitoraggio vengono organizzati in base a origini, destinazioni e configurazioni di test (test). Successivamente, è possibile modificare la visualizzazione da gruppi di test a origini, destinazioni o configurazioni di test. Scegliere quindi un'entità per esaminare i primi cinque test non superati. Ad esempio, modificare la visualizzazione in origini e destinazioni per esaminare i test rilevanti nel monitoraggio della connessione selezionato.
1. Scegliere il test che si desidera analizzare.

   ![Screenshot che mostra dove selezionare un test](./media/connection-monitor-2-preview/tg-drill.png)

    Per l'intervallo di tempo e per la percentuale di controlli non riusciti, vengono visualizzati i valori di soglia e i valori effettivi. Per RTT, vengono visualizzati i valori relativi a soglia, media, minima e massima. Vengono visualizzati anche gli avvisi generati per il test selezionato.
1. Modificare l'intervallo di tempo per visualizzare altri dati.

Per visualizzare le tendenze in RTT e la percentuale di verifiche non riuscite per un test:
1. Selezionare l'origine, la destinazione e la configurazione di test che si desidera analizzare.

    Per l'intervallo di tempo e per la percentuale di controlli non riusciti, vengono visualizzati i valori di soglia e i valori effettivi. Per RTT, vengono visualizzati i valori relativi a soglia, media, minima e massima. Vengono visualizzati anche gli avvisi generati per il test selezionato.

   ![Screenshot che mostra le metriche per un test](./media/connection-monitor-2-preview/test-drill.png)

1. Per visualizzare la topologia di rete, selezionare **topologia**.

   ![Screenshot che mostra la scheda topologia di rete](./media/connection-monitor-2-preview/test-topo.png)

1. Per visualizzare i problemi identificati, nella topologia selezionare qualsiasi hop nel percorso. Questi hop sono risorse di Azure. Questa funzionalità non è attualmente disponibile per le reti locali.

   ![Screenshot che mostra un collegamento hop selezionato nella scheda topologia](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Query di log in Log Analytics

Usare Log Analytics per creare visualizzazioni personalizzate dei dati di monitoraggio. Tutti i dati visualizzati dall'interfaccia utente sono Log Analytics. È possibile analizzare in modo interattivo i dati nel repository. Correlare i dati da Integrità agente o da altre soluzioni basate su Log Analytics. Esportare i dati in Excel o Power BI oppure creare un collegamento condivisibile.

#### <a name="metrics-in-azure-monitor"></a>Metriche in Monitoraggio di Azure

Nei monitoraggi connessioni creati prima dell'esperienza di monitoraggio connessione (anteprima) sono disponibili tutte e quattro le metriche:% probe non riusciti, AverageRoundtripMs, ChecksFailedPercent (anteprima) e RoundTripTimeMs (anteprima). Nei monitoraggi connessioni creati nell'esperienza di monitoraggio connessione (anteprima), i dati sono disponibili solo per le metriche contrassegnate con *(anteprima)*.

![Screenshot che mostra le metriche in monitoraggio connessione (anteprima)](./media/connection-monitor-2-preview/monitor-metrics.png)

Quando si usano le metriche, impostare il tipo di risorsa come Microsoft. Network/networkWatchers/connectionMonitors

| Metrica | Nome visualizzato | Unità | Tipo di aggregazione | Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % di probe non riusciti | Percentuale | Media | Percentuale di probe di monitoraggio della connettività non riuscita. | Nessuna dimensione |
| AverageRoundtripMs | Avg. Tempo di round trip (ms) | Millisecondi | Media | RTT di rete medio per i probe di monitoraggio della connettività inviati tra l'origine e la destinazione. |             Nessuna dimensione |
| ChecksFailedPercent (anteprima) | % Controlli non riusciti (anteprima) | Percentuale | Media | Percentuale di verifiche non riuscite per un test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocollo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |
| RoundTripTimeMs (anteprima) | Tempo di round trip (MS) (anteprima) | Millisecondi | Media | RTT per i controlli inviati tra l'origine e la destinazione. Questo valore non è medio. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocollo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |

#### <a name="metric-alerts-in-azure-monitor"></a>Avvisi relativi alle metriche in monitoraggio di Azure

Per creare un avviso in monitoraggio di Azure:

1. Scegliere la risorsa di monitoraggio della connessione creata in monitoraggio connessione (anteprima).
1. Assicurarsi che la **metrica** venga visualizzata come tipo di segnale per il monitoraggio della connessione.
1. In **Aggiungi condizione**per il **nome del segnale**selezionare **ChecksFailedPercent (anteprima)** o **RoundTripTimeMs (anteprima)**.
1. Per **tipo di segnale**scegliere **metrica**. Ad esempio, selezionare **ChecksFailedPercent (anteprima)**.
1. Vengono elencate tutte le dimensioni della metrica. Scegliere il nome della dimensione e il valore della dimensione. Selezionare, ad esempio, **indirizzo di origine** e quindi immettere l'indirizzo IP di qualsiasi origine nel monitoraggio della connessione.
1. In **logica avvisi**compilare i dettagli seguenti:
   * **Tipo di condizione**: **static**.
   * **Condizione** e **soglia**.
   * **Granularità e frequenza di valutazione dell'aggregazione**: monitoraggio connessione (anteprima) aggiorna i dati ogni minuto.
1. In **azioni**scegliere il gruppo di azioni.
1. Fornire i dettagli dell'avviso.
1. Creare la regola di avviso.

   ![Screenshot che illustra l'area Crea regola in monitoraggio di Azure; "Indirizzo di origine" e "nome endpoint di origine" sono evidenziati](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>Diagnosticare i problemi nella rete

Il monitoraggio connessione (anteprima) consente di diagnosticare i problemi nel monitoraggio della connessione e nella rete. I problemi nella rete ibrida vengono rilevati dagli agenti Log Analytics installati in precedenza. I problemi in Azure vengono rilevati dall'estensione Network Watcher. 

È possibile visualizzare i problemi nella rete di Azure nella topologia di rete.

Per le reti le cui origini sono macchine virtuali locali, è possibile rilevare i problemi seguenti:

* Timeout della richiesta.
* Endpoint non risolto da DNS: temporaneo o persistente. URL non valido.
* Non sono stati trovati host.
* L'origine non è in grado di connettersi alla destinazione. Destinazione non raggiungibile tramite ICMP.
* Problemi relativi ai certificati: 
    * Certificato client necessario per autenticare l'agente. 
    * L'elenco di rilocazione del certificato non è accessibile. 
    * Il nome host dell'endpoint non corrisponde al soggetto o al nome alternativo del soggetto del certificato. 
    * Il certificato radice non è presente nell'archivio Autorità di certificazione attendibili del computer locale dell'origine. 
    * Il certificato SSL è scaduto, non valido, revocato o incompatibile.

Per le reti le cui origini sono macchine virtuali di Azure, è possibile rilevare i problemi seguenti:

* Problemi dell'agente:
    * Agente arrestato.
    * Risoluzione DNS non riuscita.
    * Nessuna applicazione o listener in ascolto sulla porta di destinazione.
    * Non è stato possibile aprire il socket.
* Problemi relativi allo stato della macchina virtuale: 
    * Avvio in corso
    * Stopping
    * Arrestato
    * Deallocazione
    * Deallocato
    * Riavvio
    * Non allocato
* Voce della tabella ARP mancante.
* Il traffico è stato bloccato a causa di problemi del firewall locale o di regole NSG.
* Problemi relativi al gateway di rete virtuale: 
    * Route mancanti.
    * Il tunnel tra due gateway è disconnesso o mancante.
    * Il secondo gateway non è stato trovato dal tunnel.
    * Non sono state trovate informazioni sul peering.
* Route mancante in Microsoft Edge.
* Il traffico è stato interrotto a causa di route di sistema o UDR.
* BGP non è abilitato nella connessione gateway.
* Il probe DIP è inattivo nel servizio di bilanciamento del carico.
