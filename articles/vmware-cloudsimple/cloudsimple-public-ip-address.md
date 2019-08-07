---
title: Soluzione VMware per CloudSimple-indirizzo IP pubblico di Azure
description: Informazioni sugli indirizzi IP pubblici e sui relativi vantaggi nella soluzione VMware di CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 29d2bdb75a7f4409e6b3ffee2608cfe7adde6b1f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812530"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Panoramica dell'indirizzo IP pubblico di CloudSimple

Un indirizzo IP pubblico consente alle risorse Internet di comunicare in ingresso, a risorse di cloud privato a un indirizzo IP privato. L'indirizzo IP privato può essere una macchina virtuale o un servizio di bilanciamento del carico software. L'indirizzo IP privato è nel cloud privato vCenter. L'indirizzo IP pubblico consente di esporre i servizi in esecuzione nel cloud privato a Internet.

L'indirizzo IP pubblico è dedicato all'indirizzo IP privato fino a quando non lo si Annulla. Un indirizzo IP pubblico può essere assegnato a un solo indirizzo IP privato.

Una risorsa associata a un indirizzo IP pubblico usa sempre l'indirizzo IP pubblico per l'accesso a Internet. Per impostazione predefinita, solo l'accesso a Internet in uscita è consentito in un indirizzo IP pubblico.  Il traffico in ingresso nell'indirizzo IP pubblico è stato negato.  Per consentire il traffico in ingresso, creare una regola del firewall per l'indirizzo IP pubblico per la porta specifica.

## <a name="benefits"></a>Vantaggi

L'uso di un indirizzo IP pubblico per comunicare in ingresso fornisce:

* Prevenzione degli attacchi di tipo Denial of Service (DDoS) distribuito. Questa protezione viene abilitata automaticamente per l'indirizzo IP pubblico.
* Monitoraggio del traffico always on e mitigazione in tempo reale di attacchi comuni a livello di rete. Queste difese sono le stesse difese usate da Microsoft Servizi online.
* Tutta la scala della rete globale di Azure. La rete può essere usata per distribuire e mitigare il traffico degli attacchi tra le aree.  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [allocare un indirizzo IP pubblico](https://docs.azure.cloudsimple.com/public-ips/)
