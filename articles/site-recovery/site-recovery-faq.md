---
title: Domande generali sul servizio Azure Site Recovery
description: Questo articolo illustra le domande generali più frequenti su Azure Site Recovery.
ms.topic: conceptual
ms.date: 7/14/2020
ms.author: raynew
ms.openlocfilehash: 3da86eead5b927a2a71d7b1a28bc5966bf5f8840
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369438"
---
# <a name="general-questions-about-azure-site-recovery"></a>Domande generali su Azure Site Recovery

In questo articolo sono riepilogate le domande frequenti su Azure Site Recovery. Per scenari specifici, vedere questi articoli

- [Domande sul ripristino di emergenza di macchine virtuali di Azure in Azure](azure-to-azure-common-questions.md)
- [Domande sul ripristino di emergenza di macchine virtuali VMware in Azure](vmware-azure-common-questions.md)
- [Domande sul ripristino di emergenza di macchine virtuali Hyper-V in Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Generale

### <a name="what-does-site-recovery-do"></a>Quali sono le funzioni di Site Recovery?

Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza mediante la gestione e l'automatizzazione delle operazioni di replica delle macchine virtuali di Azure tra le aree, delle macchine virtuali e dei server fisici locali in Azure o in un data center secondario. [Altre informazioni](site-recovery-overview.md)

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>È possibile proteggere una macchina virtuale che dispone di un disco Docker?

No, questo scenario non è supportato.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Che cosa Site Recovery fare per garantire l'integrità dei dati?

Site Recovery per garantire l'integrità dei dati, sono disponibili diverse misure. Viene stabilita una connessione sicura tra tutti i servizi usando il protocollo HTTPS. In questo modo si garantisce che eventuali malware o entità esterne non possano manomettere i dati. Un'altra misura eseguita consiste nell'utilizzo di checksum. Il trasferimento dei dati tra origine e destinazione viene eseguito calcolando i checksum dei dati tra di essi. In questo modo si garantisce che i dati trasferiti siano coerenti.

## <a name="service-providers"></a>Provider di servizi

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Per i provider di servizi, Site Recovery funziona per modelli di infrastruttura dedicati e condivisi?
Sì, Site Recovery supporta i modelli di infrastruttura dedicati e condivisi.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Per i provider di servizi l'identità del tenant è condivisa con il servizio Site Recovery?
No. L'identità del tenant rimane anonima. Per i tenant non è necessario l'accesso al portale di Site Recovery. Solo l'amministratore del provider di servizi interagisce con il portale.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>I dati delle applicazioni tenant potranno mai essere ricevuti da Azure?
Quando si esegue la replica tra i siti di proprietà del provider di servizi, i dati delle applicazioni non vengono mai inviati ad Azure. I dati vengono crittografati in transito e replicati direttamente tra i siti del provider di servizi.

Se si esegue la replica in Azure, i dati dell'applicazione vengono inviati all'archiviazione di Azure, ma non al servizio Site Recovery. I dati vengono crittografati in transito e rimangono crittografati in Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>I tenant riceveranno una fattura per ogni servizio Azure?
No. La fatturazione di Azure avviene direttamente con il provider di servizi. I provider di servizi sono responsabili della generazione di fatture specifiche per i tenant.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se si esegue la replica in Azure, è sempre necessario eseguire le macchine virtuali in Azure?
No, i dati vengono replicati nelle risorse di archiviazione di Azure nella sottoscrizione. Quando si esegue un failover di test (un'analisi del ripristino di emergenza) o un failover effettivo, Site Recovery crea automaticamente macchine virtuali nella sottoscrizione.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>È possibile garantire isolamento a livello di tenant durante la replica in Azure?
Sì.

### <a name="what-platforms-do-you-currently-support"></a>Quali piattaforme sono attualmente supportate?
Sono supportate distribuzioni basate su Azure Pack, Cloud Platform System e System Center (2012 e versioni successive). [Alter informazioni](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) sull'integrazione di Azure Pack e Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Sono supportate singole distribuzioni di Azure Pack e del server VMM?
Sì, è possibile eseguire la replica da macchine virtuali Hyper-V ad Azure o tra siti del provider di servizi.  Si noti che se si esegue la replica tra i siti del provider di servizi, l'integrazione del runbook di Azure non è disponibile.

## <a name="pricing"></a>Prezzi

### <a name="where-can-i-find-pricing-information"></a>Dove sono reperibili informazioni sui prezzi?
Vedere i dettagli sui [prezzi di Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Come è possibile fare una stima dei costi durante l'uso di Site Recovery?

È possibile usare la funzione [Calcolatore dei prezzi](https://aka.ms/asr_pricing_calculator) per stimare i costi quando si usa Site Recovery.

Per una stima dettagliata dei costi, eseguire lo strumento Piano di distribuzione per [VMware](./site-recovery-deployment-planner.md) o [Hyper-V](https://aka.ms/asr-deployment-planner) e usare il [report di stima dei costi](./site-recovery-vmware-deployment-planner-cost-estimation.md).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>I dischi gestiti vengono ora usati per la replica di server fisici e macchine virtuali VMware. Vengono addebitati costi aggiuntivi per l'account di archiviazione della cache con i dischi gestiti?

No, non sono previsti addebiti aggiuntivi per la cache. Quando si esegue la replica in un account di archiviazione standard, questa archiviazione della cache è inclusa nello stesso account di archiviazione di destinazione.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sono un utente di Azure Site Recovery da più di un mese. Posso ottenere ancora i primi 31 giorni gratuiti per ogni istanza protetta?

Sì. Per ogni istanza protetta non vengono addebitati costi per Azure Site Recovery per i primi 31 giorni. Se, ad esempio, si è protetto 10 istanze negli ultimi 6 mesi e si connette un'undicesima istanza ad Azure Site Recovery, per i primi 31 giorni non verranno addebitati costi per l'undicesima istanza. Per le prime 10 istanze continueranno a venire addebitati i costi per Azure Site Recovery, in quanto sono state protette per più di 31 giorni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante i primi 31 giorni, sono previsti altri addebiti per Azure?

Sì, anche se Site Recovery è gratuito durante i primi 31 giorni di un'istanza protetta, possono venire addebitati i costi per Archiviazione di Azure, transazioni di archiviazione e trasferimento dei dati. Per una macchina virtuale ripristinata possono venire addebitati anche i costi di calcolo di Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>È previsto un costo per eseguire il failover di test o per le esercitazioni sul ripristino di emergenza?

Non sono previsti costi separati per le esercitazioni sul ripristino di emergenza. Sono previsti costi di calcolo dopo la creazione di una macchina virtuale in seguito al failover di test.



## <a name="security"></a>Sicurezza

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>I dati di replica vengono inviati al servizio Site Recovery?
No, Site Recovery non intercetta i dati replicati né raccoglie informazioni su ciò che è in esecuzione sulle macchine virtuali o sui server fisici.
I dati di replica vengono scambiati tra host Hyper-V, hypervisor VMware o server fisici e Archiviazione di Azure o il sito secondario. Site Recovery non è in grado di intercettare i dati. Al servizio Site Recovery vengono inviati solo i metadati necessari per gestire la replica e il failover.  

Site Recovery è certificato ISO 27001:2013, 27018, HIPAA e DPA e le valutazioni SOC2 e FedRAMP JAB sono in fase di completamento.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Per motivi di conformità, è necessario che anche i metadati locali restino all'interno della stessa area geografica. Site Recovery può offrire vantaggi?
Sì. Quando si crea un insieme di credenziali di Site Recovery in un'area, tutti i metadati necessari per abilitare e gestire la replica e il failover restano all'interno dei confini di tale area geografica.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery consente di crittografare la replica?
Per la replica di macchine virtuali e server fisici tra siti locali, è supportata la crittografia in transito. Per la replica di macchine virtuali e server fisici in Azure, sono supportate sia la crittografia in transito che la [crittografia inattiva (in Azure)](../storage/common/storage-service-encryption.md).

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Site Recovery da Azure ad Azure usa il protocollo TLS 1.2 per tutte le comunicazioni tra microservizi di Azure?
Sì, il protocollo TLS 1.2 viene applicato per impostazione predefinita per lo scenario Site Recovery da Azure ad Azure. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Come è possibile applicare il protocollo TLS 1.2 in scenari Site Recovery da VMware ad Azure e da server fisici ad Azure?
Gli agenti di mobilità installati negli elementi replicati comunicano con il server di elaborazione solo sul protocollo TLS 1.2. Tuttavia, la comunicazione dal server di configurazione ad Azure e dal server di elaborazione ad Azure può avvenire sul protocollo TLS 1.1 o 1.0. Per applicare il protocollo TLS 1.2 in tutti i server di configurazione e di elaborazione impostati dall'utente, seguire le indicazioni fornite nel [materiale sussidiario](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Come è possibile applicare il protocollo TLS 1.2 in scenari Site Recovery da HyperV ad Azure?
Tutte le comunicazioni tra i microservizi di Azure Site Recovery avvengono mediante il protocollo TLS 1.2. Site Recovery usa provider di sicurezza configurati nel sistema operativo e usa il protocollo TLS più recente disponibile. Sarà necessario abilitare in modo esplicito il protocollo TLS 1.2 nel registro perché Site Recovery inizi a usare TLS 1.2 per la comunicazione con i servizi. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Come è possibile applicare l'accesso limitato agli account di archiviazione, a cui si accede tramite Site Recovery servizio per la lettura e la scrittura dei dati di replica?
È possibile attivare l'identità gestita dell'insieme di credenziali di servizi di ripristino passando all'impostazione *Identity* . Una volta che l'insieme di credenziali viene registrato con Azure Active Directory, è possibile accedere agli account di archiviazione e assegnare le seguenti assegnazioni di ruolo all'insieme di credenziali:

- Account di archiviazione basati su Gestione risorse (tipo standard):
  - [Collaboratore](../role-based-access-control/built-in-roles.md#contributor)
  - [Collaboratore ai dati del BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Account di archiviazione basati su Gestione risorse (tipo Premium):
  - [Collaboratore](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietario dei dati del BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Account di archiviazione classici:
  - [Collaboratore account di archiviazione classico](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>Ripristino di emergenza

### <a name="what-can-site-recovery-protect"></a>Quali elementi può proteggere Site Recovery?
* **Macchine virtuali di Azure**: Site Recovery può replicare qualsiasi carico di lavoro in esecuzione in una macchina virtuale di Azure supportata
* **Macchine virtuali Hyper-V**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale Hyper-V.
* **Server fisici**: Site Recovery può proteggere i server fisici che eseguono Windows o Linux.
* **Macchine virtuali VMware**: Site Recovery può proteggere qualsiasi carico di lavoro in esecuzione in una macchina virtuale VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quali carichi di lavoro è possibile proteggere con Site Recovery?
È possibile usare Site Recovery per proteggere la maggior parte dei carichi di lavoro in esecuzione in una VM o in un server fisico supportati. Site Recovery fornisce il supporto per la replica compatibile con l'applicazione per consentire il ripristino delle app a uno stato intelligente. Si integra con le applicazioni Microsoft, ad esempio SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e opera a stretto contatto con importanti fornitori, tra cui Oracle, SAP, IBM e Red Hat. [Altre informazioni](site-recovery-workload.md) sulla protezione del carico di lavoro.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>È possibile gestire il ripristino di emergenza per le succursali con Site Recovery?
Sì. Quando si usa Site Recovery per gestire la replica e il failover nelle succursali, si ottiene una gestione unificata e vengono visualizzati tutti i carichi di lavoro delle succursali in una posizione centrale. È possibile eseguire facilmente i failover e amministrare il ripristino di emergenza di tutte le succursali senza abbandonare la sede centrale.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Il ripristino di emergenza è supportato per le macchine virtuali di Azure?

Sì, Site Recovery supporta il ripristino di emergenza di macchine virtuali di Azure tra aree di Azure. [Vedere le domande frequenti](azure-to-azure-common-questions.md) sul ripristino di emergenza di macchine virtuali di Azure. Per eseguire la replica tra due aree di Azure nello stesso continente, usare l'offerta Azure per il ripristino di emergenza di Azure. Non è necessario configurare il server di configurazione/elaborazione e le connessioni ExpressRoute.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Il ripristino di emergenza è supportato per le macchine virtuali VMware?

Sì, Site Recovery supporta il ripristino di emergenza di macchine virtuali VMware locali. [Vedere le domande frequenti](vmware-azure-common-questions.md) sul ripristino di emergenza di macchine virtuali VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Il ripristino di emergenza è supportato per le macchine virtuali Hyper-V?
Sì, Site Recovery supporta il ripristino di emergenza di macchine virtuali Hyper-V locali. [Vedere le domande frequenti](hyper-v-azure-common-questions.md) sul ripristino di emergenza di macchine virtuali Hyper-V.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>Il ripristino di emergenza è supportato per i server fisici?
Sì, Site Recovery supporta il ripristino di emergenza di server fisici locali che eseguono Windows e Linux in Azure o in un sito secondario. Informazioni sui requisiti per il ripristino di emergenza in [Azure](vmware-physical-azure-support-matrix.md#replicated-machines) e in un [sito secondario](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Si noti che i server fisici vengono eseguiti come macchine virtuali in Azure dopo il failover. Il failback da Azure a un server fisico locale non è attualmente supportato. È possibile eseguire il failback solo in una macchina virtuale VMware.





## <a name="replication"></a>Replica

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>È possibile replicare tramite una VPN da sito a sito in Azure?
Azure Site Recovery replica i dati in dischi gestiti o account di archiviazione di Azure su un endpoint pubblico. La replica non avviene tramite una rete VPN da sito a sito. 

### <a name="why-cant-i-replicate-over-vpn"></a>Perché non è possibile eseguire la replica su VPN?

Quando si esegue la replica in Azure, il traffico di replica raggiunge gli endpoint pubblici di Archiviazione di Azure. In questo modo è possibile eseguire la replica solo sulla rete Internet pubblica o tramite ExpressRoute (peering Microsoft o peering pubblico esistente).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>È possibile usare Riverbed SteelHead per la replica?

Il nostro partner, Riverbed, fornisce istruzioni dettagliate sull'uso di Azure Site Recovery. Vedere la loro [guida alla soluzione](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>È possibile usare ExpressRoute per replicare macchine virtuali in Azure?
Sì, [è possibile](concepts-expressroute-with-site-recovery.md).

- Azure Site Recovery replica i dati in una risorsa di Archiviazione di Azure su un endpoint pubblico. Per usare ExpressRoute per la replica con Site Recovery, è necessario configurare il [peering Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) o usare un [peering pubblico](../expressroute/about-public-peering.md) esistente (soluzione deprecata per i nuovi circuiti).
- Il peering Microsoft è il dominio di routing consigliato per la replica.
- La replica non è supportata su peering privato.
- Nel caso in cui si desideri proteggere computer VMware o computer fisici, verificare che i [requisiti di rete](vmware-azure-configuration-server-requirements.md#network-requirements) siano soddisfatti anche per il server di configurazione. Per l'orchestrazione della replica Site Recovery, il server di configurazione richiede la connettività a URL specifici. Non è possibile usare ExpressRoute per questa connettività.
- Dopo che è stato eseguito il failover delle macchine virtuali su una rete virtuale di Azure, è possibile accedere alle macchine virtuali usando la configurazione di [peering privato](../expressroute/expressroute-circuit-peerings.md#privatepeering) con la rete virtuale di Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se si esegue la replica in Azure, che tipo di account di archiviazione o disco gestito è necessario?

È necessario un account di archiviazione con ridondanza locale o con ridondanza geografica. È consigliabile usare l'archiviazione con ridondanza geografica per una maggiore resilienza dei dati in caso di interruzione del servizio a livello di area o se non è possibile recuperare l'area primaria. L'account deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino. Archiviazione Premium è supportato per la replica di macchine virtuali VMware e Hyper-V e di server fisici, quando si distribuisce Site Recovery nel portale di Azure. I dischi gestiti supportano solo l'archiviazione con ridondanza locale.

### <a name="how-often-can-i-replicate-data"></a>Con quale frequenza è possibile eseguire la replica dei dati?
* **Hyper-V:** Le macchine virtuali Hyper-V possono essere replicate ogni 30 secondi (eccetto per Archiviazione Premium), 5 minuti o 15 minuti.
* **Macchine virtuali di Azure, macchine virtuali VMware e server fisici:** in questo caso la frequenza di replica non è rilevante. La replica è continua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>È possibile estendere la replica dal sito di ripristino esistente a un sito terziario?
No, la replica concatenata o estesa non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>È possibile eseguire una replica offline la prima volta che si esegue la replica in Azure?
Questa funzionalità non è supportata. Richiedere questa funzionalità nel [forum dei commenti](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>È possibile escludere dischi specifici dalla replica?
Questa opzione è supportata quando si esegue la replica di macchine virtuali VMware e Hyper-V in Azure tramite il portale di Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>È possibile eseguire la replica delle macchine virtuali con i dischi dinamici?
I dischi dinamici sono supportati per la replica di macchine virtuali Hyper-V e quando si esegue la replica di macchine virtuali VMware e computer fisici in Azure. Il disco del sistema operativo deve essere un disco di base.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>È possibile applicare limitazioni della larghezza di banda allocata per il traffico di replica?
Sì. Altre informazioni sulla limitazione della larghezza di banda sono disponibili negli articoli seguenti:

* [Pianificazione della capacità per la replica da VM VMware e server fisici](site-recovery-plan-capacity-vmware.md)
* [Pianificazione della capacità per la replica da VM Hyper-V in Azure](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>È possibile abilitare la replica con la coerenza delle app nei server Linux? 
Sì. Azure Site Recovery per il sistema operativo Linux supporta gli script personalizzati dell'applicazione per la coerenza delle app. Lo script personalizzato con pre e post-Options verrà usato dall'agente di Azure Site Recovery Mobility durante la coerenza dell'app. Di seguito sono riportati i passaggi per abilitarlo.

1. Accedere come root nel computer.
2. Passare alla directory Azure Site Recovery percorso di installazione dell'agente di mobilità. Il valore predefinito è "/usr/local/ASR"<br>
    `# cd /usr/local/ASR`
3. Passa alla directory "VX/Scripts" in percorso di installazione<br>
    `# cd VX/scripts`
4. Creare uno script della shell bash denominato "customscript.sh" con le autorizzazioni di esecuzione per l'utente root.<br>
    a. Lo script deve supportare le opzioni della riga di comando "--pre" e "--post" (prendere nota dei trattini doppi)<br>
    b. Quando lo script viene chiamato con pre-Option, deve bloccare l'input/output dell'applicazione e, quando viene chiamato con post-opzione, deve scongelare l'input/output dell'applicazione.<br>
    c. Un modello di esempio-<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Aggiungere i comandi blocca e sblocca input/output nei passaggi precedenti e successivi per le applicazioni che richiedono la coerenza delle app. È possibile scegliere di aggiungere un altro script specificando questi e richiamarlo da "customscript.sh" con pre e post-Options.

>[!Note]
>Per supportare gli script personalizzati, la versione dell'agente di Site Recovery deve essere 9,24 o successiva.

## <a name="replication-policy"></a>Criteri di replica

### <a name="what-is-a-replication-policy"></a>Cosa sono i criteri di replica?

I criteri di replica definiscono le impostazioni per la cronologia di conservazione dei punti di recupero. Definiscono inoltre la frequenza degli snapshot coerenti con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con le impostazioni predefinite seguenti:

- 24 ore per la cronologia della conservazione dei punti di recupero.
- 4 ore per la frequenza degli snapshot coerenti con l'app.

### <a name="what-is-a-crash-consistent-recovery-point"></a>Che cos'è il punto di recupero coerente con l'arresto anomalo del sistema?

Un punto di recupero coerente con l'arresto anomalo del sistema dispone dei dati su disco come se fosse stato rimosso il cavo di alimentazione dal server durante lo snapshot. Non include tutto ciò che era contenuto in memoria quando è stato creato lo snapshot.

La maggior parte delle applicazioni esegue ora correttamente il ripristino da snapshot coerenti con l'arresto anomalo del sistema. Un punto di recupero coerente con l'arresto anomalo del sistema è in genere sufficiente per sistemi operativi senza database e per applicazioni quali file server, server DHCP e server di stampa.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual è la frequenza di generazione di punti di recupero coerenti nell'arresto anomalo del sistema?

Site Recovery crea un punto di recupero coerente con l'arresto anomalo del sistema ogni 5 minuti.

### <a name="what-is-an-application-consistent-recovery-point"></a>Che cos'è un punto di recupero coerente con l'applicazione?

I punti di recupero coerenti con l'applicazione vengono creati dagli snapshot coerenti con l'applicazione. I punti di recupero coerenti con l'applicazione acquisiscono gli stessi dati di snapshot coerenti con l'arresto anomalo del sistema, oltre a tutti i dati in memoria e a tutte le transazioni in corso.

Per via del loro contenuto aggiuntivo, gli snapshot coerenti con l'applicazione impiegano più tempo di esecuzione e sono i più usati. È consigliabile usare i punti di recupero coerenti con l'applicazione per sistemi operativi e applicazioni di database come SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual è l'impatto dei punti di recupero coerenti con l'applicazione sulle prestazioni dell'applicazione?

I punti di recupero coerenti con l'applicazione acquisiscono tutti i dati in memoria e in corso. Poiché i punti di recupero acquisiscono tali dati, richiedono framework come Servizio Copia Shadow del volume in Windows per disattivare l'applicazione. Se il processo di acquisizione è frequente, può influire sulle prestazioni quando il carico di lavoro è già elevato. Non è consigliabile usare una frequenza bassa per i punti di recupero coerenti con l'applicazione per carichi di lavoro non di database. Anche per il carico di lavoro del database, 1 ora è sufficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual è la frequenza minima di generazione di punti di recupero coerenti nell'arresto anomalo del sistema?

Site Recovery può creare un punto di recupero coerente con l'applicazione con una frequenza minima di 1 ora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Come vengono generati e salvati i punti di ripristino?

Per comprendere il modo in cui Site Recovery genera i punti di recupero, è possibile esaminare un esempio di criteri di replica. Questo criterio di replica dispone di un punto di recupero con un intervallo di conservazione di 24 ore e uno snapshot di frequenza coerente con l'applicazione di 1 ora.

Site Recovery crea un punto di recupero coerente con l'arresto anomalo del sistema ogni 5 minuti. L'utente non può modificare questa frequenza. Nell'ultima ora è possibile scegliere tra 12 punti coerenti con l'arresto anomalo del sistema e 1 punto coerente con l'applicazione. Col passare del tempo, Site Recovery elimina tutti i punti di recupero oltre l'ultima ora e salva solo 1 punto di recupero per ogni ora.

Lo screenshot seguente illustra l'esempio. Nello screenshot:

- Nell'ultima ora, sono disponibili punti di recupero con una frequenza di 5 minuti.
- Oltre l'ultima ora, Site Recovery conserva un solo punto di recupero.

   ![Elenco dei punti di recupero generati](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Fino a quando può risalire il recupero?

Il punto di recupero meno recente che è possibile usare è di 72 ore.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Ho un criterio di replica di 24 ore. Cosa accade se un problema impedisce a Site Recovery di generare punti di recupero per più di 24 ore? I precedenti punti di recupero verranno persi?

No, Site Recovery manterrà tutti i punti di recupero precedenti. A seconda dell'intervallo di conservazione dei punti di recupero, Site Recovery sostituisce il punto meno recente solo se genera nuovi punti. A causa del problema, Site Recovery non è in grado di generare nuovi punti di recupero. Fino a quando non saranno presenti nuovi punti di recupero, tutti i punti precedenti verranno conservati dopo aver raggiunto l'intervallo di conservazione.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Dopo aver abilitato la replica in una macchina virtuale, come si modifica il criterio di replica?

Passare a **Insieme di credenziali di Site Recovery** > **Infrastruttura di Site Recovery** > **Criteri di replica**. Selezionare i criteri da modificare e salvare le modifiche. Qualsiasi modifica verrà applicata anche a tutte le repliche esistenti.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Tutti i punti di recupero includono una copia completa della macchina virtuale o solo una copia differenziale?

Il primo punto di recupero che viene generato include la copia completa. I punti di recupero successivi includono le modifiche delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>L'aumento del periodo di conservazione dei punti di recupero comporta l'aumento dei costi di archiviazione?

Sì, se si aumenta il periodo di conservazione da 24 a 72 ore, Site Recovery salverà i punti di recupero per altre 48 ore. Il tempo aggiuntivo comporterà dei costi di archiviazione. Ad esempio, un singolo punto di recupero potrebbe avere variazioni delta di 10 GB con un costo per GB di $0,16 al mese. Gli addebiti aggiuntivi saranno $1,60 × 48 al mese.


## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se si esegue il failover in Azure, come è possibile accedere alle macchine virtuali di Azure dopo il failover?

È possibile accedere alle VM di Azure tramite una connessione Internet sicura, una connessione VPN da sito a sito o ExpressRoute di Azure. Per poter eseguire la connessione, è necessario completare una serie di operazioni. [Altre informazioni](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se si esegue il failover in Azure, in che modo Azure si assicura che i dati siano resilienti?
Azure è progettato nell'ottica della resilienza. Site Recovery è già predisposto per il failover a un data center secondario di Azure in conformità con il contratto di servizio di Azure. In questo caso, Microsoft si assicura che i metadati e gli insiemi di credenziali rimangano nella stessa area geografica selezionata per l'insieme di credenziali.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se si esegue la replica tra due data center, che cosa accade se nel data center principale si verifica un'interruzione imprevista?
È possibile attivare un failover non pianificato dal sito secondario. Per eseguire il failover, in Site Recovery non è necessaria la connettività dal sito primario.

### <a name="is-failover-automatic"></a>Il failover è automatico?
Il failover non è automatico. Le operazioni di failover si avviano con un singolo clic nel portale oppure mediante [PowerShell per Site Recovery](/powershell/module/az.recoveryservices) . Il failback è una semplice operazione nel portale di Site Recovery.

Per avviarlo in automatico, è possibile usare Orchestrator o Operations Manager locale per rilevare un errore della macchina virtuale e quindi attivare il failover tramite l'SDK.

* [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.
* [Altre informazioni](site-recovery-failover.md) sul failover.
* [Altre informazioni](./vmware-azure-failback.md) sul failback di VM VMware e server fisici

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se l'host locale non risponde o sperimenta un arresto anomalo del sistema, è possibile eseguire il failback a un host diverso?
Sì, è possibile usare il ripristino nel percorso alternativo per eseguire il failback in un host diverso da Azure.

* [Per macchine virtuali VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Per macchine virtuali Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automazione

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>È possibile automatizzare gli scenari di Site Recovery con un SDK?
Sì. È possibile automatizzare i flussi di lavoro di Site Recovery usando l'API Rest, PowerShell o Azure SDK. Scenari attualmente supportati per la distribuzione di Site Recovery tramite PowerShell:

* [Replica da VM Hyper-V nei cloud VMM ad Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replica da VM Hyper-V senza VMM ad Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replicare VMware in Azure con PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Aggiornamento componente/provider

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Dove si possono trovare le note sulla versione e gli aggiornamenti cumulativi di Site Recovery?

[Informazioni](site-recovery-whats-new.md) sui nuovi aggiornamenti e su come [ottenere informazioni sul rollup](service-updates-how-to.md).

## <a name="next-steps"></a>Passaggi successivi
* Leggere la [panoramica di Site Recovery](site-recovery-overview.md)