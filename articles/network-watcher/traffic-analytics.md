---
title: Analisi del traffico di Azure | Microsoft Docs
description: Informazioni su come analizzare i log dei flussi dei gruppi di sicurezza di rete di Azure con Analisi del traffico.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: jdial
ms.openlocfilehash: 9fc44fdd6ce01452ffc2506c599e3d05aa0803e1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="traffic-analytics"></a>Analisi del traffico

Analisi del traffico è una soluzione basata sul cloud che fornisce visibilità delle attività di utenti e applicazioni nelle reti cloud. Analisi del traffico analizza i log dei flussi dei gruppi di sicurezza di rete di Network Watcher per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Con Analisi del traffico è possibile:

- Visualizzare l'attività della rete nelle sottoscrizioni di Azure e identificare le aree sensibili.
- Identificare le minacce alla sicurezza e proteggere la rete con informazioni quali porte aperte, applicazioni che tentano l'accesso a Internet e macchine virtuali (VM) che si connettono a reti non autorizzate.
- Conoscere i modelli di flusso di traffico nelle aree di Azure e in Internet per ottimizzare le prestazioni e la capacità della distribuzione della rete.
- Trovare le configurazioni di rete errate che comportano connessioni non riuscite nella rete.

## <a name="why-traffic-analytics"></a>Perché usare Analisi del traffico?

È fondamentale monitorare, gestire e conoscere la rete per fare in modo che la sicurezza, la conformità e le prestazioni non vengano compromesse. Per proteggere e ottimizzare l'ambiente, è indispensabile conoscerlo. Spesso è necessario conoscere lo stato corrente della rete, chi si connette a cosa, quali porte sono aperte a Internet, il comportamento previsto della rete, il comportamento irregolare della rete e i picchi improvvisi di traffico.

Le reti cloud sono diverse dalle reti aziendali locali, in cui sono disponibili router e commutatori idonei per Netflow o per un protocollo equivalente, che consentono di raccogliere il traffico di rete IP in ingresso o in uscita in un'interfaccia di rete. Analizzando i dati sul flusso di traffico, è possibile compilare un'analisi del flusso e del volume del traffico di rete.

Le reti virtuali di Azure hanno i log dei flussi dei gruppi di sicurezza di rete, che forniscono informazioni sul traffico IP in ingresso e in uscita tramite un gruppo di sicurezza di rete associato a singole interfacce di rete, VM o subnet. Analizzando i log dei flussi dei gruppi di sicurezza di rete non elaborati e inserendo l'intelligence per sicurezza, topologia e geografia, Analisi del traffico può fornire informazioni sul flusso del traffico nell'ambiente. Analisi del traffico fornisce informazioni, tra cui gli host che comunicano di più, i protocolli applicativi che comunicano di più, le coppie di host che conversano di più, il traffico consentito/bloccato, il traffico in ingresso/uscita, le porte Internet aperte, le regole di blocco più frequenti, la distribuzione del traffico per data center di Azure, rete virtuale, subnet o reti non autorizzate.

## <a name="key-components"></a>Componenti chiave 

- **Gruppo di sicurezza di rete (NSG)**: contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a Rete virtuale di Azure. I gruppi di sicurezza di rete possono essere associati a subnet, singole VM (distribuzione classica) o singole interfacce di rete collegate a VM (Resource Manager). Per altre informazioni, vedere [Panoramica dei gruppi di sicurezza di rete](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log dei flussi dei gruppi di sicurezza di rete (NSG)**: permettono di visualizzare le informazioni sul traffico IP in ingresso e in uscita attraverso un gruppo di sicurezza di rete. Sono scritti in formato JSON e mostrano i flussi in ingresso e in uscita per ogni regola, la scheda di rete a cui si applica il flusso, informazioni a 5 tuple relative al flusso (indirizzo IP di origine/destinazione, porta di origine/destinazione e protocollo) e se il traffico è consentito o meno. Per altre informazioni sui log dei flussi dei gruppi di sicurezza di rete, vedere [Log dei flussi per i gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: servizio di Azure che raccoglie i dati di monitoraggio e li archivia in un repository centrale. Questi dati possono includere eventi, dati sulle prestazioni o dati personalizzati forniti tramite l'API di Azure. Dopo essere stati raccolti, i dati sono disponibili per generare avvisi, per l'analisi e per l'esportazione. Le applicazioni di monitoraggio, ad esempio Monitoraggio prestazioni rete e Analisi del traffico, sono basate su Log Analytics. Per altre informazioni, vedere [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Area di lavoro di Log Analytics**: istanza di Log Analytics, in cui vengono archiviati i dati relativi a un account Azure. Per altre informazioni sulle aree di lavoro di Log Analytics, vedere [Creare un'area di lavoro di Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete in Azure. Con Network Watcher è possibile attivare e disattivare i log dei flussi dei gruppi di sicurezza di rete. Per altre informazioni, vedere [Network Watcher](network-watcher-monitoring-overview.md#network-watcher).

## <a name="how-traffic-analytics-works"></a>Come funziona Analisi del traffico 

Analisi del traffico esamina i log dei flussi dei gruppi di sicurezza di rete e acquisisce log ridotti aggregando i flussi comuni tra lo stesso indirizzo IP di origine, indirizzo IP di destinazione, porta di destinazione e protocollo, ad esempio Host 1 (indirizzo IP: 10.10.10.10) che comunica con Host 2 (indirizzo IP: 10.10.20.10), 100 volte nell'arco di 1 ora usando una porta (ad esempio, 80) e un protocollo (ad esempio, http). Il log ridotto ha una voce, indicante che Host 1 e Host 2 hanno comunicato 100 volte nell'arco di 1 ora usando la porta *80* e il protocollo *HTTP*, invece di 100 voci. I log ridotti vengono migliorati con informazioni su geografia, sicurezza e topologia e quindi archiviati in un'area di lavoro di Log Analytics. L'immagine seguente illustra il flusso di dati:

![Flusso di dati per l'elaborazione dei log dei flussi dei gruppi di sicurezza di rete](media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Aree supportate

Analisi del traffico è disponibile in anteprima. Le funzionalità nella versione di anteprima non offrono lo stesso livello di disponibilità e affidabilità delle funzionalità in versione di disponibilità generale.  Anche se in versione di anteprima, è possibile usare Analisi del traffico per i gruppi di sicurezza di rete in tutte le aree seguenti: Stati Uniti centro-occidentali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti centrali, Stati Uniti occidentali, Stati Uniti occidentali 2, Europa occidentale, Europa settentrionale, Regno Unito orientale, Regno Unito meridionale, Australia orientale e Australia sud-orientale. L'area di lavoro di Log Analytics deve esistere nell'area Stati Uniti centro-occidentali, Stati Uniti orientali, Europa occidentale, Australia sud-orientale o Regno Unito meridionale.

## <a name="prerequisites"></a>prerequisiti

### <a name="enable-network-watcher"></a>Abilitare Network Watcher 

Per analizzare il traffico, è necessario avere un'istanza di Network Watcher esistente o [abilitare un'istanza di Azure Network Watcher](network-watcher-create.md) in ogni area in cui sono presenti gruppi di sicurezza di rete per cui si vuole analizzare il traffico. Analisi del traffico può essere abilitata per i gruppi di sicurezza di rete ospitati in una delle [aree supportate](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Registrare di nuovo il provider di risorse di rete 

Prima di poter usare Analisi del traffico durante l'anteprima, è necessario registrare di nuovo il provider di risorse di rete. Fare clic su **Prova** nella casella di codice seguente per aprire Azure Cloud Shell. Cloud Shell consente di accedere automaticamente alla sottoscrizione di Azure. Dopo che Cloud Shell è stato aperto, immettere il comando seguente per registrare di nuovo il provider di risorse di rete:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Selezionare un gruppo di sicurezza di rete 

Prima di abilitare la registrazione dei flussi per i gruppi di sicurezza di rete, è necessario avere un gruppo di sicurezza di rete per cui registrare i flussi. Se non è disponibile un gruppo di sicurezza di rete, vedere [Creare un gruppo di sicurezza di rete](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) per crearne uno.

Sul lato sinistro del portale di Azure selezionare **Monitoraggio**, quindi **Network Watcher** e infine selezionare **Log del flusso del NSG**. Selezionare il gruppo di sicurezza di rete per cui si vuole abilitare un log del flusso del gruppo di sicurezza di rete, come illustrato nell'immagine seguente:

![Selezione di gruppi di sicurezza di rete che richiedono l'abilitazione del log del flusso del gruppo di sicurezza di rete](media/traffic-analytics/selection-of-nsgs-that-require- enablement-of-nsg-flow-logging.png)

Se si prova ad abilitare Analisi del traffico per un gruppo di sicurezza di rete ospitato in un'area diversa dalle [aree supportate](#supported-regions), viene visualizzato un errore "Non trovato". 

## <a name="enable-flow-log-settings"></a>Abilitare le impostazioni dei log dei flussi

Prima di abilitare le impostazioni dei log dei flussi, è necessario completare le attività seguenti:

Registrare il provider Azure Insights, se non è già registrato, per la sottoscrizione:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Se non è già disponibile un account di archiviazione di Azure in cui archiviare i log dei flussi dei gruppi di sicurezza di rete, è necessario crearne uno. È possibile creare un account di archiviazione con il comando che segue. Prima di eseguire il comando, sostituire `<replace-with-your-unique-storage-account-name>` con un nome univoco in tutte le località di Azure, di lunghezza compresa tra 3 e 24 caratteri, usando solo numeri e lettere minuscole. È anche possibile modificare il nome del gruppo di risorse, se necessario.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selezionare le opzioni seguenti, come illustrato nell'immagine:

1. Per *Stato* selezionare **Sì**
2. Selezionare un account di archiviazione esistente nel quale archiviare i log dei flussi. Per archiviare i dati per sempre, impostare il valore su *0*. Si devono sostenere i costi di archiviazione di Azure per l'account di archiviazione.
3. Impostare **Conservazione** sul numero di giorni per cui si vogliono archiviare i dati.
4. Selezionare *Sì* per **Stato di Analisi del traffico**.
5. Selezionare un'area di lavoro di Log Analytics (OMS) esistente oppure selezionare **Crea una nuova area di lavoro** per crearne una nuova. Un'area di lavoro di Log Analytics viene usata da Analisi del traffico per archiviare i dati aggregati e indicizzati che vengono quindi usati per generare l'analisi. Se si seleziona un'area di lavoro esistente, deve esistere in una delle [aree supportate](#traffic-analytics-supported-regions) ed essere stata aggiornata al nuovo linguaggio di query. Se non si vuole aggiornare un'area di lavoro esistente o non si ha un'area di lavoro in un'area supportata, crearne una nuova. Per altre informazioni sui linguaggi di query, vedere [Aggiornamento di Azure Log Analytics alla nuova ricerca log](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    Non è necessario che l'area di lavoro di Log Analytics (OMS) che ospita la soluzione Analisi del traffico e i gruppi di sicurezza di rete si trovino nella stessa area. È ad esempio possibile avere Analisi del traffico in un'area di lavoro nell'area Europa occidentale e i gruppi di sicurezza di rete in Stati Uniti orientali e Stati Uniti occidentali. È possibile configurare più gruppi di sicurezza di rete nella stessa area di lavoro.
6. Selezionare **Salva**.

    ![Selezione dell'account di archiviazione, dell'area di lavoro di Log Analytics e dell'abilitazione di Analisi del traffico](media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Ripetere i passaggi precedenti per qualsiasi altro gruppo di sicurezza di rete per il quale si vuole abilitare Analisi del traffico. I dati dai log dei flussi vengono inviati all'area di lavoro, quindi assicurarsi che le leggi locali e le normative in vigore nel proprio paese consentano l'archiviazione dei dati nell'area in cui è presente l'area di lavoro.

## <a name="view-traffic-analytics"></a>Visualizzare Analisi del traffico

Sul lato sinistro del portale selezionare **Tutti i servizi**, quindi immettere *Monitoraggio* nella casella **Filtro**. Selezionare **Monitoraggio** quando viene visualizzato nei risultati della ricerca. Per iniziare a esplorare Analisi del traffico e le relative funzionalità, selezionare **Network Watcher**, quindi **Analisi del traffico (anteprima)**.

![Accesso al dashboard Analisi del traffico](media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

La prima visualizzazione del dashboard potrebbe richiedere fino a 30 minuti perché Analisi del traffico deve prima aggregare dati sufficienti per ottenere informazioni significative, prima di poter generare i report.

## <a name="usage-scenarios"></a>Scenari di utilizzo

Di seguito sono elencate alcune informazioni utili da visualizzare dopo la configurazione completa di Analisi del traffico:

### <a name="find-traffic-hotspots"></a>Trovare le aree sensibili del traffico

**Cercare**

- Quali host ricevono o inviano il maggior traffico?
    - Conoscere gli host che inviano o ricevono il maggior traffico consente di identificare gli host che elaborano il maggior traffico.
    - È possibile valutare se il volume di traffico è appropriato per un host. Il volume di traffico è un comportamento normale o è opportuno eseguire un'ulteriore analisi?
- Qual è la quantità di traffico in ingresso/uscita?
    -   È previsto che l'host riceva più traffico in ingresso che in uscita o viceversa?
- Statistiche del traffico consentito/bloccato.
    - Perché un host consente o blocca un volume di traffico significativo?

    Selezionare **Altri dettagli** sotto **Host con il maggior traffico**, come illustrato nell'immagine seguente:

    ![Dashboard che presenta l'host con i dettagli sul maggior traffico](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- L'immagine seguente illustra la tendenza temporale per i cinque host che conversano di più e i dettagli relativi ai flussi (flussi consentiti - in ingresso/uscita e negati - in ingresso/uscita) per una macchina virtuale:

    ![Tendenza per i cinque host che conversano di più](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Cercare**

- Quali sono le coppie di host che conversano di più?
    - Comportamento previsto, ad esempio comunicazione front-end-back-end, o comportamento irregolare, ad esempio traffico Internet back-end.
- Statistiche del traffico consentito/bloccato
    - Perché un host consente o blocca un volume di traffico significativo
- Protocollo applicativo di uso più frequente tra le coppie di host che conversano di più:
    - Queste applicazioni sono consentite in questa rete?
    - Le applicazioni sono configurate correttamente? Usano il protocollo appropriato per la comunicazione? Selezionare **Altri dettagli** sotto **Conversazioni più frequenti**, come illustrato nell'immagine seguente:

        ![Dashboard che presenta le conversazioni più frequenti](media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- L'immagine seguente illustra la tendenza temporale per le prime cinque conversazioni e i dettagli relativi ai flussi, ad esempio flussi consentiti e negati in ingresso e in uscita per una coppia di conversazioni:

    ![Dettagli e tendenza delle cinque conversazioni più attive](media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Cercare**

- Quale protocollo applicativo è più usato nell'ambiente e quali coppie di host in conversazione usano di più il protocollo applicativo?
    - Queste applicazioni sono consentite in questa rete?
    - Le applicazioni sono configurate correttamente? Usano il protocollo appropriato per la comunicazione? Il comportamento previsto usa porte comuni, ad esempio 80 e 443. Per la comunicazione standard, se vengono visualizzate porte insolite, potrebbe essere necessario modificare la configurazione. Selezionare **Altri dettagli** sotto **Protocolli applicazioni più frequenti** nell'immagine seguente:

        ![Dashboard che presenta i primi protocolli applicativi](media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Le immagini seguenti illustrano la tendenza temporale per i primi cinque protocolli L7 e i dettagli relativi ai flussi (ad esempio, flussi consentiti e negati) per un protocollo L7:

    ![Dettagli e tendenza dei primi cinque protocolli di livello 7](media/traffic-analytics/top five-layer-seven-protocols-details-and-trend.png)

    ![Dettagli dei flussi per il protocollo applicativo in ricerca log](media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Cercare**

- Tendenze di utilizzo della capacità di un gateway VPN nell'ambiente.
    - Ogni SKU VPN consente una certa quantità di larghezza di banda. I gateway VPN sono sottoutilizzati?
    - I gateway stanno per raggiungere la capacità massima? È consigliabile eseguire l'aggiornamento allo SKU superiore successivo?
- Quali sono gli host che conversano di più, tramite quale gateway VPN, su quale porta?
    - Questo modello è normale? Selezionare **Altri dettagli** sotto **Connessioni VPN attive più frequenti**, come illustrato nell'immagine seguente:

        ![Dashboard che presenta le connessioni VPN più attive](media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- L'immagine seguente illustra la tendenza temporale per l'utilizzo della capacità di un gateway VPN di Azure e i dettagli relativi ai flussi (ad esempio, flussi consentiti e porte):

    ![Tendenza dell'utilizzo del gateway VPN e dettagli dei flussi](media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualizzare la distribuzione del traffico per area geografica

**Cercare**

- Distribuzione del traffico per data center, ad esempio origini più frequenti di traffico verso un data center, principali reti non autorizzate in conversazione con il data center e protocolli applicativi che conversano di più.
    - Se si osserva un carico maggiore in un data center, è possibile pianificare una distribuzione efficiente del traffico.
    - Se reti non autorizzate sono in conversazione nel data center, correggere le regole dei gruppi di sicurezza di rete per bloccarle.

    Selezionare **Fare clic qui per visualizzare la mappa geografica in tempo reale** sotto **Distribuzione del traffico tra data center di Azure**, come illustrato nell'immagine seguente:

  ![Dashboard che presenta la distribuzione del traffico](media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- La mappa geografica mostra la barra multifunzione in alto per selezionare parametri come i data center (distribuiti/nessuna distribuzione/attivi/inattivi/Analisi del traffico abilitata/Analisi del traffico non abilitata) e i paesi che forniscono traffico non dannoso/dannoso alla distribuzione attiva:

    ![Visualizzazione della mappa geografica che presenta la distribuzione attiva](media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- La mappa geografica illustra la distribuzione del traffico a un data center da paesi e continenti che comunicano con il data center tramite linee di colore blu (traffico non dannoso) e rosso (traffico dannoso):

    ![Visualizzazione della mappa geografica che presenta la distribuzione del traffico a paesi e continenti](media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Dettagli dei flussi per la distribuzione del traffico nella ricerca log](media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualizzare la distribuzione del traffico per rete virtuale

**Cercare**

- Distribuzione del traffico per rete virtuale, topologia, origini più frequenti di traffico verso la rete virtuale, principali reti non autorizzate in conversazione con la rete virtuale e protocolli applicativi che conversano di più.
    - Determinare quali coppie di reti virtuali stanno conversando. Se la conversazione non è prevista, è possibile risolvere il problema.
    - Se reti non autorizzate conversano con una rete virtuale, è possibile correggere le regole dei gruppi di sicurezza di rete per bloccare le reti non autorizzate.
 
    Selezionare **Fare clic qui per visualizzare la topologia del traffico delle reti virtuali** sotto **Distribuzione reti virtuali**, come illustrato nell'immagine seguente: 

    ![Dashboard che presenta la distribuzione delle reti virtuali](media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- La topologia delle reti virtuali mostra la barra multifunzione in alto per selezionare parametri come le connessioni esterne, i flussi attivi e i flussi dannosi di una rete virtuale (connessioni tra reti virtuali/attive/inattive).
- La topologia delle reti virtuali illustra la distribuzione del traffico verso una rete virtuale per quanto concerne i flussi (consentiti/bloccati/in ingresso/in uscita/non dannosi/dannosi), il protocollo applicativo e i gruppi di sicurezza di rete, ad esempio:

    ![Topologia delle reti virtuali che presenta i dettagli della distribuzione e dei flussi di traffico](media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![Dettagli dei flussi per la distribuzione del traffico di rete virtuale nella ricerca log](media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Cercare**

- Distribuzione del traffico per subnet, topologia, origini più frequenti di traffico verso la subnet, principali reti non autorizzate in conversazione con la subnet e protocolli applicativi che conversano di più.
    - Determinare quali coppie di subnet stanno conversando. Se si osservano conversazioni impreviste, è possibile correggere la configurazione.
    - Se reti non autorizzate sono in conversazione con una subnet, è possibile risolvere il problema configurando regole dei gruppi di sicurezza di rete, ad esempio per bloccare le reti non autorizzate.
- La topologia delle subnet mostra la barra multifunzione in alto per selezionare parametri come la subnet attiva/inattiva, le connessioni esterne, i flussi attivi e i flussi dannosi della subnet.
- La topologia delle subnet illustra la distribuzione del traffico verso una rete virtuale per quanto concerne i flussi (consentiti/bloccati/in ingresso/in uscita/non dannosi/dannosi), il protocollo applicativo e i gruppi di sicurezza di rete, ad esempio:

    ![Topologia delle subnet che presenta la distribuzione del traffico verso una rete virtuale per quanto concerne i flussi](media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Visualizzare le porte e le macchine virtuali che ricevono il traffico da Internet

**Cercare**

- Quali porte aperte sono in conversazione via Internet?
    - Se risultano aperte porte impreviste, è possibile correggere la configurazione:

        ![Dashboard che presenta le porte che ricevono e inviano il traffico a Internet](media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

        ![Dettagli degli host e delle porte di destinazione di Azure](media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Cercare**

È presente traffico dannoso nell'ambiente? Da dove ha origine? Qual è la destinazione?

![Dettaglio dei flussi di traffico dannoso in ricerca log](media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsg-rule-hits"></a>Visualizzare le tendenze nei riscontri della regola del gruppo di sicurezza di rete

**Cercare**

- Quale gruppo di sicurezza di rete/regola ha più riscontri?
- Quali sono le coppie di conversazione di origine e di destinazione più frequenti per il gruppo di sicurezza di rete?

    ![Dashboard che presenta le statistiche sui riscontri del gruppo di sicurezza di rete](media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Le immagini seguenti illustrano la tendenza temporale per i riscontri delle regole del gruppo di sicurezza di rete e i dettagli dei flussi di origine-destinazione per un gruppo di sicurezza di rete:

    ![Presentazione della tendenza temporale per i riscontri delle regole del gruppo di sicurezza di rete e delle regole del gruppo di sicurezza di rete più frequenti](media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Dettagli delle statistiche sulle regole del gruppo di sicurezza di rete più frequenti in ricerca log](media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Domande frequenti

Per le risposte alle domande frequenti, vedere [Traffic Analytics FAQ](traffic-analytics-faq.md) (Domande frequenti su Analisi del traffico).