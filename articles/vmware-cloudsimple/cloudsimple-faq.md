---
title: 'Domande frequenti: soluzione VMware di Azure di CloudSimple'
description: Domande frequenti sulla soluzione VMware di Azure di CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d1cbca20b9f1ee1d5f7eefb760ed60fa4d019050
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972866"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Domande frequenti sulla soluzione VMware di CloudSimple

## <a name="cloudsimple-service"></a>Servizio CloudSimple

**Che cos'è la soluzione VMware di Azure di CloudSimple?**

Soluzione VMware di Azure di CloudSimple trasforma ed estende i carichi di lavoro VMware in cloud privati e dedicati in Azure in pochi minuti. CloudSimple si occupa del provisioning, della gestione dell'infrastruttura e dell'orchestrazione dei carichi di lavoro tra l'ambiente locale e Azure. Poiché le tue app vengono eseguite esattamente in locale e in Azure, puoi trarre vantaggio dall'elasticità e dai servizi del cloud senza la complessità di riprogettare le tue app. CloudSimple riduce il costo totale di proprietà con un modello di consumo cloud che fornisce il provisioning su richiesta, il pagamento in base al consumo e l'ottimizzazione della capacità.  Per informazioni su funzionalità, vantaggi e scenari, vedere la pagina [relativa alla soluzione VMware in Azure di CloudSimple](cloudsimple-vmware-solutions-overview.md) .

**Che cos'è un cloud privato CloudSimple?**

Un cloud privato CloudSimple è un cloud privato e dedicato costituito da un ambiente di calcolo, archiviazione e rete a prestazioni elevate distribuito in Microsoft Azure infrastruttura (spazio di hardware e Data Center) in località di Azure.  Un cloud privato offre una piattaforma distribuita come servizio nativa di VMware. In termini di VMware, ogni cloud privato contiene esattamente un'istanza del server vCenter. Il server vCenter gestisce più nodi ESXi contenuti in uno o più cluster vSphere, insieme alla relativa archiviazione SAN virtuale (rete VSAN). Un servizio CloudSimple può contenere più cloud privati nella sottoscrizione di Azure.  Per altri dettagli, vedere [Panoramica del cloud privato](cloudsimple-private-cloud.md).

**Dove è disponibile il servizio CloudSimple?**

CloudSimple è disponibile nelle aree Stati Uniti orientali e Stati Uniti occidentali con aree aggiuntive presto disponibili.

**Ricerca per categorie abilitare la sottoscrizione per CloudSimple?**

Per abilitare la sottoscrizione per il servizio [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) CloudSimple, è possibile contattare il rappresentante del account Microsoft. Fornire l'ID sottoscrizione nel messaggio di posta elettronica per cui si vuole abilitare il servizio CloudSimple.  

**Ricerca per categorie accedere al portale di CloudSimple?**

È possibile accedere al portale di CloudSimple dalla portale di Azure.  Per informazioni dettagliate, vedere [accedere alla soluzione VMware tramite il portale di CloudSimple dal portale di Azure](access-cloudsimple-portal.md).

**Ricerca per categorie aumentare la capacità di un cloud privato?**

Per aumentare la capacità, acquistare nodi aggiuntivi dalla portale di Azure e quindi usare i nodi per espandere il cloud privato dal portale di CloudSimple.  È possibile aggiungere altri nodi a un cluster vSphere esistente o aggiungerli a un nuovo cluster vSphere.  Per informazioni dettagliate, vedere [espandere un cloud privato CloudSimple](expand-private-cloud.md).

**Cosa accade al cloud privato durante la manutenzione?**

CloudSimple fornisce notifiche diverse giorni prima di un intervallo di manutenzione pianificata.  La manutenzione viene eseguita in modo non distruttivo per garantire la disponibilità del cloud privato.  La manutenzione può essere dei seguenti tipi:

* **Infrastruttura di CloudSimple**.  L'infrastruttura CloudSimple è progettata per essere a disponibilità elevata.  Durante questo tipo di intervallo di manutenzione, i componenti ridondanti vengono aggiornati uno alla volta per evitare interruzioni del servizio. È possibile mantenere l'accesso al cloud privato vCenter, a tutte le macchine virtuali, alla connessione Internet dal cloud privato e alle connessioni ad Azure o in locale.
* **Portale di CloudSimple**. Durante questo tipo di intervallo di manutenzione, alcune funzionalità nel portale di CloudSimple potrebbero essere disabilitate o inaccessibili.  La notifica prima dell'intervallo di manutenzione include i dettagli sulle limitazioni delle funzionalità durante la manutenzione.

## <a name="connectivity"></a>Connettività

**Quali sono le opzioni di connettività alla rete dell'area CloudSimple?**

CloudSimple fornisce le opzioni di connettività seguenti per la connessione alla rete dell'area CloudSimple. È possibile utilizzare contemporaneamente più opzioni.

* **Connessione ExpressRoute dal Data Center locale alla rete dell'area CloudSimple**. Si tratta di una connessione privata sicura a bassa latenza e ad alta velocità che usa Copertura globale per collegare il circuito ExpressRoute locale al circuito CloudSimple ExpressRoute. Per istruzioni sull'impostazione della connessione, vedere [connettersi da locale a CloudSimple usando ExpressRoute](on-premises-connection.md).
* **Connessione ExpressRoute dalla rete virtuale di Azure alla rete dell'area CloudSimple**. Si tratta di una connessione privata sicura a bassa latenza e ad alta velocità che usa i gateway di rete virtuale per collegare la rete virtuale in Azure al circuito ExpressRoute di CloudSimple. Per istruzioni sull'impostazione della connessione, vedere [connettere l'ambiente cloud privato CloudSimple alla rete virtuale di Azure usando ExpressRoute](azure-expressroute-connection.md).
* **Connessione VPN da sito a sito dal Data Center locale alla rete dell'area di CloudSimple**. Si tratta di una rete privata virtuale sicura dal dispositivo VPN locale all'area del cloud privato CloudSimple.  Per informazioni dettagliate, vedere [configurare i gateway VPN nella rete CloudSimple](vpn-gateway.md).

**Ricerca per categorie connettersi a un cloud privato?**

È possibile visualizzare i dettagli del cloud privato nel portale di CloudSimple. Per connettersi a vCenter che corrisponde al cloud privato, verificare prima di tutto che venga stabilita una connessione di rete usando una VPN da sito a sito, una VPN da punto a sito o ExpressRoute. Avviare quindi il portale di CloudSimple dalla portale di Azure e fare clic su **Launch vSphere client** nella Home page o nella pagina dei dettagli del cloud privato.

**Qual è il vantaggio dei circuiti ExpressRoute?**

Un circuito ExpressRoute di Azure è una connessione sicura a bassa latenza e ad alta velocità.  CloudSimple fornisce un circuito ExpressRoute dedicato per area per cliente.  Con questo circuito è possibile stabilire una connessione sicura da locale o dalla sottoscrizione di Azure.

**Quali sono i costi di rete per la connessione a CloudSimple?  Si applicano addebiti in uscita tra CloudSimple e Azure o tra aree?**

Non sono previsti addebiti per CloudSimple per l'uscita dalla rete.  Le tariffe standard di Azure si applicano a qualsiasi traffico in uscita dalla rete virtuale o dal circuito ExpressRoute locale.

## <a name="networking"></a>Rete

**Quali funzionalità di rete sono disponibili per il cloud privato?**

È possibile eseguire il provisioning delle VLAN (e delle rispettive subnet) e delle tabelle del firewall e assegnare indirizzi IP pubblici con mapping a una macchina virtuale in esecuzione nel cloud privato. Per informazioni dettagliate sulle funzionalità di rete, vedere Panoramica di [VLAN e subnet](cloudsimple-vlans-subnets.md), [Panoramica delle tabelle del firewall](cloudsimple-firewall-tables.md)e panoramica degli [indirizzi IP pubblici](cloudsimple-public-ip-address.md).

**Ricerca per categorie configurare subnet diverse per le applicazioni nel cloud privato?**

Le VLAN vengono create nel cloud privato dal portale di CloudSimple.  Dopo aver creato una VLAN, è possibile creare un gruppo di porte distribuite nel cloud privato vCenter usando la VLAN e creare macchine virtuali connesse al gruppo di porte distribuite.  È possibile abilitare le tabelle del firewall per la VLAN/subnet e definire le regole del firewall per proteggere il traffico di rete.

**Quali impostazioni del firewall sono disponibili per i cloud privati?**

È possibile configurare le regole per il traffico nord-sud e est-ovest.  Le regole sono definite in una tabella del firewall.  La tabella del firewall può essere collegata alle VLAN nel cloud privato.  Per informazioni dettagliate, vedere [configurare le regole e le tabelle del firewall per i cloud privati](firewall.md).

**È possibile assegnare indirizzi IP pubblici alle macchine virtuali nell'ambiente cloud privato?**

Nel portale di CloudSimple è possibile allocare un nuovo indirizzo IP pubblico e associarlo all'indirizzo IP privato di una macchina virtuale o di un'appliance.  È anche possibile creare nuove regole del firewall o applicare regole del firewall esistenti per consentire il traffico da porte e indirizzi IP specifici nel portale. Per informazioni dettagliate, vedere [allocare indirizzi IP pubblici per l'ambiente del cloud privato](public-ips.md).

## <a name="security"></a>Security

**Quali sono le opzioni di sicurezza in CloudSimple?**

CloudSimple fornisce le seguenti funzionalità di sicurezza per la protezione dell'ambiente cloud privato:

* **Crittografia dei dati**inattivi. È possibile crittografare i dati inattivi presenti nella risorsa di archiviazione rete VSAN nel cloud privato. Rete VSAN supporta i server di gestione delle chiavi esterne, che possono essere distribuiti nell'ambiente Azure vNet o in locale.  Per informazioni dettagliate, vedere [configurare la crittografia rete VSAN per il cloud privato CloudSimple](vsan-encryption.md).
* **Sicurezza di rete**. Controllare il flusso del traffico di rete con regole del firewall che si applicano tra il cloud privato e Internet, il cloud privato e l'ambiente locale o all'interno di subnet del cloud privato.
* **Connessione privata sicura**. Viene stabilita una connessione privata sicura tra la rete locale e la sottoscrizione di Azure.

## <a name="compute"></a>Calcolo

**Quali tipi di host sono disponibili?**

CloudSimple offre i tipi di host seguenti:

* **Nodo CS28:** CPU: 2x 2,2 GHz, totale 28 core, 48 HT.  RAM: 256 GB.  Archiviazione: 1600 GB di cache NVMe, 5760 GB di dati (tutti i flash). Rete: SCHEDA di interfaccia di rete 2x25Gbe
* **Nodo CS36:** CPU 2x 2,3 GHz, totale 36 core, 72 HT.  RAM: 512 GB.  Archiviazione: 3200 GB NVMe cache 11520 GB di dati (tutti i flash).  Rete: SCHEDA di interfaccia di rete 2x25Gbe

**In che modo vengono gestiti gli errori hardware?**

Tutta l'infrastruttura CloudSimple viene monitorata continuamente dalla piattaforma CloudSimple e dai team operativi dei servizi.  Se viene rilevato un errore hardware, viene aggiunto un nuovo nodo al cloud privato e viene rimosso il nodo che ha avuto esito negativo.

## <a name="storage"></a>Archiviazione

**Quali tipi di archiviazione sono supportati in un cloud privato?**

CloudSimple offre archiviazione rete VSAN VMware All-Flash con ogni cloud privato.  Ogni vSphere viene creato con il proprio archivio dati rete VSAN.  Per informazioni dettagliate, vedere [componenti VMware del cloud privato-archiviazione rete VSAN](vmware-components.md#vsan-storage).

**La crittografia dei dati è supportata?**
Sì.  È possibile configurare l'archiviazione rete VSAN nel cloud privato per usare un server di gestione delle chiavi (KMS) distribuito in locale o in Azure per crittografare i dati archiviati in rete VSAN.

**Come vengono gestiti i dischi con errori?**

CloudSimple monitora continuamente tutti i componenti hardware del cloud privato.  Se viene rilevato un errore del disco o un disco viene identificato come non riuscito (in base all'euristica), viene aggiunto automaticamente un nuovo nodo al cloud privato.  Il nodo con il disco guasto o guasto viene rimosso dal cloud privato.

## <a name="vmware"></a>VMware

**Ricerca per categorie eseguire il caricamento o la migrazione su larga scala delle applicazioni e dei dati dall'ambiente locale?**

CloudSimple fornisce una soluzione VMware vSphere nativa.  Tutti gli strumenti VMware per la migrazione dei dati in blocco possono essere usati con il cloud privato.  Le opzioni includono:

* VMware HCX per la migrazione in blocco dei dati.
* Migrazione a freddo dei dati tramite Storage vMotion da locale a CloudSimple.

**È possibile installare qualsiasi strumento VMware?**

CloudSimple fornisce una soluzione VMware vSphere nativa.  Tutti gli strumenti VMware usati per la gestione dell'ambiente vSphere locale possono essere usati in CloudSimple.  CloudSimple supporta un modello BYOL (Bring your own License) per l'installazione degli strumenti VMware.

**Come vengono gestiti gli aggiornamenti e gli aggiornamenti?**

CloudSimple gestisce e aggiorna tutti i componenti dell'infrastruttura del cloud privato in modo semplice e senza interruzioni.  Tutti gli aggiornamenti e le patch di sicurezza rilasciati da VMware o dai fornitori di infrastrutture sono pianificati per l'aggiornamento non appena sono qualificati da CloudSimple.

CloudSimple non esegue aggiornamenti o aggiornamenti delle applicazioni installate nel cloud privato.

## <a name="azure-integration"></a>Integrazione di Azure

**Quali servizi di Azure sono supportati?**

CloudSimple fornisce una connessione ExpressRoute di Azure alla sottoscrizione in Azure.  Tutti i servizi in esecuzione nella sottoscrizione possono connettersi al cloud privato.  Ecco alcuni esempi:

* **Azure Active Directory** come origine di identità per CloudSimple vCenter.
* **Archiviazione di Azure** per l'archiviazione di backup, immagini e altri dati dal cloud privato.
* **Applicazioni ibride** con un'architettura di applicazione che si estende su cloud pubblici e privati.  Ad esempio, è possibile creare server Web in Azure che accedono a server applicazioni e database nel cloud privato.
* **Monitoraggio di Azure** e il **Centro sicurezza di Azure** per i carichi di lavoro in esecuzione in VMware supportano la registrazione, le metriche delle prestazioni e la gestione della sicurezza.

**Ricerca per categorie mappare i tenant VMware ad Azure?**

CloudSimple offre la possibilità di gestire le macchine virtuali VMware in un cloud privato dal portale di Azure.  È possibile eseguire il mapping di un pool di risorse vCenter configurato con vincoli di risorse desiderati alla sottoscrizione dall'amministratore globale.  

**Quali vantaggi di licenza si ottengono con Azure?**

Con CloudSimple puoi sfruttare il vantaggio Azure Hybrid Usage e risparmiare fino al 90% sulle licenze. Questo vantaggio consente di mantenere l'investimento nelle licenze Microsoft e di ridurre il TCO rispetto ad altre soluzioni cloud. Si ottengono anche aggiornamenti della sicurezza estesi per Windows Server 2008 e Microsoft SQL Server 2008.  Il modello Bring your own License (BYOL) consente di ridurre i costi per le app comuni, ad esempio Veeam e Zerto.  
