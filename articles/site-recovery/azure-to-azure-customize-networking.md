---
title: Personalizzare le configurazioni di rete per una macchina virtuale di failover | Microsoft Docs
description: Viene fornita una panoramica della personalizzazione delle configurazioni di rete per una macchina virtuale di failover nella replica di macchine virtuali di Azure con Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 191161c8185f45712052000285013a6e61c9fa6a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968882"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizzare le configurazioni di rete della VM di Azure di destinazione

Questo articolo fornisce informazioni aggiuntive sulla personalizzazione delle configurazioni di rete nella macchina virtuale (VM) di Azure di destinazione quando si esegue la replica e il ripristino di VM di Azure da un'area a un'altra, usando [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Prima di iniziare

Informazioni sul ripristino di emergenza fornito da Site Recovery per [questo scenario](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Risorse di rete supportate

È possibile fornire le configurazioni di risorse chiave seguenti per la macchina virtuale di failover durante la replica di macchine virtuali di Azure:

- [Servizio di bilanciamento del carico interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) per la subnet e per la scheda di interfaccia di rete

## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di pianificare in anticipo le configurazioni del lato di ripristino.
- Creare le risorse di rete in anticipo. Fornirla come input in modo che Azure Site Recovery servizio possano rispettare queste impostazioni e assicurarsi che la macchina virtuale di failover rispetti queste impostazioni.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Personalizzare le configurazioni di rete di failover e failover di test

1. Passare a **elementi replicati**. 
2. Selezionare la macchina virtuale di Azure desiderata.
3. Selezionare **calcolo e rete** e quindi **modifica**. Si noti che le impostazioni di configurazione della scheda di interfaccia di rete includono le risorse corrispondenti nell'origine. 

     ![Personalizzare le configurazioni di rete di failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selezionare una rete virtuale di failover di test. È possibile scegliere di lasciarlo vuoto e selezionarne uno al momento del failover di test.
5. Rete di failover è selezionato **modifica** accanto alla scheda di interfaccia di rete che si desidera configurare. Nel pannello successivo visualizzato selezionare le risorse create in precedenza corrispondenti nel failover di test e nel percorso di failover.

    ![Modificare la configurazione della scheda di interfaccia di rete](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Selezionare **OK**.

Site Recovery ora rispetta queste impostazioni e garantisce che la macchina virtuale in failover sia connessa alla risorsa selezionata tramite la NIC corrispondente.

Quando si attiva il failover di test tramite il piano di ripristino, viene sempre richiesta la rete virtuale di Azure. Questa rete virtuale verrà usata per il failover di test per i computer in cui non sono già state configurate le impostazioni del failover di test.

## <a name="troubleshooting"></a>risoluzione dei problemi

### <a name="unable-to-view-or-select-a-resource"></a>Non è possibile visualizzare o selezionare una risorsa

Se non è possibile selezionare o visualizzare una risorsa di rete, eseguire i controlli e le condizioni seguenti:

- Il campo di destinazione per una risorsa di rete è abilitato solo se la macchina virtuale di origine dispone di un input corrispondente. Si basa sul principio che, per uno scenario di ripristino di emergenza, si desidera la versione esatta o ridotta dell'origine.
- Per ogni risorsa di rete, vengono applicati alcuni filtri nell'elenco a discesa per assicurarsi che la macchina virtuale di failover possa collegarsi alla risorsa selezionata e che venga mantenuta l'affidabilità del failover. Questi filtri sono basati sulle stesse condizioni di rete che verrebbero verificate durante la configurazione della VM di origine.

Convalide del servizio di bilanciamento del carico interno:

- La sottoscrizione e l'area del servizio di bilanciamento del carico e la macchina virtuale di destinazione devono essere uguali.
- La rete virtuale associata al servizio di bilanciamento del carico interno e quella della macchina virtuale di destinazione devono essere uguali.
- Lo SKU dell'IP pubblico della macchina virtuale di destinazione e lo SKU del servizio di bilanciamento del carico interno devono essere uguali.
- Se la macchina virtuale di destinazione è configurata per essere posizionata in una zona di disponibilità, controllare se il servizio di bilanciamento del carico è con ridondanza della zona o parte di una zona di disponibilità. (I bilanciamenti del carico SKU Basic non supportano le zone e non verranno visualizzati nell'elenco a discesa in questo caso).
- Verificare che il servizio di bilanciamento del carico interno disponga di un pool back-end e di una configurazione front-end già creati.

Indirizzo IP pubblico:

- La sottoscrizione e la regione dell'indirizzo IP pubblico e della macchina virtuale di destinazione devono essere uguali.
- Lo SKU dell'IP pubblico della macchina virtuale di destinazione e lo SKU del servizio di bilanciamento del carico interno devono essere uguali.

Gruppo di sicurezza di rete:
- La sottoscrizione e la regione del gruppo di sicurezza di rete e della macchina virtuale di destinazione devono essere uguali.


> [!WARNING]
> Se la macchina virtuale di destinazione è associata a un set di disponibilità, è necessario associare l'indirizzo IP pubblico e il servizio di bilanciamento del carico interno dello stesso SKU con quello dell'IP pubblico e del servizio di bilanciamento del carico interno dell'altra VM nel set di disponibilità. In caso contrario, il failover potrebbe non riuscire.
