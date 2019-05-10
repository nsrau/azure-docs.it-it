---
title: Soluzione VMware da CloudSimple - indirizzo IP pubblico di Azure
description: Informazioni su indirizzi IP pubblici e i relativi vantaggi nella soluzione di VMware da CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f57b7397f4a2d288cd2b8b55cf23b2d635aa5f8c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209556"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Panoramica sugli indirizzi IP pubblico CloudSimple

Un indirizzo IP pubblico consente alle risorse internet di comunicare in ingresso, per le risorse di cloud privato a un indirizzo IP privato. L'indirizzo IP privato è una macchina virtuale o un servizio di bilanciamento del carico software. L'indirizzo IP privato è in vCenter cloud privato. L'indirizzo IP pubblico consente di esporre servizi in esecuzione sul cloud privato a internet.

L'indirizzo IP pubblico dedicato per l'indirizzo IP privato fino a quando non si annullare l'assegnazione. Un indirizzo IP pubblico può essere assegnato solo a un indirizzo IP privato.

Una risorsa associata sempre un indirizzo IP pubblico Usa l'indirizzo IP pubblico per l'accesso a internet. Per impostazione predefinita, solo l'accesso a internet in uscita è consentito in un indirizzo IP pubblico.  Il traffico in ingresso all'indirizzo IP pubblico è stato negato.  Per consentire il traffico in entrata, creare una regola del firewall per l'indirizzo IP pubblico a una porta specifica.

## <a name="benefits"></a>Vantaggi

L'uso di un indirizzo IP pubblico per la comunicazione in ingresso fornisce:

* Distributed denial di prevenzione degli attacchi dervice (DDoS). Questa protezione viene abilitata automaticamente per l'indirizzo IP pubblico.
* Traffico sempre attivo in tempo reale e monitoraggio mitigazione degli attacchi comuni a livello di rete. Le difese sono le stesse difese usate dai Microsoft online services.
* La scalabilità completa della rete globale di Azure. La rete può essere usata per distribuire e mitigare il traffico degli attacchi tra aree.  

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [allocare un indirizzo IP pubblico](https://docs.azure.cloudsimple.com/public-ips/)
