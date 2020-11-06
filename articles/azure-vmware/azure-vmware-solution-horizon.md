---
title: Distribuire Horizon in una soluzione VMware di Azure
description: Informazioni su come distribuire VMware Horizon in una soluzione VMware di Azure.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: fb5e5b4c5f5da4c140f8d3575b963545f3a57db8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423104"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Distribuire Horizon in una soluzione VMware di Azure 

>[!NOTE]
>Questo documento è incentrato sul prodotto VMware Horizon, noto in precedenza come Horizon 7. Horizon è una soluzione diversa rispetto a Horizon cloud in Azure, sebbene esistano alcuni componenti condivisi. I vantaggi principali della soluzione VMware di Azure includono un metodo di ridimensionamento più semplice e l'integrazione della gestione di VMware Cloud Foundation nel portale di Azure.

[VMware Horizon](https://www.vmware.com/products/horizon.html)®, una piattaforma per desktop e applicazioni virtuali, viene eseguita nel Data Center e fornisce una gestione semplice e centralizzata. Offre desktop virtuali e applicazioni su qualsiasi dispositivo, ovunque. Horizon consente di creare ed eseguire il broker di connessioni a desktop virtuali Windows e Linux, applicazioni ospitate da Desktop remoto server (RDS), desktop e computer fisici.

In questo articolo viene illustrata la distribuzione di Horizon in una soluzione VMware di Azure. Per informazioni generali su VMware Horizon, vedere la documentazione di Horizon Production:

* [Che cos'è VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [Altre informazioni su VMware Horizon](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Architettura di riferimento per Horizon](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Con l'introduzione di Horizon sulla soluzione VMware di Azure, sono ora disponibili due soluzioni VDI (Virtual Desktop Infrastructure) sulla piattaforma Azure. Il diagramma seguente riepiloga le differenze principali a livello generale.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Orizzonte sulla soluzione VMware di Azure e sul cloud Horizon in Azure" border="false":::

Horizon 2006 e versioni successive nella riga di rilascio di Horizon 8 supporta la distribuzione locale e la distribuzione della soluzione VMware di Azure. Sono disponibili alcune funzionalità di Horizon supportate in locale, ma non nella soluzione VMware di Azure. Sono supportati anche altri prodotti nell'ecosistema Horizon. Per informazioni, vedere [parità di funzionalità e interoperabilità](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Distribuisci Horizon in un cloud ibrido

È possibile distribuire Horizon in un ambiente cloud ibrido quando si usa Horizon cloud Pod Architecture (CPA) per connettere i data center locali e di Azure. CPA consente di ridimensionare la distribuzione, compilare un cloud ibrido e garantire ridondanza per la continuità aziendale e il ripristino di emergenza.  Per altre informazioni, vedere [espansione degli ambienti Horizon 7 esistenti](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA non è una distribuzione estesa; ogni pod Horizon è distinto e tutti i server di connessione che appartengono a ognuno dei singoli Pod devono trovarsi in un'unica posizione ed essere eseguiti nello stesso dominio broadcast da una prospettiva di rete.

Analogamente a data center locali o privati, Horizon può essere distribuito in un cloud privato della soluzione VMware di Azure. Nelle sezioni seguenti verranno illustrate le differenze principali nella distribuzione di Horizon in locale e nella soluzione VMware di Azure.

Il cloud privato di Azure è concettualmente identico a quello di VMware SDDC, un termine usato in genere nella documentazione di Horizon. Il resto di questo documento usa i termini interscambiabili con il cloud privato di Azure e VMware SDDC.

Per la gestione delle licenze di sottoscrizione, è necessario il connettore Horizon cloud per la soluzione Azure Horizon in Azure. Il connettore cloud può essere distribuito in rete virtuale di Azure insieme ai server di connessione Horizon.

>[!IMPORTANT]
>Il supporto del piano di controllo Horizon per la soluzione VMware Horizon in Azure non è ancora disponibile. Assicurarsi di scaricare la versione VHD di Horizon Cloud Connector.

## <a name="vcenter-cloud-admin-role"></a>ruolo di amministratore del cloud vCenter

Poiché la soluzione VMware di Azure è un servizio SDDC e Azure gestisce il ciclo di vita della soluzione VMware SDDC in Azure, il modello di autorizzazione vCenter nella soluzione VMware di Azure è limitato dalla progettazione.

I clienti devono usare il ruolo di amministratore del cloud, che dispone di un set limitato di autorizzazioni di vCenter. Il prodotto Horizon è stato modificato per collaborare con il ruolo di amministratore del cloud nella soluzione VMware di Azure, in particolare:

* Il provisioning del clone istantaneo è stato modificato per l'esecuzione in una soluzione VMware di Azure.

* Un criterio rete VSAN specifico (VMware_Horizon) è stato creato nella soluzione VMware di Azure per funzionare con Horizon, che deve essere disponibile e usato in SDDCs distribuito per Horizon.

* la cache di lettura basata sul contenuto vSphere (CBRC), nota anche come View Storage Accelerator, è disabilitata durante l'esecuzione nella soluzione VMware di Azure.

>[!IMPORTANT]
>CBRC non deve essere nuovamente attivato.

>[!NOTE]
>La soluzione VMware di Azure configura automaticamente impostazioni orizzonte specifiche purché venga distribuito Horizon 2006 (noto anche come Horizon 8) e versioni successive nel ramo Horizon 8 e selezionare l'opzione **Azure** nel programma di installazione del server di connessione Horizon.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Architettura di distribuzione della soluzione VMware di Azure Horizon

Un tipico progetto di architettura Horizon usa una strategia pod e Block. Un blocco è un singolo vCenter, mentre più blocchi combinati rendono un pod. Un pod Horizon è un'unità di organizzazione determinata dai limiti di scalabilità di Horizon. Ogni pod Horizon dispone di un portale di gestione separato, quindi una procedura di progettazione standard consiste nel ridurre al minimo il numero di Pod.

Ogni Cloud ha il proprio schema di connettività di rete. In combinazione con VMware SDDC networking/NSX Edge, la connettività di rete della soluzione VMware di Azure presenta requisiti univoci per la distribuzione di orizzonti diversi dall'ambiente locale.

Ogni cloud privato di Azure e SDDC è in grado di gestire 4.000 sessioni desktop o dell'applicazione, supponendo che:

* Il traffico del carico di lavoro viene allineato al profilo di lavoro delle attività LoginVSI.

* Viene considerato solo il traffico del protocollo, nessun dato utente.

* NSX Edge è configurato in modo da essere di grandi dimensioni.

>[!NOTE]
>Il profilo e le esigenze del carico di lavoro possono essere diversi e quindi i risultati possono variare in base al caso d'uso. I volumi di dati utente possono ridurre i limiti di scalabilità nel contesto del carico di lavoro. Ridimensionare e pianificare la distribuzione di conseguenza. Per altre informazioni, vedere le linee guida per il ridimensionamento nella sezione [dimensioni degli host della soluzione VMware di Azure per le distribuzioni Horizon](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Dato il cloud privato di Azure e il limite massimo di SDDC, è consigliabile un'architettura di distribuzione in cui i server di connessione Horizon e VMware Unified Access Gateway (UAGs) siano in esecuzione all'interno della rete virtuale di Azure. Converte in modo efficace ogni cloud privato di Azure e SDDC in un blocco. A sua volta, massimizzare la scalabilità di Horizon in esecuzione nella soluzione VMware di Azure.

La connessione dalla rete virtuale di Azure ai cloud privati/SDDCs di Azure deve essere configurata con ExpressRoute FastPath. Il diagramma seguente illustra una distribuzione di base di un pod.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Distribuzione tipica di un pod di Horizon con percorso veloce ExpressPath" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Connettività di rete per la scalabilità di Horizon nella soluzione VMware di Azure

Questa sezione illustra l'architettura di rete a un livello elevato con alcuni esempi di distribuzione comuni per semplificare la scalabilità di Horizon nella soluzione VMware di Azure. Lo stato attivo è specifico per gli elementi di rete cruciali. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Soluzione VMware Single Horizon pod in Azure

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Soluzione VMware Single Horizon pod in Azure" border="false":::

Un singolo POD Horizon è lo scenario di distribuzione più semplice, perché si distribuisce un solo Horizon pod nell'area Stati Uniti orientali.  Poiché ogni cloud privato e SDDC è stimato per la gestione delle sessioni Desktop 4.000, si distribuiscono le dimensioni massime del pod di Horizon.  È possibile pianificare la distribuzione di un massimo di tre cloud privati/SDDCs.

Con le macchine virtuali (VM) dell'infrastruttura Horizon distribuite nella rete virtuale di Azure, è possibile raggiungere le sessioni 12.000 per ogni pod Horizon. La connessione tra ogni cloud privato e SDDC alla rete virtuale di Azure è ExpressRoute percorso rapido.  Non è necessario alcun traffico East-West tra cloud privati. 

I presupposti chiave per questo esempio di distribuzione di base includono quanto segue:

* Non è presente un pod Horizon locale che si vuole connettere a questo nuovo pod usando cloud Pod Architecture (CPA).

* Gli utenti finali si connettono ai desktop virtuali tramite Internet (rispetto alla connessione tramite un data center locale).

Il controller di dominio di Active Directory viene connesso in rete virtuale di Azure con l'istanza locale di AD tramite VPN o circuito ExpressRoute.

Una variante dell'esempio di base potrebbe essere il supporto della connettività per le risorse locali. Ad esempio, gli utenti accedono ai desktop e generano il traffico delle applicazioni desktop virtuali o si connettono a un pod di Horizon locale usando CPA.

Il diagramma mostra come supportare la connettività per le risorse locali. Per connettersi alla rete aziendale alla rete virtuale di Azure, è necessario un circuito ExpressRoute.  Sarà anche necessario connettere la rete aziendale a ogni cloud privato e SDDCs usando ExpressRoute Copertura globale.  Consente la connettività dal SDDC al circuito ExpressRoute e alle risorse locali. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Connettere la rete aziendale a una rete virtuale di Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Più baccelli Horizon nella soluzione VMware di Azure in più aree

Un altro scenario consiste nel ridimensionare l'orizzonte tra più baccelli.  In questo scenario si distribuiscono due pod di Horizon in due aree diverse e la si federa usando CPA. È simile alla configurazione di rete nell'esempio precedente, ma con alcuni collegamenti tra aree aggiuntive. 

Si connetterà la Rete in ingresso virtuale di Azure ogni area ai cloud privati/SDDCs nell'altra area. Consente ai server di connessione Horizon parte della Federazione CPA di connettersi a tutti i desktop sotto la gestione. L'aggiunta di altri cloud privati/SDDCs a questa configurazione consente di eseguire il ridimensionamento a 24.000 sessioni complessive. 

Gli stessi principi si applicano se si distribuiscono due pod Horizon nella stessa area.  Assicurarsi di distribuire il secondo pod Horizon in una *rete virtuale di Azure separata*. Analogamente all'esempio di Pod singolo, è possibile connettere la rete aziendale e il Pod locale a questo esempio di più POD/aree usando ExpressRoute e Copertura globale. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Più baccelli Horizon nella soluzione VMware di Azure in più aree" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Ridimensionare gli host della soluzione VMware di Azure per le distribuzioni Horizon 

La metodologia di dimensionamento di Horizon in un host in esecuzione nella soluzione VMware di Azure è più semplice rispetto a Horizon in locale.  Questo perché l'host della soluzione VMware di Azure è standardizzato.  Il dimensionamento host esatto consente di determinare il numero di host necessari per supportare i requisiti VDI.  È fondamentale determinare il costo per desktop.

### <a name="sizing-tables"></a>Ridimensionamento di tabelle

Le tabelle mostrano i carichi di lavoro comuni per i carichi di lavoro del Knowledge Worker di login e i carichi di lavoro di Power Worker.

#### <a name="knowledge-worker-workloads"></a>Carichi di lavoro Knowledge Worker

:::image type="content" source="media/horizon/common-vdi-profiles-vsi-workloads-knowledge.png" alt-text="Tabella dei profili VDI comuni per VMware Horizon per gli account di lavoro del Knowledge Worker VSI" lightbox="media/horizon/common-vdi-profiles-vsi-workloads-knowledge.png" border="false":::

#### <a name="power-worker-workloads"></a>Carichi di lavoro di Power Worker

:::image type="content" source="media/horizon/common-vdi-profiles-vsi-workloads-power.png" alt-text="Tabella dei profili VDI comuni per VMware Horizon per i carichi di lavoro di Power Worker VSI di login" lightbox="media/horizon/common-vdi-profiles-vsi-workloads-power.png" border="false":::

### <a name="azure-vmware-solution-host-instance"></a>Istanza host della soluzione VMware di Azure

* Server r640 PowerEdge-DSS con restrizioni

* 36 core a \@ 2,3 GHz

* 576 GB DI RAM

* Controller HBA SAS a 12 Gbps HBA330 (NON RAID)

* la combinazione SATA SSD 1,92 TB USA 6 Gbps 512 2.5 in unità di disponibilità a caldo, 3 DWPD, 10512 TBW

* Intel 1,6 TB, NVMe, mixed use Express Flash, 2,5 SFF Drive, U. 2, P4600 with Carrier

* 2 gruppi di dischi rete VSAN: 1,6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Input di ridimensionamento dell'orizzonte

Ecco cosa è necessario raccogliere per il carico di lavoro pianificato:

* Numero di desktop simultanei

* VCPU necessari per desktop

* VRAM necessaria per desktop

* Spazio di archiviazione necessario per desktop

In generale, le distribuzioni VDI sono vincolate alla CPU o alla RAM, che determinano le dimensioni dell'host. Si prenda l'esempio seguente per un tipo di carico di lavoro LoginVSI Knowledge Worker, convalidato con il test delle prestazioni:

* 2.000 distribuzione desktop simultanea

* 2vCPU per desktop.

* vRAM da 4 GB per desktop.

* 50 GB di spazio di archiviazione per desktop

Per questo esempio, il numero totale di fattori host è pari a 18, ottenendo una densità della macchina virtuale per host di 111.

>[!IMPORTANT]
>I carichi di lavoro dei clienti variano a seconda di questo esempio di LoginVSI Knowledge Worker. Come parte della pianificazione della distribuzione, usare le SEs EUC VMware per esigenze specifiche di dimensionamento e prestazioni. Assicurarsi di eseguire il test delle prestazioni utilizzando il carico di lavoro effettivo e pianificato prima di finalizzare il dimensionamento dell'host e modificarlo di conseguenza.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Gestione delle licenze della soluzione VMware di Azure per Horizon 

Sono disponibili quattro componenti per i costi complessivi per l'esecuzione di Horizon in una soluzione VMware di Azure. 

### <a name="azure-vmware-solution-capacity-cost"></a>Costo della capacità della soluzione VMware di Azure

Per informazioni sui prezzi, vedere la pagina dei [prezzi della soluzione VMware di Azure](https://azure.microsoft.com/pricing/details/azure-vmware/)

### <a name="horizon-licensing-cost"></a>Costo della licenza Horizon

Esistono due licenze disponibili per l'uso con la soluzione VMware di Azure, che può essere utente simultaneo (CCU) o utente denominato (NU):

* Licenza Horizon Subscription

* Licenza Horizon Universal Subscription

Se si distribuisce solo Horizon per la soluzione VMware di Azure per il futuro prevedibile, usare la licenza Horizon Subscription poiché si tratta di un costo inferiore.

Se distribuito in una soluzione VMware di Azure e in locale, come nel caso di utilizzo del ripristino di emergenza, scegliere la licenza Horizon Universal Subscription. Include una licenza vSphere per la distribuzione locale, quindi ha un costo superiore.

Collaborare con il team di vendita VMware EUC per determinare il costo della licenza Horizon in base alle esigenze.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Costo delle macchine virtuali dell'infrastruttura Horizon nella rete virtuale di Azure

In base all'architettura di distribuzione standard, le macchine virtuali dell'infrastruttura Horizon sono costituite da server di connessione, UAGs e responsabili del volume delle app. Vengono distribuiti nella rete virtuale di Azure del cliente. Sono necessarie altre istanze native di Azure per supportare i servizi di disponibilità elevata, Microsoft SQL o Microsoft Active Directory (AD) in Azure. La tabella elenca le istanze di Azure in base a un esempio di distribuzione di 2.000 desktop. 

>[!NOTE]
>Per essere in grado di gestire l'errore, distribuire un server più del necessario per il numero di connessioni (n + 1). Il numero minimo consigliato di istanze del server di connessione, UAG e gestione volumi app è 2 e il numero di richieste aumenterà in base alla quantità di utenti che verrà supportata dall'ambiente.  Un singolo server di connessione supporta un massimo di 4.000 sessioni, sebbene la procedura consigliata sia 2.000. Sono supportati fino a sette server di connessione per ogni pod con una raccomandazione di 12.000 sessioni attive in totale per ogni pod. Per i numeri più recenti, vedere l' [articolo della Knowledge Base VMware limiti di ridimensionamento e consigli per VMware Horizon 7](https://kb.vmware.com/s/article/2150348).

| Componente dell'infrastruttura Horizon | Istanza di Azure | Numero di istanze necessarie (per 2.000-desktop)    | Commento  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Server di connessione                | D4sv3          | 2       | *Vedere la nota sopra*                         |    
| UAG                              | F2sv2          | 2       | *Vedere la nota sopra*                         |
| Gestione volumi app              | D4sv3          | 2       | *Vedere la nota sopra*                         |
| Connettore Cloud                  | D4sv3          | 1       |                                          |
| Controller AD                    | D4sv3          | 2       | *Opzione per usare il servizio AD di MSFT in Azure* |
| Database MS-SQL                  | D4sv3          | 2       | *Opzione per l'uso del servizio SQL in Azure*     |
| Condivisione file di Windows               | D4sv3          |         | *Facoltativo*                               |

Il costo della macchina virtuale dell'infrastruttura è pari a \$ 0,36 per utente al mese per la distribuzione di 2.000 desktop nell'esempio precedente. Questo esempio usa l'istanza di Azure Stati Uniti orientali 2020 prezzi. I prezzi possono variare in base all'area, alle opzioni selezionate e alla tempistica.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla soluzione VMware Horizon in Azure, vedere le [domande frequenti](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)su VMware Horizon.
