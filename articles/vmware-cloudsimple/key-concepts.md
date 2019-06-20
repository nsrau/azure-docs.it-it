---
title: Concetti chiave per l'amministrazione di Azure VMware Solution by CloudSimple
description: Descrive i concetti chiave per l'amministrazione di Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3eff61408cb190396987ace6dee21182cff4f25c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165179"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Concetti chiave per l'amministrazione di Azure VMware Solution by CloudSimple

Amministrazione di Azure VMware Solution by CloudSimple richiede una buona conoscenza dei concetti seguenti:

* Servizio CloudSimple, che viene visualizzato come soluzione di Azure VMware da CloudSimple - servizio
* Nodo CloudSimple, che viene visualizzato come soluzione di Azure VMware da CloudSimple - nodo
* Cloud privato CloudSimple
* Funzionalità servizio di rete
* Macchina virtuale CloudSimple, che viene visualizzato come soluzione di Azure VMware da CloudSimple - macchina virtuale

## <a name="cloudsimple-service"></a>Servizio CloudSimple

Con il servizio CloudSimple, è possibile creare e gestire tutte le risorse associate alle soluzioni di VMware da CloudSimple dal portale di Azure. Creare una risorsa del servizio in ogni area in cui si prevede di usare il servizio.

Altre informazioni sul [CloudSimple servizio](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Nodo CloudSimple

Un nodo CloudSimple è un host dedicato, bare metal, iperconvergente di calcolo e archiviazione in cui viene distribuito l'hypervisor VMware ESXi. Questo nodo viene quindi incorporato di VMware vSphere e vCenter, vSAN e piattaforme NSX. Vengono inoltre abilitati CloudSimple servizi di rete e servizi di rete perimetrale. Ogni nodo viene utilizzato come un'unità di capacità di calcolo e archiviazione che è possibile eseguire il provisioning per creare [cloud privati CloudSimple](cloudsimple-private-cloud.md). Per effettuare il provisioning o riservare i nodi in un'area in cui è disponibile il servizio CloudSimple.


Altre informazioni sulle [CloudSimple nodi](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Cloud privato CloudSimple

Un cloud privato CloudSimple è un ambiente isolato di stack VMware gestito da un server vCenter nel proprio dominio di gestione. Lo stack VMware include gli host ESXi, vSphere, vCenter, vSAN e NSX. Le esecuzioni dello stack su nodi (hardware bare metal dedicato e isolato) dedicati e viene utilizzato dagli utenti tramite strumenti VMware nativi che includono gestione NSX e vCenter. Nodi dedicati vengono distribuiti in località di Azure e sono gestiti da Azure. Ogni cloud privato può essere segmentato e protetti tramite servizi di rete, ad esempio le VLAN e subnet e le tabelle di firewall. Le connessioni per l'ambiente locale e la rete di Azure vengono create usando protetta, privata VPN e Azure ExpressRoute connessioni.

Altre informazioni sulle [cloud privato CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Funzionalità servizio di rete

Il servizio CloudSimple fornisce una rete per ogni area in cui viene distribuito il servizio CloudSimple. La rete è un singolo spazio di indirizzi TCP Layer 3 con routing abilitato per impostazione predefinita. Tutti i cloud privati e le subnet create in questa area comunicano tra loro senza alcuna configurazione aggiuntiva. Si crea gruppi di porte distribuite in vCenter con le reti VLAN. Per configurare e proteggere le risorse del carico di lavoro nel cloud privato, è possibile usare le funzionalità di rete seguenti:

* [VLAN e subnet](cloudsimple-vlans-subnets.md)
* [Tabelle di firewall](cloudsimple-firewall-tables.md)
* [Gateway VPN](cloudsimple-vpn-gateways.md)
* [IP pubblico](cloudsimple-public-ip-address.md)
* [Connessione di rete di Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Macchina virtuale CloudSimple

Con il servizio CloudSimple, è possibile gestire le macchine virtuali VMware nel portale di Azure. Uno o più cluster o i pool di risorse dall'ambiente di vSphere possono essere associati alla sottoscrizione in cui viene creato il servizio.

Altre informazioni su:

* [Macchine virtuali CloudSimple](cloudsimple-virtual-machines.md)
* [Mapping di sottoscrizione di Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
