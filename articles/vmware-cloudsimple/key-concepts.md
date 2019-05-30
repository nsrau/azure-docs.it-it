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
ms.openlocfilehash: 0d890553ee145ca6aafed5a34d158c6a34d9af36
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358169"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Concetti chiave per l'amministrazione di Azure VMware Solution by CloudSimple

Amministra Azure VMware soluzione da CloudSimple richiede la comprensione dei concetti seguenti.

* CloudSimple Service (visualizzato come soluzione di Azure VMware per CloudSimple - Service)
* Nodo CloudSimple (visualizzato come soluzione di Azure VMware per CloudSimple - nodo)
* Cloud privato CloudSimple
* Rete del servizio
* Macchina virtuale CloudSimple (visualizzato come soluzione di Azure VMware per CloudSimple - macchina virtuale)

## <a name="cloudsimple-service"></a>Servizio CloudSimple

Il servizio CloudSimple consente di creare e gestire tutte le risorse associate alle soluzioni di VMware da CloudSimple dal portale di Azure. Creare una risorsa del servizio in ogni area in cui si prevede di usare il servizio. 

Altre informazioni sul [CloudSimple Service](cloudsimple-service.md)

## <a name="cloudsimple-node"></a>Nodo CloudSimple

Un nodo CloudSimple è un dedicato, bare metal e host di calcolo e archiviazione in cui viene distribuito l'hypervisor VMware ESXi, iperconvergente. Questo nodo viene quindi incorporato di VMware vSphere e vCenter, vSAN e piattaforme NSX. Vengono inoltre abilitati CloudSimple servizi di rete e servizi di rete perimetrale. Ogni nodo viene utilizzato come un'unità di capacità di calcolo e archiviazione che è possibile acquistare per creare [cloud privati CloudSimple](cloudsimple-private-cloud.md). Per acquistare o riservare i nodi in un'area in cui il servizio CloudSimple è disponibile.


Altre informazioni su [CloudSimple nodi](cloudsimple-node.md)

## <a name="cloudsimple-private-cloud"></a>Cloud privato CloudSimple

Un Cloud privato CloudSimple è un ambiente isolato di stack VMware gestito da un server vCenter nel proprio dominio di gestione. Stack VMware include gli host ESXi, vSphere, vCenter, vSAN e NSX.  Le esecuzioni dello stack su nodi (dedicato e isolato hardware bare metal) dedicati e viene utilizzato dagli utenti tramite strumenti VMware nativi che includono gestione NSX e vCenter. Nodi dedicati vengono distribuiti in località di Azure e sono gestiti da Azure. Ogni Cloud privato può essere segmentato e protette tramite i servizi, ad esempio le VLAN/subnet di rete e le tabelle del firewall.  Le connessioni per l'ambiente locale e la rete di Azure vengono create tramite VPN sicura e privata e le connessioni ExpressRoute di Azure.

Altre informazioni su [CloudSimple Private Cloud](cloudsimple-private-cloud.md)

## <a name="service-networking"></a>Funzionalità servizio di rete

Il servizio CloudSimple fornisce una rete per ogni area in cui viene distribuito il servizio CloudSimple. La rete è un singolo spazio di indirizzi TCP Layer 3 con routing abilitato per impostazione predefinita. Tutti i cloud privati e le subnet create in questa area comunicano tra loro senza alcuna configurazione aggiuntiva. Si crea gruppi di porte distribuite in vCenter con le reti VLAN.  È possibile usare le funzionalità di rete seguenti per configurare e proteggere le risorse del carico di lavoro nel Cloud privato.

* [VLAN/subnet](cloudsimple-vlans-subnets.md)
* [Tabelle di firewall](cloudsimple-firewall-tables.md)
* [Gateway VPN](cloudsimple-vpn-gateways.md)
* [IP pubblico](cloudsimple-public-ip-address.md)
* [Connessione di rete di Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Macchina virtuale CloudSimple

Il servizio CloudSimple consente di gestire le macchine virtuali VMware nel portale di Azure. Uno o più cluster o i pool di risorse dall'ambiente di vSphere possono essere associati alla sottoscrizione in cui viene creato il servizio.

Altre informazioni su:

* [Macchine virtuali CloudSimple](cloudsimple-virtual-machines.md)
* [Mapping di sottoscrizione di Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
