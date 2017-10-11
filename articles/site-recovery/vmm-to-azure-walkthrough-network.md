---
title: Pianificare la rete per la replica Hyper-V (con System Center VMM) in Azure con Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra la pianificazione di rete necessaria per la replica di VM Hyper-V (con VMM) in Azure
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ef87cd82b021e40f0da05142878daff245cd9c62
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2017
---
# <a name="step-4-plan-networking-for-hyper-v-with-vmm-to-azure-replication"></a>Passaggio 4: Pianificare la rete per la replica Hyper-V in Azure (con VMM)

Dopo avere eseguito la [pianificazione della capacità](vmm-to-azure-walkthrough-capacity.md), se si esegue una distribuzione completa, leggere questo articolo per ottenere informazioni sulle considerazioni relative alla pianificazione della rete in caso di replica di VM Hyper-V locali nei cloud System Center Virtual Machine Manager (VMM) in Azure tramite il servizio [Azure Site Recovery](site-recovery-overview.md).

È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="network-mapping-for-replication-to-azure"></a>Mapping di rete per la replica in Azure

Il mapping di rete viene usato durante la replica di VM Hyper-V (gestite in VMM) in Azure. Il mapping di rete esegue il mapping tra reti VM in un server VMM e reti di Azure di destinazione. Il mapping esegue queste operazioni:

- **Connessione di rete**: dopo il failover, tutte le VM di Azure replicate sono connesse alla rete di Azure mappata. Tutte le macchine virtuali che eseguono il failover nella stessa rete possono connettersi tra loro, anche se hanno eseguito il failover in piani di ripristino diversi.
- **Gateway di rete**: se nella rete di Azure di destinazione è configurato un gateway di rete, le macchine virtuali possono connettersi ad altre macchine virtuali locali nella rete locale mappata.

### <a name="prepare-vmm-for-network-mapping"></a>Preparare VMM per il mapping di rete

Preparare VMM per il mapping di rete come segue:

1. Se non è già disponibile, preparare una [rete logica VMM](https://docs.microsoft.com/system-center/vmm/network-logical) associata al cloud in cui si trovano gli host Hyper-V.
2. Se non è già disponibile, creare una [rete VM](https://docs.microsoft.com/system-center/vmm/network-virtual) collegata alla rete logica preparata in precedenza.
3. Connettere le macchine virtuali nel server host/cluster Hyper-V del cloud VMM alla rete VM.

 
Si noti che: 
- Le nuove VM vengono aggiunte alla rete VM di origine e vengono connesse alla rete di Azure mappata quando si verifica la replica.
- Se la rete di destinazione dispone di più subnet e una di esse ha lo stesso nome di una subnet in cui si trova la macchina virtuale di origine, la macchina virtuale di replica si connette a questa subnet di destinazione dopo il failover.
- Se non è presente una subnet di destinazione con un nome corrispondente, la macchina virtuale si connette alla prima subnet della rete.
- Preparare una rete di Azure e configurare i mapping di rete durante la distribuzione dello scenario.

## <a name="connecting-to-azure-vms-after-failover"></a>Connessione alle VM di Azure dopo il failover

Quando si pianifica la strategia di replica e di failover, una delle domande principali che si deve porre riguarda la modalità di connessione alla VM di Azure dopo il failover. Quando si progetta la strategia di rete per la replica di VM di Azure, sono disponibili due opzioni:

- **Usare un indirizzo IP diverso**: è possibile scegliere di usare un intervallo di indirizzi IP diverso per la rete di VM di Azure di cui viene eseguita la replica. In questo scenario, la VM ottiene un nuovo indirizzo IP dopo il failover ed è necessario un aggiornamento del DNS.
- **Mantenere lo stesso indirizzo IP**: si potrebbe voler usare lo stesso intervallo di indirizzi della rete locale primaria per la rete di Azure dopo il failover.  Il mantenimento degli stessi indirizzi IP semplifica il ripristino riducendo i problemi di rete dopo il failover. Quando si esegue la replica in Azure, è tuttavia necessario aggiornare le route con la nuova posizione degli indirizzi IP dopo il failover.


## <a name="retain-ip-addresses"></a>Mantenere gli indirizzi IP

Site Recovery consente di mantenere gli indirizzi IP fissi durante il failover in Azure, con un failover sulla subnet.

Con il failover sulla subnet è presente una subnet specifica nel sito 1 o nel sito 2, ma mai in entrambi i siti contemporaneamente. Per mantenere lo spazio degli indirizzi IP in caso di failover, si programma l'infrastruttura del router per lo spostamento delle subnet da un sito a un altro. Durante il failover, le subnet vengono spostate con le VM protette associate. Il principale svantaggio è che in caso di errore è necessario spostare l'intera subnet.



### <a name="failover-example"></a>Esempio di failover

Esaminiamo ora un esempio di failover in Azure.

- Una società fittizia, Woodgrove Bank, dispone di un'infrastruttura locale che ospita le app aziendali. Le applicazioni per dispositivi mobili sono ospitate in Azure.
- La connettività tra le VM di Woodgrove Bank in Azure e i server locali è fornita da una connessione (VPN) da sito a sito tra la rete perimetrale locale e la rete virtuale di Azure.
- La presenza della VPN implica che la rete virtuale dell'azienda in Azure sia visualizzata come estensione della rete locale.
- Woodgrove vuole usare Site Recovery per replicare i carichi di lavoro locali in Azure.
 - Woodgrove deve tenere conto delle applicazioni e delle configurazioni che dipendono da indirizzi IP hardcoded, quindi deve mantenere gli indirizzi IP delle applicazioni dopo il failover in Azure.
 - Woodgrove ha assegnato gli indirizzi IP nell'intervallo 172.16.1.0/24, 172.16.2.0/24 alle risorse in esecuzione in Azure.


Ecco cosa deve fare Woodgrove per poter replicare le macchine virtuali in Azure mantenendo gli indirizzi IP:

1. Creare una rete virtuale di Azure Deve trattarsi di un'estensione della rete locale, per agevolare il failover delle applicazioni.
2. Azure consente di aggiungere connettività VPN da sito a sito, oltre che connettività da punto a sito, alle reti virtuali create in Azure.
3. Quando si configura la connessione da sito a sito, nella rete di Azure è possibile instradare il traffico verso il percorso locale (rete locale) solo se l'intervallo di indirizzi IP è diverso da quello degli indirizzi IP locali.
    - Ciò è dovuto al fatto che Azure non supporta le subnet estese. Se si ha quindi una subnet 192.168.1.0/24 locale, non è possibile aggiungere una rete locale 192.168.1.0/24 nella rete di Azure.
    - Si tratta di un comportamento previsto, perché Azure non sa che non ci sono VM attive nella subnet e che la subnet viene creata solo per scopi di ripristino di emergenza.
    - Per poter instradare correttamente il traffico di rete all'esterno di una rete di Azure, le subnet nella rete e nella rete locale non devono essere in conflitto.

![Prima del failover sulla subnet](./media/vmm-to-azure-walkthrough-network/network-design7.png)

### <a name="before-failover"></a>Prima del failover

1. Creare una rete aggiuntiva (ad esempio una rete di ripristino). Si tratta della rete in cui vengono create le VM di cui viene eseguito il failover.
2. Per assicurarsi che l'indirizzo IP per una VM venga mantenuto dopo un failover, nelle proprietà della VM selezionare **Configura**, specificare lo stesso indirizzo IP locale della VM e fare clic su **Salva**.
3. Quando viene eseguito il failover della VM, Azure Site Recovery assegna l'indirizzo IP specificato alla macchina virtuale.

    ![Proprietà di rete](./media/vmm-to-azure-walkthrough-network/network-design8.png)

4. Dopo l'attivazione del failover e la creazione delle VM in Azure con l'indirizzo IP richiesto, è possibile connettersi alla rete tramite una [connessione da rete virtuale a rete virtuale](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Questa azione può essere eseguita tramite script.
5. Le route devono essere modificate adeguatamente in modo da indicare lo spostamento di 192.168.1.0/24 ad Azure.

    ![Dopo il failover sulla subnet](./media/vmm-to-azure-walkthrough-network/network-design9.png)

### <a name="after-failover"></a>Dopo il failover

Se non si ha una rete di Azure come illustrato in precedenza, è possibile creare una connessione VPN da sito a sito tra il sito primario e Azure, dopo il failover.

## <a name="change-ip-addresses"></a>Modificare gli indirizzi IP

Questo [post di blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) spiega come configurare l'infrastruttura di rete di Azure quando non è necessario mantenere gli indirizzi IP dopo il failover. Inizia con una descrizione dell'applicazione, analizza come impostare la rete locale e in Azure e conclude con informazioni sull'esecuzione dei failover.  

## <a name="next-steps"></a>Passaggi successivi

Andare al [Passaggio 5: Preparare Azure](vmm-to-azure-walkthrough-prepare-azure.md)
