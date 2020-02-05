---
title: Concetti chiave per l'amministrazione di soluzioni VMware di Azure (AVS)
description: Vengono descritti i concetti chiave per l'amministrazione di soluzioni VMware di Azure (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c581ffbba7fbaefc8fbd0ffc7e92350bd69802b8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025232"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-avs"></a>Concetti chiave per l'amministrazione di soluzioni VMware di Azure (AVS)

Per amministrare le soluzioni VMware di Azure (AVS) è necessario comprendere i concetti seguenti:

* Servizio AVS, visualizzato come Azure VMware Solutions (AVS)-Service
* Nodo AVS, visualizzato come Azure VMware Solutions (AVS)-node
* Cloud privato AVS
* Rete del servizio
* Macchina virtuale AVS, visualizzata come Azure VMware Solutions (AVS)-macchina virtuale

## <a name="avs-service"></a>Servizio AVS

Con il servizio AVS è possibile creare e gestire tutte le risorse associate a VMware Solutions (AVS) dal portale di Azure. Creare una risorsa di servizio in ogni area in cui si intende usare il servizio.

Scopri di più sul [servizio AVS](cloudsimple-service.md).

## <a name="avs-node"></a>Nodo AVS

Un nodo AVS è un host di calcolo e archiviazione con iperconvergenza e bare metal dedicato in cui viene distribuito il VMware ESXi hypervisor. Questo nodo viene quindi incorporato nelle piattaforme VMware vSphere, vCenter, rete VSAN e NSX. Sono abilitati anche i servizi di rete AVS e i servizi di rete perimetrale. Ogni nodo funge da unità di capacità di calcolo e di archiviazione di cui è possibile eseguire il provisioning per creare [cloud privati AVS](cloudsimple-private-cloud.md). Si effettua il provisioning o si riservano i nodi in un'area in cui è disponibile il servizio AVS.

Altre informazioni sui [nodi AVS](cloudsimple-node.md).

## <a name="avs-private-cloud"></a>Cloud privato AVS

Un cloud privato AVS è un ambiente di stack VMware isolato gestito da un server vCenter nel proprio dominio di gestione. Lo stack VMware include host ESXi, vSphere, vCenter, rete VSAN e NSX. Lo stack viene eseguito su nodi dedicati (hardware bare metal dedicato e isolato) e viene utilizzato dagli utenti tramite strumenti VMware nativi che includono vCenter e NSX Manager. I nodi dedicati vengono distribuiti in località di Azure e sono gestiti da Azure. Ogni cloud privato AVS può essere segmentato e protetto usando i servizi di rete, ad esempio VLAN e subnet e le tabelle del firewall. Le connessioni all'ambiente locale e alla rete di Azure vengono create usando connessioni VPN sicure, private e ExpressRoute di Azure.

Scopri di più su [AVS private cloud](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rete del servizio

Il servizio AVS fornisce una rete per ogni area in cui viene distribuito il servizio AVS. La rete è un singolo spazio di indirizzi TCP di livello 3 con routing abilitato per impostazione predefinita. Tutte le subnet e i cloud privati AVS creati in questa area comunicano tra loro senza alcuna configurazione aggiuntiva. I gruppi di porte distribuite vengono creati in vCenter usando le VLAN. Per configurare e proteggere le risorse del carico di lavoro nel cloud privato AVS, è possibile usare le funzionalità di rete seguenti:

* [VLAN e subnet](cloudsimple-vlans-subnets.md)
* [Tabelle del firewall](cloudsimple-firewall-tables.md)
* [Gateway VPN](cloudsimple-vpn-gateways.md)
* [IP pubblico](cloudsimple-public-ip-address.md)
* [Connessione di rete di Azure](cloudsimple-azure-network-connection.md)

## <a name="avs-virtual-machine"></a>Macchina virtuale AVS

Con il servizio AVS è possibile gestire le macchine virtuali VMware dal portale di Azure. È possibile eseguire il mapping di uno o più cluster o pool di risorse dell'ambiente vSphere alla sottoscrizione in cui viene creato il servizio.

Altre informazioni su:

* [Macchine virtuali AVS](cloudsimple-virtual-machines.md)
* [Mapping della sottoscrizione di Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
