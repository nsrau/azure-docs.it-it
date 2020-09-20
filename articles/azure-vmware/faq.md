---
title: Domande frequenti
description: Fornisce le risposte ad alcune domande comuni sulla soluzione VMware di Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 7b4abc2d711a3da6a6df125854759e083d7e04a7
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817837"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-preview"></a>Domande frequenti sull'anteprima della soluzione VMware di Azure

Risposte alle domande frequenti sulla soluzione VMware di Azure.

## <a name="general"></a>Generale

### <a name="what-is-azure-vmware-solution"></a>Che cos'è la soluzione Azure VMware?

Con la ricerca da parte delle organizzazioni di strategie di modernizzazione IT in grado di migliorare l'agilità aziendale, ridurre i costi e accelerare l'innovazione, le piattaforme di cloud ibrido sono diventate elementi determinanti per la trasformazione digitale dei clienti. La soluzione VMware di Azure combina il software SDDC (software defined Data Center) di VMware con Microsoft Azure ecosistema globale del servizio cloud. La soluzione VMware di Azure è gestita per soddisfare i requisiti di prestazioni, disponibilità, sicurezza e conformità.

## <a name="azure-vmware-solution-service"></a>Servizio della soluzione VMware di Azure

### <a name="where-is-azure-vmware-solution-available-today"></a>Dove è attualmente disponibile la soluzione VMware di Azure?

Il servizio viene continuamente aggiunto alle nuove aree, quindi è possibile visualizzare le [informazioni più recenti sulla disponibilità del servizio](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) per altri dettagli. 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>I carichi di lavoro in esecuzione in un'istanza di soluzione VMware di Azure utilizzano o si integrano con i servizi di Azure?

Tutti i servizi di Azure saranno disponibili per i clienti della soluzione VMware di Azure. Le limitazioni relative alle prestazioni e alla disponibilità di servizi specifici dovranno essere risolte caso per caso.

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Si usano gli stessi strumenti attualmente usati per gestire le risorse dei cloud privati?

Sì. Il portale di Azure viene usato per la distribuzione e per diverse operazioni di gestione. vCenter e NSX Manager vengono usati per gestire le risorse vSphere e NSX-T.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>È possibile gestire un cloud privato con un'istanza locale di vCenter?

All'avvio, la soluzione VMware di Azure non supporterà una singola esperienza di gestione in ambienti locali e cloud privati. I cluster dei cloud privati verranno gestiti con istanze di vCenter e NSX Manager locali rispetto al cloud privato.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>È possibile usare vRealize Suite in esecuzione in locale? 

Potranno essere valutati caso per caso integrazioni e casi d'uso specifici.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>È possibile eseguire la migrazione di macchine virtuali vSphere da ambienti locali a cloud privati della soluzione VMware di Azure?

Sì. È possibile usare la migrazione di VM e vMotion per spostare le macchine virtuali in un cloud privato, se sono soddisfatti i [requisiti](https://kb.vmware.com/s/article/210695) standard per la virtualizzazione Cross vCenter.

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>È necessaria una versione specifica di vSphere negli ambienti locali?

Dato che tutti gli ambienti cloud includono HCX, è necessario vSphere versione 5.5 o successiva negli ambienti locali per vMotion.

### <a name="what-does-the-change-control-process-look-like"></a>Come si presenta il processo di controllo delle modifiche?

Gli aggiornamenti del servizio seguiranno il processo di gestione delle modifiche standard di Microsoft Azure. I clienti sono responsabili di tutte le attività di amministrazione del carico di lavoro e dei processi di gestione delle modifiche associati.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Quali sono le differenze rispetto alla soluzione Azure VMware di CloudSimple?

Con la nuova soluzione Azure VMware, Microsoft e VMware hanno una partnership diretta di provider di servizi cloud. La nuova soluzione è interamente progettata, compilata e supportata da Microsoft e approvata da VMware. A livello di architettura, le soluzioni sono coerenti con lo stack di tecnologia VMware eseguito in un'infrastruttura dedicata di Azure.

### <a name="if-im-an-existing-azure-vmware-solution-customer-what-does-this-preview-mean-for-me"></a>Che cosa comporta questa anteprima per gli attuali clienti della soluzione Azure VMware?

Non sono state apportate modifiche alla soluzione Azure VMware di CloudSimple esistente, che continua a essere supportata in Azure. La soluzione Azure VMware è supportata dal [contratto di servizio](https://aka.ms/CSVMwareSLA) Microsoft. I clienti dovrebbero continuare a usare il servizio per i carichi di lavoro di produzione. Si tratta di una soluzione disponibile, regolata dalle [condizioni del servizio Microsoft](https://azure.microsoft.com/support/legal/).

### <a name="can-i-migrate-from-azure-vmware-solution-by-cloudsimple-to-this-new-solution"></a>È possibile eseguire la migrazione dalla soluzione Azure VMware di CloudSimple a questa nuova soluzione?

Sì. La soluzione Azure VMware supporta la migrazione con strumenti VMware noti come HCX. Per i clienti interessati alla migrazione alla nuova soluzione, collaborare con il team di account Microsoft per esplorare le opzioni e il supporto disponibile.



## <a name="compute-network-and-storage"></a>Calcolo, rete e archiviazione

### <a name="is-there-more-than-one-type-of-host-available"></a>Sono disponibili più tipi di host?

È disponibile un solo tipo di host.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Quali sono le specifiche delle CPU in ogni tipo di host?

I server sono dotati di CPU dual core Intel a 18 core a 2,3 GHz.

### <a name="how-much-memory-is-in-each-host"></a>Quanta memoria è disponibile in ogni host?

I server sono dotati di 576 GB di RAM.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Qual è la capacità di archiviazione di ogni host?

Ogni host ESXi ha due diskgroups rete VSAN con un livello di capacità di 15,2 TB e un livello di cache di 3,2 TB NVMe (1,6 TB in ogni gruppo dei dischi).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Quanta larghezza di banda di rete è disponibile in ogni host ESXi?

Ogni host ESXi è una soluzione VMware di Azure configurata con schede NIC da 4 25 Gbps, con due schede di rete di cui è stato effettuato il provisioning per il traffico di sistema ESXi e due NIC di cui è stato eseguito il 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>I dati archiviati negli archivi dati rete VSAN sono crittografati?

Sì, tutti i dati rete VSAN vengono crittografati per impostazione predefinita usando le chiavi archiviate in Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Host, cluster e cloud privati

### <a name="is-the-underlying-infrastructure-shared"></a>L'infrastruttura sottostante è condivisa?

No. Gli host e i cluster dei cloud privati sono dedicati e cancellati in modo sicuro prima e dopo l'uso.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Quali sono il numero minimo e quello massimo di host per cluster?

I cluster sono scalabili tra 3 e 16 host ESXi. I cluster di prova sono limitati a tre host.

### <a name="can-i-scale-my-private-cloud-clusters"></a>I cluster dei cloud privati sono scalabili?

Sì. I cluster sono scalabili tra il numero minimo e il numero massimo di host ESXi. I cluster di prova sono limitati a tre host.

### <a name="what-are-trial-clusters"></a>Che cosa sono i cluster di prova?

I cluster di valutazione sono tre cluster host usati per la valutazione di un mese dei cloud privati della soluzione VMware di Azure.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>È possibile usare host di fascia alta per i cluster di prova?

No. Gli host ESXi di fascia alta sono riservati per l'uso in cluster di produzione.

## <a name="azure-vmware-solution-and-vmware-software"></a>Soluzione VMware di Azure e software VMware

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Quali versioni del software VMware vengono usate nei cloud privati?

I cloud privati usano vSphere 6.7, vSAN 6.7, HCX e la versione 2.5 di NSX-T.  

### <a name="do-private-clouds-use-vmware-nsx"></a>I cloud privati usano VMware NSX?

Sì, NSX-T 2,5 viene usato per il Software Defined Networking in cloud privati della soluzione VMware di Azure.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>È possibile usare VMware NSX-V in un cloud privato?

No. L'unica versione supportata di NSX è NSX-T.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>NSX è necessario in ambienti locali o in reti che si connettono a un cloud privato?

No. Non è necessario usare NSX in locale.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Come sono pianificati gli aggiornamenti del software VMware in un cloud privato?

Gli aggiornamenti del bundle software del cloud privato vengono eseguiti per preservare il software all'interno di una versione della versione più recente del bundle software da VMware. Le versioni del software del cloud privato possono essere diverse dalle versioni più recenti dei singoli componenti software (ESXi, NSX-T, vCenter, rete VSAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Con quale frequenza verrà aggiornato lo stack software del cloud privato?

Il software del cloud privato viene aggiornato in base a una pianificazione allineata al rilascio del bundle software da VMware. Gli aggiornamenti non comportano tempi di inattività per il cloud privato.

## <a name="connectivity"></a>Connettività

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Quale pianificazione degli indirizzi IP di rete è necessaria per incorporare i cloud privati con gli ambienti locali?

Per distribuire un cloud privato della soluzione VMware di Azure, è necessario uno spazio di indirizzi di rete privata/22. Questo spazio indirizzi privato non deve sovrapporsi alle altre reti virtuali in una sottoscrizione o alle reti locali.
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Ricerca per categorie connettersi da ambienti locali a un cloud privato della soluzione VMware di Azure?

È possibile connettersi al servizio con uno dei due metodi seguenti: 

- Con una VM o un gateway applicazione distribuito in una rete virtuale di Azure con peering al cloud privato tramite ExpressRoute.
- Tramite il servizio Copertura globale di ExpressRoute, dal data center locale a un circuito di Azure ExpressRoute.

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Come si connette una VM del carico di lavoro a Internet o a un endpoint di servizio di Azure?

Nel portale di Azure abilitare la connettività Internet per un cloud privato. Con NSX-T Manager, creare un router NSX-T T1 e un commutatore logico. Usare quindi vCenter per distribuire una VM nel segmento di rete definito dal commutatore logico. Tale macchina virtuale avrà l'accesso di rete a Internet e ai servizi di Azure.

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>È necessario limitare l'accesso da Internet alle VM nelle reti logiche di un cloud privato?

No. Il traffico di rete in ingresso diretto da Internet ai cloud privati non è consentito.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>È necessario limitare l'accesso a Internet dalle VM nelle reti logiche?

Sì. Sarà necessario usare NSX-T Manager per creare un firewall che limiti l'accesso delle VM a Internet.

## <a name="accounts-and-privileges"></a>Account e privilegi

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Quali account e privilegi si ottengono con il nuovo cloud privato della soluzione VMware di Azure?

Si ricevono le credenziali di un utente cloudadmin in vCenter e l'accesso come amministratore in NSX-T Manager. È anche disponibile un gruppo CloudAdmin che può essere usato per incorporare Azure Active Directory. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

### <a name="can-have-administrator-access-to-esxi-hosts"></a>È possibile avere l'accesso come amministratore agli host ESXi?

No. L'accesso come amministratore a ESXi è limitato per soddisfare i requisiti di sicurezza della soluzione.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Quali privilegi e autorizzazioni si avranno in vCenter?

Si avranno i privilegi del gruppo CloudAdmin. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Quali privilegi e autorizzazioni si avranno in NSX-T Manager?

Si avranno privilegi di amministratore completi in NSX-T e la possibilità di gestire il controllo degli accessi in base al ruolo così come con NSX-T Data Center in locale. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

> [!NOTE]
> Come parte della distribuzione di un cloud privato viene creato e configurato un router T0. Qualsiasi modifica a tale router logico o alle VM dei nodi perimetrali NSX-T influirà sulla connettività al cloud privato.

## <a name="billing-and-support"></a>Fatturazione e supporto

### <a name="how-will-i-be-billed-during-the-preview-of-azure-vmware-solution"></a>Come verrà fatturato durante l'anteprima della soluzione VMware di Azure?

La fatturazione per la soluzione VMware di Azure durante la fase di anteprima è mensile con pagamento in base al consumo. Altre opzioni saranno disponibili con il passaggio alla disponibilità generale.

### <a name="how-will-pricing-be-structured-during-the-preview-of-azure-vmware-solution"></a>In che modo verranno strutturati i prezzi durante l'anteprima della soluzione VMware di Azure?

Per domande generali sui prezzi, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/azure-vmware) della soluzione Azure VMware. I prezzi per l'anteprima sono disponibili su richiesta, contatta il team dell'account o Segui il collegamento nella pagina dei prezzi per contattare le vendite.

### <a name="who-supports-azure-vmware-solution"></a>Chi supporta la soluzione VMware di Azure?

Il supporto per la soluzione VMware di Azure viene fornito da Microsoft. Si noti che, in base alle linee guida di anteprima, si fornirà il supporto per i giorni lavorativi PST da 9 a 5 PM dal lunedì al venerdì. È possibile aprire un ticket di supporto da [questo collegamento](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Quali account sono necessari per creare un cloud privato della soluzione VMware di Azure?

Sarà necessario un account Azure in una sottoscrizione di Azure.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Ricerca per categorie richiedere un aumento della quota host per la soluzione VMware di Azure?

* È necessario un [Enterprise Agreement di Azure (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) con Microsoft.
* Sarà necessario un account Azure in una sottoscrizione di Azure.

Prima di creare la risorsa della soluzione VMware di Azure, è necessario inviare un ticket di supporto per l'allocazione dei nodi. Una volta ricevuta la richiesta, il team di supporto richiede fino a cinque giorni lavorativi per confermare la richiesta e allocare i nodi. Se si dispone di un cloud privato della soluzione VMware di Azure esistente e si vogliono allocare più nodi, si procederà nello stesso processo.


1. Nella portale di Azure in Guida e **supporto tecnico**creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
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
   ```

   Per informazioni su come registrare il provider di risorse in altri modi, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
