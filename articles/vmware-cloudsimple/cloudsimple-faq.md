---
title: Domande frequenti - soluzione VMware CloudSimple
description: Domande frequenti per Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e187a4a75a27e206a632388f1e20a94da032eb08
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595350"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Domande frequenti sulla soluzione di VMware da CloudSimple

Domande frequenti e risposte su Azure VMware Solution by CloudSimple che consentono di comprendano il servizio e come usarlo. Le domande e risposte sono suddivise nelle categorie seguenti:

* Servizio CloudSimple
* Connettività
* Rete
* Security
* Calcolo
* Archiviazione
* VMware
* Integrazione di Azure
 
## <a name="cloudsimple-service"></a>Servizio CloudSimple

**Che cos'è Azure VMware Solution by CloudSimple?**

Soluzione Azure di VMware da CloudSimple Trasforma ed estende i carichi di lavoro VMware in un cloud privato e dedicato in Azure in pochi minuti. La soluzione viene eseguito il provisioning, gestisce l'infrastruttura e Orchestra i carichi di lavoro tra origini locali e Azure. Poiché le app eseguite esattamente la stessa in locale e in Azure, è trarre vantaggio dall'elasticità e servizi del cloud senza la complessità di riarchitettare le tue app. CloudSimple riduce il costo totale di proprietà con un modello di consumo cloud che offre on demand il provisioning, pagamento come-di-aumento delle dimensioni e ottimizzazione della capacità. Per le funzionalità, vantaggi e gli scenari, vedere [What ' s Azure VMware Solution by CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**Che cos'è un cloud privato CloudSimple?**

Viene effettuato il provisioning di un cloud privato e dedicato che è costituito da un calcolo ad alte prestazioni, archiviazione e l'ambiente di rete distribuiti nell'infrastruttura di Microsoft Azure (spazio di hardware e Data Center) in posizioni di Azure. Il cloud privato offre una piattaforma di VMware nativa come un servizio. In termini di VMware, ogni cloud privato contiene esattamente un'istanza del Server vCenter. Il server vCenter Server gestisce più ESXi nodi contenuti in uno o più cluster vSphere, insieme a risorsa di archiviazione di rete vSAN corrispondente. Un servizio CloudSimple può contenere più cloud privati nella sottoscrizione di Azure. Per altre informazioni sui cloud privati, vedere [Panoramica di cloud privato](cloudsimple-private-cloud.md).

**In cui è disponibile il servizio CloudSimple?**

CloudSimple è disponibile nelle aree Stati Uniti orientali e Stati Uniti occidentali.

**Come si abilita la sottoscrizione per CloudSimple?**

Contattare il rappresentante dell'account Microsoft al [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) per abilitare la sottoscrizione per il servizio CloudSimple. Fornire l'ID della sottoscrizione nel messaggio di posta elettronica per cui si desidera servizio CloudSimple abilitato. 

**Come si accede al portale CloudSimple?**

Accedere al portale CloudSimple dal portale di Azure. Per informazioni su come accedere al portale CloudSimple, vedere [accedere alla soluzione VMware dal portale CloudSimple dal portale di Azure](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Come si aumenta la capacità per un cloud privato?**

Il provisioning di nodi dal portale di Azure ed espandere il cloud privato da portale CloudSimple. È possibile espandere il cloud privato mediante l'aggiunta di nodi a un cluster vSphere esistente o creando un nuovo cluster vSphere. Per informazioni sulla procedura, vedere [espandere un cloud privato CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**Cosa succede al mio cloud privato durante la manutenzione?**

CloudSimple offre le notifiche periodiche giorni prima della manutenzione pianificata. L'operazione di manutenzione viene eseguita in modo non distruttivo per garantire la disponibilità del cloud privato. Manutenzione può essere dei tipi seguenti:

- **Infrastruttura CloudSimple**: L'infrastruttura CloudSimple è progettato per garantire la disponibilità elevata. Durante la manutenzione, connettività e la disponibilità di cloud privato viene garantita tramite l'aggiornamento ai componenti ridondanti, uno alla volta senza alcun impatto. È necessario l'accesso il vCenter del cloud privato, tutte le macchine virtuali, connessione a internet dal cloud privato e le connessioni a un'istanza locale o in Azure.
- **Portale CloudSimple**: Durante la manutenzione, alcune funzionalità nel portale di CloudSimple potrebbe non essere accessibile o potrebbero essere disabilitate. La notifica di manutenzione include informazioni sulle operazioni eseguibili nel portale.

## <a name="connectivity"></a>Connettività

**Quali sono le opzioni di connettività alla rete CloudSimple area?**

CloudSimple offre tre diverse opzioni di connettività per connettersi alla rete CloudSimple area. Tutte le tre opzioni possono essere usate insieme:

- Connessione ExpressRoute di Azure dal data center locali a CloudSimple area rete: Ad alta velocità a bassa latenza privata connessione sicura che colma il circuito ExpressRoute da sito locale con il circuito CloudSimple ExpressRoute usando raggiungere globale. Per configurare la connessione, vedere [connessione da locale a CloudSimple usando ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- Connessione ExpressRoute dalla rete virtuale di Azure alla rete CloudSimple area: Una bassa latenza e ad alta velocità privata connessione sicura che collega la rete virtuale in Azure con il circuito CloudSimple ExpressRoute con gateway di rete virtuale. Per configurare la connessione, vedere [connettersi all'ambiente di cloud privato CloudSimple alla rete virtuale di Azure tramite ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Connessione VPN Site-to-site dal Data Center locale alla rete CloudSimple area: Una rete virtuale sicura privata dal dispositivo VPN locale all'area CloudSimple cloud privato. Per configurare la connessione, vedere [configurare una connessione VPN tra la rete locale e cloud privato CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Come ci si connette a un cloud privato?**

È possibile visualizzare i dettagli del cloud privato nel portale di CloudSimple. Per connettersi al server vCenter che corrisponde a un cloud privato, assicurarsi che viene stabilita una connessione di rete usando site-to-site, point-to-site o ExpressRoute. Quindi, avviare il portale CloudSimple dal portale di Azure. Selezionare **avvio veloce di vSphere Client** nella home page o nella pagina dei dettagli del cloud privato.

**Che cos'è il vantaggio di un circuito ExpressRoute?**

Un circuito ExpressRoute di Azure fornisce una connessione sicura ad alta velocità, bassa latenza. CloudSimple fornisce un circuito ExpressRoute dedicato per ogni area per ogni cliente. Usa questo circuito, è possibile stabilire la connessione sicura da origini locali e la sottoscrizione di Azure.

**Quali sono i costi di rete per la connessione da e verso CloudSimple? Sono presenti eventuali addebiti in uscita da e verso CloudSimple in Azure? Sono presenti eventuali addebiti in uscita tra aree?**

Non sono previsti addebiti per la rete in uscita. Le tariffe standard di Azure si applicano a tutto il traffico in uscita dalla rete virtuale o da un circuito ExpressRoute da sito locale.

## <a name="networking"></a>Rete

**Quali funzionalità di rete sono disponibili per il cloud privato?**

È possibile eseguire il provisioning di reti VLAN e le subnet e le tabelle di firewall. È possibile assegnare indirizzi IP pubblici ed eseguire il mapping a una macchina virtuale che viene eseguito nel cloud privato. Per altre informazioni, vedere [Cenni preliminari sulla VLAN e subnet](cloudsimple-vlans-subnets.md), [Cenni preliminari sulle tabelle Firewall](cloudsimple-firewall-tables.md), e [Panoramica di indirizzi IP pubblici](cloudsimple-public-ip-address.md).

**Come si imposta su subnet diverse per le applicazioni nella mio cloud privato?**

È possibile creare le VLAN nel cloud privato mediante il portale CloudSimple. Dopo aver creato la VLAN, è possibile creare un gruppo di porte distribuita nel cloud privato vCenter tramite la VLAN e creare le macchine virtuali connesse al gruppo di porte distribuita. È possibile abilitare una tabella di firewall per la VLAN o la subnet e definire le regole del firewall per proteggere il traffico di rete.

**Le impostazioni del firewall sono disponibili per il cloud privato?**

È possibile configurare regole per il traffico verso nord-sud e Ovest orientale. Le regole sono definite in una tabella di firewall. La tabella firewall può essere collegata a reti VLAN in un cloud privato. Per la procedura di installazione, vedere [configurare le tabelle di firewall e regole per i cloud privati](https://docs.azure.cloudsimple.com/firewall).

**È possibile assegnare indirizzi IP pubblici per le macchine virtuali nell'ambiente cloud privato?**

Nel portale di CloudSimple è facilmente possibile allocare un nuovo indirizzo IP pubblico e associarlo a un indirizzo IP privato della macchina virtuale o un'appliance. È anche possibile creare nuove regole del firewall o si applicano le regole firewall esistenti per consentire il traffico da porte specifiche e specifico set di indirizzi IP nel portale. Per la procedura di installazione, vedere [allocare gli indirizzi IP pubblici per un ambiente cloud privato](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Security

**Quali sono le opzioni di sicurezza su CloudSimple?**

Un cloud privato CloudSimple offre le seguenti funzionalità di sicurezza per proteggere l'ambiente cloud privato:

- **Dati di crittografia dei dati inattivi:** È possibile crittografare i dati inattivi che si trova nella rete vSAN archiviazione nel cloud privato. rete vSAN supporta un server di gestione delle chiavi esterne, che può essere distribuito nell'ambiente locale o rete virtuale Azure. Per altre informazioni, vedere [configurare la crittografia di rete vSAN per un cloud privato CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Sicurezza di rete:** Controllo network flusso del traffico da e verso il cloud privato da internet, in locale e all'interno di subnet del cloud privato con le regole del firewall.
- **Connessione sicura e privata:** Connessione sicura e privata tra la rete locale e la sottoscrizione di Azure.

## <a name="compute"></a>Calcolo

**Il tipo di host sono disponibili?**

CloudSimple offre due tipi di host:

* **Nodo CS28**: CPU:2 x da 2,2 GHz, complessivo 28 core, 48 HT. RAM: 256 GB. Archiviazione: 1600 GB NVMe Cache, dati 5760 GB (All-Flash). Rete: 2x25Gbe NIC.
* **Nodo CS36**: CPU 2 x da 2,3 GHz, complessivo 36 core, 72 HT. RAM: 512 GB. Archiviazione: 3200 GB NVMe Cache 11,520-GB di dati (All-Flash). Rete: 2x25Gbe NIC.

**Come vengono gestiti gli errori hardware?**

Tutta l'infrastruttura CloudSimple deve essere continuamente monitorato dalla piattaforma CloudSimple e ai team di operazioni di servizio. Se viene rilevato un errore hardware, un nuovo nodo viene aggiunto a un cloud privato. Il nodo in errore viene rimosso per assicurare la disponibilità elevata del cloud privato.

## <a name="storage"></a>Archiviazione

**Il tipo di archiviazione è supportato in un cloud privato?**

Offre CloudSimple **archiviazione di rete vSAN di VMware All-flash** con ogni cloud privato. Ogni vSphere viene creato con il proprio archivio dati vSAN. Per altre informazioni, vedere [i componenti VMware di cloud privato - vSAN archiviazione](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**È la crittografia dei dati supportati?**
Sì. È possibile configurare lo spazio di archiviazione di rete vSAN sul cloud privato da usare un server di gestione delle chiavi (KMS), che viene distribuito in locale o in Azure per la crittografia dei dati archiviati in una rete vSAN.

**Come vengono gestiti i dischi danneggiati?**

Monitoraggio CloudSimple monitora continuamente tutti i componenti hardware del cloud privato. Se viene rilevato l'errore del disco o un disco viene identificato come esito negativo in base all'euristica, viene automaticamente aggiunto un nuovo nodo per il cloud privato. Il nodo con un disco di un errore viene rimosso dal cloud privato.

## <a name="vmware"></a>VMware

**Come è possibile eseguire il caricamento su larga scala e la migrazione delle applicazioni e i dati in locale?**

CloudSimple offre una soluzione di vSphere di VMware nativa. Qualsiasi strumento utilizzato per la migrazione di dati bulk è utilizzabile con un cloud privato CloudSimple. Alcune delle opzioni disponibili sono:

- HCX VMware per la migrazione di massa dei dati.
- Ad accesso sporadico migrazione dei dati mediante Storage vMotion da locale a CloudSimple.

**È possibile installare gli strumenti VMware?**

CloudSimple offre una soluzione di vSphere di VMware nativa. Qualsiasi strumento che consente di gestire un ambiente di vSphere in locale possono essere utilizzata su CloudSimple. CloudSimple supporta un modello di bring-your-own-license (BYOL) per l'installazione strumenti VMware.

**In che modo vengono gestiti gli aggiornamenti?**

CloudSimple gestisce e aggiorna tutti i componenti dell'infrastruttura del cloud privato in modo non distruttivo senza problemi. Eventuali aggiornamenti o patch di protezione rilasciati da fornitori di infrastruttura o VMware è pianificata per l'aggiornamento, non appena viene qualificata da CloudSimple.

CloudSimple non esegue gli aggiornamenti delle applicazioni installate nel cloud privato.

## <a name="azure-integration"></a>Integrazione di Azure

**Quali servizi di Azure sono supportati?**

CloudSimple fornisce connessione Azure ExpressRoute per la sottoscrizione in Azure. Tutti i servizi eseguiti nella sottoscrizione di connettività di rete dispongono per il cloud privato e possono connettersi al cloud privato. Esempi:

- **Azure Active Directory**: Usare Azure Active Directory come origine di identità per vCenter CloudSimple.
- **Archiviazione di Azure**: Usare l'archiviazione per archiviare i backup, immagini e altri dati dal cloud privato.
- **Applicazioni ibride**: È possibile creare l'architettura delle applicazioni che si estende su cloud pubblici e privati. Ad esempio, è possibile creare i server web in Azure tale applicazione di accesso e i server di database in un cloud privato CloudSimple.
- **Monitoraggio di Azure** e **Centro sicurezza di Azure**: Carico di lavoro in esecuzione su VMware è possibile usare monitoraggio e il Centro sicurezza per la registrazione, le metriche delle prestazioni e gestione della sicurezza.

**Come è possibile associare il tenant di VMware in Azure?**

CloudSimple fornisce univoco consente di gestire le macchine virtuali VMware in un cloud privato dal portale di Azure. Configurare un pool di risorse di vCenter con i vincoli delle risorse desiderate possono essere associate alla sottoscrizione l'amministratore globale. 

**Quali sono i vantaggi di gestione delle licenze viene visualizzato con Azure?**

Con CloudSimple, è possibile sfruttare il vantaggio Azure Hybrid e risparmia fino al 90% sulle licenze per mantenere l'investimento in licenze per Microsoft e ridurre il costo totale di proprietà rispetto ad altri cloud. Inoltre ottenere esteso gli aggiornamenti della sicurezza per Windows Server 2008 e Microsoft SQL Server 2008. Ridurre i costi grazie al sistema BYOL nel cloud per le app più comuni come Veeam, Zerto e altri. 
