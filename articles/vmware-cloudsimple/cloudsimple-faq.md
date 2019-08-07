---
title: 'Domande frequenti: soluzione VMware di CloudSimple'
description: Domande frequenti sulla soluzione VMware di Azure di CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816236"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Domande frequenti sulla soluzione VMware di CloudSimple

Domande frequenti e risposte sulla soluzione VMware di Azure di CloudSimple che consentono di comprendere il servizio e come usarlo. Le domande e le risposte sono suddivise nelle categorie seguenti:

* Servizio CloudSimple
* Connettività
* Rete
* Security
* Calcolo
* Archiviazione
* VMware
* Integrazione di Azure
 
## <a name="cloudsimple-service"></a>Servizio CloudSimple

**Che cos'è la soluzione VMware di Azure di CloudSimple?**

Soluzione VMware di Azure di CloudSimple trasforma ed estende i carichi di lavoro VMware in cloud privati e dedicati in Azure in pochi minuti. La soluzione effettua il provisioning, gestisce l'infrastruttura e orchestra i carichi di lavoro tra l'ambiente locale e Azure. Poiché le tue app vengono eseguite esattamente in locale e in Azure, puoi trarre vantaggio dall'elasticità e dai servizi del cloud senza la complessità di riprogettare le tue app. CloudSimple riduce il costo totale di proprietà con un modello di consumo cloud che fornisce il provisioning su richiesta, il pagamento in base al consumo e l'ottimizzazione della capacità. Per funzionalità, vantaggi e scenari, vedere [che cos'è la soluzione VMware di Azure di CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Che cos'è un cloud privato CloudSimple?**

Si effettua il provisioning di un cloud privato e dedicato costituito da un ambiente di calcolo, archiviazione e rete a prestazioni elevate distribuito in Microsoft Azure infrastruttura (spazio per hardware e Data Center) in località di Azure. Il cloud privato offre una piattaforma VMware nativa come servizio. In termini di VMware, ogni cloud privato contiene esattamente un'istanza del server vCenter. Il server vCenter gestisce più nodi ESXi contenuti in uno o più cluster vSphere, insieme all'archiviazione rete VSAN corrispondente. Un servizio CloudSimple può contenere più cloud privati nella sottoscrizione di Azure. Per ulteriori informazioni sui cloud privati, vedere [Panoramica del cloud privato](cloudsimple-private-cloud.md).

**Dove è disponibile il servizio CloudSimple?**

CloudSimple è disponibile nelle aree Stati Uniti orientali e Stati Uniti occidentali.

**Ricerca per categorie abilitare la sottoscrizione per CloudSimple?**

Contattare il rappresentante del account Microsoft [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) in per abilitare la sottoscrizione per il servizio CloudSimple. Fornire l'ID sottoscrizione nel messaggio di posta elettronica per cui si vuole abilitare il servizio CloudSimple. 

**Ricerca per categorie accedere al portale di CloudSimple?**

È possibile accedere al portale di CloudSimple dalla portale di Azure. Per informazioni su come accedere al portale di CloudSimple, vedere [accedere al portale della soluzione VMware tramite CloudSimple dal portale di Azure](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Ricerca per categorie aumentare la capacità di un cloud privato?**

È possibile eseguire il provisioning dei nodi dal portale di Azure ed espandere il cloud privato dal portale di CloudSimple. Per espandere il cloud privato, è possibile aggiungere nodi a un cluster vSphere esistente o creare un nuovo cluster vSphere. Per informazioni sulla procedura, vedere [espandere un cloud privato CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Cosa accade al cloud privato durante la manutenzione?**

CloudSimple fornisce le notifiche periodiche giorni prima della manutenzione pianificata. La manutenzione viene eseguita in modo non distruttivo per garantire la disponibilità del cloud privato. La manutenzione può essere dei seguenti tipi:

- **Infrastruttura CloudSimple**: L'infrastruttura CloudSimple è progettata per essere a disponibilità elevata. Durante la manutenzione, la connettività e la disponibilità del cloud privato sono sicure aggiornando i componenti ridondanti uno alla volta senza alcun effetto. Si ha accesso al cloud privato vCenter, a tutte le macchine virtuali, alla connessione Internet dal cloud privato e alle connessioni a locale o Azure.
- **Portale di CloudSimple**: Durante la manutenzione, è possibile che alcune funzionalità nel portale di CloudSimple non siano accessibili o siano disabilitate. La notifica di manutenzione include informazioni sulle operazioni che è possibile eseguire nel portale.

## <a name="connectivity"></a>Connettività

**Quali sono le opzioni di connettività alla rete dell'area CloudSimple?**

CloudSimple offre tre diverse opzioni di connettività per la connessione alla rete dell'area CloudSimple. Tutte e tre le opzioni possono essere utilizzate insieme:

- Connessione ExpressRoute di Azure dal Data Center locale alla rete dell'area CloudSimple: Una connessione privata sicura a bassa latenza ad alta velocità che colma il circuito ExpressRoute locale con il circuito CloudSimple ExpressRoute usando Copertura globale. Per configurare la connessione, vedere [connettersi da locale a CloudSimple usando ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- Connessione ExpressRoute dalla rete virtuale di Azure alla rete dell'area CloudSimple: Una connessione privata sicura a bassa latenza e ad alta velocità che colma la rete virtuale in Azure con il circuito CloudSimple ExpressRoute usando i gateway di rete virtuale. Per configurare la connessione, vedere [connettere l'ambiente cloud privato CloudSimple alla rete virtuale di Azure usando ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Connessione VPN da sito a sito dal Data Center locale alla rete dell'area di CloudSimple: Una rete privata virtuale sicura dal dispositivo VPN locale all'area del cloud privato CloudSimple. Per configurare la connessione, vedere [configurare una connessione VPN tra la rete locale e il cloud privato CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Ricerca per categorie connettersi a un cloud privato?**

È possibile visualizzare i dettagli del cloud privato nel portale di CloudSimple. Per connettersi a vCenter che corrisponde al cloud privato, assicurarsi che venga stabilita una connessione di rete usando da sito a sito, da punto a sito o ExpressRoute. Quindi, avviare il portale di CloudSimple dal portale di Azure. Selezionare **Launch vSphere client** nella Home page o nella pagina dei dettagli del cloud privato.

**Qual è il vantaggio di un circuito ExpressRoute?**

Un circuito ExpressRoute di Azure offre una connessione protetta ad alta velocità e bassa latenza. CloudSimple fornisce un circuito ExpressRoute dedicato per area per cliente. Con questo circuito è possibile stabilire una connessione sicura da locale e dalla sottoscrizione di Azure.

**Quali sono i costi di rete per connettersi e da CloudSimple? Sono previsti addebiti in uscita da e verso CloudSimple in Azure? Sono previsti addebiti in uscita tra le aree?**

Non è previsto alcun addebito per la rete in uscita. Le tariffe standard di Azure si applicano a qualsiasi traffico in uscita dalla rete virtuale o da un circuito ExpressRoute locale.

## <a name="networking"></a>Rete

**Quali funzionalità di rete sono disponibili per il cloud privato?**

È possibile eseguire il provisioning di VLAN e delle rispettive subnet e tabelle del firewall. È possibile assegnare indirizzi IP pubblici ed eseguire il mapping a una macchina virtuale in esecuzione nel cloud privato. Per altre informazioni, vedere Panoramica di [VLAN e subnet](cloudsimple-vlans-subnets.md), panoramica delle [tabelle del firewall](cloudsimple-firewall-tables.md)e [Panoramica degli indirizzi IP pubblici](cloudsimple-public-ip-address.md).

**Ricerca per categorie configurare subnet diverse per le applicazioni nel cloud privato?**

È possibile creare VLAN nel cloud privato dal portale di CloudSimple. Dopo aver creato la VLAN, è possibile creare un gruppo di porte distribuite nel cloud privato vCenter usando la VLAN e creare macchine virtuali connesse al gruppo di porte distribuite. È possibile abilitare una tabella del firewall per la VLAN o la subnet e definire le regole del firewall per proteggere il traffico di rete.

**Quali impostazioni del firewall sono disponibili per i cloud privati?**

È possibile configurare le regole per il traffico nord-sud e est-ovest. Le regole sono definite in una tabella del firewall. La tabella del firewall può essere collegata alle VLAN nel cloud privato. Per la procedura di installazione, vedere [configurare le regole e le tabelle del firewall per i cloud privati](https://docs.azure.cloudsimple.com/firewall).

**È possibile assegnare indirizzi IP pubblici per le macchine virtuali nell'ambiente cloud privato?**

Nel portale di CloudSimple è possibile allocare facilmente un nuovo indirizzo IP pubblico e associarlo a un indirizzo IP privato della macchina virtuale o di un'appliance. È anche possibile creare nuove regole del firewall o applicare regole del firewall esistenti per consentire il traffico da porte specifiche e set specifici di indirizzi IP nel portale. Per la procedura di installazione, vedere [allocare indirizzi IP pubblici per un ambiente cloud privato](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Security

**Quali sono le opzioni di sicurezza in CloudSimple?**

Un cloud privato CloudSimple offre le seguenti funzionalità di sicurezza per la protezione dell'ambiente cloud privato:

- **Crittografia dei dati inattivi:** È possibile crittografare i dati inattivi che si trovano nell'archiviazione rete VSAN nel cloud privato. Rete VSAN supporta un server di gestione delle chiavi esterno, che può essere distribuito nella rete virtuale di Azure o nell'ambiente locale. Per altre informazioni, vedere [configurare la crittografia rete VSAN per il cloud privato CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Sicurezza di rete:** Controllare il flusso del traffico di rete da e verso il cloud privato da Internet, in locale e all'interno delle subnet del cloud privato usando le regole del firewall.
- **Connessione privata sicura:** Connessione privata sicura tra la rete locale e la sottoscrizione di Azure.

## <a name="compute"></a>Calcolo

**Quali tipi di host sono disponibili?**

CloudSimple offre due tipi di host:

* **Nodo CS28**: CPU: 2x 2,2 GHz, totale 28 core, 48 HT. RAM: 256 GB. Archiviazione: 1600-GB di cache NVMe, 5760-GB di dati (All-Flash). Rete: SCHEDA di interfaccia di rete 2x25Gbe.
* **Nodo CS36**: CPU 2x 2,3 GHz, totale 36 core, 72 HT. RAM: 512 GB. Archiviazione: 3200-GB NVMe cache 11.520-GB dati (All-Flash). Rete: SCHEDA di interfaccia di rete 2x25Gbe.

**Come vengono gestiti gli errori hardware?**

Tutta l'infrastruttura CloudSimple viene monitorata continuamente dalla piattaforma CloudSimple e dai team operativi dei servizi. Se viene rilevato un errore hardware, viene aggiunto un nuovo nodo al cloud privato. Il nodo che ha avuto esito negativo viene rimosso per garantire la disponibilità elevata del cloud privato.

## <a name="storage"></a>Archiviazione

**Quali tipi di archiviazione sono supportati in un cloud privato?**

CloudSimple offre **archiviazione rete VSAN VMware All-Flash** con ogni cloud privato. Ogni vSphere viene creato con il proprio archivio dati rete VSAN. Per altre informazioni, vedere [componenti VMware del cloud privato-archiviazione rete VSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**La crittografia dei dati è supportata?**
Sì. È possibile configurare l'archiviazione rete VSAN nel cloud privato per usare un server di gestione delle chiavi (KMS), che viene distribuito in locale o in Azure per la crittografia dei dati archiviati in rete VSAN.

**Come vengono gestiti i dischi con errori?**

Il monitoraggio di CloudSimple monitora continuamente tutti i componenti hardware del cloud privato. Se viene rilevato un errore del disco o un disco viene identificato come non riuscito in base all'euristica, viene aggiunto automaticamente un nuovo nodo al cloud privato. Il nodo con un disco guasto o guasto viene rimosso dal cloud privato.

## <a name="vmware"></a>VMware

**Ricerca per categorie eseguire il caricamento e la migrazione su larga scala delle applicazioni e dei dati dall'ambiente locale?**

CloudSimple fornisce una soluzione VMware vSphere nativa. Qualsiasi strumento usato per la migrazione dei dati in blocco può essere usato con un cloud privato CloudSimple. Di seguito sono riportate alcune delle opzioni disponibili:

- VMware HCX per la migrazione in blocco dei dati.
- Migrazione a freddo dei dati tramite Storage vMotion da locale a CloudSimple.

**È possibile installare qualsiasi strumento VMware?**

CloudSimple fornisce una soluzione VMware vSphere nativa. Qualsiasi strumento usato per gestire un ambiente vSphere locale può essere usato in CloudSimple. CloudSimple supporta un modello BYOL (Bring your own License) per l'installazione degli strumenti VMware.

**Come vengono gestiti gli aggiornamenti e gli aggiornamenti?**

CloudSimple gestisce e aggiorna tutti i componenti dell'infrastruttura del cloud privato in modo semplice e senza interruzioni. Eventuali aggiornamenti o patch di protezione rilasciati da VMware o dai fornitori di infrastrutture sono pianificati per l'aggiornamento non appena sono qualificati da CloudSimple.

CloudSimple non esegue aggiornamenti o aggiornamenti delle applicazioni installate nel cloud privato.

## <a name="azure-integration"></a>Integrazione di Azure

**Quali servizi di Azure sono supportati?**

CloudSimple fornisce la connessione ExpressRoute di Azure alla sottoscrizione in Azure. Tutti i servizi in esecuzione nella sottoscrizione hanno la connettività di rete al cloud privato e possono connettersi al cloud privato. Esempi:

- **Azure Active Directory**: Usare Azure Active Directory come origine di identità per CloudSimple vCenter.
- **Archiviazione di Azure**: Usare l'archiviazione per archiviare i backup, le immagini e altri dati dal cloud privato.
- **Applicazioni ibride**: È possibile creare un'architettura dell'applicazione che si estende su cloud pubblici e privati. Ad esempio, è possibile creare server Web in Azure che accedono a server applicazioni e database in un cloud privato CloudSimple.
- **Monitoraggio di Azure** e **Centro sicurezza di Azure**: Il carico di lavoro in esecuzione su VMware può usare il Centro sicurezza e monitoraggio per la registrazione, le metriche delle prestazioni e la gestione della sicurezza.

**Ricerca per categorie mappare i tenant VMware ad Azure?**

CloudSimple offre la possibilità di gestire le macchine virtuali VMware in un cloud privato dal portale di Azure. Un pool di risorse vCenter configurato con i vincoli di risorse desiderati può essere mappato alla sottoscrizione dall'amministratore globale. 

**Quali vantaggi di licenza si ottengono con Azure?**

Con CloudSimple è possibile sfruttare i vantaggi del Vantaggio Azure Hybrid e risparmiare fino al 90% sulle licenze per conservare l'investimento in licenze Microsoft e ridurre il costo totale di proprietà rispetto ad altri cloud. Si ottengono anche aggiornamenti della sicurezza estesi per Windows Server 2008 e Microsoft SQL Server 2008. Mantieni i tuoi costi bassi con BYOL nel cloud per app comuni come Veeam, Zerto e altri. 
