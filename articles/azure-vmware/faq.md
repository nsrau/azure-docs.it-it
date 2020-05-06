---
title: Domande frequenti
description: Fornisce le risposte ad alcune domande comuni su Azure VMware Solution (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: b3643372068df9a5c9ed6d892f8417257eecfe92
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740460"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Domande frequenti sull'anteprima della soluzione VMware di Azure (AVS)

Risposte alle domande frequenti sulla soluzione VMware di Azure (AVS).

## <a name="general"></a>Generale

**Che cos'è la soluzione Azure VMware (AVS)?**

Quando le aziende sviluppano strategie di modernizzazione per migliorare l'agilità aziendale, ridurre i costi e accelerare l'innovazione, le piattaforme cloud ibride sono emerse come gli abilitatori chiave della trasformazione digitale dei clienti. AVS combina il software SDDC (software defined Data Center) di VMware con Microsoft Azure ecosistema globale del servizio cloud. La soluzione AVS è gestita per soddisfare i requisiti di prestazioni, disponibilità, sicurezza e conformità.

## <a name="avs-service"></a>Servizio AVS

**Dove è attualmente disponibile AVS?**

Durante la fase di anteprima, è disponibile negli Stati Uniti orientali in America del Nord e in Amsterdam nell'Europa occidentale.

**I carichi di lavoro in esecuzione in un'istanza di soluzione VMware di Azure (AVS) utilizzano o si integrano con i servizi di Azure?**

Tutti i servizi di Azure saranno disponibili per i clienti della soluzione AVS. Le limitazioni relative a prestazioni e disponibilità per servizi specifici devono essere risolte in base alla situazione.

**Si utilizzano gli stessi strumenti usati ora per gestire le risorse del cloud privato?**

Sì. Il portale di Azure viene utilizzato per la distribuzione e una serie di operazioni di gestione. vCenter e NSX Manager vengono usati per gestire le risorse vSphere e NSX-T.

**È possibile gestire un cloud privato con vCenter locale?**

In fase di avvio, AVS non supporterà una singola esperienza di gestione in ambienti locali e cloud privati. I cluster di cloud privati verranno gestiti con vCenter e gestione NSX locale in un cloud privato.

**È possibile usare vRealize Suite in esecuzione in locale?** 

Integrazioni e casi d'uso specifici possono essere valutati in base alla situazione.

**È possibile eseguire la migrazione di macchine virtuali vSphere da ambienti locali a cloud privati AVS?**

Sì. È possibile usare la migrazione di VM e vMotion per spostare le macchine virtuali in un cloud privato se vengono soddisfatte lehttps://kb.vmware.com/s/article/210695piattaforme Cross vCenter [requisiti vMotion] [] standard.

**È necessaria una versione specifica di vSphere negli ambienti locali?**

Poiché tutti gli ambienti cloud sono dotati di HCX, vSphere 5,5 o versioni successive negli ambienti locali per vMotion.

**Qual è l'aspetto del processo di controllo delle modifiche?**

Gli aggiornamenti apportati al servizio stesso seguiranno il processo di gestione delle modifiche standard di Microsoft Azure. I clienti sono responsabili di qualsiasi attività di amministrazione del carico di lavoro e dei processi di gestione delle modifiche associati.

## <a name="compute-network-and-storage"></a>Calcolo, rete e archiviazione

**È disponibile più di un tipo di host?**

È disponibile un solo tipo di host.

**Quali sono le specifiche della CPU in ogni tipo di host?**

I server hanno Dual CPU Intel da 18 Core 2,3 GHz.

**Quantità di memoria in ogni host**

I server hanno 576 GB di RAM.

**Qual è la capacità di archiviazione di ogni host?**

Ogni host ESXi ha due diskgroups rete VSAN con un livello di capacità di 15,2 TB e un livello di cache di 3,2 TB NVMe (1,6 TB in ogni gruppo dei dischi).

**Quanta larghezza di banda di rete è disponibile in ogni host ESXi?**

Gli host ESXi supportano la larghezza di banda di connettività fino a 25 Gbps.

**I dati archiviati negli archivi dati rete VSAN sono crittografati?**

Sì, tutti i dati rete VSAN vengono crittografati per impostazione predefinita usando le chiavi archiviate in Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Host, cluster e cloud privati

**L'infrastruttura sottostante è condivisa?**

No, gli host e i cluster di cloud privati sono dedicati e cancellati in modo sicuro prima e dopo l'utilizzo.

**Quali sono il numero minimo e massimo di host per cluster?**

I cluster possono essere ridimensionati tra 3 e 16 host ESXi. I cluster di valutazione sono limitati a tre host.

**È possibile ridimensionare i cluster di cloud privati?**

Sì, i cluster vengono ridimensionati tra il numero minimo e massimo di host ESXi. I cluster di valutazione sono limitati a tre host.

**Che cosa sono i cluster di valutazione?**

I cluster di valutazione sono tre cluster host usati per la valutazione di un mese dei cloud privati AVS.

**È possibile usare gli host di fascia alta per i cluster di valutazione?**

No. Gli host ESXi di fascia alta sono riservati per l'uso nei cluster di produzione.

## <a name="avs-and-vmware-software"></a>Software AVS e VMware

**Quali versioni di VMware software viene usato nei cloud privati?**

I cloud privati usano vSphere 6,7, rete VSAN 6,7, HCX e la versione 2,5 di NSX-T.  

**I cloud privati usano VMware NSX?**

Sì, NSX-T 2,5 viene usato per il Software Defined Networking nei cloud privati AVS.

**È possibile usare VMware NSX-V in un cloud privato?**

No. NSX-T è l'unica versione supportata di NSX.

**NSX è necessario in ambienti locali o in reti che si connettono a un cloud privato.**

No, non è necessario usare NSX in locale.

**Qual è la pianificazione dell'aggiornamento e dell'aggiornamento per il software VMware in un cloud privato?**

Gli aggiornamenti del bundle software del cloud privato vengono eseguiti per preservare il software all'interno di una versione della versione più recente del modulo del bundle software VMware. Le versioni del software del cloud privato possono essere diverse dalle versioni più recenti dei singoli componenti software (ESXi, NSX-T, vCenter, rete VSAN).

**Con quale frequenza viene aggiornato lo stack del software del cloud privato?**

Il software per il cloud privato viene aggiornato in base a una pianificazione che tiene traccia con il rilascio del bundle software da VMware. Il cloud privato non richiede tempi di inattività per gli aggiornamenti.

## <a name="connectivity"></a>Connettività

**Quale pianificazione degli indirizzi IP di rete è necessaria per incorporare cloud privati con ambienti locali?**

Per distribuire un cloud privato AVS è necessario uno spazio degli indirizzi di rete privata/22. Questo spazio di indirizzi privato non deve sovrapporsi ad altre reti virtuali in una sottoscrizione o con reti locali.
 
**Ricerca per categorie connettersi da ambienti locali a un cloud privato AVS?**

È possibile connettersi al servizio in uno dei due metodi seguenti: 

- Con una VM o un gateway applicazione distribuito in una rete virtuale di Azure con peering tramite ExpressRoute al cloud privato.
- Tramite ExpressRoute Copertura globale dal Data Center locale a un circuito ExpressRoute di Azure.

**Ricerca per categorie connettere una macchina virtuale del carico di lavoro a Internet o a un endpoint di servizio di Azure?**

Nel portale di Azure abilitare la connettività Internet per un cloud privato. Con NSX-T Manager, creare un router NSX-T T1 e un commutire logico. Si usa quindi vCenter per distribuire una macchina virtuale nel segmento di rete definito dal Commuter logico. Tale macchina virtuale avrà accesso di rete a Internet e ai servizi di Azure.

**È necessario limitare l'accesso da Internet alle macchine virtuali in reti logiche in un cloud privato?**

No. Il traffico di rete in ingresso da Internet direttamente ai cloud privati non è consentito.

**È necessario limitare l'accesso a Internet dalle macchine virtuali su reti logiche a Internet?**

Sì. È necessario usare NSX-T Manager per creare un firewall che limita l'accesso alla macchina virtuale a Internet.

## <a name="accounts-and-privileges"></a>Account e privilegi

**Quali account e privilegi si ottengono con il nuovo cloud privato AVS?**

Sono state fornite le credenziali per un utente cloudadmin in vCenter e l'accesso amministratore su NSX-T Manager. È anche disponibile un gruppo CloudAdmin che può essere usato per incorporare Azure Active Directory. Per altre informazioni, vedere [concetti relativi all'accesso e all'identità](concepts-identity.md).

**È possibile disporre dell'accesso amministrativo agli host ESXi?**

No, l'accesso dell'amministratore a ESXi è limitato a soddisfare i requisiti di sicurezza della soluzione.

**Quali privilegi e autorizzazioni si avranno in vCenter?**

Sono disponibili i privilegi del gruppo CloudAdmin. Per altre informazioni, vedere [concetti relativi all'accesso e all'identità](concepts-identity.md).

**Quali privilegi e autorizzazioni sono disponibili in NSX-T Manager?**

Si avranno privilegi di amministratore completi su NSX-T e si potrà gestire il controllo degli accessi in base al ruolo come si farebbe con il Data Center in locale di NSX-T. Per altre informazioni, vedere [concetti relativi all'accesso e all'identità](concepts-identity.md).

> [!NOTE]
> Un router T0 viene creato e configurato come parte di una distribuzione di cloud privato. Eventuali modifiche apportate al router logico o alle VM del nodo perimetrale NSX-T potrebbero influire sulla connettività al cloud privato.

## <a name="billing-and-support"></a>Fatturazione e supporto tecnico

**Come verrà fatturato durante l'anteprima di AVS**

La fatturazione per AVS durante la fase di anteprima è mensile con pagamento in base al consumo. Opzioni aggiuntive saranno disponibili a livello generale.

**In che modo verranno strutturati i prezzi durante l'anteprima di AVS?**

Per domande generali sui prezzi, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/azure-vmware) della soluzione VMware di Azure. I prezzi per l'anteprima sono disponibili su richiesta. contattare il team dell'account o seguire il collegamento nella pagina dei prezzi per contattare le vendite.

**Chi supporta AVS?**

Il supporto per AVS viene fornito da Microsoft. Si noti che le linee guida per l'anteprima supportano i parametri con ambito. I dettagli sul supporto per questo servizio specifico durante la fase di anteprima sono disponibili su richiesta al team di account.

**Quali account sono necessari per creare un cloud privato AVS?**

È necessario un account Azure in una sottoscrizione di Azure.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
