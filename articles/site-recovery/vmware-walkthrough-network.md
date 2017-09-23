---
title: Pianificare la rete per la replica VMware in Azure | Microsoft Docs
description: Questo articolo illustra la pianificazione di rete necessaria per la replica di VM VMware in Azure
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5578a0b4-0352-41c3-9cce-56beb17a4d97
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f164ac68ba6ec650bb3996b4aa870e1b98533a23
ms.contentlocale: it-it
ms.lasthandoff: 06/29/2017

---

# <a name="step-4-plan-networking-for-vmware-to-azure-replication"></a>Passaggio 4: Pianificare la rete per la replica VMware in Azure

Questo articolo riepiloga le considerazioni relative alla pianificazione di rete quando si esegue la replica di VM VMware locali in Azure usando il servizio [Azure Site Recovery](site-recovery-overview.md).

È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="connect-to-replica-vms"></a>Connettersi alle VM di replica

Quando si pianifica la strategia di replica e failover, una delle domande principali riguarda la modalità di connessione alla VM di Azure dopo il failover. Quando si progetta la strategia di rete per la replica di VM di Azure, ci sono due opzioni:

- **Usare un indirizzo IP diverso**: è possibile scegliere di usare un intervallo di indirizzi IP diverso per la rete di VM di Azure di cui viene eseguita la replica. In questo scenario, la VM ottiene un nuovo indirizzo IP dopo il failover ed è necessario un aggiornamento del DNS.
- **Mantenere lo stesso indirizzo IP**: si potrebbe voler usare lo stesso intervallo di indirizzi del sito primario locale per la rete di Azure dopo il failover. Mantenere gli stessi indirizzi IP semplifica il ripristino riducendo i problemi di rete dopo il failover. Tuttavia, quando si esegue la replica in Azure, è necessario aggiornare le route con la nuova posizione degli indirizzi IP dopo il failover. 


## <a name="retain-ip-addresses"></a>Mantenere gli indirizzi IP

Site Recovery consente di mantenere gli indirizzi IP fissi quando viene eseguito il failover in Azure, con failover sulla subnet.

In caso di failover sulla subnet, è presente una subnet specifica nel sito 1 o nel sito 2, ma mai in entrambi i siti contemporaneamente. Per mantenere lo spazio degli indirizzi IP in caso di failover, è possibile programmare l'infrastruttura del router per lo spostamento delle subnet da un sito a un altro. Durante il failover, le subnet vengono spostate con le VM protette associate. Il principale svantaggio è che in caso di errore è necessario spostare l'intera subnet.


### <a name="failover-example"></a>Esempio di failover

Si esaminerà ora un esempio di failover in Azure.

- Una società fittizia, Woodgrove Bank, ha un'infrastruttura locale che ospita le app aziendali. Le applicazioni per dispositivi mobili sono ospitate in Azure.
- La connettività tra le VM di Woodgrove Bank in Azure e i server locali è fornita da una connessione (VPN) da sito a sito tra la rete perimetrale locale e la rete virtuale di Azure.
- La presenza della VPN implica che la rete virtuale dell'azienda in Azure sia visualizzata come estensione della rete locale.
- Woodgrove vuole usare Site Recovery per replicare i carichi di lavoro locali in Azure.
 - Woodgrove deve tenere conto delle applicazioni e delle configurazioni che dipendono da indirizzi IP hardcoded, quindi deve mantenere gli indirizzi IP delle applicazioni dopo il failover in Azure.
 - Woodgrove ha assegnato gli indirizzi IP dall'intervallo 172.16.1.0/24, 172.16.2.0/24 alle risorse in esecuzione in Azure.


Ecco cosa deve fare Woodgrove per poter replicare le VM in Azure mantenendo gli indirizzi IP:

1. Creare una rete virtuale di Azure Deve trattarsi di un'estensione della rete locale, per consentire un facile failover delle applicazioni.
2. Azure consente di aggiungere connettività VPN da sito a sito, oltre che connettività da punto a sito, alle reti virtuali create in Azure.
3. Quando si configura la connessione da sito a sito, nella rete di Azure è possibile instradare il traffico verso il percorso locale (rete locale) solo se l'intervallo di indirizzi IP è diverso da quello degli indirizzi IP locali.
    - Ciò è dovuto al fatto che Azure non supporta le subnet estese. Se si ha quindi una subnet 192.168.1.0/24 locale, non è possibile aggiungere una rete locale 192.168.1.0/24 nella rete di Azure.
    - Si tratta di un comportamento previsto, perché Azure non sa che non ci sono VM attive nella subnet e che la subnet viene creata solo per scopi di ripristino di emergenza.
    - Per poter instradare correttamente il traffico di rete all'esterno di una rete di Azure, le subnet nella rete e nella rete locale non devono essere in conflitto.

![Prima del failover sulla subnet](./media/site-recovery-network-design/network-design7.png)

### <a name="before-failover"></a>Prima del failover

1. Creare una rete aggiuntiva (ad esempio una rete di ripristino). Si tratta della rete in cui vengono create le VM di cui viene eseguito il failover.
2. Per assicurarsi che l'indirizzo IP per una VM venga mantenuto dopo un failover, nelle proprietà della VM selezionare **Configura**, specificare lo stesso indirizzo IP locale della VM e fare clic su **Salva**.
3. Quando viene eseguito il failover della VM, Azure Site Recovery assegna l'indirizzo IP specificato alla macchina virtuale.

    ![Proprietà di rete](./media/site-recovery-network-design/network-design8.png)

4. Dopo l'attivazione del failover e la creazione delle VM in Azure con l'indirizzo IP richiesto, è possibile connettersi alla rete tramite una [connessione da rete virtuale a rete virtuale](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Questa azione può essere eseguita tramite script.
5. Le route devono essere modificate in modo appropriato per indicare lo spostamento di 192.168.1.0/24 in Azure.

    ![Dopo il failover sulla subnet](./media/site-recovery-network-design/network-design9.png)

### <a name="after-failover"></a>Dopo il failover

Se non si ha una rete di Azure come illustrato in precedenza, è possibile creare una connessione VPN da sito a sito tra il sito primario e Azure, dopo il failover.

## <a name="change-ip-addresses"></a>Modificare gli indirizzi IP

Questo [post di blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) spiega come configurare l'infrastruttura di rete di Azure quando non è necessario mantenere gli indirizzi IP dopo il failover. Inizia con una descrizione dell'applicazione, analizza come impostare la rete locale e in Azure e si conclude con informazioni sull'esecuzione del failover.  

## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 5: Preparare Azure](vmware-walkthrough-prepare-azure.md)

