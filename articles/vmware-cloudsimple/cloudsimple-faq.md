---
title: Domande frequenti - Soluzione Azure VMware di CloudSimpleFAQ - Azure VMware Solution by CloudSimple
description: Domande frequenti per la soluzione Azure VMware di CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025062"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Domande frequenti su VMware Solution di CloudSimple

## <a name="cloudsimple-service"></a>Servizio CloudSimple

**Che cos'è la soluzione Azure VMware di CloudSimple?**

Azure VMware Solution by CloudSimple trasforma ed estende i carichi di lavoro VMware a cloud privati dedicati in Azure in pochi minuti. CloudSimple takes care of provisioning, managing the infrastructure, and orchestrating workloads between on-premises and Azure. Poiché le app vengono eseguite esattamente come in locale e in Azure, l'elasticità e i servizi del cloud possono trarre vantaggio dall'elasticità e dai servizi del cloud senza la complessità di riprogettare le app. CloudSimple riduce il costo totale di proprietà con un modello di consumo cloud che fornisce il provisioning su richiesta, il pay-as-you-grow e l'ottimizzazione della capacità.  Per informazioni su funzionalità, vantaggi e scenari, vedere [Soluzione VMware in Azure by CloudSimple.](cloudsimple-vmware-solutions-overview.md)

**Che cos'è un CloudSimple Private Cloud?**

Un cloud privato CloudSimple è un cloud privato dedicato costituito da un ambiente di elaborazione, archiviazione e rete ad alte prestazioni distribuito nell'infrastruttura di Microsoft Azure (spazio hardware e data center) in posizioni di Azure.A CloudSimple Private Cloud is a private, dedicated cloud that consists of a high-performance compute, storage, and networking environment deployed on Microsoft Azure infrastructure (hardware and datacenter space) in Azure locations.  Un cloud privato fornisce una "piattaforma come servizio" VMware nativa. In termini di VMware, ogni private cloud contiene esattamente un'istanza di vCenter Server. Il server vCenter gestisce più nodi ESXi contenuti in uno o più cluster vSphere, insieme all'archiviazione Virtual SAN (vSAN) corrispondente. Un servizio CloudSimple può contenere più cloud privati nella sottoscrizione di Azure.A CloudSimple service can contain multiple Private Clouds in your Azure subscription.  Per ulteriori dettagli, vedere [Panoramica del cloud privato](cloudsimple-private-cloud.md).

**Dove è disponibile il servizio CloudSimple?**

CloudSimple è disponibile nelle regioni degli Stati Uniti orientali, degli Stati Uniti occidentali e dell'Europa occidentale, con altre regioni in arrivo.

**Come si abilita l'abbonamento per CloudSimple?**

È possibile contattare il [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) rappresentante dell'account Microsoft per abilitare l'abbonamento per il servizio CloudSimple.You can contact your Microsoft account representative at to enable your subscription for the CloudSimple service. Specificare l'ID sottoscrizione nell'e-mail per cui si vuole che il servizio CloudSimple sia abilitato.  

**Come si accede al portale CloudSimple?**

È possibile accedere al portale CloudSimple dal portale di Azure.You access the CloudSimple portal from the Azure portal.  Per informazioni dettagliate, vedere [Accedere al portale VMware Solution by CloudSimple dal portale](access-cloudsimple-portal.md)di Azure.

**Come posso aumentare la capacità di un cloud privato?**

Per aumentare la capacità, acquistare nodi aggiuntivi dal portale di Azure e quindi usare i nodi per espandere il cloud privato dal portale CloudSimple.To increase capacity, purchase additional nodes from the Azure portal and then use the nodes to expand your Private Cloud from the CloudSimple portal.  È possibile aggiungere altri nodi a un cluster vSphere esistente o aggiungerli a un nuovo cluster vSphere.You can add additional nodes to an existing vSphere cluster or add them to a new vSphere cluster.  Per informazioni dettagliate, vedere [Espandere un cloud privato semplice](expand-private-cloud.md)per il cloud.

**Cosa succede al mio cloud privato durante la manutenzione?**

CloudSimple fornisce la notifica diversi giorni prima di un intervallo di manutenzione pianificato.  La manutenzione viene eseguita in modo non dirompente per garantire la disponibilità del tuo cloud privato.  La manutenzione può essere dei seguenti tipi:

* **Infrastruttura CloudSimple**.  L'infrastruttura CloudSimple è progettata per essere altamente disponibile.  Durante questo tipo di intervallo di manutenzione, i componenti ridondanti vengono aggiornati uno alla volta per evitare interruzioni del servizio. Si mantiene l'accesso al cloud privato vCenter, a tutte le macchine virtuali, alla connessione Internet dal cloud privato e alle connessioni a locale o Azure.You maintain access to your Private Cloud vCenter, all virtual machines, the internet connection from your Private Cloud, and connections to on-premises or Azure.
* **Portale CloudSimple**. Durante questo tipo di intervallo di manutenzione, alcune funzionalità del portale CloudSimple potrebbero essere disabilitate o inaccessibili.  La notifica prima dell'intervallo di manutenzione include dettagli sulle limitazioni delle funzionalità durante la manutenzione.

## <a name="connectivity"></a>Connettività

**Quali sono le opzioni di connettività per la rete di aree CloudSimple?**

CloudSimple offre le seguenti opzioni di connettività per connettersi alla rete di aree CloudSimple. È possibile utilizzare più opzioni contemporaneamente.

* **Connessione ExpressRoute dal data center locale alla rete di**aree CloudSimple . Si tratta di una connessione privata sicura ad alta velocità, a bassa latenza e con un'ora di tempo che usa Global Reach per collegare il circuito ExpressRoute locale al circuito CloudSimple ExpressRoute.This is a high-speed, low-tency, secure private connection that uses Global Reach to bridge your on-premises ExpressRoute circuit to your CloudSimple ExpressRoute circuit. Per istruzioni sulla configurazione della connessione, vedere [Connettersi da locale a CloudSimple tramite ExpressRoute.](on-premises-connection.md)
* **Connessione ExpressRoute dalla rete virtuale di Azure alla rete di aree CloudSimple**. Si tratta di una connessione privata sicura ad alta velocità, a bassa latenza e sicura che usa gateway di rete virtuale per collegare la rete virtuale in Azure al circuito CloudSimple ExpressRoute.This is a high-speed, low-tency, secure private connection that uses virtual network gateways to bridge your virtual network on Azure to your CloudSimple ExpressRoute circuit. Per istruzioni sulla configurazione della connessione, vedere [Connettere l'ambiente CloudSimple Private Cloud alla rete virtuale di Azure tramite ExpressRoute.For](azure-expressroute-connection.md)instructions on setting up the connection, see Connect your CloudSimple Private Cloud environment to the Azure virtual network using ExpressRoute .
* Connessione VPN da **sito a sito dal data center locale alla rete di aree CloudSimple**. Si tratta di una rete privata virtuale sicura dal dispositivo VPN locale all'area CloudSimple Private Cloud.  Per informazioni dettagliate, vedere [Configurare gateway VPN su rete CloudSimple](vpn-gateway.md).

**Come posso connettermi a un cloud privato?**

È possibile visualizzare i dettagli del cloud privato nel portale CloudSimple. Per connettersi al vCenter corrispondente al cloud privato, verificare innanzitutto che venga stabilita una connessione di rete tramite VPN da sito a sito, VPN da punto a sito o ExpressRoute. Avviare quindi il portale CloudSimple dal portale di Azure e fare clic su **Avvia client vSphere** nella home page o nella pagina dei dettagli del cloud privato.

**Qual è il vantaggio dei circuiti ExpressRoute?**

Un circuito Azure ExpressRoute è una connessione sicura ad alta velocità, a bassa latenza.  CloudSimple fornisce un circuito ExpressRoute dedicato per ogni area per cliente.  Usando questo circuito, è possibile stabilire una connessione sicura da locale o dalla sottoscrizione di Azure.Using this circuit, you can establish a secure connection from on-premises or your Azure subscription.

**Quali sono i costi di rete per connettersi a CloudSimple?  Vengono applicati eventuali addebiti in uscita tra CloudSimple e Azure o tra aree?**

Non è previsto alcun costo CloudSimple per l'uscita dalla rete.  Le tariffe standard di Azure si applicano a qualsiasi traffico in uscita dalla rete virtuale o dal circuito ExpressRoute locale.

## <a name="networking"></a>Rete

**Quali funzionalità di rete sono disponibili per il cloud privato?**

È possibile eseguire il provisioning di VLAN (e delle relative subnet) e delle tabelle del firewall e assegnare indirizzi IP pubblici mappati a una macchina virtuale in esecuzione nel cloud privato. Per informazioni dettagliate sulle funzionalità di rete, vedere Panoramica delle [VLAN e delle subnet](cloudsimple-vlans-subnets.md), [Panoramica](cloudsimple-firewall-tables.md)delle tabelle firewall e [Cenni](cloudsimple-public-ip-address.md)preliminari sugli indirizzi IP pubblici .

**Come si configurano subnet diverse per le applicazioni nel cloud privato?**

Le VLAN vengono create nel cloud privato dal portale CloudSimple.  Dopo aver creato una VLAN, è possibile creare un gruppo di porte distribuite nel vCenter del cloud privato usando la VLAN e creare macchine virtuali connesse al gruppo di porte distribuite.  È possibile abilitare le tabelle del firewall per la VLAN/subnet e definire le regole del firewall per proteggere il traffico di rete.

**Quali impostazioni del firewall sono disponibili per i cloud privati?**

È possibile configurare le regole per il traffico nord-sud e est-ovest.  Le regole sono definite in una tabella del firewall.  La tabella del firewall può essere collegata alle VLAN sul cloud privato.  Per informazioni dettagliate, consultate [Configurare tabelle e regole del firewall per i cloud privati.](firewall.md)

**È possibile assegnare indirizzi IP pubblici alle macchine virtuali nell'ambiente Cloud privato?**

Nel portale CloudSimple è possibile allocare un nuovo indirizzo IP pubblico e associarlo all'indirizzo IP privato di una macchina virtuale o di un'appliance.  È inoltre possibile creare nuove regole del firewall o applicare regole firewall esistenti per consentire il traffico da porte e indirizzi IP specifici nel portale. Per informazioni dettagliate, vedere [Allocare indirizzi IP pubblici per l'ambiente Private Cloud.](public-ips.md)

## <a name="security"></a>Security

**Quali sono le mie opzioni di sicurezza su CloudSimple?**

CloudSimple offre le seguenti funzionalità di sicurezza per la protezione dell'ambiente Cloud privato:

* **Dati a riposo di crittografia**. È possibile crittografare i dati inattivi che risiedano nell'archivio vSAN nel cloud privato. vSAN supporta server di gestione delle chiavi esterni, che possono essere distribuiti nella rete virtuale di Azure o nell'ambiente locale.  Per informazioni dettagliate, vedere Configurare la [crittografia vSAN per il](vsan-encryption.md)cloud privato CloudSimple .
* **Sicurezza di rete**. Controlla il flusso del traffico di rete con le regole del firewall che si applicano tra il cloud privato e Internet, il cloud privato e l'ambiente locale o all'interno di subnet del cloud privato.
* **Connessione sicura e privata**. Viene stabilita una connessione privata sicura tra la rete locale e la sottoscrizione di Azure.A secure, private connection is established between your on-premises network and your Azure subscription.

## <a name="compute"></a>Calcolo

**Che tipo di host sono disponibili?**

CloudSimple offre i seguenti tipi di host:

* **Nodo CS28:** CPU:2x 2,2 GHz, totale 28 core, 48 HT.  RAM: 256 GB.  Memoria: 1600 GB di cache NVMe, 5760 GB di dati (All-Flash). Rete: 4x25Gbe NIC
* **Nodo CS36:** CPU 2x 2x.3 GHz, totale 36 core, 72 HT.  RAM: 512 GB.  Memoria: 3200 GB NVMe cache 11520 GB di dati (All-Flash).  Rete: 4x25Gbe NIC
* **Nodo CS36m:** CPU 2x 2x.3 GHz, totale 36 core, 72 HT.  RAM: 576 GB.  Memoria: 3200 GB NVMe cache 13360 GB di dati (All-Flash).  Rete: 4x25Gbe NIC

**Come vengono gestiti gli errori hardware?**

Tutta l'infrastruttura CloudSimple viene continuamente monitorata dalla piattaforma CloudSimple e dai nostri team operativi di servizio.  Se viene rilevato un errore hardware, viene aggiunto un nuovo nodo al cloud privato e il nodo non riuscito viene rimosso.

## <a name="storage"></a>Archiviazione

**Che tipo di archiviazione è supportato in un cloud privato?**

CloudSimple offre storage vSAN VMware all-flash con ogni cloud privato.  Ogni vSphere viene creato con il proprio archivio dati vSAN.  Per informazioni dettagliate, vedere [Componenti di Private Cloud VMware - archiviazione vSAN](vmware-components.md#vsan-storage).

**La crittografia dei dati è supportata?**
Sì.  È possibile configurare l'archiviazione vSAN nel cloud privato per usare un server di gestione delle chiavi (KMS) distribuito in locale o in Azure per crittografare i dati archiviati in vSAN.

**Come vengono gestiti i dischi con errori?**

CloudSimple monitora continuamente tutti i componenti hardware del cloud privato.  Se viene rilevato un errore del disco o un disco viene identificato come inerrore (in base all'euristica), viene aggiunto automaticamente un nuovo nodo al cloud privato.  Il nodo con il disco con errori o errori viene rimosso dal cloud privato.

## <a name="vmware"></a>VMware

**Come si esegue il caricamento o la migrazione su larga scala di applicazioni e dati da locale?**

CloudSimple fornisce una soluzione VMware vSphere nativa.  Tutti gli strumenti VMware per la migrazione di massa dei dati possono essere utilizzati con il cloud privato.  Le opzioni includono:

* VMware HCX per la migrazione in blocco dei dati.
* Migrazione a freddo dei dati tramite Storage vMotion da locale a CloudSimple.

**Posso installare qualsiasi strumento VMware?**

CloudSimple fornisce una soluzione VMware vSphere nativa.  Tutti gli strumenti VMware utilizzati per la gestione dell'ambiente vSphere locale possono essere utilizzati in CloudSimple.  CloudSimple supporta un modello BYOL (Bring Your Own License) per l'installazione degli strumenti VMware.

**Come vengono gestiti gli aggiornamenti e gli aggiornamenti?**

CloudSimple gestisce e aggiorna tutti i componenti dell'infrastruttura del tuo cloud privato in modo senza interruzioni.  Tutti gli aggiornamenti e le patch di sicurezza rilasciati da VMware o dai fornitori di infrastrutture sono pianificati per l'aggiornamento non appena vengono qualificati da CloudSimple.

CloudSimple non esegue aggiornamenti o aggiornamenti delle applicazioni installate nel cloud privato.

## <a name="azure-integration"></a>Integrazione con Azure

**Quali servizi di Azure sono supportati?**

CloudSimple provides an Azure ExpressRoute connection to your subscription on Azure.  Tutti i servizi in esecuzione nell'abbonamento possono connettersi al cloud privato.  Tra gli esempi sono inclusi:

* **Azure Active Directory** come origine di identità per il vCenter CloudSimple.Azure Active Directory as an identity source for your CloudSimple vCenter.
* **Archiviazione di Azure** per l'archiviazione di backup, immagini e altri dati dal cloud privato.
* **Applicazioni ibride** con un'architettura di applicazioni che si estende su cloud pubblici e privati.  Ad esempio, è possibile creare server Web in Azure che accedono ai server applicazioni e database nel cloud privato.
* **Monitoraggio di Azure** e **centro sicurezza di Azure** per i carichi di lavoro in esecuzione su VMware supportano la registrazione, le metriche delle prestazioni e la gestione della sicurezza.

**Come si esegue il mapping dei tenant VMware ad Azure?**

CloudSimple offre la possibilità unica di gestire le macchine virtuali VMware nel cloud privato dal portale di Azure.CloudSimple provides the unique ability to manage your VMware VMs on Private Cloud from the Azure portal.  Un pool di risorse vCenter configurato con i vincoli di risorse desiderati può essere mappato alla sottoscrizione dall'amministratore globale.  

**Quali vantaggi relativi alle licenze si ottengono con Azure?**

Con CloudSimple, puoi sfruttare i vantaggi dell'utilizzo ibrido di Azure e risparmiare fino al 90% sulle licenze. Questo vantaggio preserva l'investimento in licenze Microsoft e riduce il tCO rispetto ad altre soluzioni cloud. Si ottengono inoltre gli aggiornamenti della protezione estesa per Windows Server 2008 e Microsoft SQL Server 2008.  Il modello BYOL (Bring Your Own License) ti aiuta a mantenere bassi i costi per le app comuni, come Veeam e .  
