---
title: Concetti chiave per l'amministrazione di Azure VMware Solution da CloudSimple
titleSuffix: Azure VMware Solution by CloudSimple
description: Descrive i concetti chiave per l'amministrazione di soluzioni VMware di Azure da CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efe6cc132b062e833dc85c13cf2f6c5f6289484c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564620"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solutions-by-cloudsimple"></a>Concetti chiave per l'amministrazione delle soluzioni Azure VMware di CloudSimple

L'amministrazione delle soluzioni Azure VMware da parte di CloudSimple richiede una conoscenza dei concetti seguenti:Administering Azure VMware Solutions by CloudSimple requires an understanding of the following concepts:

* Servizio CloudSimple, visualizzato come soluzioni VMware di Azure da CloudSimple - Service
* Nodo CloudSimple, visualizzato come soluzioni VMware di Azure da CloudSimple - Nodo
* CloudSimple cloud privato
* Rete di servizi
* CloudSimple macchina virtuale, che viene visualizzato come soluzioni VMware di Azure da CloudSimple - macchina virtuale

## <a name="cloudsimple-service"></a>Servizio CloudSimple

Con il servizio CloudSimple è possibile creare e gestire tutte le risorse associate a VMware Solutions da CloudSimple dal portale di Azure.With the CloudSimple service, you can create and manage all resources associated with VMware Solutions by CloudSimple from the Azure portal. Creare una risorsa del servizio in ogni area in cui si intende usare il servizio.

Ulteriori informazioni sul [servizio CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Nodo CloudSimple

Un nodo CloudSimple è un host di elaborazione e archiviazione iperconvergente dedicato, bare metal, in cui viene distribuito l'hypervisor VMware ESXi. Questo nodo viene quindi incorporato nelle piattaforme VMware vSphere, vCenter, vSAN e NSX. Sono abilitati anche i servizi di rete CloudSimple e i servizi di rete perimetrale. Ogni nodo funge da unità di capacità di elaborazione e archiviazione di cui è possibile eseguire il provisioning per creare [cloud privati CloudSimple.](cloudsimple-private-cloud.md) È possibile eseguire il provisioning o riservare nodi in un'area in cui è disponibile il servizio CloudSimple.You provision or reserve nodes in a region where the CloudSimple service is available.

Ulteriori informazioni sui [nodi CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>CloudSimple cloud privato

Un cloud privato CloudSimple è un ambiente stack VMware isolato gestito da un server vCenter nel proprio dominio di gestione. Lo stack VMware include host ESXi, vSphere, vCenter, vSAN e NSX. Lo stack viene eseguito su nodi dedicati (hardware bare metal dedicato e isolato) e viene utilizzato dagli utenti tramite strumenti VMware nativi che includono vCenter e NSX Manager. I nodi dedicati vengono distribuiti in percorsi di Azure e gestiti da Azure.Dedicated nodes are deployed in Azure locations and are managed by Azure. Ogni cloud privato può essere segmentato e protetto utilizzando servizi di rete come VLAN e subnet e tabelle firewall. Le connessioni all'ambiente locale e alla rete di Azure vengono create usando connessioni VPN sicure e private e Connessioni Azure ExpressRoute.Connections to your on-premises environment and the Azure network are created by using secure, private VPN, and Azure ExpressRoute connections.

Ulteriori informazioni sul [cloud privato CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Rete di servizi

Il servizio CloudSimple fornisce una rete per ogni area in cui viene distribuito il servizio CloudSimple.The CloudSimple service provides a network per region where your CloudSimple service is deployed. La rete è un singolo spazio di indirizzi TCP Layer 3 con routing abilitato per impostazione predefinita. Tutti i cloud privati e le subnet creati in questa area comunicano tra loro senza alcuna configurazione aggiuntiva. Creare gruppi di porte distribuite sul vCenter utilizzando le VLAN. È possibile usare le funzionalità di rete seguenti per configurare e proteggere le risorse del carico di lavoro nel cloud privato:You can use the following network features to configure and secure your workload resources in your private cloud:

* [VLAN e subnet](cloudsimple-vlans-subnets.md)
* [Tabelle del firewall](cloudsimple-firewall-tables.md)
* [Gateway VPN](cloudsimple-vpn-gateways.md)
* [IP pubblico](cloudsimple-public-ip-address.md)
* [Connessione di rete di Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Macchina virtuale CloudSimple

Con il servizio CloudSimple è possibile gestire le macchine virtuali VMware dal portale di Azure.With the CloudSimple service, you can manage VMware virtual machines from the Azure portal. È possibile eseguire il mapping di uno o più cluster o pool di risorse dall'ambiente vSphere alla sottoscrizione in cui viene creato il servizio.

Altre informazioni su:

* [Macchine virtuali CloudSimple](cloudsimple-virtual-machines.md)
* [Mapping delle sottoscrizioni di AzureAzure subscription mapping](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
