---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34684983"
---
Aprire una porta o creare un endpoint in una macchina virtuale (VM) di Azure tramite la creazione di un filtro di rete su una subnet o un'interfaccia di rete di VM. Questi filtri, che consentono di controllare il traffico in ingresso e in uscita, vengono inseriti in un gruppo di sicurezza di rete e collegati alla risorsa che riceve il traffico.

Si userà un esempio comune di traffico Web sulla porta 80. Dopo aver ottenuto una VM configurata per rispondere alle richieste Web sulla porta TCP 80 standard, ricordando anche di avviare i servizi e aprire tutte le regole del firewall del sistema operativo nella VM, si procederà a:

1. Creare un gruppo di sicurezza di rete.
2. Creare una regola in ingresso che consenta il traffico con:
   * intervallo di porte di destinazione impostato su "80"
   * intervallo di porte di origine "*", che consente qualsiasi porta di origine
   * valore di priorità minore di 65.500, per ottenere una priorità più alta rispetto al valore predefinito della regola in ingresso di accesso negato di tipo catch-all
3. Associare il gruppo di sicurezza di rete con l'interfaccia di rete della VM o con la subnet

È possibile creare configurazioni di rete complesse per proteggere l'ambiente usando i gruppi di sicurezza di rete e le regole. L'esempio usa solo una o due regole che consentono il traffico HTTP o la gestione remota. Per altre informazioni, vedere la sezione ["Altre informazioni"](#more-information-on-network-security-groups) seguente o [Informazioni sui gruppi di sicurezza di rete](../articles/virtual-network/security-overview.md).

