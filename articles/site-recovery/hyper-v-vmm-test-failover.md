---
title: Eseguire un'esercitazione sul ripristino di emergenza delle macchine virtuali Hyper-V in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Informazioni su come eseguire un'esercitazione sul ripristino di emergenza per le macchine virtuali Hyper-V nei cloud VMM in un Data Center secondario usando Azure Site Recovery.
services: site-recovery
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: ponatara
ms.openlocfilehash: a586eac3be39a4d3fb35dff7a4b1cc40f32f2720
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Eseguire un'esercitazione sul ripristino di emergenza per macchine virtuali Hyper-V in un sito secondario


Questo articolo illustra come eseguire un esercitazione sul ripristino di emergenza per le macchine virtuali Hyper-V gestite nei cloud di System Center Virtual Machine Manager (VMM) in un sito secondario locale usando [Azure Site Recovery](site-recovery-overview.md).

Viene eseguito un failover di testper convalidare la strategia di replica ed eseguire un'esercitazione sul ripristino di emergenza senza perdita di dati o tempi di inattività. Un failover di test non ha alcun impatto sulla replica in corso o sull'ambiente di produzione. 

## <a name="how-do-test-failovers-work"></a>Come funziona il failover di test?

Eseguire un failover di test dal server primario al sito secondario. Se si desidera semplicemente verificare che sia possibile eseguire il failover di una macchina virtuale, è possibile eseguire un failover di test senza eseguire alcuna configurazione nel sito secondario. Se si desidera verificare che il failover dell'app funzioni come previsto, è necessario configurare la rete l'e infrastruttura nella posizione secondaria.
- È possibile eseguire un failover di test su una sola macchina virtuale o in un [piano di ripristino](site-recovery-create-recovery-plans.md).
- È possibile eseguire il failover senza una rete, con una rete esistente o con una rete creata automaticamente. Altre informazioni su queste opzioni sono disponibili nella tabella seguente.
    - È possibile eseguire un failover di test senza una rete. Questa opzione risulta utile se si desidera semplicemente verificare che una macchina virtuale sia stata in grado di eseguire il failover, ma non consente di verificare una configurazione di rete.
    - Eseguire il failover con una rete esistente. È consigliabile non usare una rete di produzione.
    - Eseguire il failover e consentire a Site Recovery di creare automaticamente una rete di test. In questo caso Site Recovery crea automaticamente la rete ed esegue la pulizia quando il failover di test è stato completato.
- È necessario selezionare un punto di ripristino per il failover di test: 
    - **Elaborato più recente**: questa opzione consente di eseguire il failover di una macchina virtuale nel punto di ripristino più recente elaborato da Site Recovery. Offre un RTO (Recovery Time Objective) basso poiché non viene impiegato tempo per elaborare dati non elaborati.
    - **Coerente con l'app più recente**: questa opzione esegue il failover di una macchina virtuale nel punto di ripristino coerente con l'app più recente elaborato da Site Recovery. 
    - **Più recente**: con questa opzione vengono prima elaborati tutti i dati inviati al servizio Site Recovery per creare un punto di ripristino per ogni VM e quindi viene eseguito il failover in tale punto di ripristino. Questa opzione offre il valore RPO (Recovery Point Objective) più basso perché la macchina virtuale creata dopo il failover conterrà tutti i dati che sono stati replicati in Site Recovery all'attivazione del failover.
    - **Elaborato più recente tra più macchine virtuali**: questa opzione è disponibile per i piani di ripristino che includono una o più macchine virtuali in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali in cui è abilitata l'impostazione eseguono il failover nel punto di ripristino coerente tra più macchine comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di ripristino più recente elaborato.
    - **Coerente con l'app più recente tra più macchine virtuali**: questa opzione è disponibile per i piani di ripristino con una o più macchine virtuali in cui è abilitata la coerenza tra più macchine virtuali. Le macchine virtuali che fanno parte di un gruppo di replica eseguono il failover nel punto di ripristino coerente a livello applicazione tra più macchine comune più recente. Le altre macchine virtuali eseguono il failover nel relativo punto di ripristino più recente coerente con l'applicazione.
    - **Personalizzato**: questa opzione consente di eseguire il failover di una macchina virtuale specifica in un determinato punto di ripristino.



## <a name="prepare-networking"></a>Preparare la rete

Quando si esegue un failover di test, viene chiesto di selezionare le impostazioni di rete per le macchine di replica di test, come riepilogato nella tabella.

**Opzione** | **Dettagli** 
--- | --- 
**Nessuno** | La macchina virtuale di test viene creata nello stesso host in cui è presente la macchina virtuale di replica. Non viene aggiunta al cloud e non è connessa ad alcuna rete.<br/><br/> Dopo essere stata creata, la macchina può essere connessa a una rete VM.
**Usa esistente** | La macchina virtuale di test viene creata nello stesso host in cui è presente la macchina virtuale di replica. Non viene aggiunta al cloud.<br/><br/>Creare una rete VM isolata dalla rete di produzione.<br/><br/>Se si vuole usare una rete basata su VLAN, è consigliabile creare a questo scopo in VMM una rete logica separata non usata in produzione. Questa rete logica viene usata per creare reti VM per i failover di test.<br/><br/>La rete logica deve essere associata ad almeno una delle schede di rete di tutti i server Hyper-V che ospitano macchine virtuali.<br/><br/>Per le reti logiche VLAN i siti di rete che vengono aggiunti alla rete logica devono essere isolati.<br/><br/>Se si usa una rete logica basata su Virtualizzazione rete Windows, Azure Site Recovery crea automaticamente reti VM isolate. 
**Crea una rete** | Viene creata automaticamente una rete di test temporanea in base all'impostazione specificata in **Rete logica** e ai siti di rete correlati.<br/><br/> Il failover controlla che le macchine virtuali vengano create. |Usare questa opzione se un piano di ripristino uza più reti VM.<br/><br/> Se si utilizzano reti di virtualizzazione rete Windows, è possibile utilizzare questa opzione per creare automaticamente reti VM con le stesse impostazioni (subnet e pool di indirizzi IP) specificate nella rete della macchina virtuale di replica. Queste reti VM vengono eliminate automaticamente al termine del failover di test.<br/><br/> La macchina virtuale di test viene creata nello stesso host in cui è presente la macchina virtuale di replica. Non viene aggiunta al cloud.

### <a name="best-practices"></a>Procedure consigliate

- Testare una rete di produzione comporta tempi di inattività per i carichi di lavoro della produzione. Chiedere agli utenti di non usare le app correlate quando l'analisi di ripristino di emergenza è in corso.
- La rete di test non deve necessariamente corrispondere al tipo di rete logica VMM usato per il failover di test. Se la replica usa l'isolamento basato su VLAN e DHCP, non è necessario un pool di indirizzi IP statici per la rete VM per la replica. Di conseguenza l’uso della virtualizzazione rete Windows per il failover di test non funziona perché non è disponibile alcun pool di indirizzi. 
        Il failover di test non funziona se la rete di replica è senza isolamento e la rete di test è basata sulla virtualizzazione rete Windows. Questo accade perché la rete senza isolamento non dispone delle subnet necessarie per creare una rete di virtualizzazione rete Windows.
- È consigliabile non usare la rete che è stata selezionata per il mapping di rete per il failover di test.
- Il modo in cui le macchine virtuali di replica sono connesse alle reti VM mappate dopo il failover dipende dalla configurazione della rete VM nella console VMM.

### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rete VM configurata senza isolamento o isolamento VLAN

Se una rete VM è configurata in VMM senza isolamento o l'isolamento VLAN, tenere presente quanto segue:

- Se DHCP viene definito per la rete VM la macchina virtuale di replica viene connessa all'ID VLAN usando le impostazioni specificate per il sito di rete nella rete logica associata. La macchina virtuale riceve il relativo indirizzo IP dal server DHCP disponibile.
- Non è necessario definire un pool di indirizzi IP statico per la rete VM di destinazione. Se per la rete VM viene usato un pool di indirizzi IP statici, la macchina virtuale di replica viene connessa all'ID VLAN usando le impostazioni specificate per il sito di rete nella rete logica associata.
- La macchina virtuale riceve il proprio indirizzo IP dal pool definito per la rete VM. Se non è definito un pool di indirizzi IP statici nella rete VM di destinazione, l'allocazione di indirizzi IP avrà esito negativo. Creare il pool di indirizzi IP nei server VMM di origine e di destinazione che verranno usati per la protezione e il ripristino.

### <a name="vm-network-with-windows-network-virtualization"></a>Rete VM con virtualizzazione rete Windows

Se una rete VM è configurata in VMM con virtualizzazione rete Windows, tenere presente quanto segue:

- È necessario definire un pool statico per la rete VM di destinazione, indipendentemente dal fatto che la rete VM di origine sia configurata per usare DHCP o un pool di indirizzi IP statici. 
- Se si definisce DHCP, il server VMM di destinazione viene usato come server DHCP e offre un indirizzo IP dal pool definito per la rete VM di destinazione.
- Se per il server di origine è definito l’uso di un pool di indirizzi IP statici, il server VMM di destinazione assegna un indirizzo IP dal pool. In entrambi i casi, l'allocazione di indirizzi IP non riuscirà se non è definito un pool di indirizzi IP statici.



## <a name="prepare-the-infrastructure"></a>Preparare l'infrastruttura

Se si desidera semplicemente verificare che una macchina virtuale possa eseguire il failover, è possibile eseguire un failover di test senza un'infrastruttura. Se si desidera eseguire un'esercitazione sul ripristino di emergenza completa per testare il failover dell'applicazione, è necessario preparare l'infrastruttura nel sito secondario:

- Se si esegue un failover di test usando una rete esistente, preparare Active Directory, DHCP e DNS in tale rete.
- Se si esegue un failover di test con l'opzione per creare automaticamente una rete VM, è necessario aggiungere risorse di infrastruttura alla rete creata automaticamente prima di eseguire il failover di test. In un piano di ripristino è possibile facilitare le operazioni aggiungendo un passaggio manuale prima di Group-1 nel piano di ripristino che si intende utilizzare per il failover di test. Quindi prima di effettuare il failover di test aggiungere risorse di infrastruttura alla rete creata automaticamente.


### <a name="prepare-dhcp"></a>Preparare DHCP
Se le macchine virtuali che partecipano al failover di test usano DHCP, creare un server DHCP di test all'interno della rete isolata ai fini del failover di test.


### <a name="prepare-active-directory"></a>Preparare Active Directory
Per eseguire un failover di test per testare l'applicazione, è necessaria una copia dell'ambiente Active Directory di produzione nell'ambiente di test. Per altre informazioni, vedere le [considerazioni sul failover di test di Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Preparare DNS
Preparare un server DNS per il failover di test come segue:

* **DHCP**: se le macchine virtuali usano DHCP, l'indirizzo IP del DNS di test deve essere aggiornato nel server DHCP di test. Se si utilizza un tipo di rete di virtualizzazione rete Windows, il server VMM funge da server DHCP. Pertanto, l'indirizzo IP del DNS deve essere aggiornato nella rete di failover di test. In questo caso le macchine virtuali effettuano la registrazione nel relativo server DNS.
* **Indirizzo statico**: se le macchine virtuali usano un indirizzo IP statico, l'indirizzo IP del server DNS di test deve essere aggiornato nella rete di failover di test. Può essere necessario aggiornare il DNS con l'indirizzo IP delle macchine virtuali di test. A tale scopo, è possibile utilizzare lo script di esempio riportato di seguito:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Eseguire un failover di test

Questa procedura descrive come eseguire un failover di test per un piano di ripristino. In alternativa è possibile effettuare il failover per una singola macchina nella scheda **Macchine virtuali**.

1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover** > **Test Failover**.
2. Nel pannello **Failover di test** specificare come le macchine virtuali di replica devono essere connesse alle reti dopo il failover di test.
3. Tenere traccia dello stato di avanzamento del failover nella scheda **Processi** .
4. Dopo il completamento del failover verificare che le macchine virtuali vengano avviate correttamente.
5. Al termine fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Questa operazione elimina le macchine virtuali e le reti create da Site Recovery durante il failover di test. 

![Failover di test](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> L'indirizzo IP assegnato a una macchina virtuale durante un failover di test corrisponde all'indirizzo IP che la macchina virtuale riceverebbe durante un failover pianificato o non pianificato, a condizione che l'indirizzo IP sia disponibile nella rete di failover di test. Se lo stesso indirizzo IP non è disponibile nella rete di failover di test, la macchina virtuale riceve un altro indirizzo IP disponibile in tale rete.



### <a name="run-a-test-failover-to-a-production-network"></a>Eseguire un failover di test in una rete di produzione

È consigliabile non eseguire un failover di test nella rete del sito di ripristino di produzione specificato durante il mapping di rete. Se tuttavia si ha comunque necessità di convalidare la connettività di rete end-to-end in una macchina virtuale sottoposta a failover, tenere presente quanto segue:

* Verificare che la macchina virtuale primaria sia arrestata mentre si esegue il failover di test. In caso contrario si avranno due macchine virtuali con la stessa identità in esecuzione contemporaneamente sulla stessa rete. Questa situazione può provocare conseguenze indesiderate.
* Quando si esegue la pulizia delle macchine virtuali del failover di test, tutte le modifiche apportate a tali macchine vanno perdute. Le modifiche non vengono replicate nella macchina virtuale primaria.
* Questa modalità di esecuzione dei test determina un tempo di inattività dell'applicazione di produzione. Chiedere agli utenti di non usare l'applicazione mentre è in corso l'esercitazione per il ripristino di emergenza.  


## <a name="next-steps"></a>Passaggi successivi
Dopo l'esito positivo di un'esercitazione sul ripristino di emergenza, è possibile provare a [eseguire un failover completo](site-recovery-failover.md).



