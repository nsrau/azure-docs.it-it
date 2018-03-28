---
title: Predisporre il server Hyper-V locale per il ripristino di emergenza delle VM Hyper-V in Azure| Microsoft Docs
description: Informazioni su come predisporre le VM Hyper-V locali non gestite da System Center VMM per il ripristino di emergenza in Azure con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1290a186ca8e83b09f53b286e80c5ce75f08d88c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Predisporre i server Hyper-V locali per il ripristino di emergenza in Azure

Questa esercitazione descrive come predisporre l'infrastruttura Hyper-V locale per replicare le VM Hyper-V in Azure allo scopo di effettuare un ripristino di emergenza. È possibile, ma non necessario, gestire gli host Hyper-V da System Center Virtual Machine Manager (VMM).  In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Esaminare i requisiti di Hyper-V e i requisiti di VMM, se necessario.
> * Predisporre VMM, se necessario
> * Verificare l'accesso Internet alle posizioni di Azure
> * Predisporre le VM in modo che sia possibile accedervi dopo il failover in Azure

Questa è la seconda esercitazione della serie. Assicurarsi di aver [configurato i componenti di Azure](tutorial-prepare-azure.md) come descritto nell'esercitazione precedente.



## <a name="review-requirements-and-prerequisites"></a>Esaminare i requisiti e i prerequisiti

Assicurarsi che gli host Hyper-V e le macchine virtuali siano conformi ai requisiti.

1. [Verificare](hyper-v-azure-support-matrix.md#on-premises-servers) i requisiti del server locale.
2. [Controllare i requisiti](hyper-v-azure-support-matrix.md#replicated-vms) per le macchine virtuali Hyper-V da replicare in Azure.
3. Controllare le funzionalità di [rete](hyper-v-azure-support-matrix.md#hyper-v-network-configuration) dell'host Hyper-V e il supporto dell'[archiviazione](hyper-v-azure-support-matrix.md#hyper-v-host-storage) host e guest per gli host Hyper-V in locale.
4. Controllare cosa è supportato per le [funzionalità di rete di Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), l'[archiviazione](hyper-v-azure-support-matrix.md#azure-storage) e il [calcolo](hyper-v-azure-support-matrix.md#azure-compute-features), dopo il failover.
5. Le macchine virtuali in locale replicate in Azure devono essere conformi ai [requisiti delle macchine virtuali di Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Predisporre VMM (facoltativo)

Se gli host Hyper-V sono gestiti da VMM, è necessario preparare il server VMM locale. 

- Verificare che il server VMM disponga di almeno un cloud, con uno o più gruppi host. L'host Hyper-V in cui sono in esecuzione le macchine virtuali deve trovarsi nel cloud.
- Preparare il server VMM per il mapping di rete.

### <a name="prepare-vmm-for-network-mapping"></a>Preparare VMM per il mapping di rete

Se si usa VMM, il [mapping di rete](site-recovery-network-mapping.md) esegue il mapping tra reti di VM VMM locali e reti virtuali di Azure. Il mapping garantisce che le macchine virtuali di Azure siano connesse alla rete giusta quando vengono create dopo il failover.

Preparare VMM per il mapping di rete come segue:

1. Assicurarsi di disporre di una [rete logica VMM](https://docs.microsoft.com/system-center/vmm/network-logical) associata al cloud in cui si trovano gli host Hyper-V.
2. Assicurarsi di disporre di una [rete VM](https://docs.microsoft.com/system-center/vmm/network-virtual) collegata alla rete logica.
3. In VMM connettere le VM alla rete VM.

## <a name="verify-internet-access"></a>Verificare l'accesso a Internet

1. Ai fini dell'esercitazione, la configurazione più semplice per gli host Hyper-V e il server VMM, se disponibile, consiste nell'accedere a Internet direttamente, senza ricorrere a un proxy. 
2. Assicurarsi che gli host Hyper-V e il server VMM, se presenti, possano accedere a questi URL: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Assicurarsi che:
    - Se sono presenti regole del firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure.
    - Consentire gli [intervalli IP del data center di Azure ](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443).
    - Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali (usati per il controllo di accesso e la gestione delle identità).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

In uno scenario di failover può essere necessario connettersi alla rete locale replicata.

Per connettersi alle macchine virtuali Windows tramite RDP dopo il failover, consentire l'accesso come segue:

1. Per accedere tramite Internet, abilitare RDP nella macchina virtuale locale prima del failover. Assicurarsi che le regole TCP e UDP siano aggiunte per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.
2. Per accedere tramite VPN da sito a sito, abilitare RDP nel computer locale. RDP deve essere consentito in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.
   Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135). Quando si attiva un failover, nella macchina virtuale non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile accedere alla macchina virtuale fino al completamento dell'aggiornamento.
3. Dopo il failover nella macchina virtuale Windows di Azure selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se non è possibile connettersi, controllare che la macchina virtuale sia in esecuzione e rivedere i [suggerimenti per la risoluzione dei problemi](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Dopo il failover, è possibile accedere alle macchine virtuali di Azure usando lo stesso indirizzo IP della macchina virtuale replicata in locale o un indirizzo IP diverso. [Altre informazioni](concepts-on-premises-to-azure-networking.md) sulla configurazione degli indirizzi IP per il failover.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Set up disaster recovery to Azure for Hyper-V VMs (Configurare il ripristino di emergenza in Azure per le VM Hyper-V)](tutorial-hyper-v-to-azure.md)
> [Set up disaster recovery to Azure for Hyper-V VMs in VMM clouds (Configurare il ripristino di emergenza in Azure per le VM Hyper-V nei cloud VMM)](tutorial-hyper-v-vmm-to-azure.md)
