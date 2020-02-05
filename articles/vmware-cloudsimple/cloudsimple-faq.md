---
title: Domande frequenti-soluzioni VMware di Azure (AVS)
description: Domande frequenti per le soluzioni VMware di Azure (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025062"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>Domande frequenti sulla soluzione VMware da AVS

## <a name="avs-service"></a>Servizio AVS

**Informazioni sulle soluzioni VMware (AVS) di Azure**

Azure VMware Solutions (AVS) trasforma ed estende i carichi di lavoro VMware in cloud privati e dedicati in Azure in pochi minuti. AVS si occupa del provisioning, della gestione dell'infrastruttura e dell'orchestrazione dei carichi di lavoro tra l'ambiente locale e Azure. Poiché le tue app vengono eseguite esattamente in locale e in Azure, puoi trarre vantaggio dall'elasticità e dai servizi del cloud senza la complessità di riprogettare le tue app. AVS riduce il costo totale di proprietà con un modello di consumo cloud che fornisce il provisioning su richiesta, il pagamento in base al consumo e l'ottimizzazione della capacità. Per informazioni su funzionalità, vantaggi e scenari, vedere [che cos'è la soluzione VMware in Azure](cloudsimple-vmware-solutions-overview.md) .

**Che cos'è un cloud privato AVS?**

Un cloud privato AVS è un cloud privato e dedicato costituito da un ambiente di calcolo, archiviazione e rete a prestazioni elevate distribuito nell'infrastruttura Microsoft Azure (spazio hardware e Data Center) in località di Azure. Un cloud privato AVS fornisce una piattaforma distribuita come servizio nativa di VMware. In termini di VMware ogni cloud privato AVS contiene esattamente un'istanza del server vCenter. Il server vCenter gestisce più nodi ESXi contenuti in uno o più cluster vSphere, insieme alla relativa archiviazione SAN virtuale (rete VSAN). Un servizio AVS può contenere più cloud privati AVS nella sottoscrizione di Azure. Per altri dettagli, vedere [Panoramica di AVS private cloud](cloudsimple-private-cloud.md).

**Dove è disponibile il servizio AVS?**

AVS è disponibile nelle aree Stati Uniti orientali, Stati Uniti occidentali ed Europa occidentale con aree aggiuntive presto disponibili.

**Ricerca per categorie abilitare la sottoscrizione per AVS?**

È possibile contattare il rappresentante del account Microsoft presso [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) per abilitare la sottoscrizione per il servizio AVS. Fornire l'ID sottoscrizione nel messaggio di posta elettronica per il quale si vuole abilitare AVS Service. 

**Ricerca per categorie accedere al portale AVS?**

È possibile accedere al portale AVS dalla portale di Azure. Per informazioni dettagliate, vedere [accedere al portale di soluzioni VMware (AVS) dalla portale di Azure](access-cloudsimple-portal.md).

**Ricerca per categorie aumentare la capacità per un cloud privato AVS?**

Per aumentare la capacità, acquistare nodi aggiuntivi dalla portale di Azure e quindi usare i nodi per espandere il cloud privato AVS dal portale AVS. È possibile aggiungere altri nodi a un cluster vSphere esistente o aggiungerli a un nuovo cluster vSphere. Per informazioni dettagliate, vedere [expand an AVS private cloud](expand-private-cloud.md).

**Cosa accade al cloud privato AVS durante la manutenzione?**

AVS fornisce notifiche diversi giorni prima di un intervallo di manutenzione pianificata. La manutenzione viene eseguita in modo non distruttivo per garantire la disponibilità del cloud privato AVS. La manutenzione può essere dei seguenti tipi:

* **Infrastruttura AVS**. L'infrastruttura AVS è progettata per essere a disponibilità elevata. Durante questo tipo di intervallo di manutenzione, i componenti ridondanti vengono aggiornati uno alla volta per evitare interruzioni del servizio. È possibile mantenere l'accesso a AVS private cloud vCenter, a tutte le macchine virtuali, alla connessione Internet dal cloud privato AVS e alle connessioni a locali o Azure.
* **Portale AVS**. Durante questo tipo di intervallo di manutenzione, alcune funzionalità del portale AVS potrebbero essere disabilitate o inaccessibili. La notifica prima dell'intervallo di manutenzione include i dettagli sulle limitazioni delle funzionalità durante la manutenzione.

## <a name="connectivity"></a>Connettività

**Quali sono le opzioni di connettività per la rete AVS Region?**

AVS fornisce le opzioni di connettività seguenti per la connessione alla rete dell'area AVS. È possibile utilizzare contemporaneamente più opzioni.

* **Connessione ExpressRoute dal Data Center locale alla rete dell'area AVS**. Si tratta di una connessione privata sicura a bassa latenza e ad alta velocità che usa Copertura globale per collegare il circuito ExpressRoute locale al circuito AVS ExpressRoute. Per istruzioni sull'impostazione della connessione, vedere [connettersi da locale a AVS usando ExpressRoute](on-premises-connection.md).
* **Connessione ExpressRoute dalla rete virtuale di Azure alla rete dell'area AVS**. Si tratta di una connessione privata sicura a bassa latenza e ad alta velocità che usa i gateway di rete virtuale per collegare la rete virtuale in Azure al circuito AVS ExpressRoute. Per istruzioni sull'impostazione della connessione, vedere [connettere l'ambiente del cloud privato AVS alla rete virtuale di Azure tramite ExpressRoute](azure-expressroute-connection.md).
* **Connessione VPN da sito a sito dal Data Center locale alla rete dell'area AVS**. Si tratta di una rete privata virtuale sicura dal dispositivo VPN locale all'area del cloud privato AVS. Per informazioni dettagliate, vedere [configurare i gateway VPN nella rete AVS](vpn-gateway.md).

**Ricerca per categorie connettersi a un cloud privato AVS?**

È possibile visualizzare i dettagli del cloud privato AVS nel portale AVS. Per connettersi a vCenter che corrisponde al cloud privato AVS, verificare prima di tutto che venga stabilita una connessione di rete usando una VPN da sito a sito, una VPN da punto a sito o ExpressRoute. Avviare quindi il portale AVS dalla portale di Azure e fare clic su **Launch vSphere client** nella Home page o nella pagina dei dettagli AVS private cloud.

**Qual è il vantaggio dei circuiti ExpressRoute?**

Un circuito ExpressRoute di Azure è una connessione sicura a bassa latenza e ad alta velocità. AVS fornisce un circuito ExpressRoute dedicato per area per cliente. Con questo circuito è possibile stabilire una connessione sicura da locale o dalla sottoscrizione di Azure.

**Quali sono i costi di rete per la connessione ad AVS? Eventuali addebiti in uscita sono applicabili tra AVS e Azure o tra aree?**

Non sono previsti costi di AVS per l'uscita dalla rete. Le tariffe standard di Azure si applicano a qualsiasi traffico in uscita dalla rete virtuale o dal circuito ExpressRoute locale.

## <a name="networking"></a>Rete

**Quali funzionalità di rete sono disponibili per il cloud privato AVS?**

È possibile eseguire il provisioning delle VLAN (e delle rispettive subnet) e delle tabelle del firewall e assegnare indirizzi IP pubblici con mapping a una macchina virtuale in esecuzione nel cloud privato AVS. Per informazioni dettagliate sulle funzionalità di rete, vedere Panoramica di [VLAN e subnet](cloudsimple-vlans-subnets.md), [Panoramica delle tabelle del firewall](cloudsimple-firewall-tables.md)e panoramica degli [indirizzi IP pubblici](cloudsimple-public-ip-address.md).

**Ricerca per categorie configurare subnet diverse per le applicazioni nel cloud privato AVS?**

Le VLAN vengono create nel cloud privato AVS dal portale AVS. Dopo aver creato una VLAN, è possibile creare un gruppo di porte distribuite nel cloud privato AVS con la VLAN e creare macchine virtuali connesse al gruppo di porte distribuite. È possibile abilitare le tabelle del firewall per la VLAN/subnet e definire le regole del firewall per proteggere il traffico di rete.

**Quali impostazioni del firewall sono disponibili per i cloud privati AVS?**

È possibile configurare le regole per il traffico nord-sud e est-ovest. Le regole sono definite in una tabella del firewall. La tabella del firewall può essere collegata alle VLAN nel cloud privato AVS. Per informazioni dettagliate, vedere [configurare le regole e le tabelle del firewall per i cloud privati AVS](firewall.md).

**È possibile assegnare indirizzi IP pubblici alle macchine virtuali nell'ambiente di cloud privato AVS?**

Nel portale di AVS è possibile allocare un nuovo indirizzo IP pubblico e associarlo all'indirizzo IP privato di una macchina virtuale o di un'appliance. È anche possibile creare nuove regole del firewall o applicare regole del firewall esistenti per consentire il traffico da porte e indirizzi IP specifici nel portale. Per informazioni dettagliate, vedere [allocare indirizzi IP pubblici per l'ambiente di cloud privato AVS](public-ips.md).

## <a name="security"></a>Sicurezza

**Quali sono le opzioni di sicurezza in AVS?**

AVS fornisce le seguenti funzionalità di sicurezza per la protezione dell'ambiente di cloud privato AVS:

* **Crittografia dei dati**inattivi. È possibile crittografare i dati inattivi presenti nella risorsa di archiviazione rete VSAN nel cloud privato AVS. Rete VSAN supporta i server di gestione delle chiavi esterne, che possono essere distribuiti nell'ambiente Azure vNet o in locale. Per informazioni dettagliate, vedere [configurare la crittografia rete VSAN per il cloud privato AVS](vsan-encryption.md).
* **Sicurezza di rete**. Controllare il flusso del traffico di rete con regole del firewall che si applicano tra il cloud privato AVS e Internet, il cloud privato AVS e l'ambiente locale o all'interno di subnet del cloud privato AVS.
* **Connessione privata sicura**. Viene stabilita una connessione privata sicura tra la rete locale e la sottoscrizione di Azure.

## <a name="compute"></a>Calcolo

**Quali tipi di host sono disponibili?**

AVS offre i tipi di host seguenti:

* **Nodo CS28:** CPU: 2x 2,2 GHz, totale 28 core, 48 HT.  RAM: 256 GB.  Archiviazione: 1600 GB di cache NVMe, 5760 GB di dati (tutti i flash). Rete: 4x25Gbe NIC
* **Nodo CS36:** CPU 2x 2,3 GHz, totale 36 core, 72 HT.  RAM: 512 GB.  Archiviazione: 3200 GB NVMe della cache 11520 GB di dati (tutti i flash).  Rete: 4x25Gbe NIC
* **Nodo CS36m:** CPU 2x 2,3 GHz, totale 36 core, 72 HT.  RAM: 576 GB.  Archiviazione: 3200 GB NVMe della cache 13360 GB di dati (tutti i flash).  Rete: 4x25Gbe NIC

**In che modo vengono gestiti gli errori hardware?**

Tutta l'infrastruttura AVS viene monitorata continuamente dalla piattaforma AVS e dai team operativi dei servizi. Se viene rilevato un errore hardware, viene aggiunto un nuovo nodo al cloud privato AVS e viene rimosso il nodo che ha avuto esito negativo.

## <a name="storage"></a>Archiviazione

**Quali tipi di archiviazione sono supportati in un cloud privato AVS?**

AVS offre archiviazione rete VSAN VMware All-Flash con ogni AVS private cloud. Ogni vSphere viene creato con il proprio archivio dati rete VSAN. Per informazioni dettagliate, vedere [AVS private cloud VMware Components-rete VSAN storage](vmware-components.md#vsan-storage).

**La crittografia dei dati è supportata?**
Sì. È possibile configurare l'archiviazione rete VSAN nel cloud privato AVS per usare un server di gestione delle chiavi (KMS) distribuito in locale o in Azure per crittografare i dati archiviati in rete VSAN.

**Come vengono gestiti i dischi con errori?**

AVS monitora continuamente tutti i componenti hardware del cloud privato AVS. Se viene rilevato un errore del disco o un disco viene identificato come non riuscito (in base all'euristica), viene automaticamente aggiunto un nuovo nodo al cloud privato AVS. Il nodo con il disco guasto o guasto viene rimosso dal cloud privato AVS.

## <a name="vmware"></a>VMware

**Ricerca per categorie eseguire il caricamento o la migrazione su larga scala delle applicazioni e dei dati dall'ambiente locale?**

AVS fornisce una soluzione VMware vSphere nativa. Tutti gli strumenti VMware per la migrazione dei dati in blocco possono essere usati con il cloud privato AVS. Le opzioni includono:

* VMware HCX per la migrazione in blocco dei dati.
* Migrazione a freddo dei dati tramite Storage vMotion da locale a AVS.

**È possibile installare qualsiasi strumento VMware?**

AVS fornisce una soluzione VMware vSphere nativa. Tutti gli strumenti VMware usati per la gestione dell'ambiente vSphere locale possono essere usati in AVS. AVS supporta un modello BYOL (Bring your own License) per l'installazione degli strumenti VMware.

**Come vengono gestiti gli aggiornamenti e gli aggiornamenti?**

AVS gestisce e aggiorna tutti i componenti dell'infrastruttura del cloud privato AVS in modo semplice e senza interruzioni. Tutti gli aggiornamenti e le patch di sicurezza rilasciati da VMware o dai fornitori di infrastrutture sono pianificati per l'aggiornamento non appena sono qualificati da AVS.

AVS non esegue aggiornamenti o aggiornamenti delle applicazioni installate nel cloud privato AVS.

## <a name="azure-integration"></a>Integrazione di Azure

**Quali servizi di Azure sono supportati?**

AVS fornisce una connessione di Azure ExpressRoute alla sottoscrizione in Azure. Tutti i servizi in esecuzione nella sottoscrizione possono connettersi al cloud privato AVS. Ad esempio:

* **Azure Active Directory** come origine di identità per AVS vCenter.
* **Archiviazione di Azure** per l'archiviazione di backup, immagini e altri dati dal cloud privato AVS.
* **Applicazioni ibride** con un'architettura di applicazione che si estende su cloud privati pubblici e AVS. Ad esempio, è possibile creare server Web in Azure che accedono a server applicazioni e database nel cloud privato AVS.
* **Monitoraggio di Azure** e il **Centro sicurezza di Azure** per i carichi di lavoro in esecuzione in VMware supportano la registrazione, le metriche delle prestazioni e la gestione della sicurezza.

**Ricerca per categorie mappare i tenant VMware ad Azure?**

AVS fornisce la capacità univoca di gestire le macchine virtuali VMware in AVS private cloud dal portale di Azure. È possibile eseguire il mapping di un pool di risorse vCenter configurato con vincoli di risorse desiderati alla sottoscrizione dall'amministratore globale. 

**Quali vantaggi di licenza si ottengono con Azure?**

Con AVS puoi sfruttare il vantaggio Azure Hybrid Usage e risparmiare fino al 90% sulle licenze. Questo vantaggio consente di mantenere l'investimento nelle licenze Microsoft e di ridurre il TCO rispetto ad altre soluzioni cloud. Si ottengono anche aggiornamenti della sicurezza estesi per Windows Server 2008 e Microsoft SQL Server 2008. Il modello Bring your own License (BYOL) consente di ridurre i costi per le app comuni, ad esempio Veeam e Zerto. 
