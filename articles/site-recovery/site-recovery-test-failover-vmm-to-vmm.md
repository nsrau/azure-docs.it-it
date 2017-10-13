---
title: Failover di test (da VMM a VMM) in Azure Site Recovery | Microsoft Docs
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
ms.date: 06/05/2017
ms.author: pratshar
ms.openlocfilehash: afc4790d5714ce7145c8f4291a05acc2e9882a9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Failover di test (da VMM a VMM) in Site Recovery


Questo articolo contiene informazioni e istruzioni per eseguire un failover di test o un'esercitazione per il ripristino di emergenza di macchine virtuali (VM) e server fisici protetti con Azure Site Recovery. Come sito di ripristino si usa un sito locale gestito con System Center Virtual Machine Manager (VMM).

Il failover di test viene eseguito per convalidare la strategia di replica o eseguire un'esercitazione sul ripristino di emergenza senza perdita di dati o tempi di inattività. Un failover di test non ha alcun impatto sulla replica in corso o sull'ambiente di produzione. È possibile eseguirlo in una macchina virtuale o in un [piano di ripristino](site-recovery-create-recovery-plans.md). Quando si attiva un failover di test è necessario specificare la rete a cui si connetteranno le macchine virtuali di test. È possibile rilevare lo stato di avanzamento del failover di test nella pagina **Processi**.  

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum di Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-the-infrastructure-for-test-failover"></a>Preparare l'infrastruttura per il failover di test
Per eseguire un failover di test usando una rete esistente preparare Active Directory, DHCP e DNS in tale rete.

Se si vuole eseguire un failover di test con l'opzione per la creazione automatica di reti VM aggiungere un passaggio manuale prima di Group-1 (Gruppo-1) nel piano di ripristino che si prevede di usare per il failover di test. Quindi prima di effettuare il failover di test aggiungere risorse di infrastruttura alla rete creata automaticamente.

### <a name="things-to-note"></a>Punti da notare
In caso di replica in un sito secondario, il tipo di rete usato dalla macchina di replica non deve necessariamente corrispondere al tipo di rete logica usato per il failover di test, ma alcune combinazioni potrebbero non funzionare. Se la replica utilizza DHCP e isolamento basato su VLAN, non è necessario un pool di indirizzi IP statici per la rete VM per la replica. Di conseguenza l’uso della virtualizzazione rete Windows per il failover di test non funziona perché non è disponibile alcun pool di indirizzi. 

Il failover di test non funziona anche quando la rete di replica è senza isolamento e la rete di test è basata sulla virtualizzazione rete Windows. Questo accade perché la rete senza isolamento non dispone delle subnet necessarie per creare una rete di virtualizzazione rete Windows.

Il modo in cui le macchine virtuali di replica sono connesse alle reti VM mappate dopo il failover dipende dalla configurazione della rete VM nella console VMM.

#### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rete VM configurata senza isolamento o isolamento VLAN
Se DHCP viene definito per la rete VM la macchina virtuale di replica viene connessa all'ID VLAN usando le impostazioni specificate per il sito di rete nella rete logica associata. La macchina virtuale riceve il relativo indirizzo IP dal server DHCP disponibile. 

Non è necessario definire un pool di indirizzi IP statico per la rete VM di destinazione. Se per la rete VM viene usato un pool di indirizzi IP statici, la macchina virtuale di replica viene connessa all'ID VLAN usando le impostazioni specificate per il sito di rete nella rete logica associata.

La macchina virtuale riceve il proprio indirizzo IP dal pool definito per la rete VM. Se non è definito un pool di indirizzi IP statici nella rete VM di destinazione, l'allocazione di indirizzi IP avrà esito negativo. Creare il pool di indirizzi IP nei server VMM di origine e di destinazione che verranno usati per la protezione e il ripristino.

#### <a name="vm-network-with-windows-network-virtualization"></a>Rete VM con virtualizzazione rete Windows
Se una rete VM è configurata con virtualizzazione rete Windows è necessario definire un pool statico per la rete VM di destinazione, sia che la rete VM di origine sia configurata per usare DHCP o un pool di indirizzi IP statici. 

Se si definisce DHCP, il server VMM di destinazione viene usato come server DHCP e offre un indirizzo IP dal pool definito per la rete VM di destinazione. Se per il server di origine è definito l’uso di un pool di indirizzi IP statici, il server VMM di destinazione assegna un indirizzo IP dal pool. In entrambi i casi, l'allocazione di indirizzi IP non riuscirà se non è definito un pool di indirizzi IP statici.


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

![Pannello Failover di test](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Selezionare **Piani di ripristino** > *nome_pianodiripristino*. Fare clic su **Failover** > **Test Failover**.
1. Nel pannello **Failover di test** specificare come le macchine virtuali dovranno essere connesse alle reti dopo il failover di test. Per altre informazioni, vedere [Opzioni di rete](#network-options-in-site-recovery).
1. Tenere traccia dello stato di avanzamento del failover nella scheda **Processi** .
1. Dopo il completamento del failover verificare che le macchine virtuali vengano avviate correttamente.
1. Al termine fare clic su **Cleanup test failover** (Pulizia failover di test) nel piano di ripristino. Fare clic su **Note** per registrare e salvare eventuali osservazioni associate al failover di test. Questa operazione elimina le macchine virtuali e le reti create durante il failover di test.


## <a name="network-options-in-site-recovery"></a>Opzioni di rete in Site Recovery

Quando si esegue un failover di test viene chiesto di selezionare le impostazioni di rete per le macchine di replica di test. Sono disponibili varie opzioni.  

| **Opzione di failover di test** | **Descrizione** | **Controllo del failover** | **Dettagli** |
| --- | --- | --- | --- |
| **Effettuare il failover a un sito VMM secondario, senza rete** |Non selezionare una rete VM. |Il failover controlla che le macchine di test vengono create.<br/><br/>La macchina virtuale di test viene creata nello stesso host in cui è presente la macchina virtuale di replica. Non viene aggiunta al cloud in cui si trova la macchina virtuale di replica. |<p>La macchina sottoposta a failover non è connessa a nessuna rete.<br/><br/>La macchina può essere connessa a una rete VM dopo che è stata creata. |
| **Effettuare il failover a un sito VMM secondario, con rete** |Selezionare una rete VM esistente. |Il failover controlla che le macchine virtuali vengano create. |La macchina virtuale di test viene creata nello stesso host in cui è presente la macchina virtuale di replica. Non viene aggiunta al cloud in cui si trova la macchina virtuale di replica.<br/><br/>Creare una rete VM isolata dalla rete di produzione.<br/><br/>Se si vuole usare una rete basata su VLAN, è consigliabile creare a questo scopo in VMM una rete logica separata non usata in produzione. Questa rete logica viene usata per creare reti VM per i failover di test.<br/><br/>La rete logica deve essere associata ad almeno una delle schede di rete di tutti i server Hyper-V che ospitano macchine virtuali.<br/><br/>Per le reti logiche VLAN i siti di rete che vengono aggiunti alla rete logica devono essere isolati.<br/><br/>Se si usa una rete logica basata su Virtualizzazione rete Windows, Azure Site Recovery crea automaticamente reti VM isolate. |
| **Effettuare il failover a un sito VMM secondario, creare una rete** |Viene creata automaticamente una rete di test temporanea in base all'impostazione specificata in **Rete logica** e ai siti di rete correlati. |Il failover controlla che le macchine virtuali vengano create. |Utilizzare questa opzione se il piano di ripristino utilizza più reti VM. Se si utilizzano reti di virtualizzazione rete Windows, è possibile utilizzare questa opzione per creare automaticamente reti VM con le stesse impostazioni (subnet e pool di indirizzi IP) specificate nella rete della macchina virtuale di replica. Queste reti VM vengono eliminate automaticamente al termine del failover di test.</p><p>La macchina virtuale di test viene creata nello stesso host in cui è presente la macchina virtuale di replica. Non viene aggiunta al cloud in cui si trova la macchina virtuale di replica. |

> [!TIP]
> L'indirizzo IP assegnato a una macchina virtuale durante un failover di test corrisponde all'indirizzo IP che la macchina virtuale riceverebbe durante un failover pianificato o non pianificato, a condizione che l'indirizzo IP sia disponibile nella rete di failover di test. Se lo stesso indirizzo IP non è disponibile nella rete di failover di test, la macchina virtuale riceve un altro indirizzo IP disponibile in tale rete.
>
>


## <a name="test-failover-to-a-production-network-on-a-recovery-site"></a>Failover di test su una rete di produzione in un sito di ripristino
Per un failover di test è consigliabile scegliere una rete diversa dalla rete del sito di ripristino di produzione specificata in [Mapping di rete](https://docs.microsoft.com/azure/site-recovery/site-recovery-network-mapping). Se si vuole comunque convalidare la connettività di rete end-to-end in una macchina virtuale sottoposta a failover, tenere presente quanto segue:

* Verificare che la macchina virtuale primaria sia arrestata quando si esegue il failover di test. In caso contrario si avranno due macchine virtuali con la stessa identità in esecuzione contemporaneamente sulla stessa rete. Questa situazione può provocare conseguenze indesiderate.
* Quando si esegue la pulizia delle macchine virtuali del failover di test, tutte le modifiche apportate a tali macchine vanno perdute. Le modifiche non vengono replicate nella macchina virtuale primaria.
* Questa modalità di esecuzione dei test determina un tempo di inattività dell'applicazione di produzione. Chiedere agli utenti di non usare l'applicazione mentre è in corso l'esercitazione per il ripristino di emergenza.  


## <a name="next-steps"></a>Passaggi successivi
Dopo l'esito positivo di un failover di test è possibile provare a eseguire un [failover](site-recovery-failover.md).
