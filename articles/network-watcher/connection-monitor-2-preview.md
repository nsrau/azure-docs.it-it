---
title: Monitoraggio connessione (anteprima) | Microsoft Docs
description: Informazioni su come utilizzare monitoraggio connessione (anteprima) per monitorare le comunicazioni di rete in un ambiente distribuito
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 43c49cce1dd53edd5c2b13b01a31f94752579dff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169330"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>Monitoraggio della connettività unificata con monitoraggio connessione (anteprima)

Il monitoraggio connessione (anteprima) offre funzionalità unificate di monitoraggio della connessione end-to-end in Azure Network Watcher per distribuzioni ibride e cloud di Azure. Azure Network Watcher offre strumenti per monitorare, diagnosticare e visualizzare le metriche relative alla connettività per le distribuzioni di Azure.

Casi d'uso principali:

- Si dispone di una macchina virtuale del server Web front-end che comunica con una VM del server di database in un'applicazione multilivello. Si vuole controllare la connettività di rete tra le due VM.
- Si vuole che le macchine virtuali nell'area Stati Uniti orientali vengano ping macchine virtuali nell'area Stati Uniti centrali e confrontare le latenze di rete tra aree
- Sono presenti più siti di Office locali in città come Seattle. connessione agli URL di Office 365. Si vuole confrontare le latenze riscontrate dagli utenti usando gli URL di Office 365 di Seattle e Ashburn.
- È stata configurata un'applicazione ibrida che richiede la connettività a un endpoint di archiviazione di Azure. Si desidera confrontare le latenze tra un sito locale e l'applicazione Azure connettendosi allo stesso endpoint di archiviazione di Azure.
- Si vuole controllare la connettività dalle macchine virtuali di Azure che ospitano l'applicazione cloud alle configurazioni locali.

In questa fase di anteprima, la soluzione riunisce il meglio di due funzionalità chiave, ovvero il monitoraggio delle [connessioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) di Network Watcher e il [monitoraggio della connettività del servizio](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)di monitoraggio prestazioni rete (NPM).

Evidenzia

* Esperienza unificata e intuitiva per le esigenze di Azure e di monitoraggio ibrido
* Cross Region, monitoraggio della connettività tra aree di lavoro
* Frequenze di probe più elevate e visibilità migliore sulle prestazioni della rete
* Avvisi più veloci per le distribuzioni ibride
* Supporto per i controlli di connettività basati su HTTP, TCP e ICMP
* Metriche e supporto Log Analytics per le configurazioni di test di Azure e non Azure

![Monitoraggio connessione](./media/connection-monitor-2-preview/hero-graphic.png)

Attenersi ai passaggi indicati di seguito per avviare il monitoraggio tramite monitoraggio connessione (anteprima)

## <a name="step-1-install-monitoring-agents"></a>Passaggio 1: installare gli agenti di monitoraggio

Il monitoraggio della connessione si basa su file eseguibili leggeri per l'esecuzione di controlli di connettività.  Sono supportati i controlli di connettività da ambienti Azure e locali. Il file eseguibile specifico da usare dipende dal fatto che la VM sia ospitata in Azure o in locale.

### <a name="agents-for-azure-virtual-machines"></a>Agenti per macchine virtuali di Azure

Affinché il monitoraggio della connessione riconosca le VM di Azure come origine per il monitoraggio, è necessario installare l'estensione della macchina virtuale Network Watcher Agent (nota anche come estensione Network Watcher). L'estensione dell'agente Network Watcher è un requisito per l'attivazione del monitoraggio end-to-end e altre funzionalità avanzate nelle macchine virtuali di Azure. È possibile [creare una macchina virtuale e installare l'estensione Network Watcher](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[.](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)  È anche possibile installare, configurare e risolvere i problemi di Network Watcher estensione per [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) e [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) separatamente.

Se le regole NSG o firewall bloccano la comunicazione tra l'origine e la destinazione, il monitoraggio della connessione rileva il problema e lo Mostra come un messaggio di diagnostica nella topologia. Per abilitare il monitoraggio della connessione, assicurarsi che le regole NSG e firewall consentano i pacchetti su TCP o ICMP tra l'origine e la destinazione.

### <a name="agents-for-on-premise-machines"></a>Agenti per computer locali

Affinché il monitoraggio della connessione riconosca i computer locali come origini per il monitoraggio, è necessario installare l'agente Log Analytics nei computer e abilitare la soluzione Monitoraggio prestazioni rete. Questi agenti sono collegati alle aree di lavoro Log Analytics e richiedono l'ID dell'area di lavoro e la chiave primaria configurati prima di poter avviare il monitoraggio.

Per installare Log Analytics Agent per computer Windows, seguire le istruzioni indicate in [questo collegamento](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

Verificare che la destinazione sia raggiungibile in presenza di firewall o appliance di rete virtuale (NVA) nel percorso.

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>Passaggio 2: abilitare Network Watcher per la sottoscrizione

Tutte le sottoscrizioni con VNET sono abilitate con Network Watcher. Quando si crea una rete virtuale nella sottoscrizione, Network Watcher verrà abilitata automaticamente nell'area e nella sottoscrizione della rete virtuale. Non vi è alcun effetto sulle risorse o sull'addebito associato per abilitare automaticamente Network Watcher. Verificare che Network Watcher non sia disabilitato in modo esplicito nella sottoscrizione. Per ulteriori informazioni, vedere [Enable Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="step-3-create-connection-monitor"></a>Passaggio 3: creare il monitoraggio della connessione 

Il _monitoraggio della connessione_ monitora la comunicazione a intervalli regolari e informa la raggiungibilità, la latenza e le modifiche alla topologia di rete tra gli agenti di origine e gli endpoint di destinazione. Le origini possono essere macchine virtuali di Azure o computer locali in cui è installato un agente di monitoraggio. Gli endpoint di destinazione possono essere URL di Office 365, URL di Dynamics 365, URL personalizzati, ID di risorsa di VM di Azure, IPv4, IPv6, FQDN o qualsiasi nome di dominio.

### <a name="accessing-connection-monitor-preview"></a>Accesso a monitoraggio connessione (anteprima)

1. Dal home page portale di Azure visitare Network Watcher
2. Fare clic sulla scheda "monitoraggio connessione (anteprima)" nella sezione monitoraggio nel riquadro sinistro di Network Watcher.
3. È possibile visualizzare tutti i monitoraggi connessione creati con l'esperienza di monitoraggio connessione (anteprima). Tutti i monitoraggi connessioni creati utilizzando la scheda monitoraggio connessione classica saranno visibili nella scheda monitoraggio connessione.

    ![Creazione di un monitoraggio della connessione](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>Creazione di un monitoraggio della connessione

I monitoraggi connessione creati con monitoraggio connessione (anteprima) offrono la possibilità di aggiungere VM locali e di Azure come origini e di monitorare la connettività agli endpoint, che possono estendersi in Azure o in qualsiasi altro URL/IP.

Di seguito sono riportate le entità in un monitoraggio della connessione:

* Risorsa di monitoraggio della connessione: risorsa di Azure specifica dell'area. Tutte le entità indicate di seguito sono proprietà di una risorsa di monitoraggio connessione.
* Endpoint: tutte le origini e le destinazioni che partecipano ai controlli di connettività vengono chiamate come endpoint. Esempi di endpoint: macchine virtuali di Azure, agenti locali, URL, indirizzi IP
* Configurazione di test: ogni configurazione di test è specifica del protocollo. In base al protocollo scelto, è possibile definire porta, soglie, frequenza di test e altri parametri
* Gruppo di test: ogni gruppo di test contiene endpoint di origine, endpoint di destinazione e configurazioni di test. Ogni monitoraggio della connessione può contenere più di un gruppo di test
* Test: la combinazione di endpoint di origine, endpoint di destinazione e configurazione di test crea un test. Il test è il livello più basso in cui sono disponibili i dati di monitoraggio (i controlli non riusciti% e RTT)

 ![Creazione di un monitoraggio della connessione](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>Dal portale

Per creare un monitoraggio della connessione, attenersi ai passaggi indicati di seguito:

1. Nel dashboard di monitoraggio connessione (anteprima) fare clic su "Crea" nell'angolo superiore sinistro.
2. Nella scheda di base immettere le informazioni per il monitoraggio della connessione
   1. Nome del monitoraggio della connessione: nome del monitoraggio della connessione. Le regole di denominazione standard per le risorse di Azure si applicano qui.
   2. Sottoscrizione: scegliere una sottoscrizione per il monitoraggio della connessione.
   3. Area: scegliere un'area per la risorsa di monitoraggio della connessione. È possibile selezionare solo le macchine virtuali di origine create in questa area.
   4. Configurazione dell'area di lavoro: è possibile utilizzare l'area di lavoro predefinita creata da monitoraggio connessione per archiviare i dati di monitoraggio facendo clic sulla casella di controllo predefinita. Per scegliere un'area di lavoro personalizzata, deselezionare questa casella. Scegliere la sottoscrizione e l'area per selezionare l'area di lavoro che conterrà i dati di monitoraggio.
   5. Fare clic su "Avanti: gruppi di test" per aggiungere gruppi di test

      ![Creazione di un monitoraggio della connessione](./media/connection-monitor-2-preview/create-cm-basics.png)

3. Nella scheda gruppi di test fare clic su "+ gruppo di test" per aggiungere un gruppo di test. Utilizzare la _creazione di gruppi di test in monitoraggio connessione_ per aggiungere gruppi di test. Fare clic su "verifica + crea" per esaminare il monitoraggio della connessione.

   ![Creazione di un monitoraggio della connessione](./media/connection-monitor-2-preview/create-tg.png)

4. Nella scheda "verifica e crea" esaminare le informazioni di base e i gruppi di test prima di creare il monitoraggio della connessione. Per modificare il monitoraggio della connessione dalla visualizzazione "verifica e creazione":
   1. Per modificare i dettagli di base, usare l'icona a forma di matita come specificato dalla casella 1 nell'immagine 2
   2. Per modificare i singoli gruppi di test, fare clic sul gruppo di test che si desidera modificare per aprire il gruppo di test in modalità di modifica.
   3. Costo corrente/mese indica il costo durante l'anteprima. Attualmente non è previsto alcun addebito per l'utilizzo di monitoraggio connessione, quindi in questa colonna verrà visualizzato zero. Costo effettivo/mese indica il prezzo che verrà addebitato dopo la disponibilità generale. Si noti che gli addebiti per l'inserimento di log Analytics verranno applicati anche durante l'anteprima.

5. Nella scheda "verifica e creazione" fare clic sul pulsante "Crea" per creare il monitoraggio della connessione.

   ![Creazione di un monitoraggio della connessione](./media/connection-monitor-2-preview/review-create-cm.png)

6.  Il monitoraggio connessione (anteprima) creerà la risorsa di monitoraggio della connessione in background.

#### <a name="from-armclient"></a>Da Armclient

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

address: '\&lt;FQDN of your on-premise agent'

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

Comando di distribuzione:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>Creazione di gruppi di test nel monitoraggio della connessione

Ogni gruppo di test nel monitoraggio della connessione include le origini e la destinazione che vengono testate sui parametri di rete dei controlli non riusciti e RTT sulle configurazioni di test.

#### <a name="from-portal"></a>Dal portale

Per creare un gruppo di test in un monitoraggio connessione, è necessario specificare il valore per i campi indicati di seguito:

1. Disabilita gruppo di test: se si seleziona questo campo, il monitoraggio verrà disabilitato per tutte le origini e le destinazioni specificate nel gruppo di test. Questa opzione è deselezionata per impostazione predefinita.
2. Nome: nome del gruppo di test
3. Origini: è possibile specificare le VM di Azure e i computer locali come origini se gli agenti sono installati. Fare riferimento al passaggio 1 per installare un agente specifico per l'origine.
   1. Fare clic sulla scheda "Azure Agents" per selezionare agenti di Azure. Verranno visualizzate solo le VM elencate, associate all'area specificata al momento della creazione del monitoraggio della connessione. Per impostazione predefinita, le macchine virtuali vengono raggruppate nella sottoscrizione a cui appartengono e i gruppi vengono compressi. È possibile eseguire il drill-down dal livello delle sottoscrizioni agli altri livelli della gerarchia:

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      È anche possibile modificare il valore del campo "Raggruppa per" per avviare l'albero da qualsiasi altro livello. Ad esempio: Group by – VNET mostrerà le VM con agenti nella gerarchia reti virtuali-\&gt; Subnet-\&gt; Macchine virtuali con agenti.

      ![Aggiungi origini](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. Fare clic sulla scheda "non-Azure Agents" per selezionare agenti locali. Per impostazione predefinita, gli agenti vengono raggruppati in aree di lavoro in un'area. Verranno elencate solo le aree di lavoro con Monitoraggio prestazioni rete soluzione configurata. Aggiungere la soluzione Monitoraggio prestazioni rete nell'area di lavoro dall'[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). È anche possibile usare la procedura descritta in [aggiungere soluzioni di monitoraggio di Azure dalla raccolta di soluzioni](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) . Per impostazione predefinita, viene visualizzata l'area selezionata nella scheda informazioni di base della vista crea monitoraggio connessione. È possibile modificare l'area e scegliere gli agenti dalle aree di lavoro dall'area appena selezionata. È anche possibile modificare il valore del campo "Raggruppa per" per raggruppare le subnet.

      ![Origini non Azure](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. Fare clic su "verifica" per esaminare gli agenti Azure e non Azure selezionati.

      ![Verifica origini](./media/connection-monitor-2-preview/review-sources.png)

   4. Fare clic su "operazione completata" al termine della selezione delle origini.

4. Destinazioni: è possibile monitorare la connettività alle macchine virtuali di Azure o a qualsiasi endpoint (IP pubblico, URL, FQDN) specificandone le destinazioni. In un singolo gruppo di test è possibile aggiungere macchine virtuali di Azure, URL O365, URL d365 o endpoint personalizzati.

   1. Fare clic sulla scheda "macchine virtuali di Azure" per selezionare le macchine virtuali di Azure come destinazioni. Per impostazione predefinita, le macchine virtuali di Azure vengono raggruppate nella gerarchia delle sottoscrizioni nella stessa area selezionata nella scheda informazioni di base della vista crea monitoraggio connessione. È possibile modificare l'area e scegliere macchine virtuali di Azure dall'area appena selezionata. È possibile eseguire il drill-down dal livello delle sottoscrizioni agli altri livelli della gerarchia, ad esempio gli agenti di Azure.

      ![Aggiungi destinazioni](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![Aggiungi destinazioni 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. Fare clic sulla scheda "endpoint" per selezionare gli endpoint come destinazioni. L'elenco di endpoint verrà popolato con gli URL di test O365 e d365, raggruppati in base al nome.  È anche possibile scegliere un endpoint creato in altri gruppi di test nello stesso monitoraggio della connessione. Per aggiungere un nuovo endpoint, fare clic su "+ endpoint" nell'angolo superiore destro dello schermo e specificare l'URL dell'endpoint/IP/FQDN e il nome

      ![Aggiungi endpoint](./media/connection-monitor-2-preview/add-endpoints.png)

   3. Fare clic su "verifica" per esaminare gli agenti Azure e non Azure selezionati.
   4. Fare clic su "operazione completata" al termine della selezione delle origini.

5. Configurazione di test: è possibile associare un numero qualsiasi di configurazioni di test in un gruppo di test specificato. Il portale lo limita a una configurazione di test per gruppo di test, ma usa Armclient per aggiungerne altri.
   1. Nome: nome della configurazione di test
   2. Protocollo: è possibile scegliere tra TCP, ICMP o HTTP. Per impostare HTTP su HTTPS, selezionare HTTP come protocollo e 443 come porta
   3. Crea configurazione di test di rete: questa casella di controllo viene visualizzata solo se si seleziona HTTP nel campo protocollo. Abilitare questo campo per creare un'altra configurazione di test usando le stesse origini e destinazioni specificate nel passaggio 3 e 4 sul protocollo TCP/ICMP. La configurazione di test appena creata è denominata "\&lt; nome specificato in 5. a\&gt;\_networkTestConfig "
   4. Disabilitare traceroute: questo campo sarà applicabile per i gruppi di test con TCP o ICMP come protocollo.  Selezionare questo campo per arrestare le origini dall'individuazione della topologia e del tempo di round trip hop-by-hop.
   5. Porta di destinazione: è possibile personalizzare questo campo per inserire una porta di destinazione di propria scelta.
   6. Frequenza di test: questo campo determina la frequenza con cui le origini eseguiranno il ping delle destinazioni sul protocollo e sulla porta specificati in precedenza. È possibile scegliere tra 30 secondi, 1 minuto, 5 minuti, 15 minuti e 30 minuti. Le origini proveranno la connettività alle destinazioni in base al valore scelto.  Se ad esempio si seleziona 30 secondi, le origini verificheranno la connettività alla destinazione almeno una volta in 30 secondi.
   7. Soglie di integrità: è possibile impostare le soglie nei parametri di rete indicati di seguito
      1. Controlli non riusciti in%-percentuale di controlli non riusciti quando le origini controllano la connettività alla destinazione nei criteri specificati in precedenza. Per il protocollo TCP/ICMP, i controlli non riusciti in% possono essere equivalenti alla percentuale di perdita dei pacchetti. Per il protocollo HTTP, questo campo rappresenta il numero di richieste HTTP che non hanno ricevuto una risposta.
      2. RTT in millisecondi: tempo di round trip in millisecondi quando le origini si connettono alla destinazione sulla configurazione di test specificata in precedenza.

      ![Aggiungi TG](./media/connection-monitor-2-preview/add-test-config.png)

Tutte le origini e le destinazioni aggiunte a un gruppo di test con la configurazione di test specificata vengono suddivise in singoli test. Ad esempio:

* Gruppo di test: TG1
* Origini: 3 (A, B, C)
* Destinazioni: 2 (D, E)
* Configurazione di test: 2 (config 1, config 2)
* Test creati: totale = 12

| **Numero di test** | **Origine** | **Destinazione** | **Nome della configurazione di test** |
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

* Numero massimo di monitoraggi connessione per ogni sottoscrizione per area: 100
* Numero massimo di gruppi di test per ogni monitoraggio della connessione-20
* Numero massimo di origini + destinazioni per monitoraggio connessione-100
* Numero massimo di configurazioni di test per ogni monitoraggio della connessione: 20 tramite Armclient. 2 tramite il portale.

## <a name="step-4--data-analysis-and-alerts"></a>Passaggio 4: analisi dei dati e avvisi

Una volta creato un monitoraggio della connessione, le origini controllano la connettività alle destinazioni in base alla configurazione di test specificata.

### <a name="checks-in-a-test"></a>Verifica in un test

In base al protocollo selezionato da un utente nella configurazione di test, il monitoraggio connessione (anteprima) esegue una serie di controlli per la coppia di destinazione di origine sulla frequenza di test scelta.

Se si seleziona HTTP, il servizio calcola il numero di risposte HTTP che hanno restituito un codice di risposta per determinare la percentuale di verifiche non riuscite.  Per calcolare RTT viene misurato il tempo impiegato per ricevere la risposta di una chiamata HTTP.

Se è selezionata l'opzione TCP o ICMP, il servizio calcola il pacchetto% per determinare il% dei controlli non riusciti. Per calcolare RTT viene misurato il tempo impiegato per ricevere l'ACK per i pacchetti inviati. Se sono stati abilitati i dati traceroute per i test di rete, è possibile visualizzare la perdita e la latenza hop-by-hop per la rete locale.

### <a name="states-of-a-test"></a>Stati di un test

In base ai dati restituiti dai controlli in un test, ogni test può quindi avere gli Stati seguenti:

* Pass = se i valori effettivi per i controlli hanno esito negativo% e RTT sono entro le soglie specificate
* Fail = quando i valori effettivi per i controlli non sono riusciti% o RTT Cross specificati. Se non viene specificata alcuna soglia, un test viene contrassegnato come non riuscito quando i controlli hanno esito negativo% = 100%
* Avviso: quando i criteri per i controlli non riusciti% non sono specificati. In tal caso, monitoraggio connessione (anteprima) utilizza un criterio di impostazione automatica come soglia e quando tale soglia viene violata lo stato del test è impostato su "avviso"

### <a name="data-collection-analysis-and-alerts"></a>Raccolta dati, analisi e avvisi

Tutti i dati raccolti da monitoraggio connessione (anteprima) vengono archiviati nell'area di lavoro Log Analytics configurata al momento della creazione del monitoraggio connessione. I dati di monitoraggio sono disponibili anche nelle metriche di monitoraggio di Azure. È possibile usare Log Analytics per tenere i dati di monitoraggio fino a quando si desidera, ma monitoraggio di Azure archivia le metriche per impostazione predefinita per 30 giorni **.** È quindi possibile [impostare avvisi basati sulle metriche per i dati](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>Monitoraggio di dashboard nella soluzione di monitoraggio della connessione

Verrà visualizzato un elenco del monitoraggio della connessione a cui si ha accesso per una determinata selezione di sottoscrizioni, aree, timestamp, origine e tipi di destinazione.

Quando si passa a monitoraggio connessione (anteprima) dal servizio Network Watcher, è possibile scegliere di **visualizzare**:

* Monitoraggio connessione (impostazione predefinita): elenco di tutti i monitoraggi connessioni creati per le sottoscrizioni, le aree, il timestamp, i tipi di origine e di destinazione scelti
* Gruppi di test: elenco di tutti i gruppi di test creati per le sottoscrizioni, le aree, il timestamp, i tipi di origine e di destinazione scelti. Questi gruppi di test non vengono filtrati nel monitoraggio della connessione
* Test: elenco di tutti i test in esecuzione per le sottoscrizioni, le aree, il timestamp, i tipi di origine e di destinazione scelti. Questi test non vengono filtrati nel monitoraggio della connessione o nei gruppi di test.

È possibile espandere ogni monitoraggio della connessione nei gruppi di test e in ogni gruppo di test nei vari singoli test in esecuzione nel dashboard. Contrassegnato come [1] nell'immagine seguente.

È possibile filtrare l'elenco in base a:

* Filtri di primo livello: sottoscrizioni, aree, origine timestamp e tipi di destinazione. Contrassegnato come [2] nell'immagine seguente.
* Filtri basati sullo stato: filtro di secondo livello sullo stato del monitoraggio della connessione/gruppo di test/test. Contrassegnato come [3] nell'immagine seguente.
* Campo di ricerca: scegliere "tutti" per eseguire una ricerca generica. Per eseguire la ricerca su un'entità specifica, usare l'elenco a discesa per limitare i risultati della ricerca. Contrassegnato come [4] nell'immagine seguente.

![Filtra test](./media/connection-monitor-2-preview/cm-view.png)

Ad esempio:

1. Per esaminare tutti i test in tutti i monitoraggi connessione (anteprima) in cui IP di origine = 10.192.64.56:
   1. Modificare la visualizzazione in base a "test"
   2. Cerca archiviato = 10.192.64.56
   3. Usare l'elenco a discesa accanto a Value per selezionare "Sources"
2. Per filtrare solo i test non superati in tutti i monitoraggi connessione (anteprima) in cui IP di origine = 10.192.64.56
   1. Modificare la visualizzazione in base a "test"
   2. Selezionare "Interrompi" dai filtri basati sullo stato.
   3. Campo di ricerca = 10.192.64.56
   4. Usare l'elenco a discesa accanto a Value per selezionare "Sources"
3. Per filtrare solo i test non superati in tutti i monitoraggi connessione (anteprima) in cui la destinazione è outlook.office365.com
   1. Modificare la visualizzazione in base a "test"
   2. Selezionare "Interrompi" dai filtri basati sullo stato.
   3. Campo di ricerca = outlook.office365.com
   4. Usare l'elenco a discesa accanto a valore per selezionare "destinazioni"

   ![Test non superati](./media/connection-monitor-2-preview/tests-view.png)

Per visualizzare le tendenze dei controlli non riusciti% e RTT per:

1. Monitoraggio connessione
   1. Fare clic sul monitoraggio della connessione che si desidera analizzare in dettaglio
   2. Per impostazione predefinita, si visualizzano i dati di monitoraggio per "gruppi di test"

      ![Visualizza metriche per](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. Scegliere il gruppo di test che si desidera analizzare in dettaglio

      ![Metriche per TG](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. Verranno visualizzati i primi 5 test non superati sui controlli non riusciti% o RTT msec per il gruppo di test scelto nel passaggio precedente. Per ogni test, verranno visualizzate le linee di tendenza per i controlli non riusciti% e RTT msec
   5. Selezionare un test nell'elenco precedente oppure scegliere un altro test da analizzare in dettaglio.
   6. Per l'intervallo di tempo selezionato, per i controlli non riusciti% verranno visualizzati i valori di soglia e effettivi. Per RTT msec, verranno visualizzati i valori Threshold, AVG, min e max.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. Modificare l'intervallo di tempo per visualizzare altri dati
  8. È possibile modificare la visualizzazione nel passaggio b e scegliere di visualizzare le origini, le destinazioni o le configurazioni di test. Scegliere quindi un'origine basata su test non superati ed esaminare i 5 test non superati.  Ad esempio, scegliere Visualizza per: origini e destinazioni per esaminare tutti i test eseguiti tra la combinazione nel monitoraggio della connessione selezionato.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. Gruppo di test
   1. Fare clic sul gruppo di test che si desidera analizzare in dettaglio
   2. Per impostazione predefinita, si visualizzano i dati di monitoraggio per "origine + destinazione + configurazione di test (test)"

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. Scegliere il test che si desidera analizzare in dettaglio
   4. Per l'intervallo di tempo selezionato, per i controlli non riusciti% verranno visualizzati i valori di soglia e effettivi. Per RTT msec, verranno visualizzati i valori Threshold, AVG, min e max. Verranno visualizzati anche gli avvisi attivati specifici del test selezionato.
   5. Modificare l'intervallo di tempo per visualizzare altri dati
   6. È possibile modificare la visualizzazione nel passaggio b e scegliere di visualizzare le origini, le destinazioni o le configurazioni di test. Scegliere quindi un'entità per esaminare i 5 test non superati.  Ad esempio, scegliere Visualizza per: origini e destinazioni per esaminare tutti i test eseguiti tra la combinazione nel monitoraggio della connessione selezionato.

3. Test
   1. Fare clic sulla configurazione di origine + destinazione + test da analizzare in dettaglio
   2. Per l'intervallo di tempo selezionato, per i controlli non riusciti% verranno visualizzati i valori di soglia e effettivi. Per RTT msec, verranno visualizzati i valori Threshold, AVG, min e max. Verranno visualizzati anche gli avvisi attivati specifici del test selezionato.

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. È anche possibile fare clic su "topologia" per visualizzare la topologia di rete in qualsiasi momento.

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. È possibile fare clic su qualsiasi hop del collegamento per rete di Azure per visualizzare i problemi identificati da monitoraggio connessione. Questa funzionalità non è attualmente disponibile per le reti locali.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Query di log in monitoraggio di Azure Log Analytics

Usare Log Analytics per creare visualizzazioni personalizzate dei dati di monitoraggio. Tutti i dati visualizzati nell'interfaccia utente vengono popolati da Log Analytics. È possibile eseguire l'analisi interattiva dei dati nel repository e correlare i dati provenienti da origini diverse, ad esempio integrità agente e altre applicazioni basate su Log Analytics. È anche possibile esportare i dati in Excel, Power BI o in un collegamento condivisibile.

#### <a name="metrics-in-azure-monitor"></a>Metriche in Monitoraggio di Azure

Per il monitoraggio della connessione creato prima dell'esperienza di monitoraggio connessione (anteprima), tutte e quattro le metriche sarebbero disponibili. Per i monitoraggi di connessione creati tramite monitoraggio connessione (anteprima), i dati saranno disponibili solo per le metriche con tag "(anteprima)".

Tipo di risorsa: Microsoft. Network/networkWatchers/connectionMonitors

| Metrica | Nome visualizzato per la metrica | Unità | Tipo di aggregazione | Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | % di probe non riusciti | Percentuale | Media | % di probe di monitoraggio connettività non riusciti | Nessuna dimensione |
| AverageRoundtripMs | Tempo medio di round trip (MS) | Millisecondi | Media | Tempo medio di round trip della rete (in ms) per i probe di monitoraggio della connettività inviati tra origine e destinazione |             Nessuna dimensione |
| ChecksFailedPercent (anteprima) | % Controlli non riusciti (anteprima) | Percentuale | Media | % di controlli non riusciti per un test | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * SourceResourceId <br> * SourceType <br> * Protocollo <br> * DestinationAddress <br> * Destination <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * Area |
| RoundTripTimeMs (anteprima) | Tempo di round trip (MS) (anteprima) | Millisecondi | Media | Tempo di round trip (MS) per i controlli inviati tra l'origine e la destinazione. Questo valore non è medio | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * SourceResourceId <br> * SourceType <br> * Protocollo <br> * DestinationAddress <br> * Destination <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * Area |

 ![Monitorare le metriche](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Avvisi relativi alle metriche in monitoraggio di Azure

Per creare un avviso:

1. Scegliere la risorsa di monitoraggio della connessione creata utilizzando monitoraggio connessione (anteprima)
2. Verificare che "metrica" venga visualizzato come tipo di segnale per la risorsa selezionata nel passaggio precedente
3. In Aggiungi condizione scegliere nome del segnale come ChecksFailedPercent (anteprima) o RoundTripTimeMs (anteprima) e tipo di segnale come metrica. Ad esempio: scegliere ChecksFailedPercent (anteprima)
4. Verranno elencate tutte le dimensioni applicabili in base alle metriche.  Scegliere il nome della dimensione e il valore della dimensione. Ad esempio, scegliere l'indirizzo di origine e specificare l'indirizzo IP di qualsiasi origine richiesta nella risorsa di monitoraggio della connessione scelta nel passaggio 1
5. In logica avvisi scegliere:
   1. Tipo di condizione-statico
   2. Condizione e soglia
   3. Granularità e frequenza dell'aggregazione della valutazione-monitoraggio connessione (anteprima) aggiorna i dati ogni minuto.
6.  In azioni scegliere il gruppo di azioni
7. Fornire i dettagli dell'avviso
8. Creare la regola di avviso

   ![Avvisi](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>Passaggio 5: diagnosticare i problemi nella rete

Il monitoraggio della connessione consente di diagnosticare i problemi corrispondenti alla risorsa di monitoraggio della connessione e alla rete. I problemi nella rete ibrida verranno rilevati dagli agenti Log Analytics installati nel passaggio 1 e i problemi in Azure verranno rilevati dall'estensione Network Watcher.  I problemi nella rete ibrida saranno visibili nella pagina diagnostica e i problemi nella rete di Azure saranno visibili nella topologia di rete.

Per le reti con macchine virtuali locali come origini, viene rilevato quanto segue:

* Timeout della richiesta
* Endpoint non risolto da DNS: temporaneo o persistente. URL non valido.
* Non sono stati trovati host.
* L'origine non è in grado di connettersi alla destinazione. Destinazione non raggiungibile tramite ICMP.
* Problema relativo al certificato: il certificato client necessario per autenticare l'agente, l'elenco di rilocazione dei certificati non è accessibile, il nome host dell'endpoint non corrisponde al soggetto o al nome alternativo del soggetto del certificato, certificato radice mancante nell'archivio Autorità di certificazione attendibili del computer locale dell'origine, certificato SSL scaduto/non valido/revocato, incompatibile

Per le reti con macchine virtuali di Azure sono presenti origini, che vengono rilevate:

* Problemi dell'agente: l'agente è stato arrestato, la risoluzione DNS non è riuscita, nessuna applicazione/listener in ascolto sulla porta di destinazione, non è stato possibile aprire il socket
* Problemi relativi allo stato della macchina virtuale: avvio, arresto, arresto, deallocazione, deallocazione, riavvio, non allocata
* Voce della tabella ARP mancante
* Traffico bloccato a causa di problemi locali del firewall, regole NSG
* Gateway VNET: Route mancanti, il tunnel tra due gateway è disconnesso o mancante o il secondo gateway non è stato trovato dal tunnel, non sono state trovate informazioni sul peering
* Route mancante in MS Edge.
* Traffico interrotto a causa di route di sistema o UDR
* BGP non abilitato nella connessione gateway
* Sonda DIP in basso alla Load Balancer
