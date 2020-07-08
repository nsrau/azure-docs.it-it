---
title: Domande frequenti
description: Risposte ad alcune domande comuni sulla soluzione Azure VMware (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 1649b5649bd18b7ab53f3cc0196d7dff0f6f5b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84112693"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Domande frequenti sull'anteprima della soluzione Azure VMware (AVS)

Risposte alle domande frequenti sulla soluzione Azure VMware (AVS).

## <a name="general"></a>Generale

**Che cos'è la soluzione Azure VMware (AVS)?**

Con la ricerca da parte delle organizzazioni di strategie di modernizzazione IT in grado di migliorare l'agilità aziendale, ridurre i costi e accelerare l'innovazione, le piattaforme di cloud ibrido sono diventate elementi determinanti per la trasformazione digitale dei clienti. AVS combina il software VMware Software Defined Data Center (SDDC) con l'ecosistema globale di servizi cloud di Microsoft Azure. La soluzione AVS è gestita in modo da soddisfare i requisiti di prestazioni, disponibilità, sicurezza e conformità.

## <a name="avs-service"></a>Servizio AVS

**Dove è attualmente disponibile AVS?**

Durante l'anteprima è disponibile negli Stati Uniti orientali, in America del Nord, e ad Amsterdam, nell'Europa occidentale.

**I carichi di lavoro eseguiti in un'istanza della soluzione Azure VMware (AVS) si integrano con i servizi di Azure o li utilizzano?**

Tutti i servizi di Azure saranno disponibili per i clienti della soluzione AVS. Le limitazioni relative alle prestazioni e alla disponibilità di servizi specifici dovranno essere risolte caso per caso.

**Si usano gli stessi strumenti attualmente usati per gestire le risorse dei cloud privati?**

Sì. Il portale di Azure viene usato per la distribuzione e per diverse operazioni di gestione. vCenter e NSX Manager vengono usati per gestire le risorse vSphere e NSX-T.

**È possibile gestire un cloud privato con un'istanza locale di vCenter?**

Al momento del lancio, AVS non supporterà un'unica esperienza di gestione in ambienti locali e ambienti cloud privati. I cluster dei cloud privati verranno gestiti con istanze di vCenter e NSX Manager locali rispetto al cloud privato.

**È possibile usare vRealize Suite in esecuzione in locale?** 

Potranno essere valutati caso per caso integrazioni e casi d'uso specifici.

**È possibile eseguire la migrazione di VM vSphere da ambienti locali a cloud privati AVS?**

Sì. È possibile usare la migrazione di VM e vMotion per spostare le macchine virtuali in un cloud privato se vengono soddisfatti i requisiti standard di Cross vCenter [vMotion] [https://kb.vmware.com/s/article/210695 ].

**È necessaria una versione specifica di vSphere negli ambienti locali?**

Dato che tutti gli ambienti cloud includono HCX, è necessario vSphere versione 5.5 o successiva negli ambienti locali per vMotion.

**Come si presenta il processo di controllo delle modifiche?**

Gli aggiornamenti del servizio seguiranno il processo di gestione delle modifiche standard di Microsoft Azure. I clienti sono responsabili di tutte le attività di amministrazione del carico di lavoro e dei processi di gestione delle modifiche associati.

**Quali sono le differenze rispetto alla soluzione Azure VMware di CloudSimple?**

Con la nuova soluzione Azure VMware, Microsoft e VMware hanno una partnership diretta di provider di servizi cloud. La nuova soluzione è interamente progettata, realizzata e supportata da Microsoft e approvata da VMware. A livello di architettura, le soluzioni sono coerenti con lo stack di tecnologia VMware eseguito in un'infrastruttura dedicata di Azure.

**Che cosa comporta questa anteprima per gli attuali clienti della soluzione Azure VMware?**

Non sono state apportate modifiche alla soluzione Azure VMware di CloudSimple esistente, che continua a essere supportata in Azure. La soluzione Azure VMware è supportata dal [contratto di servizio](https://aka.ms/CSVMwareSLA) Microsoft. I clienti dovrebbero continuare a usare il servizio per i carichi di lavoro di produzione. Si tratta di una soluzione disponibile, regolata dalle [condizioni del servizio Microsoft](https://azure.microsoft.com/support/legal/).

**È possibile eseguire la migrazione dalla soluzione Azure VMware di CloudSimple a questa nuova soluzione?**

Sì. La soluzione Azure VMware supporta la migrazione con strumenti VMware noti come HCX. I clienti interessati alla migrazione alla nuova soluzione sono invitati a collaborare con il team dell'account Microsoft per esplorare le opzioni e il supporto disponibile.



## <a name="compute-network-and-storage"></a>Calcolo, rete e archiviazione

**Sono disponibili più tipi di host?**

È disponibile un solo tipo di host.

**Quali sono le specifiche delle CPU in ogni tipo di host?**

I server sono dotati di CPU dual core Intel a 18 core a 2,3 GHz.

**Quanta memoria è disponibile in ogni host?**

I server sono dotati di 576 GB di RAM.

**Qual è la capacità di archiviazione di ogni host?**

Ogni host ESXi è dotato di due gruppi di dischi VSAN con livello di capacità di 15,2 TB e livello di cache NVMe di 3,2 TB (1,6 TB in ogni gruppo di dischi).

**Quanta larghezza di banda di rete è disponibile in ogni host ESXi?**

Gli host ESXi supportano fino a 25 Gbps di larghezza di banda di connettività.

**Ai dati archiviati negli archivi dati VSAN viene applicata la crittografia dei dati inattivi?**

Sì. Per impostazione predefinita, tutti i dati VSAN vengono crittografati usando le chiavi archiviate in Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Host, cluster e cloud privati

**L'infrastruttura sottostante è condivisa?**

No. Gli host e i cluster dei cloud privati sono dedicati e cancellati in modo sicuro prima e dopo l'uso.

**Quali sono il numero minimo e quello massimo di host per cluster?**

I cluster sono scalabili tra 3 e 16 host ESXi. I cluster di prova sono limitati a tre host.

**I cluster dei cloud privati sono scalabili?**

Sì. I cluster sono scalabili tra il numero minimo e il numero massimo di host ESXi. I cluster di prova sono limitati a tre host.

**Che cosa sono i cluster di prova?**

I cluster di prova sono tre cluster di host usati per valutazioni di un mese dei cloud privati AVS.

**È possibile usare host di fascia alta per i cluster di prova?**

No. Gli host ESXi di fascia alta sono riservati per l'uso in cluster di produzione.

## <a name="avs-and-vmware-software"></a>AVS e software VMware

**Quali versioni del software VMware vengono usate nei cloud privati?**

I cloud privati usano vSphere 6.7, vSAN 6.7, HCX e la versione 2.5 di NSX-T.  

**I cloud privati usano VMware NSX?**

Sì. NSX-T 2.5 viene usato per le reti SDN (Software Defined Networking) nei cloud privati AVS.

**È possibile usare VMware NSX-V in un cloud privato?**

No. L'unica versione supportata di NSX è NSX-T.

**NSX è necessario negli ambienti locali o nelle reti che si connettono a un cloud privato?**

No. Non è necessario usare NSX in locale.

**Come sono pianificati gli aggiornamenti del software VMware in un cloud privato?**

Gli aggiornamenti del bundle software del cloud privato vengono eseguiti per preservare il software all'interno di una versione della versione più recente del bundle software da VMware. Le versioni del software del cloud privato possono differire dalle versioni più recenti dei singoli componenti software (ESXi, NSX-T, vCenter, VSAN).

**Con quale frequenza verrà aggiornato lo stack software del cloud privato?**

Il software del cloud privato viene aggiornato in base a una pianificazione allineata al rilascio del bundle software da VMware. Gli aggiornamenti non comportano tempi di inattività per il cloud privato.

## <a name="connectivity"></a>Connettività

**Quale pianificazione degli indirizzi IP di rete è necessaria per incorporare i cloud privati con gli ambienti locali?**

Per distribuire un cloud privato AVS è necessario uno spazio indirizzi di rete privato /22. Questo spazio indirizzi privato non deve sovrapporsi alle altre reti virtuali in una sottoscrizione o alle reti locali.
 
**Come si esegue la connessione dagli ambienti locali a un cloud privato AVS?**

È possibile connettersi al servizio con uno dei due metodi seguenti: 

- Con una VM o un gateway applicazione distribuito in una rete virtuale di Azure con peering al cloud privato tramite ExpressRoute.
- Tramite il servizio Copertura globale di ExpressRoute, dal data center locale a un circuito di Azure ExpressRoute.

**Come si connette una VM del carico di lavoro a Internet o a un endpoint di servizio di Azure?**

Nel portale di Azure abilitare la connettività Internet per un cloud privato. Con NSX-T Manager, creare un router NSX-T T1 e un commutatore logico. Usare quindi vCenter per distribuire una VM nel segmento di rete definito dal commutatore logico. Tale macchina virtuale avrà l'accesso di rete a Internet e ai servizi di Azure.

**È necessario limitare l'accesso da Internet alle VM nelle reti logiche di un cloud privato?**

No. Il traffico di rete in ingresso diretto da Internet ai cloud privati non è consentito.

**È necessario limitare l'accesso a Internet dalle VM nelle reti logiche?**

Sì. Sarà necessario usare NSX-T Manager per creare un firewall che limiti l'accesso delle VM a Internet.

## <a name="accounts-and-privileges"></a>Account e privilegi

**Quali account e privilegi si ottengono con il nuovo cloud privato AVS?**

Si ricevono le credenziali di un utente cloudadmin in vCenter e l'accesso come amministratore in NSX-T Manager. È anche disponibile un gruppo CloudAdmin che può essere usato per incorporare Azure Active Directory. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

**È possibile avere l'accesso come amministratore agli host ESXi?**

No. L'accesso come amministratore a ESXi è limitato per soddisfare i requisiti di sicurezza della soluzione.

**Quali privilegi e autorizzazioni si avranno in vCenter?**

Si avranno i privilegi del gruppo CloudAdmin. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

**Quali privilegi e autorizzazioni si avranno in NSX-T Manager?**

Si avranno privilegi di amministratore completi in NSX-T e la possibilità di gestire il controllo degli accessi in base al ruolo così come con NSX-T Data Center in locale. Per altre informazioni, vedere [Concetti relativi ad accesso e identità](concepts-identity.md).

> [!NOTE]
> Come parte della distribuzione di un cloud privato viene creato e configurato un router T0. Qualsiasi modifica a tale router logico o alle VM dei nodi perimetrali NSX-T influirà sulla connettività al cloud privato.

## <a name="billing-and-support"></a>Fatturazione e supporto

**Come avverrà la fatturazione durante l'anteprima di AVS?**

Durante l'anteprima, la fatturazione per AVS è mensile con pagamento in base al consumo. Altre opzioni saranno disponibili con il passaggio alla disponibilità generale.

**Come saranno strutturati i prezzi durante l'anteprima di AVS?**

Per domande generali sui prezzi, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/azure-vmware) della soluzione Azure VMware. I prezzi dell'anteprima sono disponibili su richiesta. Contattare il team dell'account oppure seguire il collegamento nella pagina dei prezzi per contattare il reparto vendite.

**Chi supporta AVS?**

Il supporto per AVS è offerto da Microsoft. Si noti che, in base alle linee guida per l'anteprima, verrà fornito supporto nell'orario di ufficio 9-17 PST, dal lunedì al venerdì. È possibile aprire un ticket di supporto da [questo collegamento](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

**Quali account sono necessari per creare un cloud privato AVS?**

Sarà necessario un account Azure in una sottoscrizione di Azure.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Ricerca per categorie richiedere un aumento della quota host per la soluzione VMware di Azure?**

È possibile richiedere un aumento della quota [inviando una richiesta di supporto](..\azure-portal\supportability\how-to-create-azure-support-request.md). Il team responsabile della gestione delle quote valuterà la richiesta e la approverà entro tre giorni lavorativi.  

> [!IMPORTANT]
> Per poter richiedere un aumento della quota, assicurarsi prima di tutto di registrare il provider di risorse **Microsoft.AVS** nel portale di Azure.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Per informazioni su come registrare il provider di risorse in altri modi, vedere [Provider e tipi di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

1. Nel portale di Azure creare una richiesta di supporto in **Guida e supporto** selezionando **Nuova richiesta di supporto** e quindi specificare le informazioni seguenti per il ticket.
   - **Tipo di problema:** Tecnici
   - **Sottoscrizione:** ID sottoscrizione
   - **Service:**  Soluzione Azure VMware 
   - **Riepilogo:** Aumento di quota
   - **Tipo di problema:** Capacity Management Issues (Problemi di gestione della capacità)
   - **Sottotipo del problema:** Customer Request for Additional Host Quota/Capacity (Richiesta del cliente di capacità/quota host aggiuntiva)

1. Nella descrizione del ticket di supporto specificare le informazioni seguenti nella scheda Dettagli:
   - Numero di nodi aggiuntivi   
   - SKU nodi
   - Region

   > [!NOTE] 
   > Per impostazione predefinita, verrà concesso un minimo di quattro nodi.

1. Fare clic su **Rivedi e crea** per inviare la richiesta.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
