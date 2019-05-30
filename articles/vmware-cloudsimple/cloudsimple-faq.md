---
title: Domande frequenti - soluzione VMware CloudSimple
description: Domande frequenti per Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358529"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Domande frequenti sulla soluzione di VMware da CloudSimple

Domande frequenti e risposte sulla soluzione di VMware da CloudSimple che consentono di comprendano il servizio e come usarlo.  Le domande e risposte sono organizzate nelle categorie seguenti.

* CloudSimple Service
* Connettività
* Rete
* Security
* Calcolo
* Archiviazione
* VMware
* Integrazione di Azure
  
## <a name="cloudsimple-service"></a>CloudSimple Service

**Che cos'è la soluzione VMware da CloudSimple?**

**La soluzione VMware da CloudSimple** Trasforma ed estende i carichi di lavoro VMware in un cloud privato e dedicato in Azure in pochi minuti. Noi ci occupiamo di provisioning, la gestione dell'infrastruttura e orchestrare i carichi di lavoro tra origini locali e Azure. Poiché le app eseguite esattamente la stessa in locale e in Azure, è trarre vantaggio dall'elasticità e servizi del cloud senza la complessità di riarchitettare le tue app. CloudSimple riduce il costo totale di proprietà con un modello di consumo cloud che offre on demand il provisioning, pagamento come-di-aumento delle dimensioni e ottimizzazione della capacità.  Visualizzare [qual è la soluzione VMware in Azure da CloudSimple](cloudsimple-vmware-solutions-overview.md) per funzionalità, vantaggi e gli scenari.

**Che cos'è un Cloud privato CloudSimple?**

Viene effettuato il provisioning di un cloud privato e dedicato composto di calcolo ad alte prestazioni, archiviazione e ambiente distribuito nell'infrastruttura di Microsoft Azure (spazio di hardware e Data Center) di rete nella località di Azure.  Cloud privato offre una piattaforma VMware nativa 'as-a-service'. In termini di VMware, ogni Cloud privato contiene esattamente un'istanza del Server vCenter. Il server vCenter Server gestisce più ESXi nodi contenuti in uno o più cluster vSphere, con l'archiviazione SAN virtuale (vSAN) corrispondente. Un CloudSimple Service può contenere più cloud privati nella sottoscrizione di Azure.  Per altre informazioni sui cloud privati, vedere [Panoramica di Cloud privato](cloudsimple-private-cloud.md).

**Dove si trova servizio CloudSimple disponibili?**

CloudSimple è disponibile nelle aree Stati Uniti orientali e Stati Uniti occidentali.

**Come si abilita la sottoscrizione per CloudSimple?**

È possibile contattare il rappresentante dell'account Microsoft al [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) per abilitare la sottoscrizione per il servizio CloudSimple. Fornire l'ID della sottoscrizione nel messaggio di posta elettronica per cui si desidera servizio CloudSimple abilitato.  

**Come si accede al portale CloudSimple?**

Portale CloudSimple è accedere dal portale di Azure.  Visualizzare [l'accesso a VMware soluzione dal portale CloudSimple dal portale di Azure](https://docs.azure.cloudsimple.com/access-cloudsimple-portal) articolo per informazioni dettagliate sull'accesso al portale CloudSimple. 

**Come si aumenta la capacità per un Cloud privato?**

Si acquisto nodi dal portale di Azure e si espande il Cloud privato da portale CloudSimple.  È possibile espandere del Cloud privato aggiungendo altri nodi al cluster vSphere esistente o creando nuovi cluster vSphere.  Visualizzare [espandere un Cloud privato CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud) articolo per la procedura.

**Cosa succede al mio Cloud privato durante la manutenzione?**

CloudSimple fornisce le notifiche periodiche giorni prima della manutenzione pianificata.  L'operazione di manutenzione viene eseguita in modo da garantire la disponibilità della Cloud privata senza interruzioni.  Manutenzione può essere dei tipi seguenti:

1. **Infrastruttura CloudSimple:**  Infrastruttura CloudSimple è progettato per garantire la disponibilità elevata.  Durante la manutenzione, connettività e la disponibilità della Cloud privata è garantita mediante l'aggiornamento di componenti ridondanti uno alla volta senza alcun impatto.  Si avrà accesso a vCenter del Cloud privato Microsoft, tutte le macchine virtuali, connessione a internet dal Cloud privato e le connessioni a un'istanza locale o in Azure.
2. **Portale CloudSimple:** Durante la manutenzione, alcune funzionalità sul portale CloudSimple potrebbero non essere accessibile o disabilitato.  Notifica di manutenzione includerà i dettagli di cosa può essere fatto nel portale.

## <a name="connectivity"></a>Connettività

**Quali sono le opzioni di connettività alla rete area CloudSimple?**

CloudSimple offre tre diverse opzioni di connettività per connettersi alla rete CloudSimple area.  Tutte e tre possono essere usati insieme.

1. Connessione ExpressRoute dal proprio data center locale ad CloudSimple area rete - connessione privata con una latenza bassa ad alta velocità sicura bridging il circuito ExpressRoute da sito locale con il circuito ExpressRoute di CloudSimple usando raggiungere globale. Visualizzare [Connect in locale per l'uso di ExpressRoute CloudSimple](https://docs.azure.cloudsimple.com/on-premises-connection) articolo per configurare la connessione.
2. Connessione ExpressRoute dalla rete virtuale di Azure alla rete area CloudSimple - connessione privata con una latenza bassa ad alta velocità sicura bridging della rete virtuale in Azure con il circuito CloudSimple ExpressRoute usando i gateway di rete virtuale.  Visualizzare [connettere l'ambiente Cloud privato CloudSimple a rete virtuale di Azure tramite ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection) articolo per configurare la connessione.
3. Connessione VPN Site-to-Site dal Data Center locale alla rete area CloudSimple - virtual private network protette dal dispositivo VPN locale per l'area del Cloud privato Microsoft CloudSimple.  [Configura connessione VPN tra la rete locale e Cloud privato CloudSimple] vedere l'articolo per la configurazione di connessione VPN.

**Come ci si connette a un Cloud privato?**

È possibile visualizzare i dettagli del Cloud privato nel portale di CloudSimple. Per connettersi al server vCenter corrispondente del Cloud privato, assicurarsi che rete connessione stabilita usando Site-to-Site, Point-to-Site o ExpressRoute. Quindi, avviare il portale CloudSimple dal portale di Azure fare clic su *avvio veloce di vSphere Client* nella Home page o nella pagina dei dettagli del Cloud privato.

**Che cos'è il vantaggio del circuito ExpressRoute?**

Circuito ExpressRoute fornisce una connessione sicura con latenza bassa ad alta velocità.  CloudSimple fornisce un circuito ExpressRoute dedicato per ogni area per ogni cliente.  Usa questo circuito, è possibile stabilire la connessione sicura da on-premises e/o la sottoscrizione di Azure.

**Quali sono i costi di rete per la connessione da e verso CloudSimple. Eventuali addebiti in uscita da e verso CloudSimple in Azure? Tra le aree?**

Non sono previsti addebiti per qualsiasi rete in uscita.  Le tariffe standard di Azure si applicano a tutto il traffico in uscita dalla rete virtuale o dal circuito ExpressRoute da sito locale.

## <a name="networking"></a>Rete

**Quali funzionalità di rete sono disponibili per il Cloud privato?**

È possibile effettuare il provisioning di reti VLAN (e le subnet), tabelle, del firewall e assegnare indirizzi IP pubblici ed eseguire il mapping a una macchina virtuale in esecuzione nel Cloud privato.  Per altre informazioni, vedere [Cenni preliminari sulla VLAN/subnet](cloudsimple-vlans-subnets.md), [Cenni preliminari sulle tabelle Firewall](cloudsimple-firewall-tables.md), e [Panoramica di indirizzi IP pubblici](cloudsimple-public-ip-address.md) articoli.

**Come si imposta su subnet diverse per le applicazioni nella mio del Cloud privato Microsoft?**

È possibile creare reti VLAN sul Cloud privato mediante il portale CloudSimple.  Dopo aver creato la VLAN, è possibile creare un gruppo di porte distribuita nel Cloud privato vCenter con le VLAN e creare le macchine virtuali connesse al gruppo di porte distribuita.  È possibile abilitare la tabella di firewall per la VLAN/Subnet e definire le regole del firewall per proteggere il traffico di rete.

**Le impostazioni del firewall sono disponibili per il cloud privato?**

È possibile configurare regole per il traffico verso nord-sud e Ovest orientale.  Le regole sono definite in una tabella di firewall.  La tabella di firewall può essere collegata a VLAN(s) sul Cloud privato.  Visualizzare [configurare le tabelle di firewall e regole per i cloud privati](https://docs.azure.cloudsimple.com/firewall) articolo per la procedura di installazione.

**È possibile assegnare indirizzi IP pubblici per le macchine virtuali nell'ambiente Cloud privato?**

Nel portale di CloudSimple è facilmente possibile allocare un nuovo indirizzo IP pubblico e associarlo a un indirizzo IP privato della macchina virtuale o un'appliance.  È anche possibile creare nuove regole del firewall o si applicano le regole firewall esistenti per consentire il traffico da porte specifiche e/o specifici set di indirizzi IP nel portale. Visualizzare [allocare gli indirizzi IP pubblici per l'ambiente Cloud privato](https://docs.azure.cloudsimple.com/public-ips) per la procedura di installazione.

## <a name="security"></a>Security

**Quali sono le opzioni di sicurezza su CloudSimple?**

Cloud privato CloudSimple offre le seguenti funzionalità di sicurezza per proteggere l'ambiente Cloud privato:

1. **I dati di crittografia dei dati inattivi**: È possibile crittografare i dati inattivi che si trovano sulla rete vSAN risorsa di archiviazione nel Cloud privato. server di gestione delle chiavi esterne, che può essere distribuito nell'ambiente locale o rete virtuale di Azure supporta la rete vSAN.  Visualizzare [configurare la crittografia di rete vSAN per il Cloud privato CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption) per altri dettagli.
2. **Sicurezza di rete**: Controllo network il flusso del traffico da/verso il Cloud privato da Internet, in locale e all'interno di subnet della Cloud privata mediante le regole del firewall.
3. **Connessione privata, protetta**: Connessione privata protetta tra la rete locale e la sottoscrizione di Azure.

## <a name="compute"></a>Calcolo

**Il tipo di host sono disponibili?**

CloudSimple offre due tipi di host:

* **Nodo CS28:** CPU:2 x da 2,2 GHz, complessivo 28 core, 48 HT.  RAM: 256 GB.  Archiviazione: 1600 GB NVMe Cache 5760 GB di dati (All-Flash). Rete: 2x25Gbe NIC
* **Nodo CS36:** CPU 2 x da 2,3 GHz, complessivo 36 core, 72 HT.  RAM: 512 GB.  Archiviazione: GB 3200 NVMe Cache 11,520 GB di dati (All-Flash).  Rete: 2x25Gbe NIC

**Come vengono gestiti gli eventuali errori hardware?**

Tutta l'infrastruttura CloudSimple deve essere continuamente monitorato dalla piattaforma CloudSimple e i team di operazioni di servizio.  Se viene rilevato un errore hardware, viene aggiunto un nuovo nodo del Cloud privato e viene rimossa nodo in errore garantendo la disponibilità elevata del Cloud privato.

## <a name="storage"></a>Archiviazione

**Il tipo di archiviazione è supportato in un Cloud privato?**

Offre CloudSimple **archiviazione di rete vSAN di VMware All-flash** con ogni Cloud privato.  Ogni vSphere viene creato con il proprio archivio dati vSAN.  Visualizzare [i componenti VMware di Cloud privato - vSAN archiviazione](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage) per altri dettagli vedere l'articolo.

**È la crittografia dei dati supportati?**
Sì.  È possibile impostare lo spazio di archiviazione di rete vSAN sul Cloud privato da usare un server di gestione delle chiavi (KMS) che viene distribuito in locale o in Azure per la crittografia dei dati archiviati in una rete vSAN

**Come vengono gestiti i dischi danneggiati?**

Monitoraggio CloudSimple monitora continuamente tutti i componenti hardware del Cloud privato.  Se viene rilevato alcun errore del disco o un disco viene identificato come errori (in base all'euristica), viene automaticamente aggiunto un nuovo nodo verso il Cloud privato.  Il nodo con un errore del disco viene rimosso dal Cloud privato.

## <a name="vmware"></a>VMware

**Come è possibile eseguire su larga scala caricare/eseguire la migrazione di applicazioni e i dati in locale?**

CloudSimple fornisce soluzione nativa vSphere di VMware.  Qualsiasi strumento utilizzato per la migrazione di dati bulk è utilizzabile con il Cloud privato CloudSimple.  Alcune delle opzioni disponibili sono:

1. HCX VMware per la migrazione di massa dei dati.
2. Migrazione dei dati mediante Storage vMotion da locale a CloudSimple ad accesso sporadico.

**È possibile installare gli strumenti VMware?**

CloudSimple fornisce soluzione nativa vSphere di VMware.  Qualsiasi strumento utilizzato per la gestione di vSphere ambiente locale può essere usato su CloudSimple.  CloudSimple supporta modello Bring-Your-Own-License (BYOL) per l'installazione strumenti VMware.

**In che modo vengono gestiti gli aggiornamenti?**

CloudSimple gestisce e aggiorna tutti i componenti dell'infrastruttura della Cloud privata in modo trasparente senza interruzioni.  Eventuali aggiornamenti o patch di protezione rilasciati da fornitori di infrastruttura o VMware verrà pianificata per l'aggiornamento, non appena viene qualificata da CloudSimple.

CloudSimple non esegue gli aggiornamenti delle applicazioni installate sul Cloud privato.

## <a name="azure-integration"></a>Integrazione di Azure

**Quali servizi di Azure sono supportati?**

CloudSimple fornisce connessione Azure ExpressRoute per la sottoscrizione in Azure.  Tutti i servizi in esecuzione nella sottoscrizione disponga della connettività di rete per il Cloud privato e può connettersi al Cloud privato.  Esempi:

1. **Azure Active Directory** come origine di identità per vCenter CloudSimple
2. **Archiviazione di Azure** per l'archiviazione dei backup, immagini e altri dati dal Cloud privato
3. **Applicazioni ibride** -è possibile creare l'architettura delle applicazioni che si estende su cloud pubblici e privati.  Ad esempio, è possibile creare webservers in Azure che accedono a database e server applicazioni sul Cloud privato CloudSimple.
4. **Monitoraggio di Azure** e **Centro sicurezza di Azure** -carico di lavoro in esecuzione su VMware possibile usarli per la registrazione, le metriche delle prestazioni e gestione della sicurezza.

**Come è possibile associare il tenant di VMware in Azure?**

CloudSimple fornisce univoco consente di gestire le macchine virtuali VMware in un Cloud privato dal portale di Azure.  Un pool di risorse di vCenter (configurato con i vincoli di risorse desiderato) può essere associato alla sottoscrizione l'amministratore globale.  

**Quali sono i vantaggi di gestione delle licenze viene visualizzato con Azure?**

Con CloudSimple, è possibile sfruttare il vantaggio Azure Hybrid sull'utilizzo e risparmia fino al 90% le licenze di mantenere l'investimento speso nel Licenses Microsoft, abbassando il TCO rispetto ad altri cloud. Inoltre ottenere esteso gli aggiornamenti della sicurezza per Windows Server 2008 e Microsoft SQL Server 2008.  Ridurre i costi bassa con Bring Your Own licenze (BYOL) nel cloud per le app più comuni come Veeam, Zerto e ad altri utenti.  