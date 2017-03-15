---
title: Failover di test (da VMM a VMM) in Site Recovery - Azure | Documentazione Microsoft
description: Azure Site Recovery coordina la replica, il failover e il ripristino di macchine virtuali e server fisici. Informazioni sul failover in Azure o in un centro dati secondario.
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/15/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 6b1a5b2879a7b98ec4ad3e8ebbc9e95c0740d89f
ms.openlocfilehash: 3aaa005319b1ce2a10cd913c63b31860d31b797e
ms.lasthandoff: 02/23/2017


---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Failover di test (da VMM a VMM) in Site Recovery
> [!div class="op_single_selector"]
> * [Testare il failover in Azure](./site-recovery-test-failover-to-azure.md)
> * [Testare il failover (da VMM a VMM)](./site-recovery-test-failover-vmm-to-vmm.md)


Questo articolo contiene informazioni e istruzioni per eseguire un failover di test o un'esercitazione sul ripristino di emergenza di macchine virtuali e server fisici protetti con Site Recovery, usando un sito locale gestito VMM come sito di ripristino. 

Per inviare commenti o domande, è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Il failover di test viene eseguito per convalidare la strategia di replica o eseguire un'esercitazione sul ripristino di emergenza senza perdita di dati o tempi di inattività. Un failover di test non ha alcun impatto sulla replica in corso o sull'ambiente di produzione. Il failover di test può essere eseguito su una macchina virtuale o un [piano di ripristino](site-recovery-create-recovery-plans.md). Quando si attiva un failover di test, è necessario specificare la rete a cui si connetteranno le macchine virtuali di test. Dopo aver attivato un failover di test, è possibile tenere traccia dello stato di avanzamento nella pagina **Processi**.  


## <a name="preparing-infrastructure-for-test-failover"></a>Preparazione dell'infrastruttura per il failover di test
* Se si desidera eseguire un failover di test utilizzando una rete esistente, preparare Active Directory, DHCP e DNS in tale rete.
* Se si desidera eseguire un failover di test utilizzando l'opzione per creare automaticamente reti VM, aggiungere un passaggio manuale prima del gruppo&1; del piano di ripristino che si intende utilizzare per il failover di test e quindi aggiungere le risorse dell'infrastruttura alla rete creata automaticamente prima di eseguire il failover di test.

### <a name="things-to-note"></a>Punti da notare
* In caso di replica a un sito secondario, il tipo di rete utilizzato dalla macchina di replica non deve necessariamente corrispondere al tipo di rete logica utilizzato per il failover di test, ma alcune combinazioni potrebbero non funzionare. Se la replica utilizza DHCP e isolamento basato su VLAN, non è necessario un pool di indirizzi IP statici per la rete VM per la replica. Di conseguenza, l’uso della virtualizzazione di rete Windows per il failover di test non funziona perché non è disponibile alcun pool di indirizzi. Il failover di test, inoltre, non funzionerà se la rete di replica è senza isolamento e la rete di test è basata sulla virtualizzazione rete Windows. Questo accade perché la rete senza isolamento non dispone delle subnet necessarie per creare una rete di virtualizzazione rete Windows.
* Il modo in cui le macchine virtuali di replica sono connesse alle reti VM mappate dopo il failover dipende dalla configurazione della rete VM nella console VMM:
  * **Rete VM configurata senza isolamento o isolamento VLAN**: se DHCP è definito per la rete VM, la macchina virtuale di replica verrà connessa all'ID VLAN utilizzando le impostazioni specificate per il sito di rete nella rete logica associata. La macchina virtuale riceverà il relativo indirizzo IP dal server DHCP disponibile. Non è necessario un pool di indirizzi IP statico definito per la rete VM di destinazione. Se un indirizzo IP statico viene utilizzato per la rete VM, la macchina virtuale di replica verrà connessa all'ID VLAN utilizzando le impostazioni specificate per il sito di rete nella rete logica associata. La macchina virtuale riceverà il relativo indirizzo IP dal pool definito per la rete VM. Se non è definito un pool di indirizzi IP statici nella rete VM di destinazione, l'allocazione di indirizzi IP avrà esito negativo. Il pool di indirizzi IP deve essere creato nei server VMM di origine e di destinazione che si desidera utilizzare per la protezione e il ripristino.
  * **Rete VM con virtualizzazione rete Windows**: se una rete VM è configurata con questa impostazione, è necessario definire un pool statico per la rete VM di destinazione, indipendentemente dal fatto che la rete VM di origine sia configurata per utilizzare DHCP o un pool di indirizzi IP statici. Se si definisce DHCP, il server VMM di destinazione verrà utilizzato come server DHCP e fornirà un indirizzo IP dal pool definito per la rete VM di destinazione. Se per il server di origine è definito l’uso di un pool di indirizzi IP statici, il server VMM di destinazione allocherà un indirizzo IP dal pool. In entrambi i casi, l'allocazione di indirizzi IP non riuscirà se non è definito un pool di indirizzi IP statici.


### <a name="prepare-dhcp"></a>Preparare DHCP
Se le macchine virtuali coinvolte nel failover di test utilizzano DHCP, un server DHCP di test deve essere creato all'interno della rete isolata creata ai fini del failover di test.

### <a name="prepare-active-directory"></a>Preparare Active Directory
Per eseguire un failover di test per testare l'applicazione, è necessaria una copia dell'ambiente Active Directory di produzione nell'ambiente di test. Per altri dettagli, vedere la sezione [Considerazioni sul failover di test per Active Directory](site-recovery-active-directory.md#test-failover-considerations) . 

### <a name="prepare-dns"></a>Preparare DNS
Preparare un server DNS per il failover di test come segue:

* **DHCP**: se le macchine virtuali utilizzano DHCP, l'indirizzo IP del DNS di test deve essere aggiornato nel server DHCP di test. Se si utilizza un tipo di rete di virtualizzazione rete Windows, il server VMM funge da server DHCP. Pertanto, l'indirizzo IP del DNS deve essere aggiornato nella rete di failover di test. In questo caso, le macchine virtuali effettueranno la registrazione al relativo server DNS.
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
Questa procedura descrive come eseguire un failover di test per un piano di ripristino. In alternativa è possibile eseguire il failover per una singola macchina virtuale o server fisico nella scheda **Macchine virtuali** .

![Failover di test](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover** > **Test Failover**.
1. Nel pannello **Failover di test** specificare come le macchine virtuali dovranno essere connesse alle reti dopo il failover di test. Per altri dettagli, vedere le [opzioni di rete](#network-options-in-site-recovery).
1. Tenere traccia dello stato di avanzamento del failover nella scheda **Processi** . 
1. Successivamente, verificare che le macchine virtuali vengano avviate correttamente
1. Al termine, fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Verranno eliminate le macchine virtuali e le reti create durante il failover di test. 


## <a name="network-options-in-site-recovery"></a>Opzioni di rete in Site Recovery

Quando si esegue un failover di test viene chiesto di selezionare le impostazioni di rete per le macchine di replica di test. Si dispone di una serie di opzioni.  

| **Opzione di failover di test** | **Descrizione** | **Controllo del failover** | **Dettagli** |
| --- | --- | --- | --- |
| **Eseguire il failover a un sito VMM secondario, senza rete** |Non selezionare una rete VM |Il failover controlla che le macchine di test vengono create.<br/><br/>La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica. |<p>La macchina di cui è stato eseguito il failover non sarà connessa ad alcuna rete.<br/><br/>La macchina può essere connessa a una rete VM dopo che è stata creata |
| **Eseguire il failover a un sito VMM secondario, con rete** |Selezionare una rete VM esistente |Il failover controlla che le macchine virtuali vengono create |La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.<br/><br/>Creare una rete di VM isolata dalla rete di produzione<br/><br/>Se si vuole usare una rete basata su VLAN, è consigliabile creare una rete logica separata, non usata in produzione, in VMM con queste finalità. Questa rete logica viene usata per creare reti di VM per il failover di test.<br/><br/>La rete logica deve essere associata almeno a una delle schede di rete di tutti i server Hyper-V che ospitano macchine virtuali.<br/><br/>Per le reti logiche VLAN, i siti della rete aggiunti alla rete logica devono essere isolati.<br/><br/>Se si usa una rete logica basata su Virtualizzazione rete Windows, Azure Site Recovery crea automaticamente reti VM isolate. |
| **Eseguire il failover a un sito VMM secondario, creare una rete** |Una rete di test temporanea verrà creata automaticamente in base all'impostazione specificata in **Rete logica** e ai relativi siti di rete |Il failover controlla che le macchine virtuali vengono create |Utilizzare questa opzione se il piano di ripristino utilizza più reti VM. Se si utilizzano reti di virtualizzazione rete Windows, è possibile utilizzare questa opzione per creare automaticamente reti VM con le stesse impostazioni (subnet e pool di indirizzi IP) specificate nella rete della macchina virtuale di replica. Queste reti VM vengono eliminate automaticamente al termine del failover di test.</p><p>La macchina virtuale di test viene creata sullo stesso host in cui è presente la macchina virtuale di replica, ma non viene aggiunta al cloud in cui si trova la macchina virtuale di replica. |

> [!TIP]
> L'indirizzo IP assegnato a una macchina virtuale durante un failover di test corrisponde all'indirizzo IP che riceverebbe durante un failover pianificato o non pianificato, presupponendo che l'indirizzo IP sia disponibile nella rete di failover di test. Se lo stesso indirizzo IP non è disponibile nella rete di failover di test, la macchina virtuale riceverà un altro indirizzo IP disponibile in tale rete.
>
>


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Failover di test in una rete di produzione nel sito di ripristino 
Durante un failover di test, è consigliabile scegliere una rete diversa rispetto alla rete del sito di ripristino di produzione specificata in **Mapping di rete**. Se tuttavia si vuole comunque convalidare la connettività di rete end-to-end in una macchina virtuale sottoposta a failover, tenere presente quanto segue:

1. Verificare che la macchina virtuale primaria sia arrestata quando si esegue il failover di test. In caso contrario, due macchine virtuali con la stessa identità saranno in esecuzione contemporaneamente nella stessa rete e ciò può causare conseguenze indesiderate. 
1. Tutte le modifiche apportate alle macchine virtuali del failover di test andranno perse con la pulizia di tali macchine virtuali. Le modifiche non verranno replicate nella macchina virtuale primaria.
1. Questa modalità di esecuzione dei test determina un tempo di inattività dell'applicazione di produzione. È consigliabile chiedere agli utenti di non usare l'applicazione mentre è in corso l'esercitazione sul ripristino di emergenza.  


## <a name="next-steps"></a>Passaggi successivi
Dopo l'esito positivo di un failover di test, si può provare a eseguire un [failover](site-recovery-failover.md).

