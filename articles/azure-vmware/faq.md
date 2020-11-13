---
title: Domande frequenti
description: Fornisce le risposte ad alcune domande comuni sulla soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: dikamath
ms.openlocfilehash: 99e9e53693e42f907250331894f55ed0160f9e30
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577516"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Domande frequenti sulla soluzione VMware di Azure

Risposte alle domande frequenti sulla soluzione VMware di Azure.

## <a name="general"></a>Generale

#### <a name="what-is-azure-vmware-solution"></a>Che cos'è la soluzione Azure VMware?

Quando le aziende sviluppano strategie di modernizzazione per migliorare l'agilità aziendale, ridurre i costi e accelerare l'innovazione, le piattaforme cloud ibride sono emerse come gli abilitatori chiave della trasformazione digitale dei clienti. La soluzione VMware di Azure combina il software Software-Defined Data Center (SDDC) di VMware con Microsoft Azure ecosistema globale del servizio cloud. La soluzione VMware di Azure è gestita per soddisfare i requisiti di prestazioni, disponibilità, sicurezza e conformità.

## <a name="azure-vmware-solution-service"></a>Servizio della soluzione VMware di Azure

#### <a name="where-is-azure-vmware-solution-available-today"></a>Dove è attualmente disponibile la soluzione VMware di Azure?

Il servizio viene continuamente aggiunto alle nuove aree, quindi è possibile visualizzare le [informazioni più recenti sulla disponibilità del servizio](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) per altri dettagli. 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>I carichi di lavoro in esecuzione in un'istanza di soluzione VMware di Azure utilizzano o si integrano con i servizi di Azure?

Tutti i servizi di Azure saranno disponibili per i clienti della soluzione VMware di Azure. Le limitazioni relative alle prestazioni e alla disponibilità di servizi specifici dovranno essere risolte caso per caso.

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Si usano gli stessi strumenti attualmente usati per gestire le risorse dei cloud privati?

Sì. Il portale di Azure viene usato per la distribuzione e per diverse operazioni di gestione. vCenter e NSX Manager vengono usati per gestire le risorse vSphere e NSX-T.

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>È possibile gestire un cloud privato con un'istanza locale di vCenter?

All'avvio, la soluzione VMware di Azure non supporterà una singola esperienza di gestione in ambienti locali e cloud privati. I cluster dei cloud privati verranno gestiti con istanze di vCenter e NSX Manager locali rispetto al cloud privato.

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>È possibile usare vRealize Suite in esecuzione in locale? 

Potranno essere valutati caso per caso integrazioni e casi d'uso specifici.

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>È possibile eseguire la migrazione di macchine virtuali vSphere da ambienti locali a cloud privati della soluzione VMware di Azure?

Sì. È possibile usare la migrazione di VM e vMotion per spostare le macchine virtuali in un cloud privato, se sono soddisfatti i [requisiti](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) standard per la virtualizzazione Cross vCenter.

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>È necessaria una versione specifica di vSphere negli ambienti locali?

Tutti gli ambienti cloud sono dotati di VMware HCX, vSphere 5,5 o versioni successive negli ambienti locali per vMotion.

#### <a name="what-does-the-change-control-process-look-like"></a>Come si presenta il processo di controllo delle modifiche?

Gli aggiornamenti apportati al servizio stesso seguiranno il processo di gestione delle modifiche standard di Microsoft Azure. I clienti sono responsabili di tutte le attività di amministrazione del carico di lavoro e dei processi di gestione delle modifiche associati.

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Quali sono le differenze rispetto alla soluzione Azure VMware di CloudSimple?

Con la nuova soluzione Azure VMware, Microsoft e VMware hanno una partnership diretta di provider di servizi cloud. La nuova soluzione è interamente progettata, compilata e supportata da Microsoft e approvata da VMware. Con l'architettura, le soluzioni sono coerenti con lo stack di tecnologie VMware in esecuzione su un'infrastruttura di Azure dedicata.



#### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Le macchine virtuali della soluzione VMware di Azure possono essere gestite da VMRC?
Sì, a condizione che il sistema su cui è installato possa accedere al cloud privato vCenter e usa DNS pubblico per risolvere i nomi host ESXi.

#### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Sono disponibili istruzioni speciali per l'installazione e l'uso di VMRC con le VM della soluzione VMware di Azure?
No, usare le [istruzioni fornite da VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html) e soddisfare i prerequisiti della macchina virtuale specificati in tali istruzioni. 

#### <a name="is-vmware-hcx-supported-on-vpns"></a>VMware HCX è supportato nelle VPN?
No, a causa dei requisiti di larghezza di banda e latenza.

#### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>È possibile usare Azure Bastion per la connessione alle macchine virtuali della soluzione VMware di Azure?
Azure Bastion è il servizio consigliato per connettersi alla casella di salto per impedire l'esposizione della soluzione VMware di Azure a Internet. Non è possibile usare Azure Bastion per connettersi alle macchine virtuali della soluzione VMware di Azure perché non sono oggetti IaaS di Azure.

#### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Può Azure Load Balancer essere usato internamente per le macchine virtuali della soluzione VMware di Azure?
No. Azure Load Balancer Internal supporta solo le macchine virtuali IaaS di Azure. Azure Load Balancer non supporta i pool back-end basati su IP; solo le macchine virtuali di Azure o gli oggetti del set di scalabilità di macchine virtuali in cui le macchine virtuali di Azure VMware

#### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>È possibile usare un gateway ExpressRoute esistente per connettersi alla soluzione VMware di Azure?
Sì, è possibile usare un gateway ExpressRoute esistente per connettersi alla soluzione VMware di Azure, purché non superi il limite di quattro circuiti ExpressRoute per ogni rete virtuale.  Tuttavia, per accedere alla soluzione Azure VMware dall'ambiente locale tramite ExpressRoute è necessario avere Copertura globale ExpressRoute, in quanto il gateway ExpressRoute non fornisce il routing transitivo tra i circuiti connessi.

## <a name="compute-network-storage-and-backup"></a>Calcolo, rete, archiviazione e backup

#### <a name="is-there-more-than-one-type-of-host-available"></a>Sono disponibili più tipi di host?

È disponibile un solo tipo di host.

#### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Quali sono le specifiche delle CPU in ogni tipo di host?

I server sono dotati di CPU dual core Intel a 18 core a 2,3 GHz.

#### <a name="how-much-memory-is-in-each-host"></a>Quanta memoria è disponibile in ogni host?

I server sono dotati di 576 GB di RAM.

#### <a name="what-is-the-storage-capacity-of-each-host"></a>Qual è la capacità di archiviazione di ogni host?

Ogni host ESXi ha due diskgroups rete VSAN con un livello di capacità di 15,2 TB e un livello di cache NVMe di 3,2 TB (1,6 TB in ogni gruppo dei dischi).

#### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Quanta larghezza di banda di rete è disponibile in ogni host ESXi?

Ogni host ESXi della soluzione VMware di Azure è configurato con NIC da 4 25 Gbps, due schede di rete di cui è stato effettuato il provisioning per il traffico di sistema ESXi e due NIC con provisioning per il traffico del carico 

#### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>I dati archiviati negli archivi dati rete VSAN sono crittografati?

Sì, tutti i dati rete VSAN vengono crittografati per impostazione predefinita usando le chiavi archiviate in Azure Key Vault.

#### <a name="you-document-that-commvault-veritas-and-veeam-have-extended-their-backup-solutions-to-work-with-azure-vmware-solution-what-about-other-independent-software-vendor-isv-backup-solutions"></a>Si documenta che CommVault, Veritas e Veeam hanno esteso le proprie soluzioni di backup per l'uso con la soluzione VMware di Azure. Quali sono le soluzioni di backup di fornitori di software indipendenti (ISV)?

Per quanto sappiamo, qualsiasi soluzione di backup che usa VMware VADP con la modalità di trasporto HotAdd dovrebbe funzionare direttamente nella soluzione VMware di Azure.

#### <a name="what-about-support-for-isv-backup-solutions"></a>Per quanto riguarda il supporto per le soluzioni di backup ISV?

Poiché queste soluzioni di backup sono installate e gestite dai clienti, possono contattare il rispettivo ISV per il supporto. 

#### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Quali sono i criteri di archiviazione corretti per l'installazione del deduplicato?

Usare i criteri di archiviazione *thin_provision* per il modello di macchina virtuale.  Il valore predefinito è *thick_provision*.

#### <a name="are-the-snmp-infrastructure-logs-shared"></a>I log dell'infrastruttura SNMP sono condivisi?

No.

## <a name="hosts-clusters-and-private-clouds"></a>Host, cluster e cloud privati

#### <a name="is-the-underlying-infrastructure-shared"></a>L'infrastruttura sottostante è condivisa?

No. Gli host e i cluster dei cloud privati sono dedicati e cancellati in modo sicuro prima e dopo l'uso.

#### <a name="what-are-the-minimum-and-the-maximum-number-of-hosts-per-cluster"></a>Qual è il numero minimo e massimo di host per cluster?

I cluster sono scalabili tra 3 e 16 host ESXi. I cluster di prova sono limitati a tre host.

#### <a name="can-i-scale-my-private-cloud-clusters"></a>I cluster dei cloud privati sono scalabili?

Sì, i cluster vengono ridimensionati tra il numero minimo e il numero massimo di host ESXi. I cluster di prova sono limitati a tre host.

#### <a name="what-are-trial-clusters"></a>Che cosa sono i cluster di prova?

I cluster di valutazione sono tre cluster host usati per le valutazioni di un mese per i cloud privati della soluzione VMware di Azure.

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>È possibile usare host di fascia alta per i cluster di prova?

No. Gli host ESXi di fascia alta sono riservati per l'uso in cluster di produzione.

## <a name="azure-vmware-solution-and-vmware-software"></a>Soluzione VMware di Azure e software VMware

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Quali versioni del software VMware vengono usate nei cloud privati?

I cloud privati usano vSphere 6,7, rete VSAN 6,7, VMware HCX e la versione 2,5 di NSX-T.  

#### <a name="do-private-clouds-use-vmware-nsx"></a>I cloud privati usano VMware NSX?

Sì, NSX-T 2,5 viene usato per la rete definita dal software in cloud privati della soluzione VMware di Azure.

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>È possibile usare VMware NSX-V in un cloud privato?

No. L'unica versione supportata di NSX è NSX-T.

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>NSX è necessario in ambienti locali o in reti che si connettono a un cloud privato?

No. Non è necessario usare NSX in locale.

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Come sono pianificati gli aggiornamenti del software VMware in un cloud privato?

Gli aggiornamenti del bundle software del cloud privato vengono eseguiti per preservare il software all'interno di una versione della versione più recente del bundle software da VMware. Le versioni del software del cloud privato possono essere diverse dalle versioni più recenti dei singoli componenti software (ESXi, NSX-T, vCenter, rete VSAN).

#### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Con quale frequenza verrà aggiornato lo stack software del cloud privato?

Il software per il cloud privato viene aggiornato in base a una pianificazione che tiene traccia del rilascio del bundle software da VMware. Gli aggiornamenti non comportano tempi di inattività per il cloud privato.

## <a name="connectivity"></a>Connettività

#### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Quale pianificazione degli indirizzi IP di rete è necessaria per incorporare i cloud privati con gli ambienti locali?

Per distribuire un cloud privato della soluzione VMware di Azure, è necessario uno spazio di indirizzi di rete privata/22. Questo spazio di indirizzi privato non deve sovrapporsi ad altre reti virtuali in una sottoscrizione o con reti locali.
 
#### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Ricerca per categorie connettersi da ambienti locali a un cloud privato della soluzione VMware di Azure?

È possibile connettersi al servizio con uno dei due metodi seguenti: 

- Con una VM o un gateway applicazione distribuito in una rete virtuale di Azure con peering al cloud privato tramite ExpressRoute.
- Tramite ExpressRoute Copertura globale dalla data center locale a un circuito ExpressRoute di Azure.

#### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Come si connette una VM del carico di lavoro a Internet o a un endpoint di servizio di Azure?

Nel portale di Azure abilitare la connettività Internet per un cloud privato. Con NSX-T Manager, creare un router NSX-T T1 e un commutatore logico. Usare quindi vCenter per distribuire una VM nel segmento di rete definito dal commutatore logico. Tale macchina virtuale avrà l'accesso di rete a Internet e ai servizi di Azure.

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>È necessario limitare l'accesso da Internet alle VM nelle reti logiche di un cloud privato?

No. Il traffico di rete in ingresso diretto da Internet ai cloud privati non è consentito.

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>È necessario limitare l'accesso a Internet dalle VM nelle reti logiche?

Sì. Sarà necessario usare NSX-T Manager per creare un firewall che limiti l'accesso delle VM a Internet.


#### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>La soluzione VMware di Azure può usare i gateway ExpressRoute ospitati WAN virtuali di Azure?
Sì.

#### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>È possibile stabilire la connettività di transito tra la soluzione locale e la soluzione VMware di Azure tramite la rete WAN virtuale di Azure tramite ExpressRoute Copertura globale?
La rete WAN virtuale di Azure non fornisce il routing transitivo tra due circuiti ExpressRoute connessi e un gateway ExpressRoute WAN non virtuale. L'uso di ExpressRoute Copertura globale consente la connettività tra la soluzione locale e la soluzione VMware di Azure, ma passa attraverso la rete globale di Microsoft anziché l'hub WAN virtuale.


## <a name="accounts-and-privileges"></a>Account e privilegi

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Quali account e privilegi si ottengono con il nuovo cloud privato della soluzione VMware di Azure?

Si ricevono le credenziali di un utente cloudadmin in vCenter e l'accesso come amministratore in NSX-T Manager. È anche disponibile un gruppo CloudAdmin che può essere usato per incorporare Azure Active Directory. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>È possibile avere l'accesso come amministratore agli host ESXi?

No. L'accesso come amministratore a ESXi è limitato per soddisfare i requisiti di sicurezza della soluzione.

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Quali privilegi e autorizzazioni si avranno in vCenter?

Si avranno i privilegi del gruppo CloudAdmin. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Quali privilegi e autorizzazioni si avranno in NSX-T Manager?

Si avranno privilegi di amministratore completi in NSX-T e la possibilità di gestire il controllo degli accessi in base al ruolo così come con NSX-T Data Center in locale. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

> [!NOTE]
> Come parte della distribuzione di un cloud privato viene creato e configurato un router T0. Qualsiasi modifica a tale router logico o alle VM dei nodi perimetrali NSX-T influirà sulla connettività al cloud privato.

## <a name="billing-and-support"></a>Fatturazione e supporto

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>In che modo verranno strutturati i prezzi per la soluzione VMware di Azure?

Per domande generali sui prezzi, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/azure-vmware) della soluzione VMware di Azure. 

#### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>È possibile acquistare una soluzione VMware di Azure tramite un CSP Microsoft?

Sì, i clienti possono distribuire una soluzione VMware di Azure in una sottoscrizione di Azure gestita da un CSP.

#### <a name="who-supports-azure-vmware-solution"></a>Chi supporta la soluzione VMware di Azure?

Microsoft offre supporto per la soluzione VMware di Azure. È possibile inviare una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). 

Per le sottoscrizioni gestite da CSP, il primo livello di supporto viene fornito dal provider di soluzioni nello stesso modo in cui CSP esegue per altri servizi di Azure.

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Quali account sono necessari per creare un cloud privato della soluzione VMware di Azure?

Sarà necessario un account Azure in una sottoscrizione di Azure.

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Le soluzioni Red Hat sono supportate nella soluzione VMware di Azure?

Microsoft e Red Hat condividono un team di supporto integrato che fornisce un punto di contatto unificato per gli ecosistemi Red Hat in esecuzione nella piattaforma Azure.  Analogamente ad altri servizi della piattaforma Azure che funzionano con Red Hat Enterprise Linux, la soluzione VMware di Azure rientra nell'accesso al cloud e nel supporto integrato e Red Hat Enterprise Linux è supportata per l'esecuzione su una soluzione VMware di Azure in Azure.

#### <a name="is-vmware-hcx-enterprise-edition-available-and-if-so-how-much-does-it-cost"></a>VMware HCX Enterprise Edition è disponibile e, in caso affermativo, qual è il costo?

VMware HCX Enterprise Edition (EE) è disponibile con la soluzione Azure VMware come funzione/servizio in *anteprima*. Mentre la soluzione VMware HCX EE per Azure VMware è in anteprima, si tratta di una funzione/servizio gratuita, soggetta a termini e condizioni del servizio in anteprima. Dopo il passaggio del servizio VMware HCX EE alla disponibilità generale, si riceverà un preavviso di 30 giorni relativo al passaggio alla fatturazione. È possibile disattivare o rifiutare esplicitamente il servizio.

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Ricerca per categorie richiedere un aumento della quota host per la soluzione VMware di Azure?

Per le sottoscrizioni gestite da CSP, il cliente deve eseguire la richiesta al partner e il team partner collaborerà con Microsoft per aumentare la quota per la sottoscrizione. 

Per le sottoscrizioni EA, il cliente deve seguire questa procedura.

* È necessario un [Enterprise Agreement di Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) con Microsoft.
* Sarà necessario un account Azure in una sottoscrizione di Azure.

Prima di creare la risorsa della soluzione VMware di Azure, è necessario inviare un ticket di supporto per allocare i nodi. Sono necessari fino a cinque giorni lavorativi per confermare la richiesta e allocare i nodi. Se è disponibile un cloud privato della soluzione Azure VMware ed è necessaria l'allocazione di altri nodi, sarà necessario seguire lo stesso processo.


1. Nella portale di Azure in Guida e **supporto tecnico** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
   - **Tipo di problema:** Tecnici
   - **Sottoscrizione:** Selezionare la sottoscrizione
   - **Servizio:** Tutti i servizi > soluzione VMware di Azure
   - **Risorsa:** Domanda generale 
   - **Riepilogo:** Capacità necessaria
   - **Tipo di problema:** Capacity Management Issues (Problemi di gestione della capacità)
   - **Sottotipo del problema:** Customer Request for Additional Host Quota/Capacity (Richiesta del cliente di capacità/quota host aggiuntiva)

1. Nella **Descrizione** del ticket di supporto, nella scheda **Dettagli** , fornire:

   - POC o produzione 
   - Nome area
   - Numero di nodi
   - Altri dettagli

   >[!NOTE]
   >La soluzione VMware di Azure consiglia un minimo di tre nodi per avviare il cloud privato e per la ridondanza di N + 1 nodi. 

1. Selezionare **Verifica + crea** per inviare la richiesta.

   Per confermare la richiesta, l'addetto al supporto richiederà fino a cinque giorni lavorativi.

   >[!IMPORTANT] 
   >Se si dispone già di una soluzione VMware di Azure esistente e si richiedono nodi aggiuntivi, si noti che per l'allocazione dei nodi sono necessari cinque giorni lavorativi. 

1. Prima di poter effettuare il provisioning dei nodi, assicurarsi di registrare il provider di risorse **Microsoft. AVS** nella portale di Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>

   For additional ways to register the resource provider, see [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md). 

#### Are Reserved Instances available for purchasing through the Cloud Solution Provider (CSP) program?

Yes. CSP can purchase reserved instances on behalf of their customers, see [Save costs with a reserved instance](reserved-instance.md) article. 


## Customer communication

#### How can I receive an alert when Azure sends service health notifications to my Azure subscription?

Service issues, planned maintenance, health advisories, security advisories notifications are published through **Service Health** in the Azure portal.  You can take timely actions when you set up activity log alerts for these notifications. For more information, see [Create service health alerts using the Azure portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Screenshot of Service Health notifications":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md