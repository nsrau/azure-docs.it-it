---
title: "Requisiti di rete per la connettività delle macchine virtuali dopo il failover in Azure con Azure Site Recovery | Microsoft Docs"
description: Linee guida relative ai requisiti di rete per la connessione alle macchine virtuali di Azure dopo il failover da un sito locale con Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: f02cdbea-0940-48bf-9fa5-f38d9e584fae
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/12/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 01c8e664465350b9dd382502c65cc3fda350797c
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="networking-for-vm-connectivity-after-failover"></a>Requisiti di rete per la connettività delle macchine virtuali dopo il failover

Questo articolo presenta i requisiti di rete per la connessione alle macchine virtuali di Azure dopo aver usato il servizio [Azure Site Recovery](site-recovery-overview.md) per la replica e il failover in Azure.

Questo articolo contiene informazioni relative agli argomenti seguenti:

> [!div class="checklist"]
> * Metodi di connessione che è possibile usare
> * Come usare un indirizzo IP diverso per le macchine virtuali di Azure replicate
> * Come mantenere gli indirizzi IP per le macchine virtuali di Azure dopo il failover

## <a name="connecting-to-replica-vms"></a>Connessione alle macchine virtuali di replica

Quando si pianifica la strategia di replica e di failover, una delle domande principali che si deve porre riguarda la modalità di connessione alla VM di Azure dopo il failover. Quando si progetta la strategia di rete per la replica di VM di Azure, ci sono due opzioni:

- **Usare un indirizzo IP diverso**: è possibile scegliere di usare un intervallo di indirizzi IP diverso per la rete di VM di Azure di cui viene eseguita la replica. In questo scenario, la VM ottiene un nuovo indirizzo IP dopo il failover ed è necessario un aggiornamento del DNS.
- **Mantenere lo stesso indirizzo IP**: si potrebbe voler usare lo stesso intervallo di indirizzi del sito primario locale per la rete di Azure dopo il failover. Mantenere gli stessi indirizzi IP semplifica il ripristino riducendo i problemi di rete dopo il failover. Quando si esegue la replica in Azure, è tuttavia necessario aggiornare le route con la nuova posizione degli indirizzi IP dopo il failover. 

## <a name="retaining-ip-addresses"></a>Mantenimento degli indirizzi IP

Site Recovery consente di mantenere gli indirizzi IP fissi durante il failover in Azure, con un failover sulla subnet.

- Con il failover sulla subnet è presente una subnet specifica nel sito 1 o nel sito 2, ma mai in entrambi i siti contemporaneamente.
- Per mantenere lo spazio degli indirizzi IP in caso di failover, si programma l'infrastruttura del router per lo spostamento delle subnet da un sito a un altro.
- Durante il failover, le subnet vengono spostate con le VM protette associate. Il principale svantaggio è che in caso di errore è necessario spostare l'intera subnet.


### <a name="failover-example"></a>Esempio di failover

Di seguito viene descritto un failover di esempio in Azure usando una società fittizia, Woodgrove Bank.

- Woodgrove Bank ospita le app aziendali in un sito locale. La società ospita le proprie app per dispositivi mobili in Azure.
- Tra la rete perimetrale locale della società e la rete virtuale di Azure vi è connettività VPN da sito a sito. A causa della connessione VPN, la rete virtuale in Azure viene visualizzata come estensione della rete locale.
- Woodgrove vuole eseguire la replica dei carichi di lavoro locali in Azure con Site Recovery.
 - Poiché Woodgrove ha alcune app che dipendono da indirizzi IP hardcoded, deve mantenere gli indirizzi IP delle app dopo il failover in Azure.
 - Le risorse in esecuzione in Azure usano l'intervallo di indirizzi IP compreso tra 172.16.1.0/24 e 172.16.2.0/24.

![Prima del failover sulla subnet](./media/site-recovery-network-design/network-design7.png)

**Infrastruttura prima del failover**


Ecco cosa deve fare Woodgrove per poter replicare le macchine virtuali in Azure mantenendo gli indirizzi IP:


1. Creare una rete virtuale di Azure in cui verranno create le macchine virtuali di Azure dopo il failover dei computer locali. Deve trattarsi di un'estensione della rete locale, per agevolare il failover delle applicazioni.
2. Prima del failover, in Site Recovery la società assegna lo stesso indirizzo IP nelle proprietà del computer. Dopo il failover, Site Recovery assegna questo indirizzo alla macchina virtuale di Azure.
3. Dopo l'esecuzione del failover e dopo la creazione delle macchine virtuali di Azure con lo stesso indirizzo IP, queste si connettono alla rete usando una [connessione da rete virtuale a rete virtuale](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Questa azione può essere eseguita tramite script.
4. È necessario modificare le route, in modo che riflettano il passaggio dell'indirizzo 192.168.1.0/24 ad Azure.


**Infrastruttura dopo il failover**

![Dopo il failover sulla subnet](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Connessione da sito a sito

Oltre alla connessione da rete virtuale a rete virtuale, dopo il failover Woodgrove può configurare la connettività VPN da sito a sito:
- Quando si configura una connessione da sito a sito, nella rete di Azure è possibile instradare il traffico verso l'ambiente locale (rete locale) solo se l'intervallo di indirizzi IP è diverso da quello degli indirizzi IP locali. Ciò è dovuto al fatto che Azure non supporta le subnet estese. Se è presente una subnet 192.168.1.0/24 in locale, non è possibile aggiungere una rete locale 192.168.1.0/24 nella rete di Azure. Si tratta di un comportamento previsto, perché Azure non sa che non ci sono VM attive nella subnet e che la subnet viene creata solo per scopi di ripristino di emergenza.
- Per poter instradare correttamente il traffico di rete all'esterno di una rete di Azure, le subnet nella rete e nella rete locale non devono essere in conflitto.




## <a name="assigning-new-ip-addresses"></a>Assegnazione di nuovi indirizzi IP

Questo [post di blog](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) spiega come configurare l'infrastruttura di rete di Azure quando non è necessario mantenere gli indirizzi IP dopo il failover. Inizia con una descrizione dell'applicazione, analizza come impostare la rete locale e in Azure e conclude con informazioni sull'esecuzione dei failover. 

## <a name="next-steps"></a>Passaggi successivi
[Eseguire un failover](site-recovery-failover.md)





