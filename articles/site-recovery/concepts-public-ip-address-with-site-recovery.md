---
title: Usare gli indirizzi IP pubblici dopo il failover con Azure Site Recovery | Microsoft Docs
description: Questo articolo descrive come configurare gli indirizzi IP pubblici con Azure Site Recovery e Gestione traffico di Microsoft Azure per il ripristino di emergenza e la migrazione
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: mayg
ms.openlocfilehash: 80c38ecc766d60fba578e877998aeb216ea66012
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215277"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Configurare gli indirizzi IP pubblici dopo il failover

Gli indirizzi IP pubblici consentono la comunicazione in ingresso dalle risorse Internet alle risorse di Azure, nonché la comunicazione in uscita dalle risorse di Azure a Internet e servizi pubblici di Azure con un indirizzo IP assegnato alla risorsa.
- Comunicazioni in ingresso da Internet alla risorsa, ad esempio macchine virtuali (VM) di Azure, i gateway applicazione Azure, Azure Load Balancer, i gateway VPN di Azure e altro. È comunque possibile comunicare con alcune risorse, quali le macchine virtuali da Internet se una macchina virtuale non dispone di un indirizzo IP pubblico assegnato, fino a quando la macchina virtuale fa parte di un pool back-end di bilanciamento del carico a cui viene assegnato un indirizzo IP pubblico.
- Connettività in uscita verso Internet tramite un indirizzo IP prevedibile. Una macchina virtuale può, ad esempio, comunicare in uscita con Internet senza avere un indirizzo IP pubblico assegnato: per impostazione predefinita il relativo indirizzo sarà l'indirizzo di rete convertito da Azure in un indirizzo pubblico non prevedibile. L'assegnazione di un indirizzo IP pubblico a una risorsa consente di conoscere l'indirizzo IP usato per la connessione in uscita. Anche se prevedibile, l'indirizzo può essere modificato, a seconda del metodo di assegnazione scelto. Per altre informazioni, vedere [Creare un indirizzo IP pubblico](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Per altre informazioni sulle connessioni in uscita dalle risorse di Azure, vedere [Connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

In Azure Resource Manager un indirizzo IP pubblico è una risorsa che ha proprietà specifiche. Alcune delle risorse a cui è possibile associare una risorsa indirizzo IP pubblico sono:

* Interfacce di rete di macchina virtuale
* Servizi di bilanciamento del carico con connessione Internet
* Gateway VPN
* Gateway di applicazione

Questo articolo descrive come è possibile usare gli indirizzi IP pubblici con Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Assegnazione di indirizzi IP pubblici usando il piano di ripristino

L'indirizzo IP pubblico dell'applicazione di produzione **non può essere mantenuto in caso di failover**. I carichi di lavoro avviati come parte del processo di failover devono essere assegnati a una risorsa IP pubblica di Azure disponibile nell'area di destinazione. Questo passaggio può essere eseguito manualmente o in modo automatico con i piani di ripristino. Un piano di ripristino raccoglie i computer in gruppi di ripristino. Consente di definire un processo di ripristino sistematico. È possibile usare un piano di ripristino per determinare l'ordine e automatizzare le azioni necessarie in ogni passaggio, usando i runbook di Automazione di Azure per il failover in Azure oppure gli script.

La configurazione è la seguente:
- Creare un [piano di ripristino](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) e raggruppare i carichi di lavoro nel piano in base alle esigenze.
- Personalizzare il piano aggiungendo un passaggio per associare un indirizzo IP pubblico usando gli script dei [Runbook di Automazione di Azure](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) nella macchina virtuale su cui si è verificato il failover.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Commutazione degli endpoint pubblici con il routing di livello DNS

Gestione traffico di Microsoft Azure consente il routing a livello DNS tra gli endpoint e può aiutare a [ridurre gli RTO](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) per uno scenario di ripristino di emergenza. 

Altre informazioni sugli scenari di failover con Gestione traffico:
1. [Failover da locale ad Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) con Gestione traffico 
2. [Failover da Azure ad Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) con Gestione traffico 

La configurazione è la seguente:
- Creare un [profilo di Gestione traffico](../traffic-manager/traffic-manager-create-profile.md).
- Usando il metodo di routing **Priorità**, creare due endpoint: **Primario** per l'origine e **Failover** per Azure. All'endpoint **Primario** viene assegnata la priorità 1, mentre all'endpoint **Failover** viene assegnata la priorità 2.
- L'endpoint **Primario** può essere [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) oppure [Esterno](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) a seconda che l'ambiente di origine sia all'interno o all'esterno di Azure.
- L'endpoint **Failover** viene creato come un endpoint di **Azure**. Usare un **indirizzo IP pubblico statico** poiché si tratta di un endpoint esterno per Gestione traffico nell'evento di emergenza.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Gestione traffico di Azure con Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Leggere altre informazioni sui [metodi di routing](../traffic-manager/traffic-manager-routing-methods.md) di Gestione traffico.
- Leggere altre informazioni sui [piani di ripristino](site-recovery-create-recovery-plans.md) per automatizzare il failover delle applicazioni.
