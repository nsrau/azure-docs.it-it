---
title: Concetti chiave per l'amministrazione della soluzione VMware di Azure con CloudSimple
description: Vengono descritti i concetti chiave per l'amministrazione della soluzione VMware di Azure con CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e434065feb218b0a46220b95cc6bfd9a7033de30
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816120"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Concetti chiave per l'amministrazione della soluzione VMware di Azure di CloudSimple

Per l'amministrazione della soluzione VMware di Azure da parte di CloudSimple è necessario comprendere i concetti seguenti:

* Servizio CloudSimple, che viene visualizzato come soluzione VMware di Azure da CloudSimple-Service
* Nodo CloudSimple, che viene visualizzato come soluzione VMware di Azure da CloudSimple-node
* Cloud privato CloudSimple
* Rete del servizio
* Macchina virtuale CloudSimple, che viene visualizzata come soluzione VMware di Azure da CloudSimple-macchina virtuale

## <a name="cloudsimple-service"></a>Servizio CloudSimple

Con il servizio CloudSimple è possibile creare e gestire tutte le risorse associate alle soluzioni VMware tramite CloudSimple dal portale di Azure. Creare una risorsa di servizio in ogni area in cui si intende usare il servizio.

Altre informazioni sul [servizio CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Nodo CloudSimple

Un nodo CloudSimple è un host di calcolo e archiviazione con iperconvergenza e bare metal dedicato in cui viene distribuito il VMware ESXi hypervisor. Questo nodo viene quindi incorporato nelle piattaforme VMware vSphere, vCenter, rete VSAN e NSX. Sono abilitati anche i servizi di rete CloudSimple e i servizi di rete perimetrale. Ogni nodo funge da unità di capacità di calcolo e di archiviazione di cui è possibile eseguire il provisioning per creare [cloud privati CloudSimple](cloudsimple-private-cloud.md). Si effettua il provisioning o si riservano i nodi in un'area in cui è disponibile il servizio CloudSimple.


Altre informazioni sui [nodi CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Cloud privato CloudSimple

Un cloud privato CloudSimple è un ambiente di stack VMware isolato gestito da un server vCenter nel proprio dominio di gestione. Lo stack VMware include host ESXi, vSphere, vCenter, rete VSAN e NSX. Lo stack viene eseguito su nodi dedicati (hardware bare metal dedicato e isolato) e viene utilizzato dagli utenti tramite strumenti VMware nativi che includono vCenter e NSX Manager. I nodi dedicati vengono distribuiti in località di Azure e sono gestiti da Azure. Ogni cloud privato può essere segmentato e protetto usando i servizi di rete, ad esempio VLAN e subnet e le tabelle del firewall. Le connessioni all'ambiente locale e alla rete di Azure vengono create usando connessioni VPN sicure, private e ExpressRoute di Azure.

Scopri di più sul [cloud privato CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rete del servizio

Il servizio CloudSimple fornisce una rete per ogni area in cui viene distribuito il servizio CloudSimple. La rete è un singolo spazio di indirizzi TCP di livello 3 con routing abilitato per impostazione predefinita. Tutti i cloud privati e le subnet creati in questa area comunicano tra loro senza alcuna configurazione aggiuntiva. I gruppi di porte distribuite vengono creati in vCenter usando le VLAN. Per configurare e proteggere le risorse del carico di lavoro nel cloud privato, è possibile usare le funzionalità di rete seguenti:

* [VLAN e subnet](cloudsimple-vlans-subnets.md)
* [Tabelle del firewall](cloudsimple-firewall-tables.md)
* [Gateway VPN](cloudsimple-vpn-gateways.md)
* [IP pubblico](cloudsimple-public-ip-address.md)
* [Connessione di rete di Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Macchina virtuale CloudSimple

Con il servizio CloudSimple è possibile gestire le macchine virtuali VMware dal portale di Azure. È possibile eseguire il mapping di uno o più cluster o pool di risorse dell'ambiente vSphere alla sottoscrizione in cui viene creato il servizio.

Altre informazioni su:

* [Macchine virtuali CloudSimple](cloudsimple-virtual-machines.md)
* [Mapping della sottoscrizione di Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
