---
title: Personalizzare le configurazioni di rete per una macchina virtuale di failover Documenti Microsoft
description: Viene fornita una panoramica delle configurazioni di rete per personalizzare le configurazioni di rete per una macchina virtuale di failover nella replica delle macchine virtuali di Azure tramite Azure Site Recovery.Provides an overview of customize networking configurations for a failover VM in the replication of Azure VMs using Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292859"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizzare le configurazioni di rete della VM di Azure di destinazione

Questo articolo fornisce indicazioni sulla personalizzazione delle configurazioni di rete nella macchina virtuale (VM) di Azure di destinazione durante la replica e il ripristino di macchine virtuali di Azure da un'area a un'altra, usando [Azure Site Recovery.This](site-recovery-overview.md)article provides guidance on customizing networking configurations on the target Azure virtual machine (VM) when you're replicating and recovering Azure VMs from one region to another, using Azure Site Recovery .

## <a name="before-you-start"></a>Prima di iniziare

Informazioni sul ripristino di emergenza fornito da Site Recovery per [questo scenario](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Risorse di rete supportate

È possibile fornire le configurazioni delle risorse chiave seguenti per la macchina virtuale di failover durante la replica delle macchine virtuali di Azure:You can provide the following key resource configurations for the failover VM while replicating Azure VMs:

- [Servizio di bilanciamento del carico internoInternal load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [IP pubblico](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) sia per la subnet che per la scheda di interfaccia di reteNetwork security group both for the subnet and for the NIC

## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi di pianificare in anticipo le configurazioni lato di ripristino.
- Creare le risorse di rete in anticipo. Fornire come input in modo che il servizio Azure Site Recovery possa rispettare queste impostazioni e garantire che la macchina virtuale di failover aderisca a queste impostazioni.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Personalizzare le configurazioni di rete di failover e di failover di failoverCustomize failover and test failover networking configurations

1. Passare a **Elementi replicati**. 
2. Selezionare la macchina virtuale di Azure desiderata.
3. Selezionare **Calcola e rete,** quindi **Selezionare Modifica**. Si noti che le impostazioni di configurazione NIC includono le risorse corrispondenti nell'origine. 

     ![Personalizzare le configurazioni di rete di failoverCustomize the failover networking configurations](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selezionare una rete virtuale di failover di test. È possibile scegliere di lasciarlo vuoto e selezionarne uno al momento del failover di test.
5. La rete di failover è Seleziona **modifica** vicino alla scheda di interfaccia di rete che si desidera configurare. Nel pannello successivo che si apre selezionare le risorse precreate corrispondenti nel percorso di failover e failover del test.

    ![Modificare la configurazione NIC](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Selezionare **OK**.

Site Recovery ora rispetterà queste impostazioni e assicurerà che la macchina virtuale in failover sia connessa alla risorsa selezionata tramite la scheda di interfaccia di rete corrispondente.

Quando si attiva il failover di test tramite il piano di ripristino, verrà sempre chiesto alla rete virtuale di Azure.When you trigger the test failover via Recovery Plan, it will always ask the Azure virtual network. Questa rete virtuale verrà utilizzata per il failover di test per i computer che non hanno impostazioni di failover di test preconfigurate.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="unable-to-view-or-select-a-resource"></a>Impossibile visualizzare o selezionare una risorsa

Se non è possibile selezionare o visualizzare una risorsa di rete, eseguire i controlli e le condizioni seguenti:If you can't select or view a networking resource, go through the following checks and conditions:

- Il campo di destinazione per una risorsa di rete è abilitato solo se la macchina virtuale di origine ha un input corrispondente. Ciò si basa sul principio che per uno scenario di ripristino di emergenza, si desidera la versione esatta o ridotta dell'origine.
- Per ogni risorsa di rete, alcuni filtri vengono applicati nell'elenco a discesa per garantire che la macchina virtuale di failover possa connettersi alla risorsa selezionata e che l'affidabilità del failover venga mantenuta. Questi filtri si basano sulle stesse condizioni di rete che sarebbero state verificate durante la configurazione della macchina virtuale di origine.

Convalide del servizio di bilanciamento del carico interno:Internal load balancer validations:

- La sottoscrizione e l'area del servizio di bilanciamento del carico e la macchina virtuale di destinazione devono essere uguali.
- La rete virtuale associata al servizio di bilanciamento del carico interno e a quella della macchina virtuale di destinazione deve essere la stessa.
- Lo SKU IP pubblico della macchina virtuale di destinazione e lo SKU del servizio di bilanciamento del carico interno devono essere uguali.
- Se la macchina virtuale di destinazione è configurata per essere inserita in una zona di disponibilità, verificare se il servizio di bilanciamento del carico è ridondante di zona o parte di qualsiasi zona di disponibilità. In questo caso, i servizi di bilanciamento del carico SKU di base non supportano le zone e non verranno visualizzati nell'elenco a discesa.
- Verificare che il servizio di bilanciamento del carico interno disponga di un pool back-end e di una configurazione front-end precreati.

Indirizzo IP pubblico:

- La sottoscrizione e l'area dell'indirizzo IP pubblico e della macchina virtuale di destinazione devono essere uguali.
- Lo SKU IP pubblico della macchina virtuale di destinazione e lo SKU del servizio di bilanciamento del carico interno devono essere uguali.

Gruppo di sicurezza di rete:Network security group:
- La sottoscrizione e l'area del gruppo di sicurezza di rete e della macchina virtuale di destinazione devono essere uguali.


> [!WARNING]
> Se la macchina virtuale di destinazione è associata a un set di disponibilità, è necessario associare l'indirizzo IP pubblico e il servizio di bilanciamento del carico interno dello stesso SKU a quelli dell'INDIRIZZO IP pubblico e del servizio di bilanciamento del carico interno dell'altra macchina virtuale nel set di disponibilità. In caso contrario, il failover potrebbe non riuscire.
