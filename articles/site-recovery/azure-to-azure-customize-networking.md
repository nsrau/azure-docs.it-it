---
title: Personalizzare le configurazioni di rete per la macchina virtuale di failover | Microsoft Docs
description: Viene fornita una panoramica della personalizzazione delle configurazioni di rete per la macchina virtuale di failover nella replica di macchine virtuali di Azure con Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 907a698a675a039dfdc852210adecb94c7bfab25
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886897"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizzare le configurazioni di rete della macchina virtuale di Azure di destinazione

Questo articolo fornisce informazioni aggiuntive sulla personalizzazione delle configurazioni di rete nella macchina virtuale di Azure di destinazione quando si esegue la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra, usando [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Prima di iniziare

Informazioni sul ripristino di emergenza fornito da Site Recovery per [questo scenario](azure-to-azure-architecture.md).

## <a name="support-networking-resources"></a>Supportare le risorse di rete

Per la macchina virtuale di failover è possibile specificare le configurazioni di risorse chiave seguenti durante la replica di macchine virtuali di Azure.

- [Servizio di bilanciamento del carico interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) per la subnet e per la scheda di interfaccia di rete

## <a name="pre-requisites"></a>Prerequisiti

- Assicurarsi di pianificare in anticipo le configurazioni del lato di ripristino.
- È necessario creare le risorse di rete in anticipo. Fornirla come input in modo che Azure Site Recovery servizio possano rispettare queste impostazioni e assicurarsi che la macchina virtuale di failover rispetti queste impostazioni.

## <a name="steps-to-customize-failover-networking-configurations"></a>Passaggi per personalizzare le configurazioni di rete di failover

1. Passare a **elementi replicati**. 
2. Fare clic sulla macchina virtuale di Azure desiderata.
3. Fare clic su **calcolo e rete**, quindi su **modifica**. Si noterà che le impostazioni di configurazione della scheda di interfaccia di rete includono le risorse corrispondenti nell'origine. 

     ![Personalizza](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Fare clic su **modifica** accanto alla scheda di interfaccia di rete che si desidera configurare. Nel pannello successivo visualizzato selezionare le risorse create in precedenza corrispondenti nella destinazione.

    ![NIC-drill-down](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Fare clic su **OK**.

Site Recovery ora rispetta queste impostazioni e garantisce che la macchina virtuale in failover sia connessa alla risorsa selezionata tramite la NIC corrispondente.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="unable-to-view-or-select-a-resource"></a>Non è possibile visualizzare o selezionare una risorsa

Se non si è in grado di selezionare o visualizzare una risorsa di rete, effettuare le seguenti verifiche & condizioni:

- Il campo di destinazione per una risorsa di rete viene abilitato solo se la macchina virtuale di origine dispone di un input corrispondente. Questo si basa sul principio che, per uno scenario di ripristino di emergenza, è necessario specificare la versione esatta o ridotta dell'origine.
- Per ogni risorsa di rete in questione, vengono applicati alcuni filtri nell'elenco a discesa per assicurarsi che la macchina virtuale di failover possa collegarsi alla risorsa selezionata e che venga mantenuta l'affidabilità del failover. Questi filtri sono basati sulle stesse condizioni di rete che verrebbero verificate durante la configurazione della VM di origine.

Convalide del servizio di bilanciamento del carico interno:

1. La sottoscrizione e l'area di LB e la macchina virtuale di destinazione devono essere uguali.
2. La rete virtuale associata al Load Balancer interno e quella della VMshould di destinazione sono uguali.
3. Lo SKU dell'IP pubblico della macchina virtuale di destinazione e lo SKU del LoadBalancer interno devono essere uguali.
4. Se la macchina virtuale di destinazione è configurata per essere posizionata in una zona di disponibilità, controllare se il servizio di bilanciamento del carico è con ridondanza della zona o parte di una zona di disponibilità. I bilanciamenti del carico SKU Basic non supportano le zone e non verranno visualizzati nell'elenco a discesa in questo caso.
5. Verificare che il LoadBalancer interno disponga di un pool back-end e di una configurazione front-end già creati.


Indirizzo IP pubblico:
    
1. La sottoscrizione e l'area dell'IP pubblico e la macchina virtuale di destinazione devono essere uguali.
2. Lo SKU dell'IP pubblico della macchina virtuale di destinazione e lo SKU del LoadBalancer interno devono essere uguali.

Gruppo di sicurezza di rete:
1. La sottoscrizione e l'area del gruppo di sicurezza di rete e la macchina virtuale di destinazione devono essere uguali.


> [!WARNING]
> Se la macchina virtuale di destinazione è associata a un set di disponibilità, è necessario associare il servizio di bilanciamento del carico interno/IP pubblico dello stesso SKU del servizio di bilanciamento del carico interno/IP pubblico di altre VM nel set di disponibilità. In caso contrario, potrebbe verificarsi un errore di failover.
