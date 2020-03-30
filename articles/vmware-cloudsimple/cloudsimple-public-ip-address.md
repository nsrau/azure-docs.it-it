---
title: Soluzione Azure VMware di CloudSimple - Indirizzo IP pubblico
description: Informazioni sugli indirizzi IP pubblici e sui relativi vantaggi nella soluzione Azure VMware di CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024977"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Panoramica dell'indirizzo IP pubblico CloudSimple

Un indirizzo IP pubblico consente alle risorse Internet di comunicare in ingresso alle risorse Private Cloud a un indirizzo IP privato. L'indirizzo IP privato è una macchina virtuale o un servizio di bilanciamento del carico software nel vCenter del cloud privato. L'indirizzo IP pubblico consente di esporre i servizi in esecuzione sul cloud privato a Internet.

L'indirizzo IP pubblico è dedicato all'indirizzo IP privato fino a quando non viene annullato l'assegnazione. Un indirizzo IP pubblico può essere assegnato a un solo indirizzo IP privato.

Una risorsa associata a un indirizzo IP pubblico utilizza sempre l'indirizzo IP pubblico per l'accesso a Internet. Per impostazione predefinita, solo l'accesso a Internet in uscita è consentito su un indirizzo IP pubblico.  Il traffico in ingresso sull'indirizzo IP pubblico viene negato.  Per consentire il traffico in ingresso, creare una regola del firewall per l'indirizzo IP pubblico alla porta specifica.

## <a name="benefits"></a>Vantaggi

L'utilizzo di un indirizzo IP pubblico per comunicare in ingresso consente di:Using a public IP address to communicate inbound provides:

* Prevenzione degli attacchi DDoS (Distributed Denial of Service). Questa protezione viene abilitata automaticamente per l'indirizzo IP pubblico.
* Monitoraggio del traffico sempre in onda e mitigazione in tempo reale di attacchi comuni a livello di rete. Queste difese sono le stesse difese utilizzate dai servizi online Microsoft.
* L'intera scala della rete globale di Azure.The entire scale of the Azure global network. La rete può essere utilizzata per distribuire e mitigare il traffico di attacco tra le aree.  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [assegnare un indirizzo IP pubblico](public-ips.md)